---
title: 'Quickstart: Criar servidor - Azure PowerShell - Base de Dados Azure para PostgreSQL - Servidor Único'
description: Quickstart guia para criar uma Base de Dados Azure para PostgreSQL - Servidor Único usando Azure PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 91351c0b2982c6ee0e96cc1433c0fadf67e3bcc0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010661"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Quickstart: Criar uma base de dados Azure para PostgreSQL - Servidor Único usando PowerShell

Este quickstart descreve como usar o PowerShell para criar uma Base de Dados Azure para servidor PostgreSQL num grupo de recursos Azure. Pode utilizar o PowerShell para criar e gerir os recursos Azure de forma interativa ou em scripts.

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

A tabela seguinte contém uma lista de parâmetros e valores de amostra geralmente utilizados para o `New-AzPostgreSqlServer` cmdlet.

|        **Definição**         | **Valor de exemplo** |                                                                                                                                                             **Descrição**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Escolha um nome globalmente único em Azure que identifique a sua Base de Dados Azure para o servidor PostgreSQL. O nome do servidor só pode conter letras, números e o caracteres hífen (-) do hífen. Quaisquer caracteres maiúsculas que sejam especificados são automaticamente convertidos para minúsculas durante o processo de criação. Tem de conter entre 3 e 63 carateres. |
| ResourceGroupName          | myResourceGroup  | Indique o nome do grupo de recursos do Azure.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | O nome do SKU. Segue em **abreviatura os \_ \_ vCores de geração de preços de** convenção. Para obter mais informações sobre o parâmetro Sku, consulte as informações que se seguem a esta tabela.                                                                                                                                           |
| BackupRetentionDay         | 7                | Quando tempo se deve reter uma cópia de segurança. A unidade é dias. O intervalo é de 7-35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Ativado          | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não. Este valor não pode ser ativado para servidores no nível de preços básicos e não pode ser alterado após a criação do servidor. Valores permitidos: Ativado, Desativado.                                                                                                      |
| Localização                   | westus           | A região de Azure para o servidor.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Ativado          | Se o SSL deve ser ativado ou não para este servidor. Valores permitidos: Ativado, Desativado.                                                                                                                                                                                                                                                 |
| ArmazenamentoInMb                | 51200            | A capacidade de armazenamento do servidor (a unidade é megabytes). Armazenamento VálidoInMb é um mínimo de 5120 MB e aumenta em incrementos de 1024 MB. Para obter mais informações sobre os limites de tamanho de armazenamento, consulte [a Base de Dados Azure para os níveis de preços pós-SQL](./concepts-pricing-tiers.md).                                                                               |
| Versão                    | 9.6              | A versão principal do PostgreSQL.                                                                                                                                                                                                                                                                                                                 |
| Nome do AdministradorUser      | myadmin          | O nome de utilizador para o início de sessão do administrador. Não pode ser **azure_superuser**, **admin**, **administrador**, **raiz**, **convidado** nem **público**.                                                                                                                                                                                            |
| Palavra de Passagem de Administradores | `<securestring>` | A palavra-passe do utilizador do administrador sob a forma de uma cadeia segura. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres das três categorias seguintes: letras em maiúsculas do inglês, letras em minúsculas do inglês, números e carateres não alfanuméricos.                                       |

O valor do parâmetro **Sku** segue os **\_ \_ vCores de geração de cálculo de nível de** preços da convenção, como mostrado nos seguintes exemplos.

- `-Sku B_Gen5_1` mapas para Basic, Gen 5 e 1 vCore. Esta opção é a mais pequena SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Para obter informações sobre valores **válidos de Sku** por região e para níveis, consulte [a Base de Dados Azure para os níveis de preços pós-SQL](./concepts-pricing-tiers.md).

O exemplo a seguir cria um servidor PostgreSQL na região **oeste dos EUA** chamado **mydemoserver** no grupo de recursos **myresourcegroup** com um login de administração de servidor de **myadmin**. É um servidor Gen 5 no nível de preços de uso geral com 2 vCores e backups geo-redundantes ativados. Documente a palavra-passe utilizada na primeira linha do exemplo, uma vez que esta é a palavra-passe para a conta de administração do servidor PostgreSQL.

