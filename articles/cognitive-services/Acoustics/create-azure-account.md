---
title: Configuração da conta do projeto Acoustics o Azure Batch
titlesuffix: Azure Cognitive Services
description: Nesta explicação de procedimento descreve como configurar uma conta do Azure Batch para utilização com o projeto Acoustics Unity e Unreal integrações de motor.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: d3b761630124ef7f72269fe0712bf22647968d59
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137033"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuração da conta do projeto Acoustics o Azure Batch
Nesta explicação de procedimento descreve como configurar uma conta do Azure Batch para utilização com o projeto Acoustics Unity e Unreal integrações de motor.

## <a name="get-an-azure-subscription"></a>Obter uma subscrição do Azure
Uma [subscrição do Azure](https://azure.microsoft.com/free/) é necessária antes de configurar contas do Batch e armazenamento. Se estiver a iniciar cópia de segurança pela primeira vez, o Azure fornece alguns recursos gratuitos de tempo limitado e crédito de $200.

## <a name="create-azure-batch-and-storage-accounts"></a>Criar contas do Azure Batch e armazenamento
Em seguida, siga [estas instruções](https://docs.microsoft.com/azure/batch/batch-account-create-portal) para configurar o Azure Batch e associados a contas de armazenamento do Azure.

Escolha as opções predefinidas para contas do Batch e armazenamento:
  
  ![Nova conta do Batch](media/new-batch-account-create.png)

  ![Nova conta de armazenamento](media/batch-storage-account-create.png)

Demora alguns minutos para o Azure implementar as contas. Procure uma notificação de conclusão, no canto superior direito no portal.
  
  ![Contas implementadas](media/batch-accounts-deploy-notification.png)

As suas contas devem agora estar visíveis no seu dashboard.
  
  ![Dashboard do portal](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurar acoustics criar da interface do Usuário com credenciais do Azure
Clique na ligação de conta do Batch no dashboard, em seguida, clique nas **chaves** link na página de conta de Batch para acessar as suas credenciais.
  
  ![Ligação de chaves do batch](media/batch-access-keys.png)

  ![Credenciais de conta do batch](media/batch-keys-info.png)

Clique nas **conta de armazenamento** link na página para acessar as credenciais da conta de armazenamento do Azure.
  
  ![Credenciais da Conta de Armazenamento](media/storage-keys-info.png)

Introduza estas credenciais no [Plug-in de criar do Unity](unity-baking.md) ou [Plug-in de criar Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipos de nós e suporte de região
Projeto Acoustics requer que fsv2 e H-computação série a com otimização de nós de VM do Azure que podem não ser suportados em todas as regiões do Azure. Verifique se existem [esta tabela](https://azure.microsoft.com/global-infrastructure/services) para garantir que está a escolher a localização certa para a sua conta do Batch.
![Máquinas virtuais do Azure por região](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Atualizar a sua quota
Contas de Batch do Azure aprovisionadas na conta de criação com um limite de 20 núcleos de computação. Podemos aumentar este limite de tempo de criar mais rápido, uma vez que consegue paralelizar tão sua carga de trabalho acoustics por muitos nós, até ao número de pontos de sonda em seu cenário. Pode pedir um aumento de quota ao clicar no **Quota** ligar na sua página de portal do Azure Batch e, em seguida, clicar em **aumentar a Quota de pedido**:

![Aumento de Quota do Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Passos Seguintes
* Integrar o plug-in do projeto Acoustics no seu [Unity](unity-integration.md) ou [Unreal](unreal-integration.md) projeto

