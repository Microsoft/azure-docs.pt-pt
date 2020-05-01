---
title: 'Quickstart: Criar um servidor - Azure PowerShell - Base de Dados Azure para MySQL'
description: Este quickstart descreve como usar o PowerShell para criar uma Base de Dados Azure para servidor MySQL num grupo de recursos Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc
ms.openlocfilehash: 2e12da29a8388bf4a232930c3737be7ddce80d12
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611947"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>Quickstart: Criar uma base de dados Azure para o servidor MySQL usando powerShell

Este quickstart descreve como usar o PowerShell para criar uma Base de Dados Azure para servidor MySQL num grupo de recursos Azure. Pode utilizar o PowerShell para criar e gerir os recursos do Azure interactivamente ou em scripts.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se ligue à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell estiver em pré-visualização, deve instalá-lo `Install-Module -Name Az.MySql -AllowPrerelease`separadamente do módulo Az PowerShell utilizando o seguinte comando: .
> Uma vez que o módulo Az.MySql PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa dentro da Azure Cloud Shell.

Se esta for a sua primeira utilização da Base de Dados Azure para o serviço MySQL, deve registar o fornecedor de recursos **Microsoft.DBforMySQL.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione um ID de subscrição específico utilizando o cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo seguinte cria um grupo de recursos chamado **myresourcegroup** na região **dos EUA Ocidentais.**

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL

Crie uma Base de Dados Azure `New-AzMySqlServer` para o servidor MySQL com o cmdlet. Cada servidor pode gerir múltiplas bases de dados. Geralmente, é utilizada uma base de dados em separado para cada projeto ou para cada utilizador.

A tabela seguinte contém uma lista de parâmetros `New-AzMySqlServer` geralmente utilizados e valores de amostra para o cmdlet.

|        **Definição**         | **Valor de exemplo** |                                                                                                                                                             **Descrição**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome                       | mydemoserver     | Escolha um nome globalmente único em Azure que identifique a sua Base de Dados Azure para o servidor MySQL. O nome do servidor só pode conter letras, números e o caráter do hífen (-). Quaisquer caracteres maiúsculos especificados são automaticamente convertidos para minúsculas durante o processo de criação. Tem de conter entre 3 e 63 carateres. |
| ResourceGroupName          | myResourceGroup  | Indique o nome do grupo de recursos do Azure.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | O nome do SKU. Segue em abreviatura os vCores de **geração\_\_** de calculadores de nível de preços da convenção. Para mais informações sobre o parâmetro Sku, consulte a informação que se segue a esta tabela.                                                                                                                                           |
| Dia da Retenção de Backup         | 7                | Quando tempo se deve reter uma cópia de segurança. A unidade é dias. O intervalo é de 7-35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Ativado          | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não. Este valor não pode ser ativado para servidores no nível de preços básicos e não pode ser alterado após a criação do servidor. Valores permitidos: Ativado, Desativado.                                                                                                      |
| Localização                   | westus           | A região de Azure para o servidor.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Ativado          | Se o SSL deve ser ativado ou não para este servidor. Valores permitidos: Ativado, Desativado.                                                                                                                                                                                                                                                 |
| ArmazenamentoInmb                | 51200            | A capacidade de armazenamento do servidor (a unidade é megabytes). Armazenamento VálidoInMb é um mínimo de 5120 MB e aumenta em incrementos de 1024 MB. Para obter mais informações sobre os limites de tamanho do armazenamento, consulte a Base de Dados Azure para os níveis de [preços MySQL](./concepts-pricing-tiers.md).                                                                               |
| Versão                    | 5.7              | A versão principal do MySQL.                                                                                                                                                                                                                                                                                                                 |
| Nome de utilizador de administrador      | myadmin          | O nome de utilizador para o início de sessão do administrador. Não pode ser **azure_superuser**, **admin**, **administrador**, **raiz**, **convidado** nem **público**.                                                                                                                                                                                            |
| AdministradorLoginPassword | `<securestring>` | A palavra-passe do utilizador administrador sob a forma de uma corda segura. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres das três categorias seguintes: letras em maiúsculas do inglês, letras em minúsculas do inglês, números e carateres não alfanuméricos.                                       |

O valor do parâmetro **Sku** segue os vCores de **geração\_\_de calculadores** de nível de preços da convenção, como mostram os seguintes exemplos.

- `-Sku B_Gen5_1`mapas para Basic, Gen 5 e 1 vCore. Esta opção é a mais pequena SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Para obter informações sobre valores **sku** válidos por região e para níveis, consulte a Base de Dados Azure para os níveis de [preços MySQL](./concepts-pricing-tiers.md).

