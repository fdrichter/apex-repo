FROM container-registry.oracle.com/database/ords:23.4.0

ARG APEX_DB_PASS
ARG APEX_DB_SERVICE

# Criar diretório para conexão
RUN mkdir -p /opt/oracle/variables \
    && echo "CONN_STRING=sys/${APEX_DB_PASS}@${APEX_DB_SERVICE}:1521/XEPDB1" > /opt/oracle/variables/conn_string.txt \
    && chmod 777 /opt/oracle/variables/conn_string.txt

# Copiar arquivos de configuração para diretório do container
COPY jetty-compression.xml /opt/oracle/jetty-compression.xml
COPY settings.xml /opt/oracle/settings.xml
COPY pool.xml /opt/oracle/pool.xml

# Criar script de inicialização
COPY <<EOF /opt/oracle/init-config.sh
#!/bin/bash
set -e

# Garantir que o diretório existe
mkdir -p /etc/ords/config/global

# Copiar arquivos de configuração
cp /opt/oracle/jetty-compression.xml /etc/ords/config/global/
cp /opt/oracle/settings.xml /etc/ords/config/global/
cp /opt/oracle/pool.xml /etc/ords/config/global/

# Garantir permissões corretas
chmod 644 /etc/ords/config/global/jetty-compression.xml
chmod 644 /etc/ords/config/global/settings.xml
chmod 644 /etc/ords/config/global/pool.xml

# Executar o comando original do entrypoint
exec "\$@"
EOF

RUN chmod +x /opt/oracle/init-config.sh

# Definir o script como entrypoint
ENTRYPOINT ["/opt/oracle/init-config.sh"]
CMD ["catalina.sh", "run"]
