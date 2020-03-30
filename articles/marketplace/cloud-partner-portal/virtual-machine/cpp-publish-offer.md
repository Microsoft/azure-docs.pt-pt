---
title: Publique uma oferta de máquina virtual no Azure Marketplace
description: Lista as etapas necessárias para publicar uma oferta de máquina virtual existente no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: c35f721131b997dcfdb0f23a91a39329168b757c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277349"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicar uma oferta de máquina virtual

 O último passo, depois de ter definido a oferta no portal e criado os ativos técnicos associados, é submeter a oferta para publicação. O diagrama seguinte retrata os principais passos do processo de publicação para "ir ao vivo":

![Etapas de publicação para oferta de máquinavirtual](./media/publishvm_013.png)

O quadro seguinte descreve estes passos e fornece uma estimativa máxima de tempo para a sua conclusão:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Passo editorial**           | **Tempo**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar pré-requisitos         | 15 min   | As informações de oferta e as configurações de oferta são validadas.                        |
| Validação do test drive (opcional) | Duas horas | Se tiver selecionado para ativar o Test Drive, a Microsoft valida a configuração test drive, a sua implementação e replicação através das regiões selecionadas. |
| Certificação                  | 3 dias | A oferta é analisada pela Equipa de Certificação Azure. Este passo irá realizar verificações para vírus, malware, conformidade de segurança e problemas de segurança. O feedback é fornecido se um problema for encontrado. |
| Aprovisionamento                   | 4 dias   | A oferta vm é replicada em sistemas de produção de mercado.               |
| Registo de embalagens e gerações de chumbo | \<1 hora  | Os ativos técnicos da Oferta são embalados para uso do cliente e os sistemas de chumbo são configurados e configurados. |
|  Assinatura de editor             |  -        | Revisão final do editor e confirmação antes da oferta ir ao vivo. Pode implementar a sua oferta nas subscrições selecionadas (nas etapas de informação de oferta) para verificar se cumpre todos os seus requisitos.  |
| Aprovisionamento                   | 4 dias | A oferta de VM finalizada é replicada em sistemas e regiões de produção de mercado. | 
| Em direto                           | 4 dias | A oferta vm é lançada, replicada para as regiões necessárias, e disponibilizada ao público. |
|  |  |

Deixe-o até 16 dias para que este processo esteja concluído.  Depois de passar por estes passos de publicação, a sua oferta VM será listada no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

