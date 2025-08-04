FROM container-registry.oracle.com/database/ords:23.4.0

ARG APEX_DB_PASS
ARG APEX_DB_SERVICE

# Criar diretórios necessários e preparar conexão
RUN mkdir -p /opt/oracle/variables \
    && mkdir -p /etc/ords/config/global \
    && echo "CONN_STRING=sys/${APEX_DB_PASS}@${APEX_DB_SERVICE}:1521/XEPDB1" > /opt/oracle/variables/conn_string.txt \
    && chmod 777 /opt/oracle/variables/conn_string.txt

# Copiar arquivos de configuração diretamente no container
COPY jetty-compression.xml /etc/ords/config/global/
COPY settings.xml /etc/ords/config/global/

# Definir permissões para os arquivos copiados
RUN chmod 644 /etc/ords/config/global/jetty-compression.xml \
    && chmod 644 /etc/ords/config/global/settings.xml
