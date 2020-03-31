---
title: Visão geral de implantação - Avere vFXT para Azure
description: Visão geral da implantação de Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153688"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT para Azure - visão geral de implantação

Este artigo dá uma visão geral dos passos necessários para obter um Avere vFXT para o cluster Azure em funcionamento.

São necessárias várias tarefas antes e depois de criar o cluster vFXT do Azure Marketplace. Ter uma noção clara do processo de início ao fim irá ajudá-lo a analisar o esforço necessário.

## <a name="deployment-steps"></a>Passos da implementação

Depois de [planear o seu sistema,](avere-vfxt-deploy-plan.md)pode começar a criar o seu cluster Avere vFXT.

Um modelo de Gestor de Recursos Azure no Mercado Azure recolhe as informações necessárias e implanta automaticamente todo o cluster.

Depois do cluster vFXT estar em funcionamento, ainda existem alguns passos de configuração a tomar antes de usá-lo. Se criou um novo recipiente de armazenamento Blob, vai querer transferir os seus dados para ele. Se utilizar um sistema de armazenamento NAS, tem de o adicionar depois de o cluster ser criado. Vai querer ligar clientes ao cluster.

Aqui está uma visão geral de todos os passos.

1. Configurar pré-requisitos

   Antes de criar um VM, deve criar uma nova subscrição para o projeto Avere vFXT, configurar a propriedade de subscrição, verificar quotas e solicitar um aumento se necessário, e aceitar termos para a utilização do software Avere vFXT. Leia [Prepare-se para criar o Avere vFXT](avere-vfxt-prereqs.md) para instruções detalhadas.

1. Criar o cluster Avere vFXT

   Utilize o Azure Marketplace para criar o Avere vFXT para cluster Azure. Um modelo recolhe as informações necessárias e executa scripts para criar o produto final.

   A criação de cluster envolve estes passos, que são todos feitos pelo modelo de mercado:

   * Criar novas infraestruturas de rede e grupos de recursos, se necessário
   * Criar um controlador de cluster

     O controlador de cluster é um VM simples que reside na mesma rede virtual que o cluster Avere vFXT e tem o software personalizado necessário para criar e gerir o cluster. O controlador cria os nós vFXT e forma o cluster, e também fornece uma interface de linha de comando para gerir o cluster durante a sua vida útil.

     Se criar uma nova rede virtual ou subnet durante a implementação, o seu controlador terá um endereço IP público. Isto significa que o controlador pode servir como hospedeiro de salto para ligar ao cluster de fora da rede virtual.

   * Crie os VMs do nó cluster

   * Criar o cluster a partir dos nós individuais

   * Opcionalmente, crie um novo recipiente Blob e configure-o como armazenamento de back-end para o cluster

   A criação do cluster é descrita em detalhe na [implantação do cluster vFXT](avere-vfxt-deploy.md).

1. Configurar o cluster

   Ligue-se à interface de configuração Avere vFXT (Painel de Controlo Avere) para personalizar as definições do cluster. Opte pela monitorização do suporte e adicione o seu sistema de armazenamento se estiver a utilizar armazenamento de hardware ou recipientes Blob adicionais.

   * [Aceder ao cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Ativar suporte](avere-vfxt-enable-support.md)
   * [Configurar o armazenamento](avere-vfxt-add-storage.md) (se necessário)

1. Montar clientes

   Siga as diretrizes no [cluster Avere vFXT](avere-vfxt-mount-clients.md) para aprender sobre o equilíbrio de carga e como as máquinas de cliente devem montar o cluster.

1. Adicionar dados (se necessário)

   Como o Avere vFXT é uma cache de vários clientes escalável, a melhor maneira de mover dados para um novo recipiente de armazenamento back-end é com uma estratégia multi-cliente, multi-threaded file copy.

   Se precisar de mover os dados do conjunto de trabalho para um novo recipiente Blob ou outro sistema de armazenamento traseiro, siga as instruções em [mover os dados para o cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Passos seguintes

Continue a [preparar-se para criar o Avere vFXT](avere-vfxt-prereqs.md) para completar as tarefas pré-requisitos.
