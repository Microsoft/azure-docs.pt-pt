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
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309678"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuração da conta do projeto Acoustics o Azure Batch
Nesta explicação de procedimento descreve como configurar uma conta do Azure Batch para utilização com o projeto Acoustics Unity e Unreal integrações de motor.

## <a name="get-an-azure-subscription"></a>Obter uma subscrição do Azure
Uma [subscrição do Azure](https://azure.microsoft.com/free/) é necessária antes de configurar contas do Batch e armazenamento. Se estiver a iniciar cópia de segurança pela primeira vez, o Azure fornece alguns recursos gratuitos de tempo limitado e crédito de $200.

## <a name="create-azure-batch-and-storage-accounts"></a>Criar contas do Azure Batch e armazenamento
Em seguida, siga [estas instruções](https://docs.microsoft.com/azure/batch/batch-account-create-portal) para configurar o Azure Batch e associados a contas de armazenamento do Azure.

Escolha as opções predefinidas para contas do Batch e armazenamento:
  
  ![Captura de ecrã do Azure Batch novas contas de opções que mostra as predefinições](media/new-batch-account-create.png)

  ![As opções que mostra as predefinições de contas de captura de ecrã do armazenamento do Azure novo](media/batch-storage-account-create.png)

Demora alguns minutos para o Azure implementar as contas. Procure uma notificação de conclusão, no canto superior direito no portal.
  
  ![Contas de captura de ecrã do Azure implementadas notificação](media/batch-accounts-deploy-notification.png)

As suas contas devem agora estar visíveis no seu dashboard.
  
  ![Dashboard do portal de captura de ecrã do Azure que mostra uma conta de Batch e armazenamento](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurar acoustics criar da interface do Usuário com credenciais do Azure
Clique na ligação de conta do Batch no dashboard, em seguida, clique nas **chaves** link na página de conta de Batch para acessar as suas credenciais.
  
  ![Conta de captura de ecrã do Azure Batch com ligação para a página de chaves realçada](media/batch-access-keys.png)

  ![Página de chaves de conta de captura de ecrã do Azure Batch com chaves de acesso](media/batch-keys-info.png)

Clique nas **conta de armazenamento** link na página para acessar as credenciais da conta de armazenamento do Azure.
  
  ![Página de chaves de conta de captura de ecrã do armazenamento do Azure com chaves de acesso](media/storage-keys-info.png)

Introduza estas credenciais no [Plug-in de criar do Unity](unity-baking.md) ou [Plug-in de criar Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipos de nós e suporte de região
Projeto Acoustics requer que fsv2 e H-computação série a com otimização de nós de VM do Azure que podem não ser suportados em todas as regiões do Azure. Verifique se existem [esta tabela](https://azure.microsoft.com/global-infrastructure/services) para garantir que está a escolher a localização certa para a sua conta do Batch.
![Captura de ecrã a mostrar as máquinas de virtuais do Azure por região](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Atualizar a sua quota
Contas de Batch do Azure aprovisionadas na conta de criação com um limite de 20 núcleos de computação. Podemos aumentar este limite de tempo de criar mais rápido, uma vez que consegue paralelizar tão sua carga de trabalho acoustics por muitos nós, até ao número de pontos de sonda em seu cenário. Pode pedir um aumento de quota ao clicar no **Quota** ligar na sua página de portal do Azure Batch e, em seguida, clicar em **aumentar a Quota de pedido**:

![Página de captura de ecrã de Quota do Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Passos Seguintes
* Integrar o plug-in do projeto Acoustics no seu [Unity](unity-integration.md) ou [Unreal](unreal-integration.md) projeto

