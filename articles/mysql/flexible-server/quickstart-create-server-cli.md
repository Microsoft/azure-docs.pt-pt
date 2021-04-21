---
title: 'Quickstart: Criar um servidor - Azure CLI - Azure Database for MySQL - Servidor Flexível'
description: Este quickstart descreve como usar o CLI Azure para criar uma Base de Dados Azure para o MySQL Flexible Server num grupo de recursos Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770242"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Quickstart: Criar uma base de dados Azure para o MySQL Flexible Server utilizando O Azure CLI

Este quickstart mostra como usar os comandos [Azure CLI](/cli/azure/get-started-with-azure-cli) em [Azure Cloud Shell](https://shell.azure.com) para criar uma Base de Dados Azure para o MySQL Flexible Server em cinco minutos. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O [Azure Cloud Shell](../../cloud-shell/overview.md) é uma concha interativa gratuita que pode usar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode abrir o Cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e selecione **Enter** para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/reference-index#az_login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure.

```azurecli-interactive
az login
```

Selecione a subscrição específica sob a sua conta usando o comando [conjunto de conta az.](/cli/azure/account#az_account_set) Tome nota do valor de **id** da saída de **login az** para usar como valor para o argumento **de subscrição** no comando. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter toda a sua subscrição, utilize [a lista de conta az](/cli/azure/account#az_account_list).

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Criar um servidor flexível

Crie um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md) utilizando o `az group create` comando e, em seguida, crie o seu servidor flexível MySQL dentro deste grupo de recursos. Deverá indicar um nome exclusivo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Crie um servidor flexível com o `az mysql flexible-server create` comando. Cada servidor pode conter várias bases de dados. O seguinte comando cria um servidor utilizando predefinições e valores de serviço a partir do [contexto local](/cli/azure/local-context)do seu Azure CLI: 

```azurecli-interactive
az mysql flexible-server create
```

O servidor criado tem os atributos abaixo: 
- Nome do servidor gerado automaticamente, nome de utilizador de administração, senha de administração, nome do grupo de recursos (se não especificado já no contexto local) e no mesmo local que o seu grupo de recursos 
- Predefinições de serviço para as restantes configurações do servidor: nível de cálculo (Burstable), tamanho de computação/SKU (B1MS), período de retenção de backup (7 dias) e versão MySQL (5.7)
- O método de conectividade padrão é o acesso privado (VNet Integration) com uma rede virtual gerada automaticamente e sub-rede

> [!NOTE] 
> O método de conectividade não pode ser alterado após a criação do servidor. Por exemplo, se selecionou *acesso privado (Integração VNet)* durante a criação, então não pode alterar para *acesso público (endereços IP permitidos)* após a criação. Recomendamos vivamente a criação de um servidor com acesso privado para aceder de forma segura ao seu servidor utilizando a Integração VNet. Saiba mais sobre o acesso privado no [artigo de conceitos.](./concepts-networking.md)

Se quiser alterar quaisquer incumprimentos, consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis. 

Abaixo está alguma saída de amostra: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Se quiser alterar quaisquer incumprimentos, consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis. 

## <a name="create-a-database"></a>Criar uma base de dados
Executar o seguinte comando para criar uma base de **dados, nova base de dados** se ainda não tiver criado uma.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Se for este o caso, não pode ligar ao servidor, a menos que o departamento de TI abra a porta 3306.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **fullyQualifiedDomainName** e o **administratorLogin**. Abaixo está uma amostra da saída JSON: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Ligar e testar a ligação utilizando o Azure CLI

A Azure Database for MySQL Flexible Server permite-lhe ligar-se ao seu servidor Mysql com o comando Azure ```az mysql flexible-server connect``` CLI. Este comando permite testar a conectividade do seu servidor de base de dados, criar uma base de dados de arranque rápida e executar consultas diretamente contra o seu servidor sem ter de instalar mysql.exe ou a bancada mySQL Workbench.  Também pode utilizar executar o comando num modo interativo para executar várias consultas.

Execute o seguinte script para testar e validar a ligação à base de dados a partir do seu ambiente de desenvolvimento.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Exemplo:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Deve ver a seguinte saída para uma ligação bem sucedida:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Se a ligação falhar, experimente estas soluções:
- Verifique se a porta 3306 está aberta na sua máquina cliente.
- se o nome de utilizador e a palavra-passe do administrador do servidor estiverem corretos
- se tiver uma regra de firewall configurada para a sua máquina cliente
- se configurar o seu servidor com acesso privado em rede virtual, certifique-se de que a sua máquina cliente está na mesma rede virtual.

Execute o seguinte comando para executar uma única consulta utilizando ```--querytext``` o argumento, ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exemplo:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Para saber mais sobre a utilização do ```az mysql flexible-server connect``` comando, consulte a documentação de [ligação e consulta.](connect-azure-cli.md)

## <a name="connect-using-mysql-command-line-client"></a>Conecte-se usando o cliente da linha de comando mysql

Se criou o seu servidor flexível utilizando o acesso privado (Integração VNet), terá de se ligar ao seu servidor a partir de um recurso dentro da mesma rede virtual que o seu servidor. Pode criar uma máquina virtual e adicioná-la à rede virtual criada com o seu servidor flexível. Consulte a [documentação de acesso privado](how-to-manage-virtual-network-portal.md) para saber mais.

Se criou o seu servidor flexível utilizando o acesso público (endereços IP autorizados), pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor. Consulte a documentação das [regras de firewall](how-to-manage-firewall-portal.md) para obter orientação passo a passo.

Pode utilizar [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [mySQL Workbench](./connect-workbench.md) para se ligar ao servidor a partir do seu ambiente local. A Azure Database for MySQL Flexible Server suporta ligar as aplicações do seu cliente ao serviço MySQL utilizando a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL). O TLS é um protocolo padrão da indústria que garante ligações de rede encriptadas entre o servidor da base de dados e as aplicações do cliente, permitindo-lhe aderir aos requisitos de conformidade. Para se conectar com o seu servidor flexível MySQL, você precisará de baixar o [certificado SSL público](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) para verificação da autoridade de certificado. Para saber mais sobre a ligação com ligações encriptadas ou o SSL desativado, consulte a [Connect to Azure Database for MySQL - Servidor Flexível com documentação de ligações encriptadas.](how-to-connect-tls-ssl.md)

O exemplo a seguir mostra como ligar-se ao seu servidor flexível utilizando a interface de linha de comando mysql. Primeiro instalará a linha de comando mysql se ainda não estiver instalada. Você irá baixar o certificado DigiCertGlobalRootCA necessário para ligações SSL. Utilize a definição de cadeia de ligação --ssl-mode=REQUIRED para impor a verificação do certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o parâmetro -ssl-ca. Substitua os valores pelo nome e senha do seu servidor.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Se tiver provisionado o seu servidor flexível utilizando **o acesso público,** também pode utilizar [o Azure Cloud Shell](https://shell.azure.com/bash) para se ligar ao seu servidor flexível utilizando o cliente mysql pré-instalado, como mostrado abaixo:

Para utilizar o Azure Cloud Shell para ligar ao seu servidor flexível, terá de permitir o acesso em rede do Azure Cloud Shell ao seu servidor flexível. Para isso, pode ir à lâmina **de rede** no portal Azure para o seu servidor flexível MySQL e verificar a caixa na secção **Firewall** que diz: "Permitir o acesso público de qualquer serviço Azure dentro do Azure a este servidor" como mostrado na imagem abaixo e clique em Guardar para persistir a definição.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Screenshot que mostra como permitir o acesso do Azure Cloud Shell ao servidor flexível MySQL para configuração da rede de acesso público.":::
 
 
> [!NOTE]
> A verificação do **acesso público a partir de qualquer serviço Azure dentro do Azure a este servidor** deve ser utilizado apenas para desenvolvimento ou teste. Configura a firewall para permitir ligações a partir de endereços IP atribuídos a qualquer serviço ou ativo Azure, incluindo ligações a partir das subscrições de outros clientes.

Clique em **Experimentá-lo** para lançar o Azure Cloud Shell e utilizar os seguintes comandos para ligar ao seu servidor flexível. Use o nome do servidor, o nome de utilizador e a palavra-passe no comando. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Ao ligar-se ao seu servidor flexível utilizando a Azure Cloud Shell, será necessário utilizar --ssl=parâmetro verdadeiro e não --ssl-mode=REQUIRED.
> A principal razão é que a Azure Cloud Shell vem com mysql.exe cliente pré-instalado da distribuição MariaDB que requer um parâmetro de ssl enquanto o cliente mysql da distribuição da Oracle requer o parâmetro do modo ssl.

Se vir a seguinte mensagem de erro enquanto se liga ao seu servidor flexível seguindo o comando anteriormente, falhou a definição da regra de firewall utilizando a regra "Permitir o acesso público de qualquer serviço Azure dentro do Azure a este servidor" mencionado anteriormente ou a opção não é guardada. Por favor, tente configurar a firewall e tente novamente.

ERROR 2002 (HY000): Não é possível ligar ao servidor MySQL <servername> em (115)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisa destes recursos para outro início rápido/tutorial, pode eliminá-los ao executar o seguinte comando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Se quiser eliminar o servidor recém-criado, pode executar o `az mysql server delete` comando.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
> [Conecte e consulta usando Azure CLI](connect-azure-cli.md) 
>  [Ligue à Base de Dados Azure para o MySQL - Servidor Flexível com ligações](how-to-connect-tls-ssl.md) 
>  encriptadas [Construa uma aplicação web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)
