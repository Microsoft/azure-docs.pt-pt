---
title: Visão geral da implementação - Avere vFXT para Azure
description: Saiba como implementar um avere vFXT para cluster Azure com esta visão geral. Os artigos relacionados têm instruções de implantação específicas.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271231"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT para Azure - visão geral da implantação

Este artigo dá uma visão geral dos passos necessários para obter um Avere vFXT para o agrupamento Azure em funcionamento.

São necessárias várias tarefas antes e depois de criar o cluster vFXT a partir do Azure Marketplace. Ter uma noção clara do processo de início ao fim irá ajudá-lo a estender o esforço necessário.

## <a name="deployment-steps"></a>Passos da implementação

Depois de [planear o seu sistema,](avere-vfxt-deploy-plan.md)pode começar a criar o seu cluster Avere vFXT.

Um modelo de Gestor de Recursos Azure no Azure Marketplace recolhe as informações necessárias e implanta automaticamente todo o cluster.

Depois do cluster vFXT estar a funcionar, ainda existem alguns passos de configuração a tomar antes de o utilizar. Se criou um novo recipiente de armazenamento Blob, vai querer mover os seus dados para ele. Se utilizar um sistema de armazenamento NAS, tem de o adicionar após a criação do cluster. Vai querer ligar os clientes ao cluster.

Aqui está uma visão geral de todos os passos.

1. Configurar pré-requisitos

   Antes de criar um VM, você deve criar uma nova subscrição para o projeto Avere vFXT, configurar a propriedade de subscrição, verificar quotas e solicitar um aumento se necessário, e aceitar termos para usar o software Avere vFXT. Leia [Prepare-se para criar o Avere vFXT](avere-vfxt-prereqs.md) para obter instruções detalhadas.

1. Criar o cluster Avere vFXT

   Utilize o Azure Marketplace para criar o avere vFXT para o cluster Azure. Um modelo recolhe as informações necessárias e executa scripts para criar o produto final.

   A criação de cluster envolve estes passos, que são todos feitos pelo modelo de mercado:

   * Criar novas infraestruturas de rede e grupos de recursos, se necessário
   * Criar um controlador de cluster

     O controlador de cluster é um VM simples que reside na mesma rede virtual que o cluster Avere vFXT e tem o software personalizado necessário para criar e gerir o cluster. O controlador cria os nós vFXT e forma o cluster, e também fornece uma interface de linha de comando para gerir o cluster durante a sua vida útil.

     Se criar uma nova rede virtual ou sub-rede durante a implementação, o seu controlador terá um endereço IP público. Isto significa que o controlador pode servir de hospedeiro de salto para ligar ao cluster de fora da rede virtual.

   * Criar os VMs de nó de cluster

   * Criar o cluster a partir dos nóns individuais

   * Opcionalmente, crie um novo recipiente Blob e configuga-o como armazenamento de back-end para o cluster

   A criação de cluster é descrita em detalhe no [conjunto implementar o cluster vFXT](avere-vfxt-deploy.md).

1. Configurar o cluster

   Ligue-se à interface de configuração Avere vFXT (Painel de Controlo Avere) para personalizar as definições do cluster. Opte pela monitorização de suporte e adicione o seu sistema de armazenamento se estiver a utilizar armazenamento de hardware ou recipientes Blob adicionais.

   * [Aceder ao cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Permitir o suporte](avere-vfxt-enable-support.md)
   * [Configure o armazenamento](avere-vfxt-add-storage.md) (se necessário)

1. Montar clientes

   Siga as diretrizes no [Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md) para aprender sobre o equilíbrio de carga e como as máquinas de clientes devem montar o cluster.

1. Adicionar dados (se necessário)

   Como o Avere vFXT é uma cache multi-cliente escalável, a melhor maneira de mover dados para um novo recipiente de armazenamento de back-end é com uma estratégia de cópia de ficheiro multi-cliente e multi-threaded.

   Se necessitar de mover os dados do conjunto de trabalho para um novo recipiente Blob ou outro sistema de armazenamento de back-end, siga as instruções em [Mover dados para o cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Passos seguintes

Continue a [preparar-se para criar o Avere vFXT](avere-vfxt-prereqs.md) para completar as tarefas pré-requisitos.
