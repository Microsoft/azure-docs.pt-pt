---
title: Configurar SSL-banco de dados do Azure para MySQL
description: Instruções sobre como configurar corretamente o banco de dados do Azure para MySQL e os aplicativos associados para usar corretamente as conexões SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 590e2e7e735af55f8c7ad96f87f947115f8783fc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770565"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurar a conectividade SSL em seu aplicativo para se conectar com segurança ao banco de dados do Azure para MySQL
O banco de dados do Azure para MySQL dá suporte à conexão do servidor de banco de dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="step-1-obtain-ssl-certificate"></a>Etapa 1: obter o certificado SSL
Baixe o certificado necessário para se comunicar por SSL com o servidor de banco de dados do Azure para MySQL do [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e salve o arquivo de certificado em sua unidade local (este tutorial usa c:\ssl, por exemplo).
**Para o Microsoft Internet Explorer e o Microsoft Edge:** Após a conclusão do download, renomeie o certificado para BaltimoreCyberTrustRoot. CRT. PEM.

## <a name="step-2-bind-ssl"></a>Etapa 2: associar SSL

Para obter cadeias de conexão de linguagem de programação específica, consulte o [código de exemplo](howto-configure-ssl.md#sample-code) abaixo.

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Conectando ao servidor usando o MySQL Workbench sobre SSL
Configure o MySQL Workbench para se conectar com segurança via SSL. 

1. Na caixa de diálogo Configurar nova conexão, navegue até a guia **SSL** . 

1. Atualize o campo **usar SSL** para "exigir".

1. No campo **arquivo de AC SSL:** , insira o local do arquivo de **BaltimoreCyberTrustRoot. CRT. pem**. 
    
    ![Salvar configuração SSL](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Para conexões existentes, você pode associar o SSL clicando com o botão direito do mouse no ícone de conexão e escolhendo Editar. Em seguida, navegue até a guia **SSL** e associe o arquivo de certificado.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Conectando-se ao servidor usando a CLI do MySQL sobre SSL
Outra maneira de associar o certificado SSL é usar a interface de linha de comando do MySQL executando os comandos a seguir. 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Ao usar a interface de linha de comando do MySQL no Windows, você pode receber um erro `SSL connection error: Certificate signature check failed`. Se isso ocorrer, substitua os parâmetros de `--ssl-mode=REQUIRED --ssl-ca={filepath}` por `--ssl`.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Etapa 3: impondo conexões SSL no Azure 
### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
Usando o portal do Azure, visite seu banco de dados do Azure para servidor MySQL e clique em **segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **impor conexão SSL** e, em seguida, clique em **salvar**. A Microsoft recomenda sempre habilitar a configuração **impor conexão SSL** para aumentar a segurança.
![Enable-SSL](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Você pode habilitar ou desabilitar o parâmetro de **imposição de SSL** usando valores habilitados ou desabilitados, respectivamente em CLI do Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Etapa 4: verificar a conexão SSL
Execute o comando de **status** do MySQL para verificar se você se conectou ao servidor MySQL usando SSL:
```dos
mysql> status
```
Confirme se a conexão está criptografada examinando a saída, que deve mostrar: **SSL: a criptografia em uso é AES256-SHA** 

## <a name="sample-code"></a>Código de exemplo
Para estabelecer uma conexão segura com o banco de dados do Azure para MySQL sobre SSL do seu aplicativo, consulte os exemplos de código a seguir:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (usando PDO)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-mysql-connector-for-java"></a>Java (conector do MySQL para Java)
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb-connector-for-java"></a>Java (conector do MariaDB para Java)
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Passos seguintes
Examinar várias opções de conectividade de aplicativo seguindo [bibliotecas de conexão para o banco de dados do Azure para MySQL](concepts-connection-libraries.md)
