---
title: Ligue-se ao recurso de espaço de trabalho do Synapse Studio a partir de uma rede restrita
description: Este artigo irá ensiná-lo a conectar-se aos recursos do seu espaço de trabalho Azure Synapse Studio a partir de uma rede restrita
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d94ee3145fb073dae982019fd4096cc2ceb7cd86
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578336"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Ligue-se aos recursos do espaço de trabalho do Synapse Studio a partir de uma rede restrita

O leitor-alvo deste artigo é a administração de TI da empresa que está a gerir a rede restrita da empresa. O administrador de TI está prestes a permitir a ligação de rede entre o Azure Synapse Studio e a estação de trabalho dentro desta rede restrita.

A partir deste artigo, você vai aprender a conectar-se ao seu espaço de trabalho Azure Synapse a partir de um ambiente de rede restrito. 

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição Azure** : Se não tiver uma subscrição do Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Espaço de trabalho Azure Synapse** : Se não tiver um Estúdio Synapse, crie um espaço de trabalho synapse da Azure Synapse Analytics. O nome do espaço de trabalho será necessário no passo seguinte 4.
* **Uma rede restrita** : A rede restrita é mantida pela administração de TI da empresa. O nome da rede virtual e a sua sub-rede serão necessários no passo seguinte 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Passo 1: Adicionar regras de segurança de saída da rede à rede restrita

Terá de adicionar quatro regras de segurança de saída da rede com quatro etiquetas de serviço. Saiba mais sobre [a visão geral das etiquetas de serviço](/azure/virtual-network/service-tags-overview) 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (Opcional. Adicione este tipo de regra apenas quando pretender partilhar os dados com a Microsoft.)

**Azure Resource Manager** de saída detalhes como abaixo. Quando estiver a criar as outras três regras, substitua o valor da " etiqueta de **serviço** de destino " com a escolha do nome de marca de serviço " **AzureFrontDoor.Frontend** ", " **AzureActiveDirectory** ", " **AzureMonitor** " da lista de seleção drop-down.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>Passo 2: Criar Azure Synapse Analytics (centros de ligação privada)

Você precisará criar um Azure Synapse Analytics (centros de ligação privada) a partir do portal Azure. Procure " **Azure Synapse Analytics (centros de ligação privada)** " através do portal Azure e, em seguida, preencha o campo necessário e crie-o. 

> [!Note]
> A região deve ser igual à que é o seu espaço de trabalho da Sinapse.

![Criação de centros de ligação privados Synapse Analytics](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-endpoint-for-synapse-studio-gateway"></a>Passo 3: Criar ponto final privado para porta de entrada do Estúdio Synapse

Para aceder ao portal Synapse Studio, terá de criar um ponto final privado a partir do portal Azure. Procure " **Private Link** " através do portal Azure. Selecione " **Criar ponto final privado** " no " Private Link **Center** " e, em seguida, preencha o campo necessário e crie-o. 

> [!Note]
> A região deve ser igual à que é o seu espaço de trabalho da Sinapse.

![Criação de ponto final privado para o estúdio Synapse 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

No separador seguinte de " **Recurso** ", escolha o hub de ligação privada, que foi criado no Passo 2 acima.

![Criação de ponto final privado para o estúdio Synapse 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

No separador seguinte da " **Configuração** ", 
* Escolha o nome de rede virtual restrito que tem para " **Rede Virtual** ".
* Escolha a sub-rede da rede virtual restrita para " **Subnet** ". 
* Selecione " **Sim** " para " **Integrar-se com a zona privada de DNS** ".

![Criação de ponto final privado para o estúdio Synapse 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Após a criação do ponto final do link privado, pode aceder à página de entrada da ferramenta web do estúdio Synapse. No entanto, ainda não poderá aceder aos recursos dentro do seu espaço de trabalho Synapse até que tenha de completar o próximo passo.

## <a name="step-4-create-private-endpoints-for-synapse-studio-workspace-resource"></a>Passo 4: Criar pontos finais privados para o recurso espaço de trabalho do Estúdio Synapse

Para aceder aos recursos dentro do seu recurso de espaço de trabalho Synapse Studio, você precisará criar pelo menos um ponto final de ligação privada com " **Dev** " tipo de " **sub-recurso alvo** " e dois outros pontos finais de ligação privada opcional com tipos de " **Sql** " ou " **SqlOnDemand** " depende de quais os recursos no espaço de trabalho do estúdio Synapse que você gostaria de aceder. Esta criação de ponto final de ligação privada para o espaço de trabalho do estúdio Synapse é semelhante à criação acima do ponto final.  

Preste atenção às áreas abaixo no separador " **Recurso** ":
* Selecione " **Microsoft.Synapse/workspaces** " para " **Tipo de recurso** ".
* Selecione " **YourWorkSpaceName** " para " **Recurso** " que criou antes.
* Selecione o tipo de ponto final em " **Sub-recurso target** ":
  * **Sql** : é para execução de consulta SQL na piscina SQL.
  * **SqlOnDemand:** é para execução de consulta incorporada SQL.
  * **Dev** : é para aceder a tudo o resto dentro dos espaços de trabalho do Synapse Studio. É necessário criar pelo menos o ponto final de ligação privada com este tipo.

![Criação de ponto final privado para o espaço de trabalho do estúdio Synapse](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-synapse-studio-workspace-linked-storage"></a>Passo 5: Criar pontos finais privados para o espaço de trabalho do Synapse Studio ligado ao armazenamento

Para aceder ao armazenamento ligado ao explorador de armazenamento no espaço de trabalho do Estúdio Synapse, terá de criar um ponto final privado com os passos semelhantes acima do passo 3. 

Preste atenção às áreas abaixo no separador " **Recurso** ":
* Selecione " **Microsoft.Synapse/storageAcounts** " para " **Tipo de recurso** ".
* Selecione " **YourWorkSpaceName** " para " **Recurso** " que criou antes.
* Selecione o tipo de ponto final em " **Sub-recurso target** ":
  * **blob** : é para o armazenamento de bolhas Azure.
  * **dfs** : é para Azure Data Lake Storage Gen2.

![Criação de ponto final privado para o espaço de trabalho do estúdio Synapse ligado ao armazenamento](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Agora, você pode aceder ao recurso de armazenamento ligado do explorador de armazenamento no seu espaço de trabalho Synapse Studio dentro do vNet.

Se o seu espaço de trabalho tiver " **Enable managed virtual network** " durante a sua criação de espaço de trabalho como abaixo,

![Criação de ponto final privado para o espaço de trabalho do estúdio Synapse ligado ao armazenamento 1](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

E você gostaria que o seu Notebook acedesse aos recursos de armazenamento ligados sob determinada conta de armazenamento, você precisa adicionar um " **Managed private endpoints** " no seu Estúdio Synapse. O " **nome da conta de armazenamento** " deve ser aquele a que o seu caderno precisa de aceder. Conheça os passos detalhados da [Create a Managed private endpoint até à sua fonte de dados](./how-to-create-managed-private-endpoints.md).

Após a criação deste ponto final, o " **estado de aprovação** " será " **Pendente** ", você precisa solicitar ao proprietário desta conta de armazenamento para aprová-lo no separador " **Ligações de ponto final privado** " desta conta de armazenamento no portal Azure. Depois de aprovado, o seu Caderno poderá aceder aos recursos de armazenamento ligados nesta conta de armazenamento.

Agora, tudo pronto. Você pode aceder ao seu recurso de espaço de trabalho do estúdio Synapse.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Rede Virtual gerida do espaço de trabalho](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)
