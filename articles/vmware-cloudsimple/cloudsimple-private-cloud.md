---
title: Solução Azure VMware by CloudSimple - Nuvens Privadas
description: Saiba mais sobre cloudSimple Private Clouds e conceitos.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024959"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple Private Cloud visão geral

CloudSimple transforma e estende cargas de trabalho vMware a nuvens públicas em minutos. Utilizando o serviço CloudSimple, pode implementar vMware de forma nativa na infraestrutura de metal bare Azure. A sua implantação vive em localizações do Azure e integra-se totalmente com o resto da nuvem Azure.

A solução CloudSimple proporciona uma continuidade operacional completa do VMware. Esta solução dá-lhe os benefícios da nuvem pública de:

* Elasticidade
* Innovation
* Eficiência

Com a CloudSimple, beneficia-se de um modelo de consumo em nuvem que reduz o custo total de propriedade. Oferece também a provisionamento a pedido, o pagamento à sua cresce e a otimização da capacidade.

CloudSimple é totalmente compatível com:

* Ferramentas existentes
* Competências
* Processos

Esta compatibilidade permite que as suas equipas gerem cargas de trabalho na nuvem Azure, sem perturbar este tipo de políticas:

* Rede
* Segurança  
* Proteção de dados  
* Auditoria

A CloudSimple gere a infraestrutura e todos os serviços de networking e gestão necessários. O serviço CloudSimple permite à sua equipa focar-se em:

* Valor do negócio
* Aprovisionamento de aplicações
* Continuidade do negócio
* Suporte
* Aplicação da política

## <a name="private-cloud-environment-overview"></a>Visão geral do ambiente da nuvem privada

Uma nuvem privada é uma pilha de VMware isolada que suporta:

* Anfitriões esXi
* vCenter
* vSAN
* NSX

As Nuvens Privadas são geridas através do portal CloudSimple. Eles têm o seu próprio servidor vCenter no seu próprio domínio de gestão.

A pilha continua:

* Nós dedicados
* Nódosos de hardware de metal isolados

Os utilizadores consomem a pilha através de ferramentas VMware nativas, incluindo:

* vCenter
* Gestor NSX

Pode instalar nódosos dedicados em localizações de Azure. Depois pode geri-los com azure e CloudSimple. Uma Nuvem Privada é composta por um ou mais aglomerados de vSphere, e cada cluster contém 3 a 16 nós.

Você pode criar uma Nuvem Privada usando nós comprados, pay-as-you-go, ou nós reservados e dedicados.

Pode ligar a Nuvem Privada ao seu ambiente no local e à rede Azure utilizando as seguintes ligações:

* Proteger
* VPN privada
* Azure ExpressRoute

O ambiente Private Cloud foi concebido para eliminar pontos únicos de falha:

* Os clusters ESXi são configurados com alta disponibilidade vSphere e são dimensionados para ter pelo menos um nó sobressalente para resiliência.
* vSAN fornece armazenamento primário redundante. VSan requer pelo menos três nódosos para fornecer proteção contra uma única falha. Pode configurar o vSAN para fornecer uma maior resiliência para clusters maiores.
* Pode configurar vCenter, PSC e NSX Manager VMs com a política de armazenamento RAID-10 para proteger contra falhas de armazenamento. vSphere HA protege contra falhas de nó e rede.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Cenários para implantar uma Nuvem Privada

Aqui estão alguns casos de uso de exemplo para a implantação de Cloud Privada.

### <a name="data-center-retirement-or-migration"></a>Reforma ou migração de centros de dados

* Obtenha capacidade adicional quando atingir os limites do seu datacenter existente ou atualizar hardware.
* Adicione a capacidade necessária na nuvem e elimine as dores de cabeça da gestão de atualizações de hardware.
* Reduzir o risco e o custo das migrações em nuvem em comparação com conversões ou rearquitetura sem tempo.
* Utilize ferramentas e habilidades vMware familiares para acelerar as migrações em nuvem. Na nuvem, utilize os serviços Azure para modernizar as suas aplicações ao seu ritmo.

### <a name="expand-on-demand"></a>Expandir a procura

* Expanda-se para a nuvem para atender a necessidades inesperadas, como novos ambientes de desenvolvimento ou explosões de capacidade sazonal.
* Crie uma nova capacidade a pedido e guarde-a apenas o tempo que precisar.
* Reduza o seu investimento frontal, acelere a velocidade de provisionamento e reduza a complexidade com a mesma arquitetura e políticas tanto no local como na nuvem.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Recuperação de desastres e ambientes de trabalho virtuais na nuvem azure

* Estabeleça acesso remoto a dados, apps e desktops na nuvem Azure. Com ligações de largura de banda alta, você carrega/descarrega dados rapidamente para recuperar de incidentes. As redes de baixa latência dão-lhe tempos de resposta rápidos que os utilizadores esperam de uma aplicação de desktop.

* Reproduza todas as suas políticas e networking na nuvem utilizando o portal CloudSimple e ferramentas vMware familiares. A replicação reduz o esforço e o risco de criar e gerir implementações de DR e VDI.

### <a name="high-performance-applications-and-databases"></a>Aplicações e bases de dados de alto desempenho

* Execute as suas cargas de trabalho mais exigentes com a arquitetura hiperconvergente fornecida pela CloudSimple.
* Executar o Oracle, o servidor Microsoft SQL, sistemas de middleware e bases de dados sem SQL de alto desempenho.
* Experimente a nuvem como o seu próprio centro de dados com ligações de rede de alta velocidade de 25 Gbps. As ligações de alta velocidade permitem-lhe executar aplicações híbridas que se estendem no local, VMware no Azure e cargas de trabalho privadas Azure e Azure, sem comprometer o desempenho.

### <a name="true-hybrid"></a>Verdadeiro híbrido

* Unifique os DevOps através dos serviços VMware e Azure.
* Otimize a administração VMware para serviços e soluções Azure que podem ser aplicadas em todas as suas cargas de trabalho.
* Aceda aos serviços públicos de nuvem sem ter de expandir o seu centro de dados ou rearquitear as suas aplicações.
* Centralize identidades, políticas de controlo de acesso, registo e monitorização de aplicações VMware no Azure.

## <a name="limits"></a>Limites

A tabela seguinte enumera os limites do nó nos recursos de uma Nuvem Privada.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada | 3 |
| Número máximo de nós em aglomerado numa nuvem privada | 16 |
| Número máximo de nós numa Nuvem Privada | 64 |
| Número mínimo de nós num novo aglomerado | 3 |

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar uma Nuvem Privada](create-private-cloud.md)
* Saiba como [configurar um ambiente de Nuvem Privada](quickstart-create-private-cloud.md)
