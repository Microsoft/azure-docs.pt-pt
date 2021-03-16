---
title: Atualizações da plataforma para Azure VMware Solution
description: Conheça as atualizações da plataforma para Azure VMware Solution.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 4f4c697f345cca093a83eab2f915aaf9e80ab10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563135"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Atualizações da plataforma para Azure VMware Solution

Atualizações importantes para a Azure VMware Solution serão aplicadas a partir de março de 2021. Receberá a notificação através do Serviço Azure Health que inclui a cronologia da manutenção. Neste artigo, você vai aprender o que esperar durante esta operação de manutenção e alterações na sua nuvem privada.

## <a name="march-15-2021"></a>15 de março de 2021 

- O serviço Azure VMware Solution irá realizar trabalhos de manutenção para atualizar o servidor vCenter na sua nuvem privada para a versão vCenter Server 6.7 Update 3l até 19 de março de 2021.

- Durante este período, o VMware vCenter estará indisponível e não será capaz de gerir VMs (parar, iniciar, criar, excluir). VMware High Availability (HA) continuará a operar para fornecer proteção para os VM existentes. O dimensionamento de nuvem privada (adicionar/remover servidores e clusters) também estará indisponível.
 
Para obter mais informações sobre esta versão vCenter, consulte [VMware vCenter Server 6.7 Atualizar 3l Notas de desbloqueio](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 de março de 2021

- A Azure VMware Solutions aplicará patches ao ESXi em nuvens privadas existentes a [VMware ESXi 6.7, Patch Release ESXi670-20201002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) até 15 de março de 2021.

- As soluções documentadas para a pilha vSphere, de acordo com [vMSA-2021-0002,](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)também serão aplicadas até 15 de março de 2021.

>[!NOTE]
>Isto não é disruptivo e não deve afetar os Serviços VMware Azure ou cargas de trabalho. Durante a manutenção, vários alertas VMware, como _a conectividade da rede Lost em DVPorts_ e _redundância de uplink perdida em DVPorts,_ aparecem no vCenter e são claros automaticamente à medida que a manutenção progride.

## <a name="post-update"></a>Pós-atualização
Uma vez concluídas, as versões mais recentes dos componentes VMware aparecem. Se notar algum problema ou tiver alguma dúvida, contacte a nossa equipa de apoio abrindo um bilhete de apoio.



