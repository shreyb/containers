FROM opensciencegrid/software-base:fresh
ENV GLIDEINWMS_RELEASE=3.7.1

# Using http://www.t2.ucsd.edu/twiki2/bin/view/UCSDTier2/FactoryRpmInstall as a basic procedure

# Make sure we have a gfactory user ahead of time
RUN useradd gfactory


# Install preliminary RPMs
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

# Bunch of config stuff goes here
#
#


# Condor tarballs for each destination OS (8.9)
ADD https://research.cs.wisc.edu/htcondor/tarball/8.9/8.9.9/release/condor-8.9.9-x86_64_CentOS8-stripped.tar.gz /var/lib/gwms-factory/condor/condor-8.9.9-x86_64_CentOS8-stripped.tar.gz
ADD https://research.cs.wisc.edu/htcondor/tarball/8.9/8.9.9/release/condor-8.9.9-x86_64_CentOS7-stripped.tar.gz /var/lib/gwms-factory/condor/condor-8.9.9-x86_64_CentOS7-stripped.tar.gz

# Condor tarballs for each destination OS (8.8)
ADD https://research.cs.wisc.edu/htcondor/tarball/8.8/8.8.12/release/condor-8.8.12-x86_64_CentOS6-stripped.tar.gz /var/lib/gwms-factory/condor/condor-8.8.12-x86_64_CentOS6-stripped.tar.gz
ADD https://research.cs.wisc.edu/htcondor/tarball/8.8/8.8.12/release/condor-8.8.12-x86_64_CentOS7-stripped.tar.gz /var/lib/gwms-factory/condor/condor-8.8.12-x86_64_CentOS7-stripped.tar.gz


# Create stripped tarballs with correct name
WORKDIR /var/lib/gwms-factory/condor
RUN for i in ./condor-*.tar.gz; do \
	create_condor_tarball gfactory-${GLIDEINWMS_RELEASE}-`basename $i` $i ;\
	done


# Pull down OSG factory config
ADD http://gfactory-2.opensciencegrid.org/glideinWMS.xml /etc/gwms-factory/glideinWMS.xml
# For glideinWMS.xml, delete all the condor_tarball lines that are _not_ specifying the current release ($GLIDEINWMS_RELEASE)
RUN awk '!/condor_tarball/ || (/condor_tarballs/ || /${GLIDEINWMS_RELEASE}/) ' /etc/gwms-factory/glideinWMS.xml > /tmp/glideinWMS.xml && mv -f /tmp/glideinWMS.xml /etc/gwms-factory/glideinWMS.xml

# Delete all the file entries from the config since we're not bringing extras along
RUN sed '/\<files\>/,/\<files\>/{//!d}' /etc/gwms-factory/glideinWMS.xml > /tmp/glideinWMS.xml && mv -f /tmp/glideinWMS.xml /etc/gwms-factory/glideinWMS.xml


RUN chown -R gfactory:gfactory /etc/gwms-factory/glideinWMS.xml


# Run upgrade for gwms-factory
RUN gwms-factory upgrade


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
