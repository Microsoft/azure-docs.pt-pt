---
title: Conectividade encriptada utilizando TLS/SSL em Base de Dados Azure para MySQL - Servidor Flexível
description: Instruções e informações sobre como ligar utilizando OTS/SSL na Base de Dados Azure para o MySQL - Servidor Flexível.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 399cf8087d39f78184cfdae4b9f0e34efecaea66
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491617"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Ligue à Base de Dados Azure para o MySQL - Servidor Flexível com ligações encriptadas

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

A Azure Database for MySQL Flexible Server suporta ligar as aplicações do seu cliente ao servidor MySQL utilizando a camada de tomadas seguras (SSL) com a encriptação de segurança da camada de transporte (TLS). O TLS é um protocolo padrão da indústria que garante ligações de rede encriptadas entre o servidor da base de dados e as aplicações do cliente, permitindo-lhe aderir aos requisitos de conformidade.

A Azure Database for MySQL Flexible Server suporta ligações encriptadas utilizando a Segurança da Camada de Transporte (TLS 1.2) por padrão e todas as ligações de entrada com TLS 1.0 e TLS 1.1 serão negadas por padrão. A aplicação de ligação encriptada ou a configuração da versão TLS no seu servidor flexível podem ser alteradas conforme discutido neste artigo. 

Seguem-se as diferentes configurações das definições SSL e TLS que pode ter para o seu servidor flexível:

| Scenario   | Definições de parâmetros do servidor      | Descrição                                    |
|------------|--------------------------------|------------------------------------------------|
|Desativar o SSL (ligações encriptadas) | require_secure_transport = OFF |Se a sua aplicação legado não suportar ligações encriptadas ao servidor MySQL, pode desativar a aplicação de ligações encriptadas ao seu servidor flexível definindo require_secure_transport=OFF.|
|Impor SSL com versão TLS < 1.2 | require_secure_transport = ON and tls_version = TLSV1 ou TLSV1.1| Se a sua aplicação antiga suportar ligações encriptadas mas necessitar da versão TLS < 1.2, pode ativar ligações encriptadas mas configurar o seu servidor flexível para permitir ligações com a versão tls (v1.0 ou v1.1) suportada pela sua aplicação|
|Impor SSL com versão TLS = 1.2 (Configuração padrão)|require_secure_transport = ON and tls_version = TLSV1.2| Esta é a configuração recomendada e padrão para o servidor flexível.|
|Impor SSL com versão TLS = 1.3 (Suportado com MySQL v8.0 ou superior)| require_secure_transport = ON and tls_version = TLSV1.3| Isto é útil e recomendado para o desenvolvimento de novas aplicações|

> [!Note]
> As alterações ao SSL Cipher no servidor flexível não são suportadas. As suítes de cifra FIPS são aplicadas por padrão quando tls_version é definida para a versão TLS 1.2 . Para versões TLS que não a versão 1.2, a Cipher SSL está definida para definições padrão que vêm com a instalação comunitária mySQL.

Neste artigo, aprenderá a:
* Configure o seu servidor flexível 
  * Com SSL desativado 
  * Com sSL aplicado com versão TLS < 1.2
* Ligue-se ao seu servidor flexível utilizando a linha de comando mysql 
  * Com ligações encriptadas desativadas
  * Com ligações encriptadas ativadas
* Verifique o estado de encriptação da sua ligação
* Conecte-se ao seu servidor flexível com ligações encriptadas utilizando várias estruturas de aplicação

## <a name="disable-ssl-on-your-flexible-server"></a>Desative o SSL no seu servidor flexível
Se a sua aplicação ao cliente não suportar ligações encriptadas, terá de desativar a aplicação de ligações encriptadas no seu servidor flexível. Para desativar a aplicação de ligações encriptadas, terá de definir require_secure_transport parâmetro do servidor para OFF, como mostrado na imagem e guardar a configuração do parâmetro do servidor para que este produza efeitos. require_secure_transport é um **parâmetro dinâmico do servidor** que entra em vigor imediatamente e não requer o reinício do servidor para fazer efeito.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Screenshot mostrando como desativar SSL com Base de Dados Azure para servidor flexível MySQL.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Conecte-se usando o cliente da linha de comando mysql com SSL desativado

O exemplo a seguir mostra como ligar-se ao seu servidor utilizando a interface de linha de comando mysql. Utilize a `--ssl-mode=DISABLED` definição de cadeia de ligação para desativar a ligação TLS/SSL do cliente Mysql. Substitua os valores pelo nome e senha do seu servidor. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
É importante notar que a definição require_secure_transport para OFF não significa que as ligações encriptadas não sejam suportadas no lado do servidor. Se definir require_secure_transport para OFF no servidor flexível, mas se o cliente ligar-se com a ligação encriptada, ele ainda será aceite. A seguinte ligação utilizando o cliente mysql para um servidor flexível configurado com require_secure_transport=OFF também funcionará como mostrado abaixo.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

