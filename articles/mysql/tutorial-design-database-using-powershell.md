---
title: 'Tutorial: Design a server - Azure PowerShell - Base de Dados Azure para MySQL'
description: Este tutorial explica como criar e gerir a Base de Dados Azure para o servidor MySQL e base de dados usando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: mvc
ms.openlocfilehash: 6bb3c25d4d4d24e626ad210c78c6ac64c560e43e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614390"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-powershell"></a>Tutorial: Designar uma Base de Dados Azure para MySQL usando powerShell

A Base de Dados do Azure para MySQL é um serviço de base de dados relacional na cloud da Microsoft baseado no motor de base de dados da Edição de Comunidade do MySQL. Neste tutorial, você usa powerShell e outros utilitários para aprender a:

> [!div class="checklist"]
> - Criar uma Base de Dados do Azure para MySQL
> - Configurar a firewall do servidor
> - Use [a ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> - Carregar os dados de exemplo
> - Consultar dados
> - Atualizar dados
> - Restaurar dados

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

O exemplo seguinte cria um servidor MySQL na região **oeste dos EUA** nomeado **mydemoserver** no grupo de recursos **myresourcegroup** com um login de servidor de **myadmin**. É um servidor Gen 5 no nível de preços de uso geral com 2 vCores e backups geo-redundantes habilitados. Documente a palavra-passe utilizada na primeira linha do exemplo, uma vez que esta é a palavra-passe para a conta de administração do servidor MySQL.

> [!TIP]
> Um nome de servidor é mapeado para um nome DNS e tem de ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

O valor do parâmetro **Sku** segue os vCores de **geração\_\_de calculadores** de nível de preços da convenção, como mostram os seguintes exemplos.

- `-Sku B_Gen5_1`mapas para Basic, Gen 5 e 1 vCore. Esta opção é a mais pequena SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Para obter informações sobre valores **sku** válidos por região e para níveis, consulte a Base de Dados Azure para os níveis de [preços MySQL](./concepts-pricing-tiers.md).

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

```azurepowershell-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Criar uma base de dados

Assim que estiver ligado ao servidor, crie uma base de dados vazia.

```sql
mysql> CREATE DATABASE mysampledb;
```

Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados recentemente criada:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados

Agora que sabe como ligar à Base de Dados do Azure para MySQL, conclua algumas tarefas básicas.

Em primeiro lugar, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas

Agora que tem uma tabela, insira alguns dados na mesma. Na janela da linha de comandos aberta, execute a seguinte consulta para inserir algumas linhas de dados.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tem agora duas linhas de dados de exemplo nas tabelas que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas

Execute a seguinte consulta para obter informações da tabela de base de dados.

```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados nas tabelas.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada em conformidade ao obter dados.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo

Pode restaurar o servidor num ponto-a-tempo anterior. Os dados restaurados são copiados para um novo servidor, e o servidor existente é deixado inalterado. Por exemplo, se uma mesa for acidentalmente largada, pode restaurar até ao momento em que agotou aquecência. Em seguida, pode recuperar a tabela em falta e os dados da cópia restaurada do servidor.

Para restaurar o servidor, utilize o `Restore-AzMySqlServer` cmdlet PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, execute o seguinte exemplo a partir do PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Quando restaura um servidor para um momento anterior, é criado um novo servidor. O servidor original e as suas bases de dados do ponto de vista especificado são copiados para o novo servidor.

Os valores de nível de localização e de preços para o servidor restaurado permanecem os mesmos que o servidor original.

Após o final do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuradas separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como fazer cópias e restaurar uma Base de Dados Azure para o servidor MySQL utilizando o PowerShell](howto-restore-server-powershell.md)
