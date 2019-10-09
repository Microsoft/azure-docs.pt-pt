---
title: Redundância regional e recuperação de failover com o cache HPC do Azure (versão prévia)
description: Técnicas para fornecer recursos de failover para recuperação de desastre com o cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: rohogue
ms.openlocfilehash: ca771ceed433c6eb0ed26bba6fcb1bf11db211f0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031620"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Usar vários caches para recuperação de failover regional

Cada instância de cache do Azure HPC é executada em uma assinatura específica e em uma região. Isso significa que o fluxo de trabalho do cache poderia possivelmente ser interrompido se a região tiver uma interrupção completa.

Este artigo descreve uma estratégia para reduzir o risco de interrupções de trabalho usando uma segunda região para failover de cache.

A chave está usando armazenamento de back-end que pode ser acessado de várias regiões. Esse armazenamento pode ser um sistema NAS local com suporte DNS apropriado ou o armazenamento de BLOBs do Azure que reside em uma região diferente do cache.

Conforme o fluxo de trabalho continua em sua região primária, os dados são salvos no armazenamento de longo prazo fora da região. Se a região de cache ficar indisponível, você poderá criar uma instância duplicada de cache do Azure HPC em uma região secundária, conectar-se ao mesmo armazenamento e retomar o trabalho do novo cache.

## <a name="planning-for-regional-failover"></a>Planejando o failover regional

Para configurar um cache que está preparado para um possível failover, siga estas etapas:

1. Verifique se o armazenamento de back-end está acessível em uma segunda região.
1. Ao planejar a criação da instância de cache primária, você também deve se preparar para replicar esse processo de instalação na segunda região. Incluir estes itens:

   1. Estrutura de rede virtual e de sub-rede
   1. Capacidade de cache
   1. Detalhes de destino de armazenamento, nomes e caminhos de namespace
   1. Detalhes sobre computadores cliente, se estiverem localizados na mesma região que o cache
   1. Comando mount para uso por clientes de cache

   > [!NOTE]
   > O cache do HPC do Azure pode ser criado programaticamente, por meio de um [modelo de Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md) ou acessando diretamente sua API. Contate a equipe de cache do Azure HPC para obter detalhes.

## <a name="failover-example"></a>Exemplo de failover

Como exemplo, imagine que você deseja localizar o cache do Azure HPC na região leste dos EUA do Azure. Ele acessará os dados armazenados em seu data center local.

Você pode usar um cache na região oeste dos EUA 2 como um backup de failover.

Ao criar o cache no leste dos EUA, prepare um segundo cache para implantação no oeste dos EUA 2. Você pode usar scripts ou modelos para automatizar essa preparação.

No caso de uma falha em toda a região no leste dos EUA, crie o cache que você preparou na região oeste dos EUA 2.

Depois que o cache for criado, adicione destinos de armazenamento que apontem para os mesmos armazenamentos de dados locais e use os mesmos caminhos de namespace agregados que os destinos de armazenamento do cache antigo.

Se os clientes originais forem afetados, crie novos clientes na região oeste dos EUA 2 para uso com o novo cache.

Todos os clientes precisarão montar o novo cache, mesmo que os clientes não tenham sido afetados pela interrupção da região. O novo cache tem endereços de montagem diferentes do antigo.

## <a name="learn-more"></a>Saiba mais

O guia de arquitetura do aplicativo do Azure inclui mais informações sobre como se [recuperar de uma interrupção de serviço em toda a região](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>).
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
