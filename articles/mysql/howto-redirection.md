---
title: Conecte-se com a redirecção - Base de Dados Azure para MySQL
description: Este artigo descreve como pode configurar a sua aplicação para ligar à Base de Dados Azure para MySQL com redirecionamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246340"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Ligue-se à Base de Dados Azure para MySQL com reorientação

Este tópico explica como ligar uma aplicação à sua Base de Dados Azure para o servidor MySQL com o modo de redirecionamento. A redirecionamento tem como objetivo reduzir a latência da rede entre aplicações de clientes e servidores MySQL, permitindo que as aplicações se conectem diretamente aos nós do servidor.

## <a name="before-you-begin"></a>Antes de começar
Inicie sessão no [Portal do Azure](https://portal.azure.com). Crie uma Base de Dados Azure para servidor MySQL com a versão do motor 5.6, 5.7 ou 8.0. Para mais detalhes, consulte como criar base de [dados Azure para servidor MySQL a partir do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou como criar base de dados [Azure para servidor MySQL utilizando cli](quickstart-create-mysql-server-database-using-azure-cli.md).

A reorientação só é suportada atualmente quando o **SSL está ativado** na sua Base de Dados Azure para o servidor MySQL. Para mais detalhes sobre como configurar o SSL, consulte [A Utilização de SSL com Base de Dados Azure para MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

O suporte para reorientação nas aplicações PHP está disponível através da extensão [mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) desenvolvida pela Microsoft. 

A extensão mysqlnd_azure está disponível para adicionar às aplicações PHP através do PECL e é altamente recomendado instalar e configurar a extensão através do [pacote PECL](https://pecl.php.net/package/mysqlnd_azure)oficialmente publicado.

> [!IMPORTANT]
> O suporte para a reorientação na extensão [PHP mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) está atualmente em pré-visualização.

### <a name="redirection-logic"></a>Lógica de reorientação

>[!IMPORTANT]
> A lógica de reorientação/comportamento inicial da versão 1.1.0 foi atualizada e **recomenda-se a utilização da versão 1.1.0+**.

O comportamento de reorientação `mysqlnd_azure.enableRedirect`é determinado pelo valor de . O quadro abaixo descreve o comportamento da reorientação com base no valor deste parâmetro a partir da **versão 1.1.0+**.

Se estiver a utilizar uma versão mais antiga da extensão mysqlnd_azure (versão 1.0.0-1.0.3), o comportamento de reorientação é determinado pelo valor de `mysqlnd_azure.enabled`. Os valores `off` válidos são (atua da mesma forma `on` que `preferred` o comportamento descrito na tabela abaixo) e (atua como na tabela abaixo).  

|**mysqlnd_azure.enableRedirect value**| **Comportamento**|
|----------------------------------------|-------------|
|`off` ou `0`|A reorientação não será utilizada. |
|`on` ou `1`|- Se o SSL não estiver ativado na Base de Dados Azure para o servidor MySQL, não será feita qualquer ligação. O seguinte erro será devolvido: *"mysqlnd_azure.enableRedirect está ligado, mas a opção SSL não está definida na cadeia de ligação. A reorientação só é possível com o SSL."*<br>- Se o SSL estiver ativado no servidor MySQL, mas a reorientação não for suportada no servidor, a primeira ligação é abortada e o seguinte erro é devolvido: *"A ligação abortada porque a reorientação não está ativada no servidor MySQL ou o pacote de rede não cumpre o protocolo de redirecionamento."*<br>- Se o servidor MySQL suportar a reorientação, mas a ligação redirecionada falhou por qualquer motivo, também aborte a primeira ligação proxy. Volte a devolva o erro da ligação redirecionada.|
|`preferred` ou `2`<br> (valor por defeito)|- mysqlnd_azure utilizarão a reorientação, se possível.<br>- Se a ligação não utilizar o SSL, o servidor não suporta a reorientação ou a ligação redirecionada não se conecto por qualquer razão não fatal enquanto a ligação proxy ainda for válida, ela recue para a primeira ligação proxy.|

As secções subsequentes do documento descreverão como instalar a `mysqlnd_azure` extensão utilizando o PECL e definir o valor deste parâmetro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões PHP 7.2.15+ e 7.3.2+
- PERA PHP 
- php-mysql
- Base de dados Azure para servidor MySQL com SSL ativado

1. Instale [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) com [o PECL](https://pecl.php.net/package/mysqlnd_azure). Recomenda-se a utilização da versão 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Localize o diretório de extensão (`extension_dir`) executando o seguinte:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Mude os diretórios para `mysqlnd_azure.so` a pasta devolvida e certifique-se de que está localizado nesta pasta. 

4. Localize a pasta para ficheiros .ini executando o abaixo: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Mude os diretórios para esta pasta devolvida. 

6. Crie um novo ficheiro `mysqlnd_azure`.ini para . Certifique-se de que a ordem do alfabeto do nome é a trás da mysqnld, uma vez que os módulos são carregados de acordo com a ordem de nome dos ficheiros ini. Por exemplo, `mysqlnd` se .ini for nomeado, `10-mysqlnd.ini`nomeie `20-mysqlnd-azure.ini`o mysqlnd ini como .

7. Dentro do novo ficheiro .ini, adicione as seguintes linhas para permitir a reorientação.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Pré-requisitos 
- Versões PHP 7.2.15+ e 7.3.2+
- php-mysql
- Base de dados Azure para servidor MySQL com SSL ativado

1. Determine se está a executar uma versão x64 ou x86 de PHP executando o seguinte comando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Descarregue a versão correspondente x64 ou x86 do [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL da [PECL](https://pecl.php.net/package/mysqlnd_azure) que corresponda à sua versão de PHP. Recomenda-se a utilização da versão 1.1.0+.

3. Extraia o ficheiro zip `php_mysqlnd_azure.dll`e encontre o DLL nomeado .

4. Localize o diretório de extensão (`extension_dir`) executando o comando abaixo:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copie `php_mysqlnd_azure.dll` o ficheiro no diretório devolvido no passo 4. 

6. Localize a pasta `php.ini` PHP contendo o ficheiro utilizando o seguinte comando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modifique `php.ini` o ficheiro e adicione as seguintes linhas extras para permitir a reorientação. 

    No âmbito da secção de Extensões Dinâmicas: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Sob a secção Definições do Módulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Confirmar a reorientação

Também pode confirmar que a reorientação está configurada com o código PHP da amostra abaixo. Crie um ficheiro `mysqlConnect.php` PHP chamado e colá o código abaixo. Atualize o nome do servidor, nome de utilizador e palavra-passe com o seu próprio. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
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
Para obter mais informações sobre as cordas de ligação, consulte [as cordas de ligação](howto-connection-string.md).
