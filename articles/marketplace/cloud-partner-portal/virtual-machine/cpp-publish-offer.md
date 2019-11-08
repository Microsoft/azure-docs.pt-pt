---
title: Publicar uma oferta de máquina virtual no Azure Marketplace
description: Lista as etapas necessárias para publicar uma oferta de máquina virtual existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 1b07f3f3edab47f8f75835dffd4cc3f89f17ab63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824415"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicar uma oferta de máquina virtual

 A última etapa, depois de definir a oferta no portal e criar os ativos técnicos associados, é enviar a oferta para publicação. O diagrama a seguir ilustra as principais etapas do processo de publicação para "entrar ao vivo":

![Etapas de publicação para a oferta de máquina virtual](./media/publishvm_013.png)

A tabela a seguir descreve essas etapas e fornece uma estimativa de tempo máxima para sua conclusão:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etapa de publicação**           | **Momento**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar pré-requisitos         | 15 min   | As informações da oferta e as configurações da oferta são validadas.                        |
| Validação da unidade de teste (opcional) | 2 horas | Se você tiver optado por habilitar o Test Drive, a Microsoft validará a configuração da unidade de teste, sua implantação e replicação por meio das regiões selecionadas. |
| Certificação                  | 3 dias | A oferta é analisada pela equipe de certificação do Azure. Esta etapa executará verificações de vírus, malware, conformidade de segurança e problemas de segurança. Os comentários serão fornecidos se um problema for encontrado. |
| Aprovisionamento                   | 4 dias   | A oferta de VM é replicada em sistemas de produção do Marketplace.               |
| Empacotamento e registro de geração de cliente potencial | \< de 1 hora  | Os ativos técnicos da oferta são empacotados para uso do cliente e os sistemas de Lead são configurados e definidos. |
|  Aprovação do Publicador             |  -        | Revisão final do Publicador e confirmação antes que a oferta fique ativa. Você pode implantar sua oferta nas assinaturas selecionadas (nas etapas de informações da oferta) para verificar se ela atende a todos os seus requisitos.  |
| Aprovisionamento                   | 4 dias | A oferta de VM finalizada é replicada em regiões e sistemas de produção do Marketplace. | 
| Em direto                           | 4 dias | A oferta de VM é liberada, replicada para as regiões necessárias e disponibilizada para o público. |
|  |  |

Aguarde até 16 dias para que esse processo seja concluído.  Depois de percorrer essas etapas de publicação, sua oferta de VM será listada na [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

