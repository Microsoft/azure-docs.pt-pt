---
title: Azure VMware Solutions (AVS) - Manutenção e atualizações da AVS
description: Descreve o processo de serviço AVS para manutenção e atualizações programadas
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025032"
---
# <a name="avs-maintenance-and-updates"></a>Manutenção e atualizações avs

O ambiente AVS Private Cloud foi concebido para não ter um único ponto de falha.

* Os clusters ESXi são configurados com HA (alta disponibilidade) de vSphere. Os clusters são dimensionados para ter pelo menos um nó sobressalente para resiliência.
* O armazenamento primário redundante é fornecido pelo vSAN, que requer pelo menos três nós para fornecer proteção contra uma única falha. a vSAN pode ser configurada para fornecer maior resiliência para clusters maiores.
* as VMs do vCenter, PSC e NSX Manager são configuradas com o armazenamento RAID-10 para proteger contra falhas de armazenamento. As VMs são protegidas contra falhas de nó/rede por vSphere HA.
* Os hosts ESXi têm ventiladores e NICs redundantes.
* Os comutadores TOR e lombada são configurados em pares de alta disponibilidade para fornecer resiliência.

A AVS monitoriza continuamente os seguintes VMs para tempo de uptime e disponibilidade, e fornece disponibilidade de SLAs:

* Hosts ESXi
* vCenter
* PSC
* NSX Manager

A AVS também monitoriza continuamente o seguinte para falhas:

* Discos rígidos
* Portas NIC físicas
* Servidores
* Fãs
* Avançado
* Interruptores
* Portas do comutador

Se um disco ou nó falhar, um novo nó será adicionado automaticamente ao cluster do VMware afetado para trazê-lo de volta à integridade imediatamente.

A AVS confirma, mantém e atualiza estes elementos VMware nas Nuvens Privadas AVS:

* ESXi
* Serviços da plataforma vCenter
* Controlador
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Cópia de segurança e restauro

A cópia de segurança AVS inclui:

* Backups incrementais noturnos de regras do vCenter, PSC e DVS.
* APIs nativas do vCenter para fazer backup de componentes na camada de aplicativo.
* Backup automático antes da atualização ou atualização do software de gerenciamento do VMware.
* a criptografia de dados do vCenter na origem antes que os dados sejam transferidos por um canal criptografado do TLS 1.2 para o Azure. Os dados são armazenados em um blob do Azure onde são replicados entre regiões.

Você pode solicitar uma restauração abrindo um [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenção

A AVS faz vários tipos de manutenção planeada.

### <a name="backendinternal-maintenance"></a>Back-end/manutenção interna

Essa manutenção normalmente envolve a reconfiguração de ativos físicos ou a instalação de patches de software. Ele não afeta o consumo normal dos ativos que estão sendo atendidos. Com NICs redundantes a ir para cada rack físico, o tráfego normal de rede e as operações da AVS Private Cloud não são afetadas. Você poderá notar um impacto no desempenho somente se sua organização espera usar a largura de banda totalmente redundante durante o intervalo de manutenção.

### <a name="avs-portal-maintenance"></a>Manutenção do portal AVS

É necessário um tempo de paragem limitado do serviço quando o plano ou infraestrutura de controlo AVS for atualizado. Atualmente, os intervalos de manutenção podem ser tão frequentes quanto uma vez por mês. Espera-se que a frequência seja recusada ao longo do tempo. A AVS fornece notificação para manutenção do portal e mantém o intervalo o mais curto possível. Durante um intervalo de manutenção do portal, os seguintes serviços continuam a funcionar sem nenhum impacto:

* Plano e aplicativos de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura do VMware

Ocasionalmente, é necessário fazer alterações na configuração da infra-estrutura do VMware. Atualmente, esses intervalos podem ocorrer a cada 1-2 meses, mas a frequência é esperada para recusar ao longo do tempo. Este tipo de manutenção pode geralmente ser feito sem interromper o consumo normal dos serviços AVS. Durante um intervalo de manutenção do VMware, os seguintes serviços continuam a funcionar sem nenhum impacto:

* Plano e aplicativos de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e upgrades

A AVS é responsável pela gestão do software VMware (ESXi, vCenter, PSC e NSX) na Nuvem Privada AVS.

As atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de bugs liberadas pelo VMware.
* **Atualizações**. Alteração de versão secundária de um componente de pilha do VMware.
* **Atualizações**. Alteração de versão principal de um componente do VMware Stack.

A AVS testa um patch de segurança crítico assim que fica disponível a partir de VMware. Por SLA, a AVS lança o patch de segurança para ambientes AVS Private Cloud dentro de uma semana.

O AVS fornece atualizações trimestrais de manutenção aos componentes do software VMware. Quando uma nova versão principal do software VMware está disponível, a AVS trabalha com os clientes para coordenar uma janela de manutenção adequada para upgrade.

## <a name="next-steps"></a>Passos seguintes

[Fazer backup de VMs de carga de trabalho usando Veeam](backup-workloads-veeam.md)
