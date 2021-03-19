---
title: Conectividade encriptada utilizando TLS/SSL em Base de Dados Azure para MySQL - Servidor Flexível
description: Instruções e informações sobre como ligar utilizando OTS/SSL na Base de Dados Azure para o MySQL - Servidor Flexível.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90940489"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>Conectividade encriptada utilizando a segurança da camada de transporte (TLS 1.2) na Base de Dados Azure para MySQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

A Azure Database for MySQL Flexible Server suporta ligar as aplicações do seu cliente ao serviço MySQL utilizando a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL). O TLS é um protocolo padrão da indústria que garante ligações de rede encriptadas entre o servidor da base de dados e as aplicações do cliente, permitindo-lhe aderir aos requisitos de conformidade.

A base de dados Azure para o MySQL Flexible Server apenas suporta ligações encriptadas utilizando a Segurança da Camada de Transporte (TLS 1.2) e todas as ligações de entrada com TLS 1.0 e TLS 1.1 serão negadas. Para todos os servidores flexíveis, a aplicação das ligações TLS está ativada e não é possível desativar o TLS/SSL para a ligação ao servidor flexível.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Aplicações que requerem verificação de certificados para conectividade TLS/SSL
Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectarem de forma segura. Azure Database for MySQL Flexible Server usa *DigiCert Global Root CA*. Faça o download deste certificado necessário para comunicar através do SSL da [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e guarde o ficheiro de certificado para a sua localização preferida. Por exemplo, este tutorial `c:\ssl` utiliza.

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Conecte-se usando o cliente da linha de comando mysql com tLS/SSL

Se criou o seu servidor flexível com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível.

Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)*, pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor.

Pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md)-- > ligar ao servidor a partir do ambiente local. 

O exemplo a seguir mostra como ligar-se ao seu servidor utilizando a interface de linha de comando mysql. Utilize a `--ssl-mode=REQUIRED` definição de cadeia de ligação para impor a verificação do certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o `--ssl-ca` parâmetro. Substitua os valores pelo nome e senha do seu servidor. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Confirme que o valor passado corresponde `--ssl-ca` ao caminho do ficheiro para o certificado que guardou.

### <a name="verify-the-tlsssl-connection"></a>Verifique a ligação TLS/SSL

Execute o comando **de estado** mysql para verificar se ligou ao seu servidor MySQL utilizando TLS/SSL:

```dos
mysql> status
```
Confirme que a ligação é encriptada através da revisão da saída, que deve mostrar:  **SSL: Cifra em uso é AES256-SHA**. Esta suíte de cifra mostra um exemplo e, com base no cliente, você pode ver uma suíte de cifra diferente.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que a sua aplicação ou enquadramento suporta ligações TLS

Alguns quadros de aplicação que utilizam o MySQL para os seus serviços de base de dados não permitem o TLS por padrão durante a instalação. O seu servidor MySQL aplica ligações TLS, mas se a aplicação não estiver configurada para TLS, a aplicação poderá não conseguir ligar-se ao servidor de base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações TLS.

## <a name="sample-code"></a>Código de exemplo
As cadeias de ligação pré-definidas na página "Connection Strings" disponível para o seu servidor no portal Azure incluem os parâmetros necessários para que as línguas comuns se conectem ao servidor da base de dados utilizando o TLS/SSL. O parâmetro TLS/SSL varia em com base no conector. Por exemplo, "useSSL=true", "sslmode=required", ou "ssl_verify_cert=true" e outras variações.

Para estabelecer uma ligação encriptada ao seu servidor flexível sobre o TLS/SSL a partir da sua aplicação, consulte as seguintes amostras de código:

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