Em resumo, require_secure_transport=OFF a definição relaxa a aplicação de ligações encriptadas no servidor flexível e permite ligações não encriptadas ao servidor do cliente além das ligações encriptadas.

## <a name="enforce-ssl-with-tls-version--12"></a>Impor SSL com versão TLS < 1.2

Se a sua aplicação suportar ligações ao servidor MySQL com SSL, mas suporta a versão TLS < 1.2, será necessário definir o parâmetro do servidor de versões TLS no seu servidor flexível. Para definir as versões TLS que pretende que o seu servidor flexível suporte, terá de definir tls_version parâmetro do servidor para TLSV1, TLSV1.1 ou TLSV1 e TLSV1.1, como mostrado na imagem e guardar a configuração do parâmetro do servidor para que produza efeito. tls_version é um **parâmetro estático do servidor** que exigirá o reinício do servidor para que o parâmetro entre em vigor.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Screenshot mostrando como definir a versão tls para uma Base de Dados Azure para servidor flexível MySQL.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Conecte-se usando o cliente da linha de comando mysql com tLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Faça o download do certificado público SSL
Para utilizar ligações encriptadas com as aplicações do seu cliente, terá de descarregar o [certificado SSL público](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) que também está disponível na lâmina de rede do portal Azure, como mostrado na imagem abaixo.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Screenshot mostrando como baixar o certificado SSL público do portal Azure.":::

Guarde o ficheiro de certificado para a sua localização preferida. Por exemplo, este tutorial utiliza `c:\ssl` ou no seu ambiente local ou no ambiente cliente onde a sua `\var\www\html\bin` aplicação está hospedada. Isto permitirá que as aplicações se conectem de forma segura à base de dados sobre ssl.

Se criou o seu servidor flexível com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível.

Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)*, pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor.

Pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md)-- > ligar ao servidor a partir do ambiente local. 

O exemplo a seguir mostra como ligar-se ao seu servidor utilizando a interface de linha de comando mysql. Utilize a `--ssl-mode=REQUIRED` definição de cadeia de ligação para impor a verificação do certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o `--ssl-ca` parâmetro. Substitua os valores pelo nome e senha do seu servidor. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Confirme que o valor passado corresponde `--ssl-ca` ao caminho do ficheiro para o certificado que guardou.

Se tentar ligar ao seu servidor com ligações não encriptadas, verá que as ligações com recurso a transporte inseguro são proibidas de se encontrar semelhantes às que se seguem:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>Verifique a ligação TLS/SSL

Execute o comando **de estado** mysql para verificar se ligou ao seu servidor MySQL utilizando TLS/SSL:

```dos
mysql> status
```
Confirme que a ligação é encriptada através da revisão da saída, que deve mostrar: **SSL: Cipher em uso é **. Esta suíte de cifra mostra um exemplo e, com base no cliente, você pode ver uma suíte de cifra diferente.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Conecte-se ao seu servidor flexível com ligações encriptadas utilizando várias estruturas de aplicação

As cadeias de ligação pré-definidas na página "Connection Strings" disponível para o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao servidor da base de dados utilizando o TLS/SSL. O parâmetro TLS/SSL varia em com base no conector. Por exemplo, "useSSL=true", "sslmode=required", ou "ssl_verify_cert=true" e outras variações.

Para estabelecer uma ligação encriptada ao seu servidor flexível sobre o TLS/SSL a partir da sua aplicação, consulte as seguintes amostras de código:

### <a name="wordpress"></a>WordPress
Baixe [o certificado público SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e adicione as seguintes linhas em wp-config.php após a linha ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (utilização de DOP)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (Conector MySQL para Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (Conector MariaDB para Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Passos seguintes
- [Utilize a bancada mySQL workbench para ligar e consultar dados na Base de Dados Azure para o MySQL Flexible Server](./connect-workbench.md)
- [Utilize PHP para ligar e consultar dados na Base de Dados Azure para o MySQL Flexible Server](./connect-php.md)
- [Crie e gere a base de dados Azure para a rede virtual MySQL Flexible Server utilizando o Azure CLI](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre [networking em Azure Database para MySQL Flexible Server](./concepts-networking.md)
- Conheça mais sobre [a Base de Dados Azure para as regras de firewall do MySQL Flexible Server](./concepts-networking.md#public-access-allowed-ip-addresses)
