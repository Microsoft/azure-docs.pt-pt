---
title: Início Rápido - criar uma Base de Dados do Azure para PostgreSQL com a CLI do Azure
description: Guia de introdução para criar e gerir a Base de Dados do Azure para o servidor PostgreSQL com a CLI (interface de linha de comandos) do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: 091babea272a609a668ebfb676a55e0fa02b8c27
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548750"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Início rápido: Criar uma Base de Dados do Azure para PostgreSQL com a CLI do Azure
A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de introdução mostra-lhe como criar uma Base de Dados do Azure para o servidor PostgreSQL num [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) com a CLI do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se estiver a executar a CLI localmente, tem de iniciar sessão na sua conta através do comando [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.
```azurecli-interactive
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade **id** da saída **az login** da sua subscrição no marcador de posição de id de subscrição.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. Deverá indicar um nome exclusivo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Crie uma [Base de Dados do Azure para o servidor PostgreSQL](overview.md) com o comando [az postgres server create](/cli/azure/postgres/server). Cada servidor pode conter várias bases de dados.


**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Escolha um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
resource-group | myResourceGroup | Indique o nome do grupo de recursos do Azure.
nome de SKU | GP_Gen5_2 | O nome de SKU. Segue a convenção {escalão de preço}_{geração de computação}_{vCores} em estenografia. Veja abaixo desta tabela para obter mais informações sobre o parâmetro de nome de SKU.
backup-retention | 7 | Quando tempo se deve reter uma cópia de segurança. A unidade é dias. O intervalo é de 7-35. 
geo-redundant-backup | Desactivado | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não. Valores permitidos: Ativado, desativado.
localização | euaoeste | A localização do Azure para o servidor.
ssl-enforcement | Activado | Se SSL deve ser ativado ou não para este servidor. Valores permitidos: Ativado, desativado.
storage-size | 51200 | A capacidade de armazenamento do servidor (a unidade é megabytes). O tamanho de armazenamento válido é 5120 MB no mínimo e aumenta em incrementos de 1024 MB. Consulte o documento que contém os [escalões de preço](./concepts-pricing-tiers.md) para obter mais informações sobre limites de tamanho de armazenamento. 
versão | 9.6 | A versão principal do PostgreSQL.
admin-user | myadmin | O nome de utilizador para o início de sessão do administrador. Não pode ser **azure_superuser**, **admin**, **administrador**, **raiz**, **convidado** nem **público**.
admin-password | *palavra-passe segura* | A palavra-passe do utilizador administrador. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres de três das seguintes categorias: Letras em maiúscula letras, em minúscula inglesas, números e carateres não alfanuméricos.


O valor do parâmetro sku-name segue a convenção {escalão de preço} \_ {geração de computação} \_ {vCores}, conforme os exemplos abaixo:
+ `--sku-name B_Gen5_1` é mapeado para básico, fins 5 e 1 vCore. Esta opção é o menor SKU disponível.
+ `--sku-name GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Leia a documentação dos [escalões de preços](./concepts-pricing-tiers.md) para entender os valores válidos por região e por escalão.

O exemplo seguinte cria um servidor PostgreSQL 9.6 em EUA Oeste com o nome `mydemoserver` no seu grupo de recursos `myresourcegroup`, com o início de sessão de administrador do servidor `myadmin`. Este é um servidor **Ger 4** de **Fins Gerais** com **2 vCores**. Substitua `<server_admin_password>` pelo seu próprio valor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!NOTE]
> Considere utilizar o escalão de preços básico se capacidades de computação e e/s são adequadas para sua carga de trabalho. Tenha em atenção que os servidores que criou no escalão de preço básico mais tarde não podem ser ampliadas para fins gerais ou com otimização de memória. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para obter mais informações.
> 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Crie uma regra de firewall ao nível do servidor do Azure PostgreSQL com o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). A regra de firewall ao nível do servidor permite que uma aplicação externa, como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), se ligue ao seu servidor através da firewall do serviço Azure PostgreSQL. 

Pode definir uma regra de firewall que abrange um intervalo IP para conseguir estabelecer ligação a partir da sua rede. O exemplo seguinte utiliza [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) para criar uma regra de firewall `AllowMyIP` para um endereço IP único.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> O servidor PostgreSQL do Azure comunica através da porta 5432. Ao ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Peça ao departamento de TI para abrir a porta 5432, para ligar ao servidor do Azure PostgreSQL.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **administratorLogin** e o **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Ligar à base de dados do PostgreSQL com psql

