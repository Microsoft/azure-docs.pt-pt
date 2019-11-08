---
title: Publicar Azure IoT Edge oferta do módulo | Azure Marketplace
description: Como publicar uma oferta de módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813835"
---
# <a name="publish-iot-edge-module-offer"></a>Publicar uma oferta de módulo do IoT Edge

 Depois de criar uma nova oferta fornecendo as informações na nova página de **oferta** , você pode publicar a oferta. Selecione **publicar** para iniciar o processo de publicação.

O diagrama a seguir mostra as etapas principais no processo de publicação para uma oferta para "entrar ao vivo".

![Etapas de publicação para a oferta de módulo IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descrição detalhada das etapas de publicação

A tabela a seguir descreve cada etapa de publicação, com uma estimativa de tempo (máximo) para concluir cada etapa.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etapa de publicação**           | **Momento**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar pré-requisitos         | 15 min   | As informações da oferta e as configurações da oferta são validadas.                        |
| Certificação                  | 2 semanas | A oferta é analisada pela equipe de certificação do Azure. Esta etapa executará verificações de vírus, malware, conformidade de segurança e problemas de segurança. Ele também verificará se essa oferta de módulo IoT Edge atende a todos os critérios de qualificação (Confira os [pré-requisitos](./cpp-prerequisites.md) e [preparando seus ativos técnicos](./cpp-create-technical-assets.md)). Os comentários serão fornecidos se um problema for encontrado. |
| Empacotamento | 1 hora  | Os ativos técnicos da oferta são empacotados para uso do cliente e os sistemas de Lead são configurados e definidos. |
|  Aprovação do Publicador             |  -        | Revisão final do Publicador e confirmação antes que a oferta fique ativa. Você pode implantar sua oferta nas assinaturas selecionadas (nas etapas de informações da oferta) para verificar se ela atende a todos os seus requisitos.  Selecione **Go Live** para que sua oferta possa passar para a próxima etapa. |
| Empacotamento                 | 1 hora | A oferta finalizada é replicada em regiões e sistemas de produção do Marketplace. | 
| Em direto                           | 4 dias |A oferta é liberada, replicada para as regiões necessárias e disponibilizada para o público. |

Aguarde até 10 dias úteis para que o processo de publicação seja concluído e a oferta seja liberada. Depois de concluir o processo de publicação, sua oferta de módulo IoT Edge será listada na [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta de módulo de IoT Edge existente no Azure Marketplace](./cpp-update-existing-offer.md)
