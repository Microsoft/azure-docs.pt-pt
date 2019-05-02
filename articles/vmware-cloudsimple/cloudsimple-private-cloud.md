---
title: Nuvens privadas na solução de VMware ao CloudSimple - Azure
description: Saiba mais sobre conceitos e CloudSimple de nuvens privadas.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577049"
---
# <a name="cloudsimple-private-cloud-overview"></a>Descrição geral de nuvem privada CloudSimple

CloudSimple transforma e estende as cargas de trabalho do VMware para nuvens públicas em minutos. Utilizar o serviço de CloudSimple, pode implementar VMware nativamente na infraestrutura do Azure bare-metal. A implementação reside em localizações do Azure e é perfeitamente integrado com o restante da cloud do Azure.

* A solução de CloudSimple fornece a continuidade operacional VMware completa. Esta solução dá-lhe os benefícios da cloud pública do:
  * Elasticidade
  * Inovação
  * Eficiência
* Com CloudSimple, se beneficiar de um modelo de consumo na cloud que reduz o custo total de propriedade. Ele também oferece a pedido provisionamento, pay-como--crescer e a otimização de capacidade.
* CloudSimple é totalmente compatível com:
  * Ferramentas existentes
  * Competências
  * Processos
* Este compatibilidade permite que as equipes a gerenciar cargas de trabalho na cloud do Azure, sem interromper as suas políticas:
  * Rede
  * Segurança  
  * Proteção de dados  
  * Auditoria
* CloudSimple gere a infraestrutura e todas as redes e gestão de serviços necessários. O serviço de CloudSimple permite que sua equipe se concentrar em:
  * Valor comercial
  * Provisionamento de aplicativos
  * Continuidade do negócio
  * Suporte
  * Aplicação da política

## <a name="private-cloud-environment-overview"></a>Descrição geral do ambiente de nuvem privada

Uma nuvem privada é uma pilha de VMware isolada, como estes ambientes:

* Anfitriões ESXi
* vCenter
* vSAN
* NSX

Nuvens privadas são geridas por um vCenter server no seu próprio domínio de gestão.

A pilha é executado em:

* Nós dedicados
* Nós de hardware isolados de metal bare

Os utilizadores consomem pilha por intermédio de ferramentas nativas do VMware, incluindo:

* vCenter
* NSX Manager

Pode implementar nós dedicados em localizações do Azure. Em seguida, pode geri-los com o Azure e CloudSimple. Uma nuvem privada consiste num ou mais clusters vSphere e cada cluster contém nós de 3 a 16.

Pode criar uma nuvem privada usando comprado nós:

* Nós de pay as you go
* Nós dedicado, reservados

Pode ligar-se a nuvem privada para o seu ambiente no local e de rede do Azure utilizando as seguintes ligações:

* Proteger
* VPN privada
* Azure ExpressRoute

O ambiente de nuvem privada foi desenvolvido para eliminar a ter um ponto único de falha:

* Clusters de ESXi estão configurados com elevada disponibilidade do vSphere e são dimensionados com, pelo menos, um nó de componentes de reserva para resiliência.
* vSAN fornece com redundância de armazenamento primário. vSan requer, pelo menos, três nós para fornecer proteção contra falha de um único. Pode configurar vSAN para fornecer maior resiliência para clusters maiores.
* Pode configurar o vCenter, PSC e VMs NSX Manager com a política de armazenamento RAID 10 para proteger contra falhas de armazenamento. Em seguida, este está protegido por vSphere HA contra falhas de nó e de rede.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Cenários de implantação de uma nuvem privada

* **Extinção do Centro de dados ou de migração**

  * Obter capacidade adicional quando atingir os limites do seu datacenter existente ou atualizar o hardware.
  * Adicione capacidade necessária na cloud e eliminar as dores de cabeça de gerenciamento de atualizações de hardware.
  * Reduza o risco e custo de migrações para a cloud, em comparação comparada conversões demoradas ou reestruturação.
  * Utilize ferramentas familiares do VMware e competências para acelerar migrações para a cloud. Na cloud, utilize os serviços do Azure para modernizar seus aplicativos ao seu ritmo.

* **Expanda a pedido**

  * Expanda para a cloud para satisfazer as necessidades imprevistas, tais como novos ambientes de desenvolvimento ou picos sazonais capacidade.
  * Criar nova capacidade a pedido e mantê-lo somente quando precisa dela.
  * Reduzir o seu investimento inicial, Acelere a velocidade do provisionamento e reduza a complexidade com a mesma arquitetura e as políticas no local e na nuvem.

* **Recuperação após desastre e ambientes de trabalho virtuais na cloud do Azure**

  * Estabeleça o acesso remoto aos dados, aplicações e áreas de trabalho na cloud do Azure. Com ligações de largura de banda alta, carregar / transferir dados rapidamente para recuperar de incidentes. Daremos a redes de baixa latência rápida de resposta o tempo que os usuários esperam que a partir de uma aplicação de ambiente de trabalho.

  * Replicar todas as suas políticas e funcionamento em rede na cloud com o portal de CloudSimple e ferramentas familiares do VMware. Essa replicação reduz o esforço e o risco de criar e gerir implementações de DR e VDI.

* **Aplicativos de alto desempenho e de bases de dados**

  * Execute cargas de trabalho mais exigentes, com a arquitetura de hiperconvergido fornecida pelo CloudSimple.
  * Execute o Oracle, Microsoft SQL server, sistemas de middleware e bases de dados de elevado desempenho não-SQL.

  * Experiência de cloud como o seu próprio Datacenter com ligações de rede de alta velocidade 25 Gbps. Ligações de alta velocidade permitem-lhe executar aplicações híbridas que abrangem o VMware no local, no Azure, e o cargas de trabalho de privado do Azure, sem comprometer o desempenho.

* **Verdadeiramente híbrido**

  * Unificar as DevOps em todos os serviços de VMware e o Azure.
  * Otimize a administração de VMware para serviços do Azure e soluções que podem ser aplicadas em todas as suas cargas de trabalho.
  * Aceder aos serviços de cloud pública sem ter de expandir o seu centro de dados ou rearquiteture seus aplicativos.
  * Centralize identidades, políticas de controlo de acesso, registo e monitorização para aplicações de VMware no Azure.

## <a name="limits"></a>Limits

Tabela a seguir mostra os limites de nó nos recursos de uma nuvem privada.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada | 3 |
| Número máximo de nós num cluster numa nuvem privada | 16 |
| Número máximo de nós numa nuvem privada | 64 |
| Número mínimo de nós num cluster novo | 3 |

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)