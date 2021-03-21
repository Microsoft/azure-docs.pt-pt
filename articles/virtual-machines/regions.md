---
title: Regiões do Azure
description: Conheça as regiões para executar máquinas virtuais em Azure.
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthnn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.openlocfilehash: e874a76cc447158127da8482f23bf03d56961818
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504614"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Regiões das máquinas virtuais no Azure

É importante compreender como e onde as suas máquinas virtuais (VMs) funcionam no Azure, juntamente com as opções para maximizar o desempenho, a disponibilidade e a redundância. Este artigo fornece-lhe uma descrição geral das funcionalidades de disponibilidade e redundância do Azure.


## <a name="what-are-azure-regions"></a>O que são as regiões do Azure?
O Azure funciona em vários datacenters em todo o mundo. Estes datacenters são agrupados por regiões geográficas, dando-lhe a flexibilidade de escolher onde pretende criar as suas aplicações. 

Cria-se recursos Azure em regiões geográficas definidas como "EUA Ocidentais", "Europa do Norte" ou "Sudeste Asiático". Pode rever a [lista de regiões e as respetivas localizações](https://azure.microsoft.com/regions/). Dentro de cada região, existem vários datacenters para fornecer redundância e disponibilidade. Esta abordagem dá-lhe flexibilidade à medida que concebe aplicações para criar VMs mais próximos dos seus utilizadores e para atender a quaisquer fins legais, de conformidade ou fiscais.

## <a name="special-azure-regions"></a>Regiões do Azure especiais
O Azure tem algumas regiões especiais que poderá pretender utilizar ao construir os seus pedidos para efeitos legais ou de conformidade. Estas regiões especiais incluem:

* **US Gov - Virginia** e **US Gov - Iowa**
  * Uma instância isolada da rede física e lógica do Azure para agências e parceiros do governo dos Estados Unidos da América, operada por pessoas selecionadas dos EUA. Inclui certificações de conformidades adicionais, como [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Leia mais sobre o [Azure Government](https://azure.microsoft.com/features/gov/).
* **Leste da China** e **Norte da China**
  * Estas regiões estão disponíveis através de uma parceria exclusiva entre a Microsoft e a 21Vianet, nos termos da qual a Microsoft não controla diretamente os datacenters. Veja mais sobre [a Azure China 21Vianet](https://www.windowsazure.cn/).
* **Alemanha Central** e **Nordeste da Alemanha**
  * Estas regiões estão disponíveis através de um modelo de fiduciário de dados, através do qual os dados dos clientes permanecem na Alemanha sob controlo da T-Systems, uma empresa da Deutsche Telekom, atuando como o fiduciário de dados alemão.

## <a name="region-pairs"></a>Pares de região
Cada região do Azure está emparelhada com outra região na mesma geografia (por exemplo, E.U.A., Europa ou Ásia). Esta abordagem permite a replicação dos recursos, como o armazenamento de VMs numa geografia, que deverá reduzir a probabilidade de desastres naturais, conflitos civis, falhas de energia ou falhas de rede física que afetem as duas regiões ao mesmo tempo. As vantagens adicionais dos pares de regiões são as seguintes:

* Em caso de uma maior indisponibilidade do Azure, uma região tem prioridade entre cada par, para ajudar a reduzir o tempo de restauro das aplicações. 
* As atualizações do Azure planeadas são lançadas para regiões emparelhadas, uma de cada vez, para minimizar o período de indisponibilidade e o risco de indisponibilidade de aplicação.
* Os dados continuam a residir na geografia do respetivo par (exceto no caso do Sul do Brasil), para efeitos de jurisdição fiscal e jurídica.

Os exemplos de pares de região incluem:

| Primário | Secundária |
|:--- |:--- |
| E.U.A. Oeste |E.U.A. Leste |
| Europa do Norte |Europa Ocidental |
| Sudeste Asiático |Ásia Leste |

Pode ver a [lista completa de pares regionais aqui](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilidade de funcionalidades
Alguns serviços ou funcionalidades de VM só estão disponíveis em determinadas regiões, como VMs com tamanhos ou tipos de armazenamento específicos. Existem também alguns serviços globais do Azure que não requerem a seleção de uma região específica, como o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), [Gestor de Tráfego](../traffic-manager/traffic-manager-overview.md) ou [DNS do Azure](../dns/dns-overview.md). Para ajudá-lo a estruturar o seu ambiente de aplicação, pode verificar a [disponibilidade dos serviços do Azure em cada região](https://azure.microsoft.com/regions/#services). Também pode [consultar programáticamente os tamanhos e restrições de VM suportados em cada região.](../azure-resource-manager/templates/error-sku-not-available.md)

## <a name="storage-availability"></a>Disponibilidade de armazenamento
Compreender as regiões e as geografias do Azure ganha importância ao considerar as opções de replicação de armazenamento disponíveis. Dependendo do tipo de armazenamento, existem opções de replicação diferentes.

**Managed Disks do Azure**
* Armazenamento localmente redundante (LRS)
  * Replica os dados três vezes na região em que criou a sua conta de armazenamento.

**Discos com base na conta de armazenamento**
* Armazenamento localmente redundante (LRS)
  * Replica os dados três vezes na região em que criou a sua conta de armazenamento.
* Armazenamento com redundância entre zonas (ZRS)
  * Replica os dados três vezes em dois ou três locais, numa única região ou em duas regiões.
* Armazenamento georredundante (GRS)
  * Replica os dados numa região secundária a centenas de quilómetros de distância da região primária.
* Armazenamento georredundante com acesso de leitura (RA-GRS)
  * Replica os dados numa região secundária, como acontece com o GRS, mas também fornece acesso só de leitura aos dados na localização secundária.

A tabela seguinte fornece uma descrição geral rápida das diferenças entre os tipos de replicação de armazenamento:

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Os dados são replicados em vários locais. |No |Yes |Yes |Yes |
| Os dados podem ser lidos a partir da localização secundária e da localização principal. |No |No |No |Yes |
| Número de cópias dos dados mantidas em nós separados. |3 |3 |6 |6 |

Pode ler mais sobre as [opções de replicação de Armazenamento do Azure aqui](../storage/common/storage-redundancy.md). Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](./managed-disks-overview.md).

### <a name="storage-costs"></a>Custos de armazenamento
Os preços podem variar consoante o tipo de armazenamento e disponibilidade que selecionou.

**Managed Disks do Azure**
* Os Discos Geridos Premium são apoiados por Solid-State Drives (SSDs) e os Discos Geridos Padrão são apoiados por discos de fiação regulares. Os Managed Disks Premium e os Standard são carregados com base na capacidade de aprovisionamento do disco.

**Discos não geridos**
* O armazenamento premium é apoiado por Solid-State Drives (SSDs) e é carregado com base na capacidade do disco.
* O armazenamento Standard está protegido por discos rotativos normais é cobrado com base na capacidade em utilização e na disponibilidade de armazenamento pretendida.
  * Para RA-GRS, existe uma cobrança adicional da Transferência de Dados de Georreplicação para a largura de banda da replicação desses dados para outra região do Azure.

Veja [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre os preços para tipos de armazenamento e opções de disponibilidade diferentes.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [as regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)