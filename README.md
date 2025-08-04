# Docker-Compose File

## Visão Geral
Este arquivo `docker-compose.yml` define um ambiente composto por múltiplos serviços, incluindo um túnel da Cloudflare, servidores NGINX, bancos de dados Oracle e um servidor ORDS. Ele utiliza a versão 3.8 do Docker Compose.

## Início Rápido
1. Clone este repositório.
2. Configure as variáveis de ambiente no arquivo `.env`.

## Serviços

### cloudflare_tunnel
- **Imagem**: `cloudflare/cloudflared:latest`
- **Função**: Cria um túnel seguro para a rede Cloudflare, utilizando um token fornecido.

### nginx
- **Imagem**: `jonasal/nginx-certbot:latest`
- **Função**: Servidor NGINX com Certbot para SSL automático.

### apex_db
- **Imagem**: `container-registry.oracle.com/database/express:latest`
- **Função**: Instância do banco de dados Oracle para a aplicação APEX.

### apex_ords
- **Imagem**: `container-registry.oracle.com/database/ords:latest`
- **Função**: Oracle REST Data Services (ORDS) para APEX.

### duplicati
- **Imagem**: `duplicati/duplicati:latest`
- **Função**: Serviço de backup e restauração de dados.

## Observações Importantes
- Variáveis de ambiente devem ser configuradas corretamente para que os serviços funcionem como esperado.
- Ajustes adicionais podem ser necessários dependendo do ambiente de implantação específico.

## Use as credenciais de login abaixo para fazer login pela primeira vez no serviço APEX:
#### apex_ords | Workspace: internal
#### apex_ords | User:      ADMIN
#### apex_ords | Password:  Welcome_1

# Comandos Úteis
### Alterar senha de banco
```bash
docker exec -it $DB_HOST ./setPassword.sh $DB_NEW_PASS
```

### Conectar via sqlplus
```bash
sqlplus sys/$DB_PASS@XEPDB1 as sysdba
```

# Comandos Úteis para o APEX_DB
### Definir Senhas (questão de segurança)
```sql
alter user APEX_LISTENER identified by $NEW_PASS
alter user APEX_REST_PUBLIC_USER identified by $NEW_PASS
alter user APEX_REST_SCHEMA identified by $NEW_PASS
alter user APEX_PUBLIC_USER identified by $NEW_PASS
alter user APEX_INSTANCE_ADMIN identified by $NEW_PASS
ALTER USER ${APEX_WORKSPACE_SCHEMA} DEFAULT TABLESPACE USERS;
```

### Dar privilégios
```sql
GRANT CREATE DATABASE LINK TO $SCHEMA;
GRANT EXECUTE ON sys.dbms_crypto TO $SCHEMA;
GRANT EXECUTE ON sys.utl_http TO $SCHEMA;
```

### Traduzir Apex para português
- Acesar container apex_ords e navegar até o diretório /opt/oracle/apex/23.2.0/builder/pt-br
- Necessário conectar no banco de dados e rodar o script de instalação de idioma
- Rodar script de instalação de idioma
```sql
@load_pt-br.sql
```

### Das permissão de ACL para o APEX (Rodar para o schema do Apex, e o schema do usuário)
```sql
begin
  dbms_network_acl_admin.append_host_ace
    ( host       => '*'
    , lower_port => null
    , upper_port => null
    , ace        => xs$ace_type
                      ( privilege_list => xs$name_list('connect', 'resolve')
                      , principal_name => 'APEX_230200'
                      , principal_type => xs_acl.ptype_db
                      )
    )
  ;
end;
/
```

### Instalar Certificados SSL
- Wallet manual: https://apex.oracle.com/pls/apex/germancommunities/apexcommunity/tipp/6121/index-en.html

- Alterar path para o certificado (manter arquivos dentro de oradata para persistência)
```sql
begin
  apex_instance_admin.set_parameter('WALLET_PATH', 'file:/opt/oracle/oradata/https_wallet');
  apex_instance_admin.set_parameter('WALLET_PWD', 'xxxxxxxxxxxxxxxxxxxxx');
  commit;
end;
```

Adicionar certificado ao wallet
- Baixar certificado root do site a ser adicionado ao wallet, em seguida adicionar ao wallet:
```sh
orapki wallet add -wallet /opt/oracle/oradata/https_wallet -cert cert_name.crt -trusted_cert -pwd xxxxxxxxxxxxxxxxxxxxx
```
# Comandos Úteis para o DATA_DB (opcional)

### Criar database link
```sql
CREATE DATABASE LINK "DATA"
  CONNECT TO "DATA" IDENTIFIED BY VALUES ':1'
  USING '(description =
(address_list =
  (address = (protocol = tcp)(host = data_db)(port = 1521))
)
(connect_data =
     (SERVICE_NAME = XEPDB1)
))';
/
```

### Criar Schema para dados
```sql
CREATE USER $DATA_USER IDENTIFIED BY $DATA_PASS;
```
- Atribuir privilégios
```sql
GRANT CONNECT, RESOURCE, CREATE VIEW TO $DATA_USER;
GRANT CREATE PROCEDURE TO $DATA_USER;
GRANT CREATE SEQUENCE TO $DATA_USER;
GRANT CREATE TRIGGER TO $DATA_USER;
GRANT CREATE TYPE TO $DATA_USER;
GRANT CREATE TABLE TO $DATA_USER;
GRANT CREATE MATERIALIZED VIEW TO $DATA_USER;
GRANT CREATE ANY DIRECTORY TO $DATA_USER;
```
### Atribuir quota ilimitada
```sql
ALTER USER DATA QUOTA UNLIMITED ON USERS;
```
