---
title: 'Tutorial: Desenhe um servidor - Azure PowerShell - Azure Database for MariaDB'
description: Este tutorial explica como criar e gerir a Base de Dados Azure para o servidor e base de dados MariaDB utilizando o PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023096"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>Tutorial: Desenhe uma base de dados Azure para MariaDB usando PowerShell

Azure Database for MariaDB é um serviço de base de dados relacional na nuvem da Microsoft com base no motor de base de dados MariaDB Community Edition. Neste tutorial, você usa PowerShell e outros utilitários para aprender a:

> [!div class="checklist"]
> - Criar um Azure Database for MariaDB
> - Configurar a firewall do servidor
> - Use [a ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> - Carregar os dados de exemplo
> - Consultar dados
> - Atualizar dados
> - Restaurar dados

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell está geralmente disponível, torna-se parte de futuros lançamentos do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se esta for a sua primeira utilização da Base de Dados Azure para o serviço MariaDB, tem de registar o fornecedor de recursos **Microsoft.DBforMariaDB.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione um ID de subscrição específico utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos chamado **myresourcegroup** na região **oeste dos EUA.**

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um Azure Database for MariaDB Server

Crie uma Base de Dados Azure para o servidor MariaDB com o `New-AzMariaDbServer` cmdlet. Cada servidor pode gerir múltiplas bases de dados. Geralmente, é utilizada uma base de dados em separado para cada projeto ou para cada utilizador.

O exemplo a seguir cria um servidor MariaDB na região **oeste dos EUA** chamado **mydemoserver** no grupo de recursos **myresourcegroup** com um login de administração de servidor de **myadmin**. É um servidor Gen 5 no nível de preços de uso geral com 2 vCores e backups geo-redundantes ativados. Documente a palavra-passe utilizada na primeira linha do exemplo, uma vez que esta é a palavra-passe para a conta de administração do servidor MariaDB.

> [!TIP]
> Um nome de servidor é mapeado para um nome DNS e tem de ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

O valor do parâmetro **Sku** segue os **\_ \_ vCores de geração de cálculo de nível de** preços da convenção, como mostrado nos seguintes exemplos.

- `-Sku B_Gen5_1` mapas para Basic, Gen 5 e 1 vCore. Esta opção é a mais pequena SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Para obter informações sobre valores **válidos de Sku** por região e para níveis, consulte [a Base de Dados Azure para os níveis de preços mariaDB](./concepts-pricing-tiers.md).

Considere usar o nível básico de preços se o cálculo leve e a I/O forem adequados para a sua carga de trabalho.

> [!IMPORTANT]
> Os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para fins gerais ou memória- otimizados e não podem ser geo-replicados.

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Crie uma base de dados Azure para a regra de firewall de nível de servidor MariaDB utilizando o `New-AzMariaDbFirewallRule` cmdlet. Uma regra de firewall ao nível do servidor permite que uma aplicação externa, como a ferramenta de linha de comando ou a `mysql` bancada De trabalho MariaDB, se conecte ao seu servidor através da base de dados Azure para firewall de serviço MariaDB.

O exemplo a seguir cria uma regra de firewall chamada **AllowMyIP** que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua um endereço IP ou um conjunto de endereços IP que correspondam à localização a que está a ligar.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> As ligações à Azure Database for MariaDB comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Neste cenário, só pode ligar-se ao servidor se o seu departamento de TI abrir a porta 3306.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião. Utilize o seguinte exemplo para determinar as informações de ligação. Tome nota dos valores do Nome e **DoMinínia Totalmente Qualificados** e **DoMinância**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Ligue-se ao servidor utilizando a ferramenta de linha de comando mysql

Ligue-se ao seu servidor utilizando a `mysql` ferramenta de linha de comando. Para descarregar e instalar a ferramenta de linha de comando, consulte [as Transferências Comunitárias MariaDB](https://dev.mysql.com/downloads/shell/). Também pode aceder a uma versão pré-instalada da ferramenta de `mysql` linha de comando em Azure Cloud Shell selecionando o botão Try **It** numa amostra de código neste artigo. Outras formas de aceder à Azure Cloud Shell são selecionar o botão **>_** na barra de ferramentas superior direita no portal Azure ou visitando [shell.azure.com](https://shell.azure.com/).

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
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

Agora que sabe como ligar-se à Base de Dados Azure para a base de dados MariaDB, preencha algumas tarefas básicas.

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

Pode restaurar o servidor para um ponto-a-tempo anterior. Os dados restaurados são copiados para um novo servidor, e o servidor existente permanece inalterado. Por exemplo, se uma mesa for acidentalmente largada, pode restaurar a hora em que ocorreu a queda. Em seguida, pode recuperar a tabela e os dados em falta a partir da cópia restaurada do servidor.

Para restaurar o servidor, utilize o `Restore-AzMariaDbServer` cmdlet PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, execute o seguinte exemplo da PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Quando restaura um servidor a um ponto-a-tempo anterior, é criado um novo servidor. O servidor original e as suas bases de dados a partir do ponto-a-tempo especificado são copiados para o novo servidor.

Os valores de localização e de nível de preços do servidor restaurado permanecem os mesmos que o servidor original.

Após o fim do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que a restauração foi iniciada. A palavra-passe pode ser alterada a partir da página **geral** do novo servidor.

O novo servidor criado durante uma restauração não possui os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuras separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como fazer o back up e restaurar uma Base de Dados Azure para o servidor MariaDB usando o PowerShell](howto-restore-server-powershell.md)