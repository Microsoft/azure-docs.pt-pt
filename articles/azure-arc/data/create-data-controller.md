---
title: Criar controlador de dados
description: Crie um controlador de dados Azure Arc num cluster típico de Kubernetes multi-node que já implementou.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: dd20a76ebc833320050c87862ad5022e99cd453a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384967"
---
# <a name="create-the-azure-arc-data-controller"></a>Criar o controlador de dados Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Visão geral da criação do controlador de dados Azure Arc

Os serviços de dados habilitados a Azure Arc podem ser criados em vários tipos diferentes de clusters Kubernetes e serviços geridos pela Kubernetes usando múltiplas abordagens diferentes.

Atualmente, a lista suportada de serviços e distribuições de Kubernetes são as seguintes:

- Azure Kubernetes Service (AKS)
- Motor de serviço Azure Kubernetes (motor AKS) em Azure Stack
- Serviço Azure Kubernetes em Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- Plataforma de contentores abertos (OCP)
- Elastic Kubernetes Service (EKS) do AWS
- Motor Google Cloud Kubernetes (GKE)
- Kubernetes open source de origem normalmente implementado com kubeadm

> [!IMPORTANT]
> * A versão mínima suportada de Kubernetes é v1.17. Consulte [questões conhecidas](./release-notes.md#known-issues) para obter informações adicionais. 
> * A versão mínima suportada do OCP é 4.3.
> * Consulte os [requisitos de conectividade](connectivity.md) para entender que conectividade é necessária entre o seu ambiente e a Azure.
> * Consulte a orientação de [configuração](storage-configuration.md) de armazenamento para entender os detalhes de como configurar o seu armazenamento persistente.
> * Se estiver a utilizar o Serviço Azure Kubernetes, o tamanho VM do seu grupo de trabalho deve ser pelo menos **Standard_D8s_v3** e utilizar **discos premium.** O cluster não deve abranger várias zonas de disponibilidade. 
> * Se estiver a utilizar outra distribuição ou serviço kubernetes, deve certificar-se de que tem um tamanho mínimo de nó de 8 GB de RAM e 4 núcleos e uma capacidade total de 32 GB de RAM disponível em todos os seus nós Kubernetes. Por exemplo, pode ter 1 nó a 32 GB de RAM e 4 núcleos ou pode ter 2 nós com RAM de 16GB e 4 núcleos cada.

> [!NOTE]
> Se estiver a utilizar a Plataforma de Recipientes OpenShift do Chapéu Vermelho no Azure, é aconselhável utilizar a versão mais recente disponível.

Dependendo da opção escolhida, serão _necessárias_ determinadas ferramentas , mas é aconselhável [instalar todas as ferramentas do cliente](./install-client-tools.md) antes de começar a criar o controlador de dados Azure Arc.

Independentemente da opção que escolha, durante o processo de criação terá de fornecer as seguintes informações:

- **Nome do controlador** de dados - Um nome descritivo para o seu controlador de dados - por exemplo, "Controlador de dados de produção", "Controlador de dados de Seattle".
- **Nome de utilizador do controlador de dados** - Qualquer nome de utilizador para o utilizador do administrador do controlador de dados.
- **Password do controlador de dados** - Uma palavra-passe para o utilizador do administrador do controlador de dados.
- **Nome do seu espaço de nomes Kubernetes** - o nome do espaço de nomes Kubernetes em que pretende criar o controlador de dados.
- **Modo conectividade** - O modo de conectividade determina o grau de conectividade do seu Azure Arc ativado ambiente de serviços de dados para Azure. Atualmente, a pré-visualização apenas suporta modos indiretamente ligados e diretamente ligados.  Para obter informações, consulte [o modo de conectividade](./connectivity.md). 
- **ID de assinatura Azure** - A assinatura Azure GUID para onde pretende que o recurso de controlador de dados em Azure seja criado.
- Nome do **grupo de recursos Azure** - O nome do grupo de recursos onde pretende que o recurso de controlador de dados em Azure seja criado.
- **Localização azul** - A localização Azure onde os metadados de recursos do controlador de dados serão armazenados em Azure. Para obter uma lista das regiões disponíveis, consulte [a infraestrutura global Azure /Produtos por região.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

## <a name="next-steps"></a>Passos seguintes

Existem múltiplas opções para a criação do controlador de dados Azure Arc:

> **Só quer experimentar as coisas?**  
> Desempenhe-se rapidamente com [o Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM!
> 
- [Criar um controlador de dados com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Criar um controlador de dados com o Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Crie um controlador de dados a partir do portal Azure através de um caderno Jupyter no Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Criar um controlador de dados com ferramentas Kubernetes como kubectl ou oc](create-data-controller-using-kubernetes-native-tools.md)
- [Crie um controlador de dados com o Azure Arc Jumpstart para uma experiência acelerada de uma implementação de teste](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
