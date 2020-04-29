---
title: Manutenção e atualizações CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve o processo de serviço CloudSimple para manutenção e atualizações programadas
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025032"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Manutenção e atualizações CloudSimple

O ambiente Private Cloud foi concebido para não ter um único ponto de falha.

* Os clusters ESXi são configurados com vSphere High Availability (HA). Os aglomerados são dimensionados para ter pelo menos um nó de reserva para resiliência.
* O armazenamento primário redundante é fornecido pela vSAN, que requer pelo menos três nós para fornecer proteção contra uma única falha. vSAN pode ser configurado para fornecer maior resiliência para clusters maiores.
* vCenter, PSC e NSX Manager VMs estão configurados com armazenamento RAID-10 para proteger contra falhas de armazenamento. Os VMs estão protegidos contra falhas de nó/rede por vSphere HA.
* Os anfitriões da ESXi têm fãs redundantes e NICs.
* Os interruptores DE TOR e coluna vertebral estão configurados em pares HA para fornecer resiliência.

A CloudSimple monitoriza continuamente os seguintes VMs para tempo de uptime e disponibilidade, e fornece disponibilidade de SLAs:

* Anfitriões esXi
* vCenter
* PSC
* Gestor NSX

A CloudSimple também monitoriza continuamente o seguinte para falhas:

* Discos rígidos
* Portas de NIC física
* Servidores
* Ventoinhas
* Power
* Comutadores
* Mudar portas

Se um disco ou nó falhar, um novo nó é automaticamente adicionado ao cluster VMware afetado para trazê-lo de volta à saúde imediatamente.

A CloudSimple recua, mantém e atualiza estes elementos VMware nas Nuvens Privadas:

* ESXi
* vCenter Serviços de Plataforma
* Controlador
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Criar cópias de segurança e restauro

O backup CloudSimple inclui:

* Backups incrementais noturnos das regras vCenter, PSC e DVS.
* vCenter apis nativo para fazer o back-up componentes na camada de aplicação.
* Backup automático antes da atualização ou atualização do software de gestão VMware.
* encriptação de dados vCenter na fonte antes de os dados serem transferidos para o Azure por um canal encriptado TLS1.2. Os dados são armazenados numa bolha Azure onde são replicados em regiões.

Pode solicitar um restauro abrindo um pedido de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="maintenance"></a>Manutenção

A CloudSimple faz vários tipos de manutenção planeada.

### <a name="backendinternal-maintenance"></a>Backend/manutenção interna

Esta manutenção envolve normalmente a reconfiguração de ativos físicos ou a instalação de patches de software. Não afeta o consumo normal dos ativos que estão a ser servidos. Com OS NICs redundantes a irem para cada rack físico, o tráfego normal de rede e as operações da Private Cloud não são afetadas. Só poderá notar um impacto de desempenho se a sua organização espera utilizar toda a largura de banda redundante durante o intervalo de manutenção.

### <a name="cloudsimple-portal-maintenance"></a>Manutenção do portal CloudSimple

É necessário um tempo de paragem de serviço limitado quando o plano ou infraestrutura de controlo CloudSimple for atualizado. Atualmente, os intervalos de manutenção podem ser tão frequentes como uma vez por mês. Espera-se que a frequência diminua com o tempo. A CloudSimple fornece notificação para manutenção do portal e mantém o intervalo o mais curto possível. Durante um intervalo de manutenção do portal, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano e aplicações de gestão VMware
* acesso vCenter
* Todo o networking e armazenamento
* Todo o tráfego azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura VMware

Ocasionalmente é necessário fazer alterações na configuração da infraestrutura VMware.  Atualmente, estes intervalos podem ocorrer a cada 1-2 meses, mas espera-se que a frequência diminua ao longo do tempo. Este tipo de manutenção pode geralmente ser feito sem interromper o consumo normal dos serviços CloudSimple. Durante um intervalo de manutenção VMware, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano e aplicações de gestão VMware
* acesso vCenter
* Todo o networking e armazenamento
* Todo o tráfego azure

## <a name="updates-and-upgrades"></a>Atualizações e Atualizações

A CloudSimple é responsável pela gestão do software VMware (ESXi, vCenter, PSC e NSX) na Cloud Privada.

As atualizações de software incluem:

* **Remendos.** Patches de segurança ou correções de bugs lançadas pela VMware.
* **Atualizações**. Alteração de versão menor de um componente de pilha VMware.
* **Upgrades**. Mudança de versão importante de um componente de pilha VMware.

A CloudSimple testa um patch de segurança crítico assim que fica disponível a partir de VMware. Por SLA, a CloudSimple lança o patch de segurança para ambientes Private Cloud dentro de uma semana.

A CloudSimple fornece atualizações trimestrais de manutenção aos componentes do software VMware. Quando uma nova versão principal do software VMware está disponível, a CloudSimple trabalha com os clientes para coordenar uma janela de manutenção adequada para upgrade.

## <a name="next-steps"></a>Passos seguintes

[VMs de carga de trabalho de back up usando Veeam](backup-workloads-veeam.md)
