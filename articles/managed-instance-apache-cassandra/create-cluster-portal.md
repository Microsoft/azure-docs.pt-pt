---
title: Quickstart - Criar exemplo gerido azure para o aglomerado Apache Cassandra a partir do portal Azure
description: Este quickstart mostra como criar um Azure Managed Instance para o aglomerado Apache Cassandra usando o portal Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e42f85bb79dcb1bfe14cacbbfda3576888b841c9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481333"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Quickstart: Criar uma instância gerida azure para o cluster Apache Cassandra a partir do portal Azure (Preview)
 
A azure Managed Instance for Apache Cassandra fornece operações automatizadas de implantação e escala para centros de dados apache cassandra geridos de código aberto, acelerando cenários híbridos e reduzindo a manutenção contínua.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este quickstart demonstra como usar o portal Azure para criar um Azure Managed Instance para o cluster Apache Cassandra.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Criar um cluster de instâncias gerido

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. A partir da barra de pesquisa, procure **por Caso Gerido para Apache Cassandra** e selecione o resultado.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Procura por Exemplo Gerido para Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Selecione **Create Managed Instance para o botão de cluster Apache Cassandra.**

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Criar o cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. A partir do **exemplo de Criar Gerido para Apache Cassandra,** insira os seguintes detalhes:

   * **Subscrição** - A partir do drop-down, selecione a sua subscrição Azure.
   * **Grupo de Recursos**- Especifique se pretende criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. Para mais informações, consulte o artigo geral [do Grupo de Recursos Azure.](../azure-resource-manager/management/overview.md)
   * **Nome do cluster** - Introduza um nome para o seu cluster.
   * **Localização** - Localização para onde o seu cluster será implantado.
   * **SKU** - O tipo de SKU para o seu cluster.
   * **Não, não, não, não. de nós**- Número de nós num aglomerado. Estes nós funcionam como réplicas para os seus dados.
   * **Senha de administração inicial de Cassandra** - Palavra-passe que é usada para criar o cluster.
   * **Confirme a palavra-passe de Cassandra** - Reenterça a sua senha.

    > [!NOTE]
    > Durante a pré-visualização pública, você pode criar o cluster de instâncias geridos nas regiões *leste dos EUA, Oeste dos EUA, Leste dos EUA 2, West US 2, Central EUA, Norte da Europa, Europa Ocidental, Sudeste Asiático e Austrália Leste.*

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Preencha o formulário de agrupamento de criação." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Em seguida, selecione o **separador 'Rede'.**

1. No painel **de rede,** escolha o nome **de Rede Virtual** e **sub-rede.** Pode selecionar uma Rede Virtual existente ou criar uma nova.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Configure detalhes de rede." lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > A implementação de uma instância gerida azure para a Apache Cassandra requer acesso à Internet. A implementação falha em ambientes onde o acesso à Internet é restrito. Certifique-se de que não bloqueia o acesso dentro do seu VNet aos seguintes serviços vitais da Azure que são necessários para que a Cassandra gerida funcione corretamente:
    > - Storage do Azure
    > - Azure KeyVault
    > - Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
    > - Monitorização do Azure
    > - Azure Active Directory
    > - Azure Security

1. Se criou um novo VNet no último passo, salte para o passo 8. Se selecionou um VNet existente, antes de criar o seu cluster, tem de aplicar algumas permissões especiais à Rede Virtual e à sub-rede. Para tal, utilize o `az role assignment create` comando, `<subscription ID>` substituindo, e com os `<resource group name>` `<VNet name>` valores adequados:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Os `assignee` `role` valores e valores no comando anterior são valores fixos, insira estes valores exatamente como mencionado no comando. Não fazê-lo levará a erros ao criar o cluster. Se encontrar algum erro ao executar este comando, poderá não ter permissões para o executar, por favor contacte o seu administrador para obter permissões.

1. Agora que terminou com networking, clique em **'Revisão +**  >  **Criar'**

    > [!NOTE]
    > Pode levar até 15 minutos para o cluster ser criado.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Reveja o resumo para criar o cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Após a implementação ter terminado, verifique o seu grupo de recursos para ver o cluster de instâncias gerido recentemente criado:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Página geral após a criação do cluster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Para navegar pelos nosdes de cluster, navegue até ao painel de Rede Virtual que utilizou para criar o cluster e abrir o painel **de visão geral** para os visualizar:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Veja os recursos do cluster." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Ligação ao seu cluster

Azure Managed Instance for Apache Cassandra não cria nós com endereços IP públicos, por isso, para se ligar ao seu recém-criado cluster Cassandra, terá de criar outro recurso dentro do VNet. Isto pode ser uma aplicação, ou uma Máquina Virtual com a ferramenta de consulta de código aberto da Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) instalada. Pode utilizar um [modelo](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) para implementar uma Máquina Virtual Ubuntu. Quando estiver implantado, utilize o SSH para ligar à máquina e instale o CQLSH utilizando os comandos abaixo:

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

## <a name="troubleshooting"></a>Resolução de problemas

Se encontrar um erro ao aplicar permissões à sua Rede Virtual, como *não pode encontrar o utilizador ou o principal de serviço na base de dados de gráficos para 'e5007d2c-4b13-4a74-9b6a-605d99f03501'*, pode aplicar manualmente a mesma permissão a partir do portal Azure. Para aplicar permissões a partir do portal, vá ao painel de controlo de **acesso (IAM)** da sua rede virtual existente e adicione uma atribuição de função para "Azure Cosmos DB" à função "Administrador de Rede". Se aparecerem duas entradas quando procura "Azure Cosmos DB", adicione ambas as entradas como mostrado na imagem seguinte: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Aplicar permissões" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A atribuição de funções DB da Azure Cosmos é utilizada apenas para fins de implantação. Azure Managed Instanced for Apache Cassandra não tem dependências de Azure Cosmos DB.   

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar este agrupamento de instâncias gerido, elimine-o com os seguintes passos:

1. A partir do menu à esquerda do portal Azure, selecione **Grupos de Recursos**.
1. A partir da lista, selecione o grupo de recursos que criou para este arranque rápido.
1. No painel **de visão geral** do grupo de recursos, selecione **Delete resource group**.
1. Na janela seguinte, insira o nome do grupo de recursos para eliminar e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar um Azure Managed Instance para o aglomerado Apache Cassandra usando o portal Azure. Pode agora começar a trabalhar com o cluster:

> [!div class="nextstepaction"]
> [Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure](deploy-cluster-databricks.md)
