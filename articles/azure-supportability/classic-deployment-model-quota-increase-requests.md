---
title: Modelo de implementação clássica do Azure | Documentos da Microsoft
description: Modelo de implementação clássica do Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5ae2a56c084116ae8d57a16696223e7990258558
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313298"
---
# <a name="classic-deployment-model"></a>Modelo de implementação clássica

Modelo de implementação clássica é a geração mais antiga ' s land de modo de implementação do Azure impõe um limite de quota de global vCPU para máquinas virtuais e conjuntos de dimensionamento de máquina virtual. Modelo de implementação clássica não é mais recomendado e agora foi substituído pelo modelo do Resource Manager. Para saber mais sobre estes dois implementação partido do Gestor de recursos e modelos de consulte a página do modelo de implementação do Resource Manager. Quando é criada uma nova subscrição, uma quota predefinida de vCPUs está atribuída à mesma. Sempre que uma nova VM a ser implantado usando o modelo de implementação clássica, a soma da utilização de vCPUs novos e existentes em todas as regiões não pode exceder a quota de vCPU aprovada para o modelo de implementação clássica. Saiba mais sobre as quotas na página de limites de serviço e subscrição do Azure.

Pode pedir um aumento no limite de vCPUs para o modelo de implementação clássica por meio de ajuda + painel de suporte ou o utilizações + Quota painel no portal.

