---
title: Configure SSL - Base de Dados Azure para MariaDB
description: Instruções para configurar corretamente a Base de Dados de Azure para a MariaDB e aplicações associadas para utilizar corretamente as ligações SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1b7127fd4807e1ddd3d5181d73c7f8e77f10cf77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055990"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Configure a conectividade SSL na sua aplicação para ligar de forma segura à Base de Dados Azure para MariaDB
A Azure Database for MariaDB suporta ligar a sua Base de Dados Azure para servidor MariaDB a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="obtain-ssl-certificate"></a>Obter certificado SSL


Faça o download do certificado necessário para comunicar através do SSL com a sua Base de Dados Azure para o servidor MariaDB e guarde o ficheiro de [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) certificado para a sua unidade local (este tutorial utiliza c:\ssl, por exemplo).
**Para o Microsoft Internet Explorer e Microsoft Edge:** Depois de concluído o download, rebatize o certificado para BaltimoreCyberTrustRoot.crt.pem.

> [!IMPORTANT] 
> O certificado de raiz SSL está previsto expirar a partir de 26 de outubro de 2020 (10/26/2020). Por favor, atualize a sua aplicação para utilizar o [novo certificado.](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) Para saber mais, consulte [as atualizações de certificados planeadas](concepts-certificate-rotation.md)

Consulte os seguintes links para certificados para servidores em nuvens soberanas: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="bind-ssl"></a>Bind SSL

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Conectar-se ao servidor usando a bancada mySQL workbench sobre sSL
Configure a bancada MySQL workbench para ligar de forma segura sobre o SSL. 

1. A partir do diálogo Configuração Nova Ligação, navegue para o separador **SSL.** 

1. Atualize o campo **Utilizar SSL** para "Exigir".

1. No **Ficheiro CA SSL:** campo, introduza a localização do ficheiro **do BaltimoreCyberTrustRoot.crt.pem**. 
    
    ![Salvar a configuração SSL](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Para as ligações existentes, pode ligar o SSL clicando à direita no ícone de ligação e escolher editar. Em seguida, navegue no separador **SSL** e ligue o ficheiro cert.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Conectar-se ao servidor utilizando o MySQL CLI em SSL
Outra forma de ligar o certificado SSL é utilizar a interface de linha de comando MySQL executando os seguintes comandos. 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Ao utilizar a interface de linha de comando MySQL no Windows, poderá receber um erro `SSL connection error: Certificate signature check failed` . Se isto ocorrer, substitua os `--ssl-mode=REQUIRED --ssl-ca={filepath}` parâmetros por `--ssl` .

## <a name="enforcing-ssl-connections-in-azure"></a>Aplicação das ligações SSL em Azure 

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
Utilizando o portal Azure, visite a sua Base de Dados Azure para o servidor MariaDB e, em seguida, clique em **Segurança de Ligação**. Utilize o botão de alternar para ativar ou desativar a **definição de ligação SSL de acionar** e, em seguida, clique em **Guardar**. A Microsoft recomenda ativar sempre a **definição de ligação Enforce SSL** para uma segurança reforçada.
![ativar ssl para o servidor MariaDB](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Pode ativar ou desativar o parâmetro **de execução do ssl** utilizando valores ativados ou desativados, respectivamente, no Azure CLI.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Verifique a ligação SSL
Execute o comando de **estado** mysql para verificar se ligou ao seu servidor MariaDB utilizando SSL:
```sql
status
```
Confirme que a ligação é encriptada através da revisão da saída, que deve mostrar:  **SSL: Cifra em uso é AES256-SHA** 

## <a name="sample-code"></a>Código de exemplo
Para estabelecer uma ligação segura à Base de Dados Azure para MariaDB sobre SSL a partir da sua aplicação, consulte as seguintes amostras de código:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Rubi nos trilhos
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
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

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
