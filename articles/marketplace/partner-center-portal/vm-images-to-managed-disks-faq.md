---
title: Estamos movendo imagens de máquina virtual (VM) para o armazenamento em disco gerenciado no Azure Marketplace
description: Para fornecer armazenamento mais rápido e confiável e suporte para novos recursos e funcionalidades do Marketplace, estamos movendo as imagens de VM do Marketplace para o armazenamento em disco gerenciado.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5eeb223601a3990ef899d7eea96378a8b2a28eee
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933536"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Estamos movendo as imagens da VM (máquina virtual) no Azure Marketplace para o armazenamento em disco gerenciado

Para fornecer armazenamento mais rápido e confiável e suporte para novos recursos e funcionalidades do Marketplace, estamos movendo as imagens de VM do Marketplace para o armazenamento em disco gerenciado.

A partir de 2 de janeiro de 2020, moveremos as imagens da VM para o armazenamento em disco gerenciado em fases. Na primeira fase, moveremos apenas imagens sem novas implantações ou máquinas virtuais em execução nos últimos 90 dias. Antes de movermos qualquer uma das imagens, enviaremos um email para permitir que o Publicador saiba quais imagens serão movidas e quando elas serão transferidas.

Os editores ou consumidores não precisam realizar nenhuma ação e os usuários não serão afetados. As ofertas do Marketplace permanecerão disponíveis e os clientes ainda poderão implantar VMs gerenciadas dessas imagens, durante e após a movimentação.

Se você tiver alguma dúvida, [entre em contato conosco](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>FAQ

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Os usuários de minhas imagens de VM sofreram uma interrupção?

Os usuários das imagens de VM não sofrerão uma interrupção. 

Na primeira fase, moveremos apenas as imagens de VM que não têm VMs em execução. Como não há nenhum usuário para essas imagens, não haverá nenhum impacto. Para as fases subsequentes também, não haverá nenhum impacto sobre os usuários.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Quanto tempo leva para que o processo seja concluído?

Pode levar até 24 horas para concluir a migração.

### <a name="do-i-need-to-take-any-action"></a>Preciso realizar qualquer ação?

Não. Os editores ou consumidores não precisam realizar nenhuma ação.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>É necessário atualizar meu sistema para chamar as APIs do portal de nuvem de uma maneira diferente depois que elas são movidas para o armazenamento de disco gerenciado?

Não. Suas chamadas à API existentes continuarão a funcionar.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Todas as imagens de minha VM seriam movidas para o disco gerenciado ao mesmo tempo?

Iremos mover todas as suas imagens de VM no mesmo dia. Você será notificado quando eles forem movidos.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Posso solicitar para agendar a movimentação de minhas imagens de VM para um momento posterior?

Recomendamos mover as imagens na data agendada. No entanto, se você tiver preocupações, entre em contato conosco para reagendar a movimentação.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Posso publicar atualizações em minhas imagens de VM durante a movimentação?

As atualizações para as imagens de VM não podem ser feitas durante a movimentação.

## <a name="next-steps"></a>Passos seguintes

Visite a página de [Guia do Publicador de oferta de máquina virtual](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) .
