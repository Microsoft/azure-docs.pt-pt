---
title: CloudSimple manutenção e atualizações
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve o processo de serviço CloudSimple para manutenção programada e atualizações
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025032"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple manutenção e atualizações

O ambiente Private Cloud foi concebido para não ter um único ponto de falha.

* Os clusters ESXi estão configurados com vSphere High Availability (HA). Os aglomerados são dimensionados para ter pelo menos um nó sobressalente para a resiliência.
* O armazenamento primário redundante é fornecido pela vSAN, que requer pelo menos três nós para fornecer proteção contra uma única falha. vSAN pode ser configurado para fornecer maior resiliência para aglomerados maiores.
* vCenter, PSC e NSX Manager VMs estão configurados com armazenamento RAID-10 para proteger contra falhas de armazenamento. Os VMs estão protegidos contra falhas de nó/rede por vSphere HA.
* Os anfitriões da ESXi têm fãs redundantes e NICs.
* Os interruptores TOR e coluna são configurados em pares HA para fornecer resiliência.

CloudSimple monitoriza continuamente os seguintes VMs para o tempo e disponibilidade, e fornece SLAs de disponibilidade:

* Anfitriões ESXi
* vCenter
* PSC
* Gerente NSX

CloudSimple também monitoriza continuamente o seguinte para falhas:

* Discos rígidos
* Portas físicas do NIC
* Servidores
* Ventoinhas
* Energia
* Comutadores
* Mudar portas

Se um disco ou nó falhar, um novo nó é automaticamente adicionado ao cluster VMware afetado para trazê-lo de volta à saúde imediatamente.

CloudSimple recua, mantém e atualiza estes elementos VMware nas Nuvens Privadas:

* ESXi
* vCenter Platform Services
* Controlador
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Criar cópias de segurança e restauro

A cópia de segurança CloudSimple inclui:

* Backups incrementais noturnos das regras vCenter, PSC e DVS.
* APIs nativos vCenter para fazer backup de componentes na camada de aplicação.
* Cópia de segurança automática antes da atualização ou atualização do software de gestão VMware.
* vCenter encriptação de dados na fonte antes de os dados serem transferidos sobre um canal encriptado TLS1.2 para Azure. Os dados são armazenados numa bolha de Azure onde é replicado em regiões.

Pode solicitar uma restauração abrindo um [pedido de Apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="maintenance"></a>Manutenção

CloudSimple faz vários tipos de manutenção planeada.

### <a name="backendinternal-maintenance"></a>Backend/manutenção interna

Esta manutenção normalmente envolve reconfigurar os ativos físicos ou instalar patches de software. Não afeta o consumo normal dos ativos que estão a ser reparados. Com os NICs redundantes a irem para cada rack físico, o tráfego normal da rede e as operações da Private Cloud não são afetadas. Só poderá notar um impacto de desempenho se a sua organização espera utilizar a largura de banda redundante durante o intervalo de manutenção.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple manutenção do portal

É necessário algum tempo de inatividade de serviço limitado quando o plano ou infraestrutura de controlo CloudSimple for atualizado. Atualmente, os intervalos de manutenção podem ser tão frequentes como uma vez por mês. Espera-se que a frequência diminua ao longo do tempo. O CloudSimple fornece a notificação para manutenção do portal e mantém o intervalo o mais curto possível. Durante um intervalo de manutenção do portal, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano de gestão VMware e aplicações
* vCenter acesso
* Todo o networking e armazenamento
* Todo o tráfego Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura VMware

Ocasionalmente é necessário fazer alterações na configuração da infraestrutura VMware.  Atualmente, estes intervalos podem ocorrer a cada 1-2 meses, mas espera-se que a frequência diminua ao longo do tempo. Este tipo de manutenção pode geralmente ser feito sem interromper o consumo normal dos serviços CloudSimple. Durante um intervalo de manutenção VMware, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano de gestão VMware e aplicações
* vCenter acesso
* Todo o networking e armazenamento
* Todo o tráfego Azure

## <a name="updates-and-upgrades"></a>Atualizações e atualizações

A CloudSimple é responsável pela gestão do ciclo de vida do software VMware (ESXi, vCenter, PSC e NSX) na Nuvem Privada.

As atualizações de software incluem:

* **Remendos**. Patches de segurança ou correções de erros lançados pela VMware.
* **Atualizações**. Alteração de versão menor de um componente de pilha VMware.
* **Atualizações**. Alteração de versão principal de um componente de pilha VMware.

O CloudSimple testa um patch de segurança crítico assim que estiver disponível a partir de VMware. Per SLA, CloudSimple lança o patch de segurança para ambientes Private Cloud dentro de uma semana.

O CloudSimple fornece atualizações trimestrais de manutenção aos componentes de software VMware. Quando uma nova versão principal do software VMware está disponível, a CloudSimple trabalha com os clientes para coordenar uma janela de manutenção adequada para upgrade.

## <a name="next-steps"></a>Passos seguintes

[Back up workload VMs usando Veeam](backup-workloads-veeam.md)