Se o seu computador cliente tiver o PostgreSQL instalado, pode utilizar uma instância local de [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para ligar a um servidor PostgreSQL do Azure. Vamos utilizar agora o utilitário da linha de comandos psql para ligar ao servidor PostgreSQL do Azure.

1. Execute o comando psql seguinte para ligar a uma Base de Dados do Azure para o servidor PostgreSQL
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Por exemplo, o comando seguinte liga à base de dados predefinida com o nome **postgres** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com**, com as credenciais de acesso. Introduza o `<server_admin_password>` que escolheu quando lhe for pedida a palavra-passe.
  
  ```bash
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  Assim que estiver ligado ao servidor, crie uma base de dados em branco na linha de comandos.
```sql
CREATE DATABASE mypgsqldb;
```

3.  Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** criada recentemente:
```sql
\c mypgsqldb
```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Ligar-se ao Servidor PostgreSQL com pgAdmin

pgAdmin é uma ferramenta de código aberto utilizada com o PostgreSQL. Pode instalar a pgAdmin a partir do site da [pgAdmin](https://www.pgadmin.org/). A versão de pgAdmin que estiver a utilizar poderá ser diferente da utilizada neste Início Rápido. Leia a documentação de pgAdmin se precisar de orientação adicional.

1. Abra a aplicação pgAdmin no computador cliente.

2. Na barra de ferramentas, vá a **Objeto**, paire o rato sobre **Criar** e selecione **Servidor**.

3. Na caixa de diálogo **Criar - Servidor**, no separador **Geral**, introduza um nome amigável exclusivo para o servidor, tal como **mydemoserver**.

   ![Separador "Geral"](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. Na caixa de diálogo **Criar - Servidor**, no separador **Ligação**, preencha a tabela de definições.

   ![Separador “Ligação”](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    parâmetro pgAdmin |Value|Descrição
    ---|---|---
    Nome/endereço do anfitrião | Nome do servidor | O valor do nome de servidor que foi utilizado quando criou anteriormente a Base de Dados do Azure para o servidor PostgreSQL. O nosso servidor de exemplo é **mydemoserver.postgres.database.azure.com.** Utilize o nome de domínio completamente qualificado (**\*.postgres.database.azure.com**), conforme mostrado no exemplo. Se não se lembrar do nome do servidor, siga os passos na secção anterior para obter as informações da ligação. 
    Porta | 5432 | A porta a utilizar quando se liga à Base de Dados do Azure para o servidor PostgreSQL. 
    Base de dados de manutenção | *postgres* | O nome predefinido da base de dados gerado pelo sistema.
    Nome de utilizador | Nome de início de sessão de administrador do servidor | O nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para o servidor PostgreSQL anteriormente. Se não se lembrar do nome de utilizador, siga os passos na secção anterior para obter as informações da ligação. O formato é *username@servername*.
    Palavra-passe | A sua palavra-passe de administrador | A palavra-passe que escolheu quando criou o servidor anteriormente neste Início Rápido.
    Função | Deixar em branco | Não é necessário indicar um nome de função neste momento. Deixe o campo em branco.
    Modo SSL | *Requerer* | Pode definir o modo SSL no separador SSL de pgAdmin. Por predefinição, todos os servidores da Base de Dados do Azure para PostgreSQL são criados com a imposição de SSL ativada. Para desativar a imposição de SSL, veja [Impor SSL](./concepts-ssl-connection-security.md).
    
5. Selecione **Guardar**.

6. No painel **Browser** do lado esquerdo, expanda o nó **Servidores**. Selecione o seu servidor, como, por exemplo, **mydemoserver**. Clique para ligar ao mesmo.

7. Expanda o nó de servidor e, em seguida, expanda **Bases de Dados** sob o mesmo. A lista deve incluir a sua base de dados *postgres* existente e quaisquer outras bases de dados que tenha criado. Pode criar várias bases de dados por servidor com a Base de Dados do Azure para PostgreSQL.

8. Clique com o botão direito em **Bases de Dados**, escolha o menu **Criar** e selecione **Base de Dados**.

9. Escreva um nome de base de dados à sua escolha no campo **Base de Dados**, como **mypgsqldb2**.

10. Selecione o **Proprietário** da base de dados na caixa de listagem. Escolha o nome de início de sessão de administrador do servidor, como o exemplo **my admin**.

   ![Criar uma base de dados em pgadmin](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Seleione **Guardar** para criar uma base de dados vazia nova.

12. No painel **Browser**, pode ver a base de dados que criou na lista de bases de dados, no nome do seu servidor.




## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que criou no guia de introdução ao eliminar o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Outros guias de introdução desta coleção têm por base este guia de introdução. Se quiser continuar a trabalhar com os guias de introdução subsequentes, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução na CLI do Azure.

```azurecli-interactive
az group delete --name myresourcegroup
```

Se quiser eliminar o único servidor criado recentemente, pode executar o comando [az postgres server delete](/cli/azure/postgres/server).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)

