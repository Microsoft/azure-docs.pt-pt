---
title: Azure VMware Solution by CloudSimple Atualização setembro 2020
description: Neste artigo, saiba o que esperar durante esta operação de manutenção e alterações na sua nuvem privada.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89447976"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware Solution by CloudSimple Atualização setembro 2020

Uma importante atualização para o serviço Azure VMware Solution será realizada em setembro. Uma notificação por e-mail, enviada como parte da manutenção, incluirá a cronologia da manutenção. Neste artigo, você aprende sobre o que esperar durante esta operação de manutenção e alterações na sua nuvem privada.

> [!NOTE]
> Esta é uma atualização não disruptiva. Durante a atualização, poderá ver um dos componentes redundantes a descer.

## <a name="vmware-infrastructure-upgrade"></a>Atualização da infraestrutura VMware

A infraestrutura VMware da sua nuvem privada será atualizada para uma versão mais recente. Isto inclui atualizações para os componentes vCenter, ESXi, NSX-T e Hybrid Cloud Extension (HCX) (se implementados) da sua nuvem privada.

Durante a atualização, um novo nó será adicionado à sua nuvem privada antes de um nó ser colocado no modo de manutenção para o funcionamento. Isto garante a capacidade da sua nuvem privada e a disponibilidade da sua nuvem privada é mantida durante o processo de upgrade. Durante a atualização dos componentes VMware, poderá ver alarmes apresentados na sua UI web vCenter. Os alarmes fazem parte das operações de manutenção realizadas pela equipa de operações de serviço.

**Versão componente**

- ESXi 6.7U3
- vCenter 6.7U3
- vSAN 6.7
- Centro de Dados NSX 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Atualizações do Datacenter

Esta atualização inclui atualizações para a infraestrutura do datacenter. Atualizações não disruptivas serão realizadas durante o período de manutenção. Irá notar redundância reduzida durante o processo de atualização. Os alertas podem ser gerados na sua infraestrutura VMware em nuvem privada, nos circuitos ExpressRoute, nas ligações GlobalReach e em quaisquer dispositivos VPN site-to-site relacionados com uma das disponibilidades de links. Isto é normal durante a atualização, uma vez que os componentes serão reiniciados como parte da atualização.

-   Se uma VPN site-to-site for implantada como uma única instância (não-HA), poderá ter de restabelecer a ligação VPN.

-   Se utilizar uma ligação VPN ponto-a-local, terá de restabelecer a ligação VPN.

## <a name="post-update"></a>Pós-atualização

Uma vez concluídas as atualizações, deverá ver versões mais recentes dos componentes VMware. Se notar algum problema ou tiver dúvidas, contacte a nossa equipa de apoio abrindo um [bilhete de apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
