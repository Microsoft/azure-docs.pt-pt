---
title: Conceitos - Atualizações e atualizações em nuvem privadas
description: Conheça os principais processos e funcionalidades de upgrade na Azure VMware Solution.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 380e97eae559145a9ef5ed7b6e7bf14f18039eed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316805"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Atualizações e atualizações privadas de nuvem Azure VMware

## <a name="overview"></a>Descrição geral

Um dos principais benefícios das nuvens privadas Azure VMware Solution é que a plataforma é mantida para si. A manutenção da plataforma inclui atualizações automatizadas para um pacote de software validado VMware, ajudando a garantir que está a utilizar a versão mais recente do software de nuvem privada Azure VMware Solution validado.

Especificamente, uma nuvem privada Azure VMware Solution inclui:

- Nódoas de servidor de metal nu dedicada aprovisionadas com hipervisor VMware ESXi 
- vCenter servidor para gerir ESXi e vSAN 
- Software VMware NSX-T definido em rede para vSphere carga de trabalho VMs  
- Loja de dados VMware vSAN para vSphere carga de trabalho VMs  
- VMware HCX para mobilidade de carga de trabalho  

Além destes componentes, uma nuvem privada Azure VMware Solution inclui recursos na base Azure necessária para a conectividade e para operar a nuvem privada. A Azure VMware Solution monitoriza continuamente a saúde tanto da base como dos componentes VMware. Quando a Azure VMware Solution deteta uma falha, toma medidas para reparar os componentes falhados. 

## <a name="what-components-get-updated"></a>Que componentes são atualizados?   

A Azure VMware Solution atualiza os seguintes componentes VMware: 

- vCenter Server e ESXi em execução nos nos acenos do servidor de metal nu 
- vSAN 
- NSX-T 

A Azure VMware Solution também atualiza o software na base, como controladores, software nos interruptores de rede e firmware nos nós de metal nu. 

## <a name="types-of-updates"></a>Tipos de atualizações

A Azure VMware Solution aplica os seguintes tipos de atualizações aos componentes VMware:

- Patches: Patches de segurança e correções de erros lançados pela VMware. 
- Atualizações: Atualizações de versão menores de um ou mais componentes VMware. 
- Upgrades: Principais atualizações de versão de um ou mais componentes VMware.

Será notificado antes e depois de os patches serem aplicados às suas nuvens privadas. Também trabalharemos consigo para agendar uma janela de manutenção antes de aplicar atualizações ou atualizações na sua nuvem privada. 

## <a name="vmware-appliance-backup"></a>Backup do aparelho VMware 

Além de fazer atualizações, a Azure VMware Solution requer uma cópia de segurança de configuração destes componentes VMware:

- vCenter Server 
- Gerente da NSX-T 

Em momentos de falha, a Azure VMware Solution pode restaurá-las a partir da cópia de segurança de configuração. 

Para obter mais informações sobre as versões de software VMware, consulte o [artigo de conceito de nuvens e clusters privados](concepts-private-clouds-clusters.md) e o [FAQ](faq.md).

## <a name="next-steps"></a>Passos seguintes

O próximo passo é [criar uma nuvem privada.](tutorial-create-private-cloud.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
