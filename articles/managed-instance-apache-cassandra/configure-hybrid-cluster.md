---
title: Quickstart - Configurar um cluster híbrido com Azure Managed Instance para Apache Cassandra
description: Este quickstart mostra como configurar um cluster híbrido com Azure Managed Instance para Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 11daa548e90aa1906ba87e081fa1e0be6fe6aff8
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430773"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Quickstart: Configurar um cluster híbrido com Azure Managed Instance para Apache Cassandra (Preview)

Azure Managed Instance for Apache Cassandra fornece operações automatizadas de implantação e escala para centros de dados apache cassandra geridos de código aberto. Este serviço ajuda-o a acelerar cenários híbridos e a reduzir a manutenção contínua.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este quickstart demonstra como usar os comandos Azure CLI para configurar um cluster híbrido. Se tiver centros de dados existentes num ambiente no local ou auto-alojado, pode utilizar a Azure Managed Instance para a Apache Cassandra adicionar outros centros de dados a esse cluster e mantê-los.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Este artigo requer a versão Azure CLI 2.12.1 ou superior. Se estiver a utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

* [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) com conectividade ao seu ambiente auto-hospedado ou no local. Para obter mais informações sobre a ligação em ambientes de instalações a Azure, consulte a rede De ligar uma rede no local ao artigo [da Azure.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Configurar um cluster híbrido

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para o seu recurso rede virtual.

1. Abra o separador **Subnetas** e crie uma nova sub-rede. Para saber mais sobre os campos no **formulário de sub-rede Adicionar,** consulte o artigo [da Rede Virtual:](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Adicione uma nova sub-rede à sua Rede Virtual." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. Agora aplicaremos algumas permissões especiais ao VNet e à sub-rede que a Cassandra Managed Instance requer, utilizando o Azure CLI. Utilize o `az role assignment create` comando, `<subscription ID>` substituindo, , e pelos `<resource group name>` `<VNet name>` `<subnet name>` valores adequados:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Os `assignee` `role` valores e valores no comando anterior são princípio de serviço fixo e identificadores de função, respectivamente.

1. Em seguida, vamos configurar recursos para o nosso cluster híbrido. Uma vez que já tem um cluster, o nome do cluster aqui será apenas um recurso lógico para identificar o nome do seu cluster existente. Certifique-se de que utiliza o nome do seu cluster existente ao definir `clusterName` e `clusterNameOverride` variáveis no seguinte script.

   Você também precisa dos nós de sementes, certificados de cliente público (se você configurar uma chave público/privada no seu ponto final cassandra), e certificados de mexericos do seu cluster existente. Também terá de usar o ID de recursos que copiou acima para definir a `delegatedManagementSubnetId` variável.

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='<Resource ID>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Deve saber onde estão guardados os certificados de coscuvilhice e/ou fofocas existentes. Se tiver dúvidas, deverá ser capaz de imprimir `keytool -list -keystore <keystore-path> -rfc -storepass <password>` os certificados. 

1. Após a criação do recurso cluster, executar o seguinte comando para obter os detalhes de configuração do cluster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. O comando anterior devolve informações sobre o ambiente de instância gerida. Você precisará dos certificados de mexericos para que possa instalá-los nos nós no seu datacenter existente. A imagem que se segue mostra a saída do comando anterior e o formato dos certificados:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Obtenha os detalhes do certificado do cluster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Em seguida, crie um novo datacenter no cluster híbrido. Certifique-se de substituir os valores variáveis pelos seus detalhes do cluster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource ID>'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedSubnetId \
       --node-count 9 
   ```

1. Agora que o novo datacenter foi criado, execute o comando do datacenter do programa para ver os seus detalhes:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. O comando anterior produz os nós de sementes do novo datacenter. Adicione os nós de sementes do novo datacenter à configuração do datacenter existente dentro do ficheiro *cassandra.yaml.* E instale os certificados de fofocas de instância gerida que recolheu anteriormente:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Obtenha detalhes do datacenter." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Se quiser adicionar mais centros de dados, pode repetir os passos acima, mas só precisa dos nós de sementes. 

1. Finalmente, utilize a seguinte consulta CQL para atualizar a estratégia de replicação em cada espaço-chave para incluir todos os datacenters em todo o cluster:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Também precisa de atualizar as tabelas de palavras-passe:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar este agrupamento de instâncias gerido, elimine-o com os seguintes passos:

1. A partir do menu à esquerda do portal Azure, selecione **Grupos de Recursos**.
1. A partir da lista, selecione o grupo de recursos que criou para este arranque rápido.
1. No painel **de visão geral** do grupo de recursos, selecione **Delete resource group**.
3. Na janela seguinte, insira o nome do grupo de recursos para eliminar e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você aprendeu a criar um cluster híbrido usando Azure CLI e Azure Managed Instance para Apache Cassandra. Agora pode começar a trabalhar com o cluster.

> [!div class="nextstepaction"]
> [Gerir a Azure Managed Instance para os recursos da Apache Cassandra utilizando o Azure CLI](manage-resources-cli.md)