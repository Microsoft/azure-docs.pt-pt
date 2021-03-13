---
title: Quickstart - Use CLI para criar Azure Managed Instance para o cluster Apache Cassandra
description: Utilize este quickstart para criar um Azure Managed Instance para o aglomerado Apache Cassandra usando O Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 6de2e0f1744b333a830fbe500e2df51e7eaca62d
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419087"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Quickstart: Criar uma instância gerida azure para o cluster Apache Cassandra usando Azure CLI (Preview)

Azure Managed Instance for Apache Cassandra fornece operações automatizadas de implantação e escala para centros de dados apache cassandra geridos de código aberto. Este serviço ajuda-o a acelerar cenários híbridos e a reduzir a manutenção contínua.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este quickstart demonstra como usar os comandos Azure CLI para criar um cluster com Azure Managed Instance para Apache Cassandra. Também mostra criar um datacenter, e escalar nós para cima ou para baixo dentro do datacenter.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) com conectividade ao seu ambiente auto-hospedado ou no local. Para obter mais informações sobre a ligação em ambientes de instalações a Azure, consulte a rede De ligar uma rede no local ao artigo [da Azure.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Este artigo requer a versão Azure CLI 2.12.1 ou superior. Se estiver a utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Criar um cluster de instâncias gerido

1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

1. Desconfie do seu ID de subscrição no Azure CLI:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Em seguida, crie uma Rede Virtual com uma sub-rede dedicada no seu grupo de recursos:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Aplique algumas permissões especiais na Rede Virtual e na sub-rede, que são exigidas pela instância gerida. Utilize o `az role assignment create` comando, `<subscription ID>` substituindo, , e pelos `<resource group name>` `<VNet name>` `<subnet name>` valores adequados:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Os `assignee` `role` valores e valores no comando anterior são valores fixos, insira estes valores exatamente como mencionado no comando. Não fazê-lo levará a erros ao criar o cluster. Se encontrar algum erro ao executar este comando, poderá não ter permissões para o executar, por favor contacte o seu administrador para obter permissões.

1. Em seguida, crie o cluster na sua rede virtual recém-criada. Executar o seguinte comando e certifique-se de que utiliza o `Resource ID` valor recuperado no comando anterior como valor da `delegatedManagementSubnetId` variável:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Finalmente, crie um datacenter para o cluster, com três nóns:

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. Uma vez criado o datacenter, se pretender escalar ou reduzir os nós no datacenter, executar o seguinte comando. Alterar o valor do `node-count` parâmetro para o valor pretendido:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Conecte-se ao seu cluster

Azure Managed Instance for Apache Cassandra não cria nós com endereços IP públicos. Para se conectar ao seu recém-criado cluster Cassandra, você deve criar outro recurso dentro da rede virtual. Este recurso pode ser uma aplicação, ou uma máquina virtual com a ferramenta de consulta de código aberto da Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) instalada. Pode utilizar um [modelo de Gestor de Recursos](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) para implantar uma máquina virtual Ubuntu. Depois de ser implantado, utilize o SSH para ligar à máquina e instalar o CQLSH como mostrado nos seguintes comandos:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o `az group delete` comando para remover o grupo de recursos, a instância gerida e todos os recursos relacionados:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar um Azure Managed Instance para o aglomerado Apache Cassandra usando Azure CLI. Pode agora começar a trabalhar com o cluster:

> [!div class="nextstepaction"]
> [Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure](deploy-cluster-databricks.md)
