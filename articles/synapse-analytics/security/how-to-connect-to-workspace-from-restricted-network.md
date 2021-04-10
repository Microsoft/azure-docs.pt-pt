---
title: Conecte-se aos recursos do espaço de trabalho no Azure Synapse Analytics Studio a partir de uma rede restrita
description: Este artigo irá ensiná-lo a conectar-se aos seus recursos do espaço de trabalho a partir de uma rede restrita
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384525"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Conecte-se aos recursos do espaço de trabalho a partir de uma rede restrita

Suponha que é um administrador de TI que está a gerir a rede restrita da sua organização. Pretende ativar a ligação de rede entre o Azure Synapse Analytics Studio e uma estação de trabalho dentro desta rede restrita. Este artigo mostra-lhe como.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição Azure**: Se não tiver uma subscrição do Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Espaço de trabalho Azure Synapse Analytics**: Pode criar um a partir de Azure Synapse Analytics. Precisa do nome do espaço de trabalho no passo 4.
* **Uma rede restrita**: O administrador de TI mantém a rede restrita para a organização e tem permissão para configurar a política de rede. Precisa do nome da rede virtual e da sua sub-rede no passo 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Passo 1: Adicionar regras de segurança de saída da rede à rede restrita

Terá de adicionar quatro regras de segurança de saída da rede com quatro etiquetas de serviço. 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (Este tipo de regra é opcional. Adicione-os apenas quando pretender partilhar os dados com a Microsoft.)

A imagem que se segue mostra detalhes para a regra de saída do Azure Resource Manager.

