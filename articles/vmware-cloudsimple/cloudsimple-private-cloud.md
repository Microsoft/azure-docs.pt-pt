---
title: Solução Azure VMware by CloudSimple - Nuvens Privadas
description: Conheça os conceitos e vantagens da CloudSimple, incluindo a continuidade operacional completa da VMware, a compatibilidade com as ferramentas, competências e processos existentes.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140722"
---
# <a name="cloudsimple-private-cloud-overview"></a>Visão geral da nuvem privada CloudSimple

CloudSimple transforma e estende cargas de trabalho VMware a nuvens públicas em minutos. Utilizando o serviço CloudSimple, pode implantar vMware de forma nativa na infraestrutura metálica Azure. A sua implantação vive em locais de Azure e integra-se totalmente com o resto da nuvem Azure.

A solução CloudSimple proporciona uma continuidade operacional completa da VMware. Esta solução dá-lhe os benefícios da nuvem pública de:

* Elasticidade
* Inovação
* Eficiência

Com o CloudSimple, você beneficia de um modelo de consumo em nuvem que reduz o seu custo total de propriedade. Também oferece provisão a pedido, pagamento à medida que cresce e otimização da capacidade.

CloudSimple é totalmente compatível com:

* Ferramentas existentes
* Competências
* Processos

Esta compatibilidade permite que as suas equipas gerem cargas de trabalho na nuvem Azure, sem perturbar este tipo de políticas:

* Rede
* Segurança  
* Proteção de dados  
* Auditoria

A CloudSimple gere a infraestrutura e todos os serviços de networking e gestão necessários. O serviço CloudSimple permite à sua equipa concentrar-se em:

* Valor do negócio
* Aprovisionamento de aplicações
* Continuidade do negócio
* Suporte
* Aplicação da política

## <a name="private-cloud-environment-overview"></a>Visão geral do ambiente da nuvem privada

Uma Nuvem Privada é uma pilha de VMware isolada que suporta:

* Anfitriões ESXi
* vCenter
* vSAN
* NSX

Nuvens Privadas são geridas através do portal CloudSimple. Têm o seu próprio servidor vCenter no seu próprio domínio de gestão.

A pilha continua:

* Nós dedicados
* Nódoas de hardware de metal nu isolada

Os utilizadores consomem a pilha através de ferramentas nativas de VMware, incluindo:

* vCenter
* Gerente NSX

Pode implementar nós dedicados em locais Azure. Depois pode geri-los com Azure e CloudSimple. Uma Nuvem Privada é composta por um ou mais aglomerados vSphere, e cada cluster contém 3 a 16 nós.

Você pode criar uma Nuvem Privada usando nós comprados, pay-as-you-go, ou nós reservados e dedicados.

Pode ligar a Nuvem Privada ao ambiente no local e à rede Azure utilizando as seguintes ligações:

* Proteger
* VPN privado
* Azure ExpressRoute

O ambiente Private Cloud é projetado para eliminar pontos únicos de falha:

* Os clusters ESXi são configurados com vSphere alta disponibilidade e são dimensionados para ter pelo menos um nó sobressalente para a resiliência.
* vSAN fornece armazenamento primário redundante. vSan requer pelo menos três nós para fornecer proteção contra uma única falha. Você pode configurar vSAN para fornecer maior resiliência para aglomerados maiores.
* Pode configurar vCenter, PSC e NSX Manager VMs com a política de armazenamento RAID-10 para proteger contra falhas de armazenamento. vSphere HA protege contra falhas de nó e rede.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Cenários para implantar uma Nuvem Privada

Aqui estão alguns casos de uso de exemplo para implantação de Private Cloud.

### <a name="data-center-retirement-or-migration"></a>Reforma ou migração do centro de dados

* Obtenha capacidade adicional quando atingir os limites do seu datacenter existente ou atualizar hardware.
* Adicione a capacidade necessária na nuvem e elimine as dores de cabeça de gerir as atualizações de hardware.
* Reduza o risco e o custo das migrações em nuvem em comparação com conversões demoradas ou rearquiteturas.
* Use ferramentas e habilidades vMware familiares para acelerar as migrações em nuvem. Na nuvem, utilize os serviços Azure para modernizar as suas aplicações ao seu ritmo.

### <a name="expand-on-demand"></a>Expandir a procura

* Expanda-se para a nuvem para atender a necessidades inesperadas, como novos ambientes de desenvolvimento ou explosões de capacidade sazonal.
* Crie uma nova capacidade a pedido e mantenha-a apenas o tempo que precisar.
* Reduza o seu investimento frontal, acelere a velocidade de provisionamento e reduza a complexidade com a mesma arquitetura e políticas tanto no local como na nuvem.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Recuperação de desastres e ambientes de trabalho virtuais na nuvem de Azure

* Estabelecer acesso remoto a dados, apps e desktops na nuvem Azure. Com ligações de largura de banda alta, você carrega/descarrega dados rapidamente para recuperar de incidentes. As redes de baixa latência dão-lhe tempos de resposta rápidos que os utilizadores esperam de uma aplicação para desktop.

* Replique todas as suas políticas e networking na nuvem usando o portal CloudSimple e ferramentas VMware familiares. A replicação reduz o esforço e o risco de criar e gerir implementações de DR e VDI.

### <a name="high-performance-applications-and-databases"></a>Aplicações e bases de dados de alto desempenho

* Execute as suas cargas de trabalho mais exigentes com a arquitetura hiperconvergizada fornecida pela CloudSimple.
* Executar Oracle, servidor Microsoft SQL, sistemas de middleware e bases de dados sem SQL de alto desempenho.
* Experimente a nuvem como o seu próprio centro de dados com ligações de rede de alta velocidade de 25 Gbps. As ligações de alta velocidade permitem-lhe executar aplicações híbridas que se estendem no local, VMware em Azure e cargas de trabalho privadas Azure, sem comprometer o desempenho.

### <a name="true-hybrid"></a>Verdadeiro híbrido

* Unifique os devOps através dos serviços VMware e Azure.
* Otimize a administração VMware para serviços e soluções Azure que podem ser aplicadas em todas as suas cargas de trabalho.
* Aceda aos serviços públicos de nuvem sem ter de expandir o seu centro de dados ou rearchitect as suas aplicações.
* Centralizar identidades, políticas de controlo de acesso, registo e monitorização de aplicações VMware no Azure.

## <a name="limits"></a>Limites

A tabela seguinte lista os limites do nó nos recursos de uma Nuvem Privada.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada | 3 |
| Número máximo de nós em um aglomerado em uma nuvem privada | 16 |
| Número máximo de nós numa Nuvem Privada | 64 |
| Número mínimo de nós num novo cluster | 3 |

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar uma Nuvem Privada](create-private-cloud.md)
* Saiba como [configurar um ambiente private Cloud](quickstart-create-private-cloud.md)
