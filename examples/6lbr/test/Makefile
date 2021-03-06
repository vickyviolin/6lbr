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

launch-6lbr-smartbridge-2br-cooja:
	@echo "*************************************************************************"
	@echo "* Creating bridge in LAZY mode, needs to be deleted manually at the end *"
	@echo "* Example:    ip link set br0 down                                      *"
	@echo "*             brctl delbr br0                                           *"
	@echo "*************************************************************************"
	../package/usr/bin/6lbr conf-templates/cooja-2-smartbridge-a.conf &
	../package/usr/bin/6lbr conf-templates/cooja-2-smartbridge-b.conf &

cooja-small: preset_1dag_10nodes.csc build-cooja config-cooja launch-cooja

cooja-2br-grid: grid_2br_3x6.csc build-cooja config-cooja launch-cooja

launch-cooja:
	java -jar ${COOJADIR}/dist/cooja.jar -quickstart=`cat ${COOJAGEN}/output/LASTFILE`

%.csc: ${COOJAGEN}/examples/config_%.py
	python2.7 gensimfile.py $<

build-cooja: do-build-cooja serial2pty radiologger-headless

do-build-cooja:
	pushd ${COOJADIR}; ant jar; popd

config-cooja:
	if [ ! -f $(COOJA_USER_CONFIG) ]; then cp $(COOJA_CONFIG_SAMPLE) $(COOJA_USER_CONFIG); fi
	if [ `whoami` = root ] && [ ! -f $(COOJA_ROOT_CONFIG) ] ; then cp $(COOJA_CONFIG_SAMPLE) $(COOJA_ROOT_CONFIG); fi
	if ! grep -Fq "serial2pty" $(COOJA_USER_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/serial2pty/" $(COOJA_USER_CONFIG); fi
	if ! grep -Fq "radiologger-headless" $(COOJA_USER_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/radiologger-headless/" $(COOJA_USER_CONFIG); fi
	if [ `whoami` = root ] && ! grep -Fq serial2pty $(COOJA_ROOT_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/serial2pty/" $(COOJA_ROOT_CONFIG); fi
	if [ `whoami` = root ] && ! grep -Fq radiologger-headless $(COOJA_ROOT_CONFIG) ; then sed -i "/^DEFAULT_PROJECTDIRS=/ s/\$$/\;\[APPS_DIR\]\/radiologger-headless/" $(COOJA_ROOT_CONFIG); fi

serial2pty:
	pushd ${COOJADIR}/apps/serial2pty; echo $(JAVA_HOME); ant jar; popd

radiologger-headless:	
	pushd ${COOJADIR}/apps/radiologger-headless; ant jar; popd

non-regression: nr-router nr-smartbridge nr-rpl-root-6lr nr-ndp-router nr-rpl-root-rpl-relay nr-multi-smartbridge nr-rpl-root-multi-rpl-relay nr-rpl-multi-6lr

nr-router:
	$(MAKE) preset_1dag_10nodes.csc
	python2.7 run_tests.py --scenarios NonRegressionScenarios --modes Router

nr-smartbridge:
	$(MAKE) preset_1dag_10nodes.csc
	python2.7 run_tests.py --scenarios NonRegressionScenarios --modes SmartBridge

nr-rpl-root-6lr:
	$(MAKE) preset_1dag_10nodes.csc
	python2.7 run_tests.py --scenarios NonRegressionScenarios --modes RplRoot6lr

nr-ndp-router:
	$(MAKE) preset_1dag_1node.csc
	python2.7 run_tests.py --scenarios NonRegressionScenarios --modes NdpRouter

nr-rpl-root-rpl-relay:
	$(MAKE) preset_1dag_10nodes.csc
	python2.7 run_tests.py --scenarios NonRegressionScenarios --modes RplRootRplRelay

nr-multi-smartbridge:
	$(MAKE) preset_2dags_20nodes_disjoint.csc
	python2.7 run_tests.py --scenarios MultiBrDisjointScenarios --modes MultiSmartBridge
	$(MAKE) grid_2br_3x6.csc
	python2.7 run_tests.py --scenarios MultiBrOverlapScenarios --modes MultiSmartBridge

nr-rpl-root-multi-rpl-relay:
	$(MAKE) preset_2dags_20nodes_disjoint.csc
	python2.7 run_tests.py --scenarios MultiBrDisjointScenarios --modes RplRootMultiRplRelay
	$(MAKE) grid_2br_3x6.csc
	python2.7 run_tests.py --scenarios MultiBrOverlapScenarios --modes RplRootMultiRplRelay

nr-rpl-multi-6lr:
	$(MAKE) preset_2dags_20nodes_disjoint.csc
	python2.7 run_tests.py --scenarios MultiBrDisjointScenarios --modes Multi6lr
	$(MAKE) grid_2br_3x6.csc
	python2.7 run_tests.py --scenarios MultiBrOverlapScenarios --modes Multi6lr
	
