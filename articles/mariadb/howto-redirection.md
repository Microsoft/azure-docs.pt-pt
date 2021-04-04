---
title: Conecte-se com reorientação - Azure Database for MariaDB
description: Este artigo descreve como pode configurar a sua aplicação para ligar à Base de Dados Azure para MariaDB com reorientação.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 3f26de72839fcaa39bff4d827aba757721736934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664907"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>Ligue à Base de Dados Azure para MariaDB com reorientação

Este tópico explica como ligar uma aplicação a sua Base de Dados Azure para servidor MariaDB com o modo de reorientação. A reorientação visa reduzir a latência da rede entre aplicações de clientes e servidores MariaDB, permitindo que as aplicações se conectem diretamente aos nós do servidor de backend.

## <a name="before-you-begin"></a>Antes de começar
Inicie sessão no [portal do Azure](https://portal.azure.com). Crie uma Base de Dados Azure para servidor MariaDB com a versão 10.2 ou 10.3 do motor. 

Para mais detalhes, consulte como criar uma Base de Dados Azure para servidor MariaDB utilizando o [portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md) ou [Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="enable-redirection"></a>Ativar a reorientação

Na sua Base de Dados Azure para o servidor MariaDB, configuure o `redirect_enabled` parâmetro `ON` para permitir ligações com o modo de reorientação. Para atualizar este parâmetro do servidor, utilize o [portal Azure](howto-server-parameters.md) ou [O Azure CLI](howto-configure-server-parameters-cli.md).

## <a name="php"></a>PHP

O suporte para a reorientação em aplicações PHP está disponível através da extensão [mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) desenvolvida pela Microsoft. 

A extensão mysqlnd_azure está disponível para adicionar às aplicações PHP através da PECL e é altamente recomendado instalar e configurar a extensão através do [pacote PECL](https://pecl.php.net/package/mysqlnd_azure)oficialmente publicado.

> [!IMPORTANT]
> O suporte para a reorientação na extensão [php mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) está atualmente em pré-visualização.

### <a name="redirection-logic"></a>Lógica de redirecionamento

>[!IMPORTANT]
> A versão inicial da lógica de reorientação/comportamento 1.1.0 foi atualizada e **recomenda-se a utilização da versão 1.1.0+**.

O comportamento de reorientação é determinado pelo valor de `mysqlnd_azure.enableRedirect` . O quadro abaixo descreve o comportamento de reorientação com base no valor deste parâmetro a partir da **versão 1.1.0+**.

Se estiver a utilizar uma versão mais antiga da extensão mysqlnd_azure (versão 1.0.0-1.0.3), o comportamento de reorientação é determinado pelo valor de `mysqlnd_azure.enabled` . Os valores válidos são `off` (atuam da mesma forma que o comportamento descrito na tabela abaixo) e `on` (atos como `preferred` na tabela abaixo).  

|**mysqlnd_azure.enableRedirect valor**| **Comportamento**|
|----------------------------------------|-------------|
|`off` ou `0`|A reorientação não será utilizada. |
|`on` ou `1`|- Se a ligação não utilizar sSL no lado do condutor, não será feita qualquer ligação. O seguinte erro será devolvido: *"mysqlnd_azure.ativar o Redirect está ligado, mas a opção SSL não está definida na cadeia de ligação. A reorientação só é possível com ssl."*<br>- Se o SSL for utilizado no lado do controlador, mas a reorientação não for suportada no servidor, a primeira ligação é abortada e o seguinte erro é devolvido: *"A ligação abortada porque a reorientação não está ativada no servidor MariaDB ou o pacote de rede não cumpre o protocolo de reorientação."*<br>- Se o servidor MariaDB suportar a reorientação, mas a ligação redirecionada falhou por qualquer motivo, também aborte a primeira ligação proxy. Devolva o erro da ligação redirecionada.|
|`preferred` ou `2`<br> (valor predefinido)|- mysqlnd_azure utilizará a reorientação, se possível.<br>- Se a ligação não utilizar sSL no lado do controlador, o servidor não suporta a reorientação, ou a ligação redirecionada não se conecte por qualquer motivo não fatal enquanto a ligação proxy ainda é válida, ela voltará para a primeira ligação proxy.|

As secções subsequentes do documento descreverão como instalar a extensão utilizando o `mysqlnd_azure` PECL e definir o valor deste parâmetro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões PHP 7.2.15+ e 7.3.2+
- PERA PHP 
- php-mysql
- Base de Dados Azure para servidor MariaDB

1. Instale [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) com [PECL.](https://pecl.php.net/package/mysqlnd_azure) Recomenda-se a utilização da versão 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Localize o diretório de extensão `extension_dir` () executando o seguinte:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Mude os diretórios para a pasta devolvida e certifique-se de que `mysqlnd_azure.so` está localizada nesta pasta. 

4. Localize a pasta para .ini ficheiros executando o seguinte: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Mude os diretórios para esta pasta devolvida. 

6. Crie um novo ficheiro .ini para `mysqlnd_azure` . Certifique-se de que a ordem do alfabeto do nome está atrás da do mysqnld, uma vez que os módulos são carregados de acordo com a ordem de nome dos ficheiros ini. Por exemplo, se `mysqlnd` .ini for `10-mysqlnd.ini` nomeado, diga o mysqlnd ini como `20-mysqlnd-azure.ini` .

7. Dentro do novo ficheiro .ini, adicione as seguintes linhas para permitir a reorientação.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões PHP 7.2.15+ e 7.3.2+
- php-mysql
- Base de Dados Azure para servidor MariaDB

1. Determine se está a executar uma versão x64 ou x86 de PHP executando o seguinte comando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Descarregue a versão correspondente x64 ou x86 do [DLL mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) da [PECL](https://pecl.php.net/package/mysqlnd_azure) que corresponda à sua versão de PHP. Recomenda-se a utilização da versão 1.1.0+.

3. Extraia o ficheiro zip e encontre o DLL `php_mysqlnd_azure.dll` nomeado.

4. Localize o diretório de extensão `extension_dir` () executando o comando abaixo:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copie o `php_mysqlnd_azure.dll` ficheiro para o diretório devolvido no passo 4. 

6. Localizar a pasta PHP que contém o `php.ini` ficheiro utilizando o seguinte comando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modifique o `php.ini` ficheiro e adicione as seguintes linhas extras para permitir a reorientação. 

    Na secção Extensões Dinâmicas: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Na secção Definições do Módulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Confirmar reorientação

Também pode confirmar que a reorientação está configurada com o código PHP abaixo da amostra. Crie um ficheiro PHP chamado `mysqlConnect.php` e cole o código abaixo. Atualize o nome do servidor, nome de utilizador e senha com o seu próprio. 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
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

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre as cordas de [ligação,](howto-connection-string.md)consulte as Cordas de Ligação .
