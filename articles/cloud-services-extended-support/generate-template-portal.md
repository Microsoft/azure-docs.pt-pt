---
title: Gerar modelo ARM para serviços na nuvem (suporte alargado) usando o portal Azure
description: Gerar e descarregar modelo ARM e ficheiro de parâmetros para serviços em nuvem (suporte alargado) usando o portal Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 215abb1ce8d65b5ecdd25aeb78c17c70e801a9d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555631"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Gerar modelo ARM para serviços na nuvem (suporte alargado) usando o portal Azure

Este artigo explica como obter o modelo ARM e o ficheiro de parâmetros do [portal Azure](https://portal.azure.com) após a implementação do serviço na nuvem (suporte alargado). O modelo ARM e o ficheiro de parâmetros podem ser utilizados em futuras implementações para atualizar ou atualizar um serviço de nuvem (suporte alargado)

## <a name="get-arm-template-via-portal"></a>Obtenha o modelo ARM via portal

  1. Vá ao seu grupo de recursos e selecione implementações.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="A imagem mostra a seleção de implementações em grupo de recursos no portal Azure.":::
  
  2. Selecione o seu serviço na nuvem (suporte alargado) e clique no modelo.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="A imagem mostra o modelo de seleção sob o serviço de nuvem (suporte alargado) no portal Azure.":::
  
  3. Descarregue o seu modelo e ficheiros de parâmetros. Estes podem ser usados para futuras implementações via PowerShell.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="A imagem mostra o ficheiro de modelo de descarregamento no portal Azure.":::
  
## <a name="next-steps"></a>Passos seguintes 
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md)
  
