# General environment variables
SHELL=/bin/bash
CONTIKIDIR=../../..
COOJADIR=${CONTIKIDIR}/tools/cooja
COOJA_USER_CONFIG=~/.cooja.user.properties
COOJA_ROOT_CONFIG=/root/.cooja.user.properties
COOJA_CONFIG_SAMPLE=./conf-templates/cooja.user.properties.example

# COOJA generation
COOJAGEN=./coojagen

launch-6lbr-router-1-cooja:
	../package/usr/bin/6lbr conf-templates/cooja-1-router.conf

cooja-small: preset_1dag_10nodes.csc build-cooja config-cooja launch-cooja

launch-cooja:
	java -jar ${COOJADIR}/dist/cooja.jar -quickstart=`cat ${COOJAGEN}/output/LASTFILE`

%.csc: ${COOJAGEN}/examples/config_%.py ${COOJAGEN}/templates/%
	python2.7 gensimfile.py $<

build-cooja: serial2pty radiologger-headless
	pushd ${COOJADIR}; ant jar; popd

config-cooja:
	if [ ! -f $(COOJA_USER_CONFIG) ]; then cp $(COOJA_CONFIG_SAMPLE) $(COOJA_USER_CONFIG); fi
	if [ `whoami` = root ] && [ ! -f $(COOJA_ROOT_CONFIG) ] ; then cp $(COOJA_CONFIG_SAMPLE) $(COOJA_ROOT_CONFIG); fi
	if ! grep -Fq "serial2pty" $(COOJA_USER_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/serial2pty/" $(COOJA_USER_CONFIG); fi
	if ! grep -Fq "radiologger-headless" $(COOJA_USER_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/radiologger-headless/" $(COOJA_USER_CONFIG); fi
	if [ `whoami` = root ] && ! grep -Fq serial2pty $(COOJA_ROOT_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/serial2pty/" $(COOJA_ROOT_CONFIG); fi
	if [ `whoami` = root ] && ! grep -Fq radiologger-headless $(COOJA_ROOT_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/radiologger-headless/" $(COOJA_ROOT_CONFIG); fi

serial2pty:
	pushd ${COOJADIR}/apps/serial2pty; ant jar; popd

radiologger-headless:	
	pushd ${COOJADIR}/apps/radiologger-headless; ant jar; popd