O exemplo seguinte cria um servidor MySQL na região **oeste dos EUA** nomeado **mydemoserver** no grupo de recursos **myresourcegroup** com um login de servidor de **myadmin**. É um servidor Gen 5 no nível de preços de uso geral com 2 vCores e backups geo-redundantes habilitados. Documente a palavra-passe utilizada na primeira linha do exemplo, uma vez que esta é a palavra-passe para a conta de administração do servidor MySQL.

> [!TIP]
> Um nome de servidor é mapeado para um nome DNS e tem de ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Considere utilizar o nível de preços básicos se a computação leve e o I/O forem adequados para a sua carga de trabalho.

> [!IMPORTANT]
> Os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para fins gerais ou memória otimizados e não podem ser geo-replicados.

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Crie uma base de dados Azure para a `New-AzMySqlFirewallRule` regra de firewall do nível do servidor MySQL utilizando o cmdlet. Uma regra de firewall ao nível do `mysql` servidor permite que uma aplicação externa, como a ferramenta de linha de comando ou a bancada de trabalho MySQL, se conectem ao seu servidor através da Base de Dados Azure para firewall de serviço MySQL.

O exemplo seguinte cria uma regra de firewall chamada **AllowMyIP** que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua um endereço IP ou uma gama de endereços IP que correspondam à localização a que está a ligar.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Neste cenário, só é possível ligar ao servidor se o seu departamento de TI abrir a porta 3306.

## <a name="configure-ssl-settings"></a>Configurar as definições de SSL

Por predefinição, são aplicadas ligações SSL entre o servidor e as aplicações cliente. Este padrão garante a segurança dos dados _em movimento_ encriptando o fluxo de dados através da Internet. Para este início rápido, desative as ligações SSL no seu servidor. Para obter mais informações, veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para MySQL).

> [!WARNING]
> A desativação de SSL não é recomendada para servidores de produção.

O exemplo seguinte desativa o SSL na sua Base de Dados Azure para o servidor MySQL.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião. Utilize o seguinte exemplo para determinar as informações de ligação. Tome nota dos valores para **FullQualifiedDomainName** e **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Ligue-se ao servidor utilizando a ferramenta de linha de comando mysql

Ligue-se ao `mysql` seu servidor utilizando a ferramenta de linha de comando. Para descarregar e instalar a ferramenta de linha de comando, consulte [MySQL Community Downloads](https://dev.mysql.com/downloads/shell/). Também pode aceder a uma versão `mysql` pré-instalada da ferramenta de linha de comando em Azure Cloud Shell selecionando o botão **Try It** numa amostra de código neste artigo. Outras formas de aceder ao Azure Cloud Shell são selecionar o botão **>_** na barra de ferramentas superior direita no portal Azure ou visitando [shell.azure.com](https://shell.azure.com/).

1. Ligue-se ao `mysql` servidor utilizando a ferramenta de linha de comando.

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. Ver estado do servidor.

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

Para obter comandos adicionais, veja [MySQL 5.7 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manual de Referência do MySQL 5.7 - Capítulo 4.5.1).

## <a name="connect-to-the-server-using-mysql-workbench"></a>Ligue-se ao servidor utilizando a bancada mySQL

1. Inicie a aplicação MySQL Workbench no computador cliente. Para descarregar e instalar a Bancada de Trabalho MySQL, consulte baixar a [bancada mySQL](https://dev.mysql.com/downloads/workbench/).

1. Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

   ![configurar ligação nova](./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Definição**    |           **Valor Sugerido**           |                      **Descrição**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | Nome da Ligação   | A Minha Ligação                           | Especifique um rótulo para esta ligação                        |
    | Método de Ligação | Standard (TCP/IP)                       | Utilize o protocolo TCP/IP para ligar à Base de Dados Azure para mySQL |
    | Nome de anfitrião          | `mydemoserver.mysql.database.azure.com` | Nome do servidor que já observou                           |
    | Porta              | 3306                                    | A porta padrão para MySQL                                 |
    | Nome de utilizador          | myadmin@mydemoserver                    | O administrador do servidor inseri-lo anteriormente observou                |
    | Palavra-passe          | *************                           | Utilize a palavra-passe da conta de administração configurada anteriormente      |

1. Para testar se os parâmetros estão configurados corretamente, clique no botão **de ligação** de teste.

1. Selecione a ligação para ligar ao servidor.

## <a name="clean-up-resources"></a>Limpar recursos

Se os recursos criados neste quickstart não forem necessários para outro quickstart ou tutorial, pode eliminá-los executando o seguinte exemplo.

> [!CAUTION]
> O exemplo seguinte elimina o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do âmbito deste arranque rápido no grupo de recursos especificado, serão igualmente eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Para eliminar apenas o servidor criado neste quickstart sem `Remove-AzMySqlServer` eliminar o grupo de recursos, utilize o cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Desenhe uma base de dados Azure para MySQL usando powerShell](tutorial-design-database-using-powershell.md)
