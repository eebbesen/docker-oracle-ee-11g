FROM oraclelinux

ARG hhost
RUN groupadd dba && useradd -m -G dba oracle && mkdir /u01 && chown oracle:dba /u01
RUN yum install -y yum install oracle-rdbms-server-11gR2-preinstall glibc-static wget unzip && yum clean all
RUN wget -O linux.x64_11gR2_database_1of2.zip http://$hhost:9090/linux.x64_11gR2_database_1of2.zip
RUN wget -q -O linux.x64_11gR2_database_2of2.zip http://$hhost:9090/linux.x64_11gR2_database_2of2.zip
RUN rm -r linux*.zip
ADD install /install
RUN /install/oracle_install.sh && rm /install/*
