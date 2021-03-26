---
title: Atualizações da plataforma para Azure VMware Solution
description: Conheça as atualizações da plataforma para Azure VMware Solution.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045217"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Atualizações da plataforma para Azure VMware Solution

A Azure VMware Solution aplicará atualizações importantes a partir de março de 2021. Receberá uma notificação através do Serviço Azure Health que inclui a cronologia da manutenção. Para obter mais informações, consulte [atualizações e atualizações privadas da Azure VMware Solution](concepts-upgrades.md).

## <a name="march-24-2021"></a>24 de março de 2021
Todas as novas nuvens privadas Azure VMware Solution são implementadas com vMware vCenter versão 6.7U3l e versão NSX-T 3.1.1. Quaisquer nuvens privadas existentes serão atualizadas e atualizadas **até junho de 2021** para os lançamentos acima mencionados.

Receberá um e-mail com a data e hora de manutenção planeadas. Pode remarcar uma atualização. O e-mail também fornece detalhes sobre a componente atualizada, o seu efeito sobre cargas de trabalho, acesso privado à nuvem e outros serviços Azure.  Uma hora antes da atualização, receberá uma notificação e, novamente, quando terminar.

## <a name="march-15-2021"></a>15 de março de 2021 

- O serviço Azure VMware Solution irá fazer trabalhos de manutenção **até 19 de março de 2021,** para atualizar o servidor vCenter na sua nuvem privada para a versão vCenter Server 6.7 Update 3l.

- O VMware vCenter estará indisponível durante este tempo.  Assim, não será capaz de gerir os seus VMs (parar, iniciar, criar, eliminar) ou escalar a nuvem privada (adicionar/remover servidores e clusters). No entanto, a VMware High Availability (HA) continuará a operar para proteger os VM existentes. 
 
Para obter mais informações sobre esta versão vCenter, consulte [VMware vCenter Server 6.7 Atualizar 3l Notas de desbloqueio](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 de março de 2021

- A Azure VMware Solution aplicará o [VMware ESXi 6.7, Patch Release ESXi670-20201002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) aos privados existentes **até 15 de março de 2021**.

- As soluções documentadas para a pilha vSphere, de acordo com [vMSA-2021-0002,](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)também serão aplicadas **até 15 de março de 2021**.

>[!NOTE]
>Isto não é disruptivo e não deve afetar os Serviços VMware Azure ou cargas de trabalho. Durante a manutenção, vários alertas VMware, como _a conectividade da rede Lost em DVPorts_ e _redundância de uplink perdida em DVPorts,_ aparecem no vCenter e são claros automaticamente à medida que a manutenção progride.

## <a name="post-update"></a>Pós-atualização
Uma vez concluídas, as versões mais recentes dos componentes VMware aparecem. Se notar algum problema ou tiver alguma dúvida, contacte a nossa equipa de apoio abrindo um bilhete de apoio.