![Screenshot dos detalhes da etiqueta de serviço do Azure Resource Manager.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

Quando estiver a criar as outras três regras, substitua a etiqueta de **serviço** de destino por **AzureFrontDoor.Frontend**, **AzureActiveDirectory** ou **AzureMonitor** da lista.

Para obter mais informações, consulte [a visão geral das etiquetas de serviço.](../../virtual-network/service-tags-overview.md)

## <a name="step-2-create-private-link-hubs"></a>Passo 2: Criar centros de ligação privados

Em seguida, crie centros de ligação privados a partir do portal Azure. Para encontrar isto no portal, procure a *Azure Synapse Analytics (centros de ligação privado)* e, em seguida, preencha as informações necessárias para a criar. 

![Screenshot do centro de ligação privada Create Synapse.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>Passo 3: Crie um ponto final privado para o seu Estúdio Synapse

Para aceder ao Azure Synapse Analytics Studio, tem de criar um ponto final privado a partir do portal Azure. Para encontrar isto no portal, procure *o Private Link*. No **Private Link Center**, selecione **Criar ponto final privado** e, em seguida, preencha as informações necessárias para criá-la. 

> [!Note]
> Certifique-se de que o valor da **Região** é o mesmo que aquele onde é o seu espaço de trabalho Azure Synapse Analytics.

![Screenshot de Criar um ponto final privado, separador Basics.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

No separador **Recursos,** escolha o hub de ligação privada, que criou no passo 2.

![Screenshot de Criar um ponto final privado, separador de recursos.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

No **separador Configuração:** 
* Para **a rede Virtual,** selecione o nome de rede virtual restrito.
* Para **a Sub-rede,** selecione a sub-rede da rede virtual restrita. 
* Para **integrar-se com a zona privada de DNS,** selecione **Sim**.

![Screenshot de Criar um ponto final privado, separador de configuração.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Após a criação do ponto final do link privado, pode aceder à página de entrada da ferramenta web para o Azure Synapse Analytics Studio. No entanto, ainda não é possível aceder aos recursos dentro do seu espaço de trabalho. Para isso, tens de completar o próximo passo.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Passo 4: Criar pontos finais privados para o seu recurso de espaço de trabalho

Para aceder aos recursos dentro do seu recurso de espaço de trabalho Azure Synapse Analytics Studio, precisa de criar o seguinte:

- Pelo menos um ponto final de ligação privada com um **sub-recurso alvo** do **Tipo Dev**.
- Dois outros pontos finais de ligação privada opcional com tipos de **Sql** ou **SqlOnDemand,** dependendo dos recursos no espaço de trabalho a que pretende aceder.

Criar estes é semelhante à forma como cria o ponto final no passo anterior.  

No **separador Recursos:**

* Para **o tipo de recurso**, selecione **Microsoft.Synapse/workspaces**.
* Para **Recurso**, selecione o nome do espaço de trabalho que criou anteriormente.
* Para **sub-recurso target,** selecione o tipo de ponto final:
  * **Sql** é para execução de consulta SQL na piscina SQL.
  * **SqlOnDemand** é para execução de consulta incorporada SQL.
  * **Dev** é para aceder a tudo o resto dentro dos espaços de trabalho do Azure Synapse Analytics Studio. É necessário criar pelo menos um ponto final de ligação privada deste tipo.

![Screenshot de Criar um ponto final privado, separador de recursos, espaço de trabalho.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Passo 5: Criar pontos finais privados para armazenamento ligado ao espaço de trabalho

Para aceder ao armazenamento ligado ao explorador de armazenamento no espaço de trabalho Azure Synapse Analytics Studio, tem de criar um ponto final privado. Os passos para isto são semelhantes aos do passo 3. 

No **separador Recursos:**
* Para **o tipo de recurso**, selecione **Microsoft.Synapse/storageAccounts**.
* Para **Recurso**, selecione o nome da conta de armazenamento que criou anteriormente.
* Para **sub-recurso target,** selecione o tipo de ponto final:
  * **Blob** é para Azure Blob Storage.
  * **dfs** é para Azure Data Lake Storage Gen2.

![Screenshot de Criar um ponto final privado, separador de recursos, armazenamento.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Agora, pode aceder ao recurso de armazenamento ligado. Dentro da sua rede virtual, no seu espaço de trabalho Azure Synapse Analytics Studio, pode utilizar o explorador de armazenamento para aceder ao recurso de armazenamento ligado.

Pode ativar uma rede virtual gerida para o seu espaço de trabalho, como mostra esta imagem:

![Screenshot do espaço de trabalho Create Synapse, com a opção de rede virtual gerida em destaque.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Se pretender que o seu portátil aceda aos recursos de armazenamento ligados numa determinada conta de armazenamento, adicione pontos finais privados geridos no âmbito do seu Azure Synapse Analytics Studio. O nome da conta de armazenamento deve ser aquele a que o seu caderno precisa de aceder. Para obter mais informações, consulte [Criar um ponto final privado gerido para a sua fonte de dados.](./how-to-create-managed-private-endpoints.md)

Depois de criar este ponto final, o estado de aprovação mostra um estado de **pendente**. Solicite a aprovação do proprietário desta conta de armazenamento, no separador de **ligações de ponto final privado** desta conta de armazenamento no portal Azure. Depois de aprovado, o seu caderno pode aceder aos recursos de armazenamento ligados nesta conta de armazenamento.

Agora, tudo pronto. Você pode aceder ao seu recurso de espaço de trabalho Azure Synapse Analytics Studio.

## <a name="appendix-dns-registration-for-private-endpoint"></a>Apêndice: Registo de DNS para ponto final privado

Se a "Integração com a zona privada de DNS" não estiver ativada durante a criação do ponto final privado como screenshot abaixo, deve criar a "**zona privada de DNS**" para cada um dos seus pontos finais privados.
![Screenshot da zona privada de DNS da Create Synapse 1.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

Para encontrar a **zona privada do DNS** no portal, procure a *zona privada do DNS*. Na zona privada do **DNS,** preencha as informações necessárias abaixo para a criar.

* Para **nome**, insira o nome dedicado da zona privada de DNS para o ponto final privado específico como abaixo:
  * **`privatelink.azuresynapse.net`** é para o ponto final privado de acesso a Azure Synapse Analytics Studio gateway. Veja este tipo de criação de ponto final privado no passo 3.
  * **`privatelink.sql.azuresynapse.net`** é para este tipo de ponto final privado de execução de consulta sql em piscina SQL e piscina embutido. Veja a criação do ponto final no passo 4.
  * **`privatelink.dev.azuresynapse.net`** é para este tipo de ponto final privado de acesso a tudo o resto dentro dos espaços de trabalho do Azure Synapse Analytics Studio. Veja este tipo de criação de ponto final privado no passo 4.
  * **`privatelink.dfs.core.windows.net`** é para o ponto final privado de acesso ao espaço de trabalho ligado Azure Data Lake Storage Gen2. Veja este tipo de criação de ponto final privado no passo 5.
  * **`privatelink.blob.core.windows.net`** é para o ponto final privado de acesso ao espaço de trabalho ligado Azure Blob Storage. Veja este tipo de criação de ponto final privado no passo 5.

![Screenshot da zona privada de DNS da Create Synapse 2.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

Depois da **zona privada de DNS** criada, insira a zona privada de DNS criada e selecione as **ligações de rede Virtual** para adicionar o link à sua rede virtual. 

![Screenshot da zona privada de DNS da Create Synapse 3.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

Preencha os campos obrigatórios como abaixo:
* Para **o nome Link,** insira o nome do link.
* Para **rede Virtual,** selecione a sua rede virtual.

![Screenshot da zona privada de DNS da Create Synapse 4.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

Depois de adicionar o link de rede virtual, é necessário adicionar o registo DNS estabelecido na **zona privada de DNS** que criou anteriormente.

* Para **nome,** insira as cordas de nome dedicadas para diferentes pontos finais privados: 
  * **web** é para o ponto final privado de acesso Azure Synapse Analytics Studio.
  * "***YourWorkSpaceName***" é para o ponto final privado da execução de consultas sql na piscina SQL e também para o ponto final privado de acesso a tudo o resto dentro dos espaços de trabalho do Azure Synapse Analytics Studio.
  * "***YourWorkSpaceName*-ondemand"** é para o ponto final privado da execução de consulta de sql em piscina embutida.
* Para **tipo**, selecione apenas o tipo de registo **DNS.** 
* Para **o endereço IP,** insira o endereço IP correspondente de cada ponto final privado. Pode obter o endereço IP na **interface da Rede a** partir da sua visão geral do ponto final privado.

![Screenshot da zona privada de DNS da Create Synapse 5.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a rede virtual do espaço de trabalho gerido.](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [os pontos finais privados geridos.](./synapse-workspace-managed-private-endpoints.md)
