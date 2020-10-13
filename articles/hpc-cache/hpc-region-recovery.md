---
title: Redundância regional e recuperação falhada com Azure HPC Cache
description: Técnicas para fornecer capacidades de failover para recuperação de desastres com cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 9159807f55ae52393b8fccec339fcc94c3e4ebb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87061376"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Use várias caches para recuperação de falhas regionais

Cada instância Azure HPC Cache é executada numa determinada subscrição e numa região. Isto significa que o seu fluxo de trabalho de cache pode ser interrompido se a região tiver uma paragem total.

Este artigo descreve uma estratégia para reduzir o risco de perturbação do trabalho, utilizando uma segunda região para o failover de cache.

A chave é usar o armazenamento back-end que é acessível a partir de várias regiões. Este armazenamento pode ser um sistema NAS no local com suporte DNS apropriado, ou armazenamento Azure Blob que reside em uma região diferente da cache.

À medida que o seu fluxo de trabalho prossegue na sua região primária, os dados são guardados no armazenamento a longo prazo fora da região. Se a região de cache ficar indisponível, pode criar uma instância de cache Azure HPC duplicada numa região secundária, ligar-se ao mesmo armazenamento e retomar os trabalhos a partir da nova cache.

## <a name="planning-for-regional-failover"></a>Planeamento para o failover regional

Para configurar uma cache que esteja preparada para uma possível falha, siga estes passos:

1. Certifique-se de que o seu armazenamento traseiro está acessível numa segunda região.
1. Ao planear criar a instância de cache primária, também deve preparar-se para replicar este processo de configuração na segunda região. Inclua estes itens:

   1. Estrutura de rede virtual e sub-rede
   1. Capacidade de cache
   1. Detalhes do alvo de armazenamento, nomes e caminhos do espaço de nome
   1. Detalhes sobre as máquinas cliente, se estiverem localizadas na mesma região que a cache
   1. Monte comando para uso por clientes cache

   > [!NOTE]
   > A Azure HPC Cache pode ser criado programáticamente, seja através de um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) ou acedendo diretamente à sua API. Contacte a equipa da Azure HPC Cache para mais detalhes.

## <a name="failover-example"></a>Exemplo de falha

Como exemplo, imagine que pretende localizar o seu Azure HPC Cache na região leste dos EUA de Azure. Acederá aos dados armazenados no seu centro de dados no local.

Você pode usar uma cache na região oeste dos EUA 2 como um backup de failover.

Ao criar a cache no Leste dos EUA, prepare uma segunda cache para implantação no Oeste dos EUA 2. Pode utilizar scripts ou modelos para automatizar esta preparação.

Em caso de uma falha na região no Leste dos EUA, crie a cache que preparou na região oeste dos EUA 2.

Após a criação da cache, adicione alvos de armazenamento que apontam para as mesmas lojas de dados no local e use os mesmos caminhos agregados de espaço de nome que os alvos de armazenamento da antiga cache.

Se os clientes originais forem afetados, crie novos clientes na região oeste dos EUA 2 para uso com a nova cache.

Todos os clientes terão de montar a nova cache, mesmo que os clientes não fossem afetados pela paralisação da região. A nova cache tem diferentes endereços de montagem do antigo.

## <a name="learn-more"></a>Saiba mais

O guia de arquitetura de aplicações Azure inclui mais informações sobre como recuperar de uma perturbação de serviço em toda a [região.](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)
