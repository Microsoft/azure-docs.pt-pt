---
title: Solução de VMware do Azure por CloudSimple - CloudSimple manutenção e atualizações
description: Descreve o processo de serviço CloudSimple para manutenção agendada e atualizações
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160249"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple manutenção e atualizações

O ambiente de nuvem privada foi desenvolvido para não que nenhum ponto único de falha:

* Clusters de ESXi são configurados com elevada disponibilidade do vSphere. Os clusters são dimensionados com, pelo menos, um nó de componentes de reserva para resiliência.
* Com redundância de armazenamento primário é fornecido pelo vSAN, que requer, pelo menos, três nós para fornecer proteção contra falha de um único. vSAN pode ser configurado para fornecer maior resiliência para clusters maiores.
* vCenter, PSC e VMs NSX Manager estão configuradas com a política de armazenamento RAID 10 para proteger contra falhas de armazenamento. As VMs estão protegidas contra falhas de rede/nó por vSphere HA.
* Anfitriões ESXi tem fãs redundantes e NICs.
* Comutadores TOR e spine são configuradas em pares HA para fornecer resiliência.

CloudSimple continuamente monitoriza as VMs seguintes para o tempo de atividade e a disponibilidade e oferece SLAs de disponibilidade:

* Anfitriões ESXi
* vCenter
* PSC
* NSX Manager

CloudSimple também monitora as seguintes opções continuamente para falhas:

* Discos rígidos
* Portas NIC físicas
* Servidores
* Fãs
* Power
* Comutadores
* Portas de comutador

Se um disco ou um nó falhar, é adicionado automaticamente um novo nó ao cluster do VMware afetado para colocá-lo novamente para o estado de funcionamento imediatamente.

CloudSimple cria cópias de segurança, mantém e atualiza esses elementos de VMware nas nuvens privadas:

* ESXi
* Serviços de plataforma do vCenter
* controlador
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Criar cópias de segurança e restauro

Cópia de segurança CloudSimple inclui:

* Cópias de segurança incrementais noturnas do vCenter, PSC e DVS regras.
* Utilização do vCenter APIs nativas para fazer cópias de segurança de componentes na camada da aplicação.
* Cópia de segurança automática antes de qualquer atualização ou a atualização do software de gerenciamento de VMware.
* Encriptação de dados na origem, pelo vCenter, antes de transferência de dados através de um canal criptografado TLS1.2 para o Azure. Os dados são armazenados num blob do Azure, onde são replicado em várias regiões.

Pode pedir um restauro, abrindo uma [pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenção

CloudSimple faz vários tipos de manutenção planeada.

### <a name="backendinternal-maintenance"></a>Manutenção de back-end/interno

Esta manutenção normalmente envolve a reconfiguração de ativos físicos ou instalação de patches de software. Ela não afeta o consumo normal dos ativos em manutenção. Com NICs redundantes vai cada bastidor físico, o tráfego de rede normal e as operações de nuvem privada não são afetadas. Pode observar um impacto no desempenho apenas se a sua organização espera que a utilizar a largura de banda completa redundante durante o intervalo de manutenção.

### <a name="cloudsimple-portal-maintenance"></a>Manutenção de portal CloudSimple

Um período de indisponibilidade do serviço limitado é necessário quando o plano de controlo de CloudSimple ou a infraestrutura é atualizada. Atualmente, os intervalos de manutenção podem ser tão frequentes que uma vez por mês. A frequência é esperada que recusar ao longo do tempo. CloudSimple fornece notificação para a manutenção portal e mantém o intervalo mais curto possíveis. Durante um intervalo de manutenção de portal, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano de gestão do VMware e de aplicações
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenção de infraestrutura do VMware

Ocasionalmente, é necessário fazer alterações na configuração da infraestrutura do VMware.  Atualmente, estes intervalos podem ocorrer a todos os meses de 1 a 2, mas espera-se a frequência para recusar ao longo do tempo. Este tipo de manutenção normalmente pode ser feito sem interromper o consumo dos serviços CloudSimple normal. Durante um intervalo de manutenção do VMware, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano de gestão do VMware e de aplicações
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e melhoramentos

CloudSimple é responsável pelo gerenciamento de ciclo de vida do software de VMware (ESXi, vCenter, PSC e NSX) na nuvem privada.

Atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de erros, lançadas pela VMware.
* **Atualizações**. Alteração de versão secundária de um componente da pilha de VMware.
* **Atualizações**. Alteração de versão principal de um componente da pilha de VMware.

CloudSimple testa um patch de segurança críticas, assim que ele se torna disponível a partir do VMware. Por SLA, CloudSimple lança o patch de segurança para ambientes de nuvem privada numa semana.

CloudSimple fornece atualizações de manutenção trimestral aos componentes de software do VMware. Quando uma nova versão principal do software de VMware estiver disponível, CloudSimple funciona com os clientes para coordenar uma janela de manutenção adequada para a atualização.

## <a name="next-steps"></a>Passos Seguintes

[Cópia de segurança de VMs de carga de trabalho com Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).