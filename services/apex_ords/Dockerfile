FROM container-registry.oracle.com/database/ords:23.4.0

ARG APEX_DB_PASS
ARG APEX_DB_SERVICE

# Criar diretório para conexão
RUN mkdir -p /opt/oracle/variables \
    && echo "CONN_STRING=sys/${APEX_DB_PASS}@${APEX_DB_SERVICE}:1521/XEPDB1" > /opt/oracle/variables/conn_string.txt \
    && chmod 777 /opt/oracle/variables/conn_string.txt

# Criar diretório temporário para arquivos de configuração
RUN mkdir -p /tmp/config

# Copiar arquivos de configuração para diretório temporário
COPY jetty-compression.xml /tmp/config/
COPY settings.xml /tmp/config/
COPY pool.xml /tmp/config/

# Criar diretório de destino e mover arquivos com permissões corretas
RUN mkdir -p /etc/ords/config/global \
    && cp /tmp/config/jetty-compression.xml /etc/ords/config/global/ \
    && cp /tmp/config/settings.xml /etc/ords/config/global/ \
    && cp /tmp/config/pool.xml /etc/ords/config/global/ \
    && chown -R root:root /etc/ords/config/global/ \
    && chmod -R 644 /etc/ords/config/global/
