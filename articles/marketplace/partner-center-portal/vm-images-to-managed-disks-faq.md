---
title: Estamos a mover imagens de máquinavirtual (VM) para gerir o armazenamento de discos no Azure Marketplace
description: Para fornecer armazenamento e suporte mais rápidos e fiáveis para novas funcionalidades e capacidades do mercado, estamos a mover imagens VM do mercado para gerir o armazenamento de discos.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 683b35661a41325cfd5baa877acdb0e37529bb94
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198739"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Estamos a mover imagens de máquinavirtual (VM) no Azure Marketplace para gerir o armazenamento de discos

Para fornecer armazenamento e suporte mais rápidos e fiáveis para novas funcionalidades e capacidades do mercado, estamos a mover imagens VM do mercado para gerir o armazenamento de discos.

A partir de 2 de janeiro de 2020, vamos mover imagens VM para gerir o armazenamento de discos em fases. Na primeira fase, só moveremos imagens sem novas implementações ou executando VMs nos 90 dias anteriores. Antes de movermos qualquer uma das imagens, enviaremos um e-mail para informar a editora quais as imagens que se movem e quando serão movidas.

Editores ou consumidores não precisam de tomar qualquer medida, e os utilizadores não serão afetados. As ofertas do mercado continuarão disponíveis e os clientes ainda poderão implementar VMs geridos a partir destas imagens, durante e após a mudança.

Se tiver alguma dúvida, [contacte-nos.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)

## <a name="faqs"></a>Perguntas mais frequentes

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Os utilizadores das minhas imagens VM sentiriam uma falha?

Os utilizadores das imagens VM não sentirão uma paragem. 

Na primeira fase, só moveremos imagens VM que não têm VMs em execução. Como não há utilizadores para estas imagens, não haverá qualquer impacto. Também para as fases seguintes, não haverá impacto nos utilizadores.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Quanto tempo demora o processo a ser concluído?

Pode levar até 24 horas para completar a migração.

### <a name="do-i-need-to-take-any-action"></a>Preciso de tomar alguma ação?

Não. Editores ou consumidores não precisam de tomar qualquer medida.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Tenho de atualizar o meu sistema para ligar para as APIs do Portal cloud de uma forma diferente depois de serem transferidas para o armazenamento de Discos Geridos?

Não. As chamadas api existentes continuarão a funcionar.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Todas as minhas imagens vm seriam movidas para O Disco Gerido ao mesmo tempo?

Vamos mover todas as suas imagens VM no mesmo dia. Vamos notificá-lo assim que forem movidos.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Posso pedir para agendar a mudança das minhas imagens VM para uma hora posterior?

Recomendamos que se desloque as imagens na data marcada. No entanto, se tiver preocupações, contacte-nos para reagendar a mudança.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Posso publicar atualizações para as minhas Imagens VM durante a mudança?

As atualizações das imagens VM não podem ser feitas durante a mudança.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>O processo de publicação mudará depois da minha Imagem VM ser transferida para O Disco Gerido?

Não, o processo de publicação permanecerá o mesmo. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>A editora pode mover as suas ofertas para O Disco Gerido?

Não, os editores não podem mover as suas ofertas para o Managed Disk. Terão de esperar e as suas imagens serão automaticamente movidas. Enviaremos notificações ao editor antes de fazermos quaisquer alterações.
