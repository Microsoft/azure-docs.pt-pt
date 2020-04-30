---
title: Redundância regional e recuperação de falhas com cache Azure HPC
description: Técnicas para fornecer capacidades de failover para recuperação de desastres com Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537275"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Use vários caches para recuperação regional de falhas

Cada instância de Cache Azure HPC funciona dentro de uma subscrição particular e numa região. Isto significa que o seu fluxo de trabalho em cache pode ser interrompido se a região tiver uma paragem total.

Este artigo descreve uma estratégia para reduzir o risco de perturbação do trabalho utilizando uma segunda região para a falha da cache.

A chave é usar armazenamento de back-end que é acessível a partir de várias regiões. Este armazenamento pode ser um sistema NAS no local com suporte DNS apropriado, ou armazenamento Azure Blob que reside em uma região diferente da cache.

À medida que o seu fluxo de trabalho prossegue na sua região primária, os dados são guardados no armazenamento a longo prazo fora da região. Se a região de cache ficar indisponível, pode criar uma instância de cache Azure HPC duplicada numa região secundária, ligar-se ao mesmo armazenamento e retomar os trabalhos a partir da nova cache.

## <a name="planning-for-regional-failover"></a>Planeamento para a falha regional

Para configurar uma cache preparada para uma possível falha, siga estes passos:

1. Certifique-se de que o seu armazenamento traseiro é acessível numa segunda região.
1. Ao planear criar a instância principal de cache, deve também preparar-se para replicar este processo de configuração na segunda região. Inclua estes itens:

   1. Rede virtual e estrutura de sub-rede
   1. Capacidade de cache
   1. Detalhes do alvo de armazenamento, nomes e caminhos do espaço de nome
   1. Detalhes sobre máquinas de clientes, se estiverem localizados na mesma região que o cache
   1. Monte comando para uso por clientes cache

   > [!NOTE]
   > A Cache Azure HPC pode ser criada programáticamente, seja através de um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) ou através do acesso direto à sua API. Contacte a equipa azure HPC Cache para obter mais detalhes.

## <a name="failover-example"></a>Exemplo de failover

Como exemplo, imagine que quer localizar o seu Cache Azure HPC na região leste dos EUA de Azure. Acederá aos dados armazenados no seu centro de dados no local.

Você pode usar um cache na região oeste dos EUA 2 como um backup de falha.

Ao criar a cache no Leste dos EUA, prepare uma segunda cache para implantação em West US 2. Pode utilizar scripts ou modelos para automatizar esta preparação.

Em caso de falha na região dos EUA, crie o cache que preparou na região oeste dos EUA 2.

Após a criação da cache, adicione alvos de armazenamento que apontem para as mesmas lojas de dados no local e utilizem os mesmos caminhos agregados do espaço de nome saque tamanha que os alvos de armazenamento da cache antiga.

Se os clientes originais forem afetados, crie novos clientes na região oeste dos EUA 2 para uso com a nova cache.

Todos os clientes terão de montar a nova cache, mesmo que os clientes não tenham sido afetados pela paralisação da região. A nova cache tem diferentes endereços de montagem do antigo.

## <a name="learn-more"></a>Saiba mais

O guia de arquitetura de aplicações Azure inclui mais informações sobre como recuperar de uma perturbação de serviço em [toda a região.](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)
