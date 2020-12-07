---
title: 'Tutorial: Desenhe uma base de dados Azure para PostgreSQL - Servidor Único - Azure PowerShell'
description: Este tutorial mostra como criar, configurar e consultar a sua primeira Base de Dados Azure para PostgreSQL - Single Server utilizando o Azure PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: cf84a8abcc0f1c890a51bf07056ac31d37107e53
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749118"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>Tutorial: Desenhe uma base de dados Azure para PostgreSQL - Servidor Único usando PowerShell

Azure Database for PostgreSQL é um serviço de base de dados relacional na nuvem da Microsoft baseado no motor de base de dados PostgreSQL Community Edition. Neste tutorial, você usa PowerShell e outros utilitários para aprender a:

> [!div class="checklist"]
> - Criar uma Base de Dados do Azure para o PostgreSQL
> - Configurar a firewall do servidor
> - Utilize o utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar uma base de dados
> - Carregar os dados de exemplo
> - Consultar dados
> - Atualizar dados
> - Restaurar dados

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Uma vez que o módulo Az.PostgreSql PowerShell está geralmente disponível, torna-se parte de futuras libertações do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se esta for a sua primeira utilização da Base de Dados Azure para o serviço PostgreSQL, tem de registar o fornecedor de recursos **Microsoft.DBforPostgreSQL.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
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

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL

Crie uma Base de Dados Azure para o servidor PostgreSQL com o `New-AzPostgreSqlServer` cmdlet. Cada servidor pode gerir múltiplas bases de dados. Geralmente, é utilizada uma base de dados em separado para cada projeto ou para cada utilizador.

O exemplo a seguir cria um servidor PostgreSQL na região **oeste dos EUA** chamado **mydemoserver** no grupo de recursos **myresourcegroup** com um login de administração de servidor de **myadmin**. É um servidor Gen 5 no nível de preços de uso geral com 2 vCores e backups geo-redundantes ativados. Documente a palavra-passe utilizada na primeira linha do exemplo, uma vez que esta é a palavra-passe para a conta de administração do servidor PostgreSQL.

> [!TIP]
> Um nome de servidor é mapeado para um nome DNS e tem de ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

O valor do parâmetro **Sku** segue os **\_ \_ vCores de geração de cálculo de nível de** preços da convenção, como mostrado nos seguintes exemplos.

- `-Sku B_Gen5_1` mapas para Basic, Gen 5 e 1 vCore. Esta opção é a mais pequena SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Para obter informações sobre valores **válidos de Sku** por região e para níveis, consulte [a Base de Dados Azure para os níveis de preços pós-SQL](./concepts-pricing-tiers.md).

Considere usar o nível básico de preços se o cálculo leve e a I/O forem adequados para a sua carga de trabalho.

> [!IMPORTANT]
> Os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para fins gerais ou memória- otimizados e não podem ser geo-replicados.

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Crie uma base de dados Azure para a regra de firewall de nível de servidor PostgreSQL utilizando o `New-AzPostgreSqlFirewallRule` cmdlet. Uma regra de firewall ao nível do servidor permite que uma aplicação externa, como a ferramenta de linha de comando ou a `psql` bancada de trabalho PostgreSQL, se conecte ao seu servidor através da base de dados Azure para firewall de serviço PostgreSQL.

O exemplo a seguir cria uma regra de firewall chamada **AllowMyIP** que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua um endereço IP ou um conjunto de endereços IP que correspondam à localização a que está a ligar.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> As ligações à Base de Dados Azure para PostgreSQL comunicam-se sobre a porta 5432. Se tentar ligar-se a partir de uma rede corporativa, o tráfego de saída sobre o porto 5432 pode não ser permitido. Neste cenário, só pode ligar-se ao servidor se o seu departamento de TI abrir a porta 5432.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião. Utilize o seguinte exemplo para determinar as informações de ligação. Tome nota dos valores do Nome e **DoMinínia Totalmente Qualificados** e **DoMinância**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Ligar à base de dados do PostgreSQL com psql

Se o seu computador cliente tiver o PostgreSQL instalado, pode utilizar uma instância local de [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para ligar a um servidor PostgreSQL do Azure. Também pode aceder a uma versão pré-instalada da ferramenta de `psql` linha de comando em Azure Cloud Shell selecionando o botão Try **It** numa amostra de código neste artigo. Outras formas de aceder à Azure Cloud Shell são selecionar o botão **>_** na barra de ferramentas superior direita no portal Azure ou visitando [shell.azure.com](https://shell.azure.com/).

1. Ligue-se ao seu servidor Azure PostgreSQL utilizando o `psql` utilitário da linha de comando.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Por exemplo, o seguinte comando liga-se à base de dados predefinido chamada **postgres** no seu servidor PostgreSQL `mydemoserver.postgres.database.azure.com` utilizando credenciais de acesso. Introduza o `<server_admin_password>` que escolheu quando lhe for pedida a palavra-passe.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se preferir utilizar um caminho URL para ligar aos Postgres, o URL codifica o sinal @ no nome de utilizador com `%40` . Por exemplo, a cadeia de ligação para psql seria, `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Assim que estiver ligado ao servidor, crie uma base de dados em branco na linha de comandos.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** criada recentemente:

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados

Agora que sabe como ligar-se à Base de Dados Azure para a base de dados PostgreSQL, preencha algumas tarefas básicas.

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

Para restaurar o servidor, utilize o `Restore-AzPostgreSqlServer` cmdlet PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, execute o seguinte exemplo da PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Quando restaura um servidor a um ponto-a-tempo anterior, é criado um novo servidor. O servidor original e as suas bases de dados a partir do ponto-a-tempo especificado são copiados para o novo servidor.

Os valores de localização e de nível de preços do servidor restaurado permanecem os mesmos que o servidor original.

Após o fim do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que a restauração foi iniciada. A palavra-passe pode ser alterada a partir da página **geral** do novo servidor.

O novo servidor criado durante uma restauração não possui os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuras separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou recursos Azure num grupo de servidores. Se não espera precisar destes recursos no futuro, elimine o grupo de servidores. Prima o botão *Eliminar* na página *'Vista Geral'* para o seu grupo de servidor. Quando solicitado numa página pop-up, confirme o nome do grupo de servidor e clique no botão final *eliminar.*

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como fazer o back up e restaurar uma Base de Dados Azure para servidor PostgreSQL usando PowerShell](howto-restore-server-powershell.md)
