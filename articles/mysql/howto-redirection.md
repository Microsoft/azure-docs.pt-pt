---
title: Conectar-se ao banco de dados do Azure para MySQL com redirecionamento
description: Este artigo descreve como você pode configurar seu aplicativo para se conectar ao banco de dados do Azure para MySQL com redirecionamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233743"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Conectar-se ao banco de dados do Azure para MySQL com redirecionamento

Este tópico explica como conectar um aplicativo ao servidor do banco de dados do Azure para MySQL com o modo de redirecionamento. O redirecionamento visa reduzir a latência de rede entre os aplicativos cliente e os servidores MySQL, permitindo que os aplicativos se conectem diretamente aos nós do servidor back-end.

> [!IMPORTANT]
> O suporte para redirecionamento no [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) php está atualmente em visualização.

## <a name="before-you-begin"></a>Antes de começar
Iniciar sessão no [portal do Azure](https://portal.azure.com). Crie um servidor de banco de dados do Azure para MySQL com o mecanismo versão 5,6, 5,7 ou 8,0. Para obter detalhes, consulte [como criar um banco de dados do Azure para servidor MySQL do portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [como criar um banco de dados do Azure para servidor MySQL usando a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

No momento, só há suporte para o redirecionamento quando o SSL está habilitado. Para obter detalhes sobre como configurar o SSL, consulte [usando o SSL com o banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure). 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões do PHP 7.2.15 + e 7.3.2 +
- PÊRA DO PHP 
- PHP-MySQL
- Banco de dados do Azure para servidor MySQL com SSL habilitado

1. Instale o [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) com [PECL](https://pecl.php.net/package/mysqlnd_azure).

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Localize o diretório de extensão (`extension_dir`) executando o seguinte:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Altere os diretórios para a pasta retornada e verifique se `mysqlnd_azure.so` está localizado nessa pasta. 

4. Localize a pasta de arquivos. ini executando o seguinte: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Altere os diretórios para essa pasta retornada. 

6. Crie um novo arquivo. ini para `mysqlnd_azure`. Certifique-se de que a ordem alfabética do nome seja posterior à do mysqnld, já que os módulos são carregados de acordo com a ordem de nome dos arquivos ini. Por exemplo, se `mysqlnd`. ini for nomeado `10-mysqlnd.ini`, nomeie o mysqlnd ini como `20-mysqlnd-azure.ini`.

7. No novo arquivo. ini, adicione as linhas a seguir para habilitar o redirecionamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões do PHP 7.2.15 + e 7.3.2 +
- PHP-MySQL
- Banco de dados do Azure para servidor MySQL com SSL habilitado

1. Determine se você está executando uma versão x64 ou x86 do PHP executando o seguinte comando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Baixe a versão x64 ou x86 correspondente do [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll de [PECL](https://pecl.php.net/package/mysqlnd_azure) que corresponde à sua versão do php. 

3. Extraia o arquivo zip e localize a DLL chamada `php_mysqlnd_azure.dll`.

4. Localize o diretório de extensão (`extension_dir`) executando o comando abaixo:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Copie o arquivo `php_mysqlnd_azure.dll` no diretório retornado na etapa 4. 

6. Localize a pasta PHP que contém o arquivo de `php.ini` usando o seguinte comando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modifique o arquivo de `php.ini` e adicione as seguintes linhas extras para habilitar o redirecionamento. 

    Na seção extensões dinâmicas: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Na seção Configurações do módulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Confirmar redirecionamento

Você também pode confirmar se o redirecionamento está configurado com o código PHP de exemplo abaixo. Crie um arquivo PHP chamado `mysqlConnect.php` e cole o código abaixo. Atualize o nome do servidor, o nome de usuário e a senha com o seu próprio. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre cadeias de conexão, consulte [cadeias de conexão](howto-connection-string.md).

