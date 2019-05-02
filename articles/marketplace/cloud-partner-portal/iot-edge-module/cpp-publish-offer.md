---
title: Publicar oferta de módulo do Azure IoT Edge | O Azure Marketplace
description: Como publicar uma oferta de módulo do IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: c853bd3bad9f02f6824c26fb5d18e9e59d921fe8
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942053"
---
# <a name="publish-iot-edge-module-offer"></a>Publicar uma oferta de módulo do IoT Edge

 Depois de criar uma nova oferta, fornecendo as informações sobre o **nova oferta** página, pode publicar a oferta. Selecione **publicar** para iniciar o processo de publicação.

O diagrama seguinte mostra os passos principais no processo de publicação de uma oferta para a "ativação online".

![Oferecem de etapas de publicação para o módulo do IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descrição detalhada dos passos de publicação

A tabela seguinte descreve cada etapa de publicação, com uma estimativa de tempo (máxima) para cada passo.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etapa de publicação**           | **tempo**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar a pré-requisitos         | 15 min   | Oferecer informações e oferecer definições são validadas.                        |
| Certificação                  | 2 semanas | Oferta é analisada pela equipe de certificação do Azure. Este passo irá realizar verificações de vírus, malware, conformidade de segurança e problemas de segurança. Também irá verificar que esta oferta de módulo do IoT Edge atende a todos os critérios de elegibilidade (consulte [pré-requisitos](./cpp-prerequisites.md) e [preparar seus ativos técnicos](./cpp-create-technical-assets.md)). Comentários é fornecido se não for encontrado um problema. |
| Empacotamento | 1 hora  | Ativos de técnicos da oferta são empacotados para uso do cliente e os sistemas de oportunidades potenciais estão configurados e de configuração. |
|  Início de sessão do publicador desativado             |  -        | Revisão do publicador final e a confirmação antes da oferta entra no ar. Pode implementar a sua oferta nas subscrições selecionadas (nos passos de informações oferta) para verificar que cumpre todos os seus requisitos.  Selecione **Go Live** para que a sua oferta pode mover para o passo seguinte. |
| Empacotamento                 | 1 hora | Oferta finalizada é replicada nas regiões e sistemas de produção do marketplace. | 
| Em direto                           | 4 dias |Oferta é lançada, replicada para as regiões necessárias e disponibilizada ao público. |

Permitir por até 10 dias úteis para concluir o processo de publicação e a oferta é liberada. Depois de concluir o processo de publicação, a oferta de módulo do IoT Edge será listada na [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Passos Seguintes

- [Atualizar uma oferta de módulo do IoT Edge existente no Azure Marketplace](./cpp-update-existing-offer.md)
