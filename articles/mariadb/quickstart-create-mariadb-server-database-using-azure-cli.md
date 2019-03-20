---
title: 'Início rápido: Criar uma base de dados do Azure para MariaDB server - CLI do Azure'
description: Este início rápido descreve como pode utilizar a CLI do Azure para criar uma Azure Database for MariaDB Server num grupo de recursos do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: 7d94834523e331ff048f787760561739765e7023
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842299"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Criar um servidor do Azure Database for MariaDB com a CLI do Azure

Pode utilizar a CLI do Azure para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este início rápido descreve como pode utilizar a CLI do Azure para criar uma Azure Database for MariaDB Server num grupo de recursos do Azure em cerca de cinco minutos. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se instalar e utilizar a CLI localmente, para este início rápido, tem de executar a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

Se tiver várias subscrições, escolha a subscrição que contém o recurso ou a subscrição na qual é cobrado. Para selecionar um ID de subscrição específico na sua conta, utilize o comando [az account set](/cli/azure/account#az-account-set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um Azure Database for MariaDB Server

Crie um servidor do Azure Database for MariaDB com o comando [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create). Cada servidor pode gerir múltiplas bases de dados. Geralmente, é utilizada uma base de dados em separado para cada projeto ou para cada utilizador.

Definição | Valor da amostra | Descrição
---|---|---
nome | **mydemoserver** | Introduza um nome exclusivo que identifique o servidor do Azure Database for MariaDB. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
resource-group | **myresourcegroup** | Introduza o nome do grupo de recursos do Azure.
sku-name | **GP_Gen5_2** | O nome do SKU. Segue a convenção *escalão de preço*\_*geração de computação*\_*vCores* em estenografia. Para obter mais informações sobre o parâmetro **sku-name**, veja a secção abaixo desta tabela.
backup-retention | **7** | Quando tempo se deve reter uma cópia de segurança. A unidade é dias. Intervalo de: 7 para 35. 
geo-redundant-backup | **Desativado** | Se as cópias de segurança georredundantes devem estar ativadas para este servidor. Valores permitidos: **Ativada**, **desativado**.
localização | **westus** | A localização do Azure para o servidor.
ssl-enforcement | **Ativado** | Se SSL deve ser ativado para este servidor. Valores permitidos: **Ativada**, **desativado**.
storage-size | **51200** | A capacidade de armazenamento do servidor (a unidade é megabytes). Os tamanhos de armazenamento válidos são 5120 MB (mínimo) com aumentos em incrementos de 1024 MB. Para obter mais informações sobre os limites de tamanho de armazenamento, veja [Escalões de preço](./concepts-pricing-tiers.md). 
versão | **10.2** | A versão do motor principal de MariaDB.
admin-user | **myadmin** | O nome de utilizador para o início de sessão do administrador. O parâmetro **admin-user** não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.
admin-password | *a sua palavra-passe* | A palavra-passe do utilizador administrador. A palavra-passe tem de conter entre 8 e 128 carateres. Tem de conter carateres de três das seguintes categorias: Letras em maiúscula letras, em minúscula inglesas, números e carateres não alfanuméricos.

O valor do parâmetro sku-name segue a convenção {escalão de preço} \_ {geração de computação} \_ {vCores}, conforme os exemplos abaixo:
+ `--sku-name B_Gen5_1` é mapeado para básico, fins 5 e 1 vCore. Esta opção é o menor SKU disponível.
+ `--sku-name GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Para obter informações sobre os valores válidos por região e para os escalões, veja [Escalões de preço](./concepts-pricing-tiers.md).

O exemplo seguinte cria um servidor com o nome **mydemoserver** na região E.U.A. Oeste. O servidor está no grupo de recursos **myresourcegroup** e tem o início de sessão de administrador do servidor **myadmin**. O servidor é um servidor de Ger 5 no escalão de preço Fins Gerais e tem 2 vCores. Um nome de servidor é mapeado para um nome DNS e tem de ser globalmente exclusivo no Azure. Substitua `<server_admin_password>` pela sua própria palavra-passe de administrador do servidor.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Considere utilizar o escalão de preços básico se capacidades de computação e e/s são adequadas para sua carga de trabalho. Tenha em atenção que os servidores que criou no escalão de preço básico mais tarde não podem ser ampliadas para fins gerais ou com otimização de memória. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/mariadb/) para obter mais informações.
> 

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Crie uma regra de firewall ao nível do servidor do Azure Database for MariaDB com o comando [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). A regra de firewall ao nível do servidor permite que uma aplicação externa, como a ferramenta de linha de comandos mysql ou o MySQL Workbench, se ligue ao seu servidor através da firewall do serviço Azure Database for MariaDB. 

O exemplo seguinte cria uma regra de firewall chamada `AllowMyIP` que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua um endereço IP ou intervalo de endereços IP que corresponde à localização a partir da qual está a estabelecer ligação. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> As ligações à Azure Database for MariaDB comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Nesse caso, pode ligar ao servidor apenas se o departamento de TI abrir a porta 3306.
> 

## <a name="configure-ssl-settings"></a>Configurar as definições de SSL

Por predefinição, são aplicadas ligações SSL entre o servidor e as aplicações cliente. Esta predefinição garante a segurança dos dados "em movimento" ao encriptar o fluxo de dados através da Internet. Para este início rápido, desative as ligações SSL no seu servidor. A desativação de SSL não é recomendada para servidores de produção. Para obter mais informações, consulte [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Azure Database for MariaDB).

O exemplo seguinte desativa a imposição de SSL no servidor do Azure Database for MariaDB:
 
```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Obter informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião. Para obter as informações de ligação, execute o seguinte comando:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte os valores **fullyQualifiedDomainName** e **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Ligar ao servidor com a ferramenta de linha de comandos mysql

Ligue ao seu servidor com a ferramenta de linha de comandos mysql. Pode [transferir](https://dev.mysql.com/downloads/) a ferramenta de linha de comandos e instalá-la no seu computador. Também pode aceder à ferramenta de linha de comandos ao selecionar o botão **Experimente** num exemplo de código neste artigo. Outra forma e aceder à ferramenta de linha de comandos é selecionar o botão **>_** na barra de ferramentas na parte superior direita no portal do Azure para abrir o **Azure Cloud Shell**.

Para ligar ao servidor com a ferramenta de linha de comandos mysql:

1. Ligar ao servidor:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Ver o estado do servidor na linha de comandos `mysql>`:

   ```sql
   status
   ```
   Deverá ver algo semelhante ao texto seguinte:

   ```bash
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> Para obter comandos adicionais, veja [MySQL 5.7 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manual de Referência do MySQL 5.7 - Capítulo 4.5.1).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Ligar ao servidor com o MySQL Workbench

1. Abra o MySQL Workbench no computador cliente. Se ainda não estiver instalado, [transfira](https://dev.mysql.com/downloads/workbench/) e instale a aplicação.

2. Na caixa de diálogo **Configurar Ligação Nova**, no separador **Parâmetros**, introduza as informações seguintes:

   ![Configurar uma ligação nova](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Definição | Valor sugerido | Descrição |
   |---|---|---|
   | Nome da Ligação | **Ligação de demonstração** | Introduza uma etiqueta para esta ligação (pode ser qualquer nome) |
   | Método de Ligação | **Standard (TCP/IP)** | Utilizar o protocolo TCP/IP para ligar ao Azure Database for MariaDB |
   | Nome de anfitrião | **mydemoserver.mariadb.database.azure.com** | O nome de servidor que anotou anteriormente. |
   | Porta | **3306** | A porta predefinida para o Azure Database for MariaDB. |
   | Nome de utilizador | **myadmin\@mydemoserver** | O início de sessão de administrador do servidor que anotou anteriormente. |
   | Palavra-passe | *a sua palavra-passe* | Utilize a palavra-passe da conta de administrador que configurou anteriormente. |

3. Para verificar se todos os parâmetros estão configurados corretamente, selecione **Testar Ligação**.

4. Selecione a ligação para ligar com êxito ao servidor.

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisa dos recursos que utilizou neste início rápido para outro início rápido ou tutorial, pode eliminá-los ao executar o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se quiser eliminar apenas o servidor que criou neste início rápido, execute o comando [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete):

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados da MariaDB com a CLI do Azure](./tutorial-design-database-cli.md)
