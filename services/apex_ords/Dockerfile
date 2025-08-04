FROM container-registry.oracle.com/database/ords:23.4.0

ARG APEX_DB_PASS
ARG APEX_DB_SERVICE

RUN mkdir -p /opt/oracle/variables \
    && echo "CONN_STRING=sys/${APEX_DB_PASS}@${APEX_DB_SERVICE}:1521/XEPDB1" > /opt/oracle/variables/conn_string.txt \
    && chmod 777 /opt/oracle/variables/conn_string.txt