> [!TIP]
> Um nome de servidor é mapeado para um nome DNS e tem de ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

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
mydemoserver.postgres.database.azure.com       myadmin
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

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Ligar-se ao Servidor PostgreSQL com pgAdmin

pgAdmin é uma ferramenta de código aberto utilizada com o PostgreSQL. Pode instalar a pgAdmin a partir do site da [pgAdmin](https://www.pgadmin.org/). A versão de pgAdmin que estiver a utilizar poderá ser diferente da utilizada neste Início Rápido. Leia a documentação de pgAdmin se precisar de orientação adicional.

1. Abra a aplicação pgAdmin no computador cliente.

1. Na barra de ferramentas, vá a **Objeto**, paire o rato sobre **Criar** e selecione **Servidor**.

1. Na caixa de diálogo **Criar - Servidor**, no separador **Geral**, introduza um nome amigável exclusivo para o servidor, tal como **mydemoserver**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="O separador geral":::

1. Na caixa de diálogo **Criar - Servidor**, no separador **Ligação**, preencha a tabela de definições.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="O separador De Ligação":::

    parâmetro pgAdmin |Valor|Descrição
    ---|---|---
    Nome/endereço do anfitrião | Nome do servidor | O valor do nome de servidor que foi utilizado quando criou anteriormente a Base de Dados do Azure para o servidor PostgreSQL. O nosso servidor de exemplo é **mydemoserver.postgres.database.azure.com.** Utilize o nome de domínio totalmente qualificado **\* (.postgres.database.azure.com)** como mostrado no exemplo. Se não se lembrar do nome do servidor, siga os passos na secção anterior para obter as informações da ligação.
    Porta | 5432 | A porta a utilizar quando se liga à Base de Dados do Azure para o servidor PostgreSQL.
    Base de dados de manutenção | *postgres* | O nome predefinido da base de dados gerado pelo sistema.
    Nome de utilizador | Nome de início de sessão de administrador do servidor | O nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para o servidor PostgreSQL anteriormente. Se não se lembrar do nome de utilizador, siga os passos na secção anterior para obter as informações da ligação. O formato é *nome de utilizador \@ servername*.
    Palavra-passe | A sua palavra-passe de administrador | A palavra-passe que escolheu quando criou o servidor anteriormente neste Início Rápido.
    Função | Deixar em branco | Não é necessário indicar um nome de função neste momento. Deixe o campo em branco.
    Modo SSL | *Requerer* | Pode definir o modo TLS/SSL no separador SSL do pgAdmin. Por predefinição, todas as bases de dados Azure para servidores PostgreSQL são criadas com a aplicação do TLS ligada. Para desligar a aplicação do TLS, consulte a [Aplicação de Configuração do TLS](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. Selecione **Guardar**.

1. No painel **Browser** do lado esquerdo, expanda o nó **Servidores**. Selecione o seu servidor, como, por exemplo, **mydemoserver**. Clique para ligar ao mesmo.

1. Expanda o nó de servidor e, em seguida, expanda **Bases de Dados** sob o mesmo. A lista deve incluir a sua base de dados *postgres* existente e quaisquer outras bases de dados que tenha criado. Pode criar várias bases de dados por servidor com a Base de Dados do Azure para PostgreSQL.

1. Clique com o botão direito em **Bases de Dados**, escolha o menu **Criar** e selecione **Base de Dados**.

1. Escreva um nome de base de dados à sua escolha no campo **Base de Dados**, como **mypgsqldb2**.

1. Selecione o **Proprietário** da base de dados na caixa de listagem. Escolha o nome de início de sessão de administrador do servidor, como o exemplo **my admin**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="Criar uma base de dados em pgAdmin":::

1. Seleione **Guardar** para criar uma base de dados vazia nova.

1. No painel **Browser**, pode ver a base de dados que criou na lista de bases de dados, no nome do seu servidor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste quickstart não forem necessários para outro arranque rápido ou tutorial, pode eliminá-los executando o seguinte exemplo.

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste arranque rápido no grupo de recursos especificado, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Para eliminar apenas o servidor criado neste arranque rápido sem eliminar o grupo de recursos, utilize o `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Desenhe uma base de dados Azure para PostgreSQL usando PowerShell](tutorial-design-database-using-powershell.md)