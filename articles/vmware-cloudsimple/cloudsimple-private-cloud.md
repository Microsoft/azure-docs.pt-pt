---
title: Nuvens privadas na solução VMware por CloudSimple – Azure
description: Saiba mais sobre nuvens e conceitos privados do CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9885366d5987870fe2739083ff47abaae9ef6ed1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816198"
---
# <a name="cloudsimple-private-cloud-overview"></a>Visão geral da nuvem privada do CloudSimple

O CloudSimple transforma e estende as cargas de trabalho do VMware para nuvens públicas em minutos. Usando o serviço CloudSimple, você pode implantar o VMware nativamente na infraestrutura bare-metal do Azure. Sua implantação reside em locais do Azure e integra-se totalmente com o restante da nuvem do Azure.

* A solução CloudSimple fornece continuidade operacional VMware completa. Essa solução oferece os benefícios da nuvem pública do:
  * Elasticidade
  * Inovação
  * Eficiência
* Com o CloudSimple, você se beneficia de um modelo de consumo de nuvem que reduz o custo total de propriedade. Ele também oferece provisionamento sob demanda, pagamento conforme o crescimento e otimização da capacidade.
* O CloudSimple é totalmente compatível com:
  * Ferramentas existentes
  * Competências
  * Processos
* Essa compatibilidade permite que suas equipes gerenciem cargas de trabalho na nuvem do Azure, sem interromper suas políticas:
  * Rede
  * Segurança  
  * Proteção de dados  
  * Auditoria
* O CloudSimple gerencia a infraestrutura e todos os serviços de rede e gerenciamento necessários. O serviço CloudSimple permite que sua equipe se concentre em:
  * Valor comercial
  * Provisionamento de aplicativos
  * Continuidade do negócio
  * Suporte
  * Aplicação da política

## <a name="private-cloud-environment-overview"></a>Visão geral do ambiente de nuvem privada

Uma nuvem privada é uma pilha VMware isolada, como esses ambientes:

* Hosts ESXi
* vCenter
* vSAN
* NSX

As nuvens privadas são gerenciadas por um servidor vCenter em seu próprio domínio de gerenciamento.

A pilha é executada em:

* Nós dedicados
* Nós de hardware bare metal isolados

Os usuários consomem a pilha por meio de ferramentas nativas do VMware, incluindo:

* vCenter
* NSX Manager

Você pode implantar nós dedicados em locais do Azure. Em seguida, você pode gerenciá-los com o Azure e o CloudSimple. Uma nuvem privada consiste em um ou mais clusters vSphere, e cada cluster contém de 3 a 16 nós.

Você pode criar uma nuvem privada usando nós provisionados:

* Nós pagos conforme o uso
* Nós reservados, dedicados

Você pode conectar a nuvem privada ao seu ambiente local e à rede do Azure usando as seguintes conexões:

* Proteger
* VPN privada
* Azure ExpressRoute

O ambiente de nuvem privada foi projetado para eliminar a existência de um ponto único de falha:

* Os clusters ESXi são configurados com alta disponibilidade do vSphere e são dimensionados para ter pelo menos um nó sobressalente para resiliência.
* a vSAN fornece armazenamento primário redundante. a vSan requer pelo menos três nós para fornecer proteção contra uma única falha. Você pode configurar a vSAN para fornecer maior resiliência para clusters maiores.
* Você pode configurar VMs do vCenter, PSC e NSX Manager com a política de armazenamento RAID-10 para proteger contra falhas de armazenamento. Em seguida, eles são protegidos pelo vSphere HA contra falhas de rede e de nó.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Cenários para implantar uma nuvem privada

* **Desativação ou migração do Data Center**

  * Obtenha capacidade adicional quando atingir os limites de seu datacenter existente ou atualizar o hardware.
  * Adicione a capacidade necessária na nuvem e elimine as dores de cabeça de gerenciamento de atualizações de hardware.
  * Reduza o risco e o custo de migrações na nuvem, em comparação com conversões demoradas ou rearquitetura.
  * Use ferramentas e habilidades conhecidas do VMware para acelerar as migrações na nuvem. Na nuvem, use os serviços do Azure para modernizar seus aplicativos no seu ritmo.

* **Expandir sob demanda**

  * Expanda para a nuvem para atender a necessidades imprevistas, como novos ambientes de desenvolvimento ou picos de capacidade sazonal.
  * Crie uma nova capacidade sob demanda e mantenha-a somente contanto que você precise dela.
  * Reduza seu investimento antecipado, acelere a velocidade do provisionamento e reduza a complexidade com a mesma arquitetura e políticas em ambos locais e na nuvem.

* **Recuperação de desastres e áreas de trabalho virtuais na nuvem do Azure**

  * Estabeleça acesso remoto a dados, aplicativos e áreas de trabalho na nuvem do Azure. Com conexões de largura de banda alta, você carrega/baixa dados rapidamente para se recuperar de incidentes. Redes de baixa latência oferecem tempos de resposta rápidos que os usuários esperam de um aplicativo de desktop.

  * Replique todas as suas políticas e redes na nuvem usando o portal do CloudSimple e ferramentas do VMware familiares. Essa replicação reduz o esforço e o risco de criar e gerenciar implementações de DR e de VDI.

* **Aplicativos e bancos de dados de alto desempenho**

  * Execute suas cargas de trabalho mais exigentes, com a arquitetura hiperconvergente fornecida pelo CloudSimple.
  * Execute Oracle, Microsoft SQL Server, sistemas de middleware e bancos de dados não SQL de alto desempenho.

  * Experimente a nuvem como sua própria data center com conexões de rede de 25 Gbps de alta velocidade. Conexões de alta velocidade permitem que você execute aplicativos híbridos que abrangem locais, VMware no Azure e cargas de trabalho privadas do Azure, sem comprometer o desempenho.

* **Verdadeiro híbrido**

  * Unificar o DevOps entre os serviços VMware e Azure.
  * Otimize a administração do VMware para serviços e soluções do Azure que podem ser aplicadas em todas as suas cargas de trabalho.
  * Acesse serviços de nuvem pública sem precisar expandir seu data center ou rearquitetar seus aplicativos.
  * Centralize identidades, políticas de controle de acesso, registro em log e monitoramento para aplicativos VMware no Azure.

## <a name="limits"></a>Limites

A tabela abaixo mostra os limites de nó dos recursos de uma nuvem privada.

| Resource | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada | 3 |
| Número máximo de nós em um cluster em uma nuvem privada | 16 |
| Número máximo de nós em uma nuvem privada | 64 |
| Número mínimo de nós em um novo cluster | 3 |

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)