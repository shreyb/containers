FROM opensciencegrid/software-base:fresh

# Using http://www.t2.ucsd.edu/twiki2/bin/view/UCSDTier2/FactoryRpmInstall as a basic procedure

# Make sure we have a gfactory user ahead of time
RUN useradd gfactory


# Install preliminary RPMs
RUN yum -y install wget
RUN yum -y install osg-ca-certs
RUN yum -y install condor
# yum can't find this for some reason - leave it out for now
# RUN yum -y install condor-cream-gahp


# Use osg-upcoming until gwms factory 3.7 is available from osg-release
RUN yum -y --enablerepo=osg-upcoming install glideinwms-factory-condor \
	glideinwms-factory

# Use OSG schedd and secondary schedd configs instead of what ships with rpm:
WORKDIR /etc/condor/config.d
# RUN rm 02_gwms_factory_schedds.config
ADD https://raw.githubusercontent.com/jdost321/factools/master/etc/condor_config/02_gwms_factory_schedds.config  02_gwms_factory_schedds.config  
ADD https://raw.githubusercontent.com/jdost321/factools/master/etc/condor_config/11_gwms_factory_secondary_schedds.config.8.6 11_gwms_factory_secondary_schedds.config

# Create secondary schedd dirs
RUN for ((i = 1;i <= 9;i++)); do \
	install -o condor -g condor -d /var/lib/condor/schedd_glideins${i}/execute;\
	install -o condor -g condor -d /var/lib/condor/schedd_glideins${i}/lock ;\
	install -o condor -g condor -d /var/lib/condor/schedd_glideins${i}/procd_pipe ;\
	install -o condor -g condor -d /var/lib/condor/schedd_glideins${i}/spool ;\
	done

# Create the FE DN file:
RUN touch 90_gwms_dns.config
RUN touch 99_local_tweaks.config




#
#
#
## Install various RPMS
#RUN yum install -y yum-plugin-priorities && \
#	yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm # EPEL
#
## Change this as soon as the cert gets resolved.  We should be simply doing yum install -y REPO for each of osg and condor
## RUN yum install -y wget
## RUN wget --no-check-certificate https://repo.opensciencegrid.org/osg/3.5/osg-3.5-el7-release-latest.rpm  # Change this as soon as cert get resolved!
#
#
## Switch these when the issue is fixed
#RUN yum install -y https://repo.opensciencegrid.org/osg/3.5/osg-3.5-el7-release-latest.rpm
#RUN yum install -y condor.x86_64
#RUN yum install -y glideinwms-factory
#
## RUN rpm -i osg-3.5-el7-release-latest.rpm
##wget --no-check-certificate https://repo.opensciencegrid.org/osg/goc/el7/production/x86_64/condor-8.6.13-1.4.osg34.el7.x86_64.rpm 
##rpm -i condor-8.6.13-1.4.osg34.el7.x86_64.rpm
#
## Proxy stuff
#RUN yum install -y vo-client  # /etc/vomses file
#RUN yum install -y voms-clients  # voms-proxy-init, etc
#
#
#
#
#
