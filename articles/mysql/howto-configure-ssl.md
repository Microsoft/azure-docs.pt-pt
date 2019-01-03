---
title: Configurar a conectividade SSL para ligar em segurança à base de dados do Azure para MySQL
description: Instruções sobre como configurar corretamente a base de dados do Azure para MySQL e aplicações associadas a corretamente utilizar ligações SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 075f20027153eb9adf5c0daedea7cf5c0b515ee4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537040"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurar a conectividade SSL na sua aplicação para ligar em segurança à base de dados do Azure para MySQL
Base de dados do Azure para MySQL suporta a ligar a sua base de dados do Azure para o servidor MySQL para aplicações de cliente, utilizando Secure Sockets Layer (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="step-1-obtain-ssl-certificate"></a>Passo 1: Obter o certificado SSL
Transferir o certificado necessário para se comunicar através de SSL com a base de dados do Azure para servidor MySQL a partir [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e guarde o ficheiro de certificado no disco local (Este tutorial utiliza c:\ssl por exemplo).
**Para o Microsoft Internet Explorer e o Microsoft Edge:** Após a conclusão do download, mudar o nome do certificado para BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Passo 2: Enlace SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>A ligar ao servidor usando a Bancada de trabalho do MySQL através de SSL
Configure o MySQL Workbench ligar de forma segura através de SSL. A caixa de diálogo Configurar ligação nova, navegue para o **SSL** separador. Na **arquivo da AC de SSL:** campo, introduza a localização do ficheiro da **BaltimoreCyberTrustRoot.crt.pem**. 
![Guardar mosaico personalizado](./media/howto-configure-ssl/mysql-workbench-ssl.png) para ligações existentes, pode fazer a ligação SSL ao clicar no ícone de conexão e selecione edit. Em seguida, navegue para o **SSL** separador e vincular o ficheiro de certificado.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>A ligar ao servidor com a CLI de MySQL através de SSL
Outra maneira de vincular o certificado SSL é usar a interface de linha de comandos do MySQL ao executar o comando seguinte:
```dos
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Passo 3:  A imposição de ligações de SSL no Azure 
### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
Utilizar o portal do Azure, visite a sua base de dados do Azure para o servidor MySQL e, em seguida, clique em **segurança de ligação**. Utilize o botão de alternar para ativar ou desativar a **ligação impor SSL** definição e, em seguida, clique em **guardar**. A Microsoft recomenda-se para sempre habilitar a **ligação impor SSL** definição para uma maior segurança.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Pode ativar ou desativar a **imposição de ssl** parâmetro utilizando valores habilitado ou desabilitado respectivamente na CLI do Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Passo 4: Verificar a ligação de SSL
Executar o mysql **estado** comando para verificar se tem ser ligada ao seu servidor MySQL através de SSL:
```dos
mysql> status
```
Confirme que a conexão seja criptografada revisando a saída, o que deve mostrar:  **SSL: Cifras em utilização é AES256 SHA** 

## <a name="sample-code"></a>Código de exemplo
Para estabelecer uma ligação segura à base de dados do Azure para MySQL através de SSL através da aplicação, consulte os seguintes exemplos de código:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
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
### <a name="javajdbc"></a>JAVA(JDBC)
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
### <a name="javamariadb"></a>Java(MariaDB)
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

## <a name="next-steps"></a>Passos Seguintes
Reveja as várias opções de conectividade de aplicativo seguindo [bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md)
