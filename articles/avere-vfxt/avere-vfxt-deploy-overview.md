---
title: Descrição geral da implementação - Avere vFXT para o Azure
description: Visão geral da implantação Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0c61db5e34ba58bb767b0bda773a54c8e65cd404
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991806"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT para o Azure - descrição geral da implementação

Este artigo fornece uma descrição geral das etapas necessárias para obter um vFXT Avere para um cluster do Azure backup e em execução.

Várias tarefas são necessárias antes e depois de criar o cluster vFXT no Azure Marketplace. Ter uma idéia clara do processo de início para concluir irá ajudá-lo a definir o âmbito o esforço necessário. 

## <a name="deployment-steps"></a>Passos da implementação

Após [planear o seu sistema](avere-vfxt-deploy-plan.md), pode começar a criar o cluster de vFXT Avere. 

Um modelo do Azure Resource Manager no Azure Marketplace recolhe as informações necessárias e implementa automaticamente todo o cluster. 

Depois do cluster de vFXT estiver em execução, desejará sabe como ligar a clientes ao mesmo e, se necessário, como mover seus dados para o novo contentor de armazenamento de Blobs.  

Aqui está uma visão geral de todos os passos.

1. Configurar os pré-requisitos 

   Antes de criar uma VM, tem de criar uma nova subscrição para o projeto de vFXT Avere, configurar a propriedade da subscrição, verifique as quotas e pedir um aumento, se necessário e aceitar os termos de utilização de software de vFXT Avere. Leia [preparar para criar o vFXT Avere](avere-vfxt-prereqs.md) para obter instruções detalhadas.

1. Criar uma função de acesso para os nós de cluster

   O Azure utiliza [controlo de acesso baseado em funções](../role-based-access-control/index.yml) (RBAC) para autorizar o nó de cluster de VMs para executar determinadas tarefas. Por exemplo, os nós do cluster tem de ser capaz de atribuir ou reatribuem endereços IP para outros nós do cluster. Antes de criar o cluster, tem de definir uma função que lhes concede as permissões adequadas.

   Leia [criar a função de acesso de nó de cluster](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) para obter instruções.

   O controlador de cluster também utiliza uma função de acesso, mas pode aceitar a função predefinida, o proprietário, em vez de criar seu próprio. Se quiser criar uma função personalizada para o controlador de cluster, leia [função de acesso de controlador personalizado](avere-vfxt-controller-role.md). 

1. Criar o cluster de vFXT Avere 

   Utilize o Azure Marketplace para criar o vFXT Avere para cluster do Azure. Um modelo recolhe as informações necessárias e executa scripts para criar o produto final.

   Estes passos, que são feitos pelo modelo de marketplace envolve a criação do cluster: 

   * Criar nova rede grupos de recursos de infraestrutura e, se necessário
   * Criar um *controlador do cluster*  

     O controlador de cluster é uma VM simple que residem na mesma rede virtual do cluster de vFXT Avere e personalizadas de software necessárias para criar e gerir o cluster. O controlador cria os nós de vFXT e formulários, o cluster, e também fornece uma interface de linha de comandos para gerir o cluster durante seu ciclo de vida.

     Se criar uma nova vnet durante a implementação, o seu controlador terão um endereço IP público. Isso significa que o controlador pode servir como um anfitrião de ligação para ligar para o cluster a partir de fora da vnet.

   * Criar o cluster de VMs de nó

   * Configuração do nó de cluster de VMs para constituir o cluster

   * Opcionalmente, criar um novo contentor de BLOBs e configurá-lo como armazenamento de back-end para o cluster

1. Configurar o cluster 

   Ligue-se para a interface de configuração de vFXT Avere (painel de controlo de Avere) para personalizar definições do cluster. Escolher para a monitorização de suporte e adicione o seu sistema de armazenamento se estiver a utilizar um centro de dados no local.

   * [Aceder ao cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Ativar o suporte](avere-vfxt-enable-support.md)
   * [Configurar o armazenamento](avere-vfxt-add-storage.md) (se necessário)

1. Clientes de montagem

   Siga as diretrizes [montar o cluster de vFXT Avere](avere-vfxt-mount-clients.md) para saber mais sobre o balanceamento de carga e como máquinas de cliente devem montar o cluster.

1. Adicionar dados (se necessário)

   Como o vFXT Avere é uma cache de cliente multi dimensionável, é a melhor forma de mover dados para um novo contentor de armazenamento de back-end com a estratégia de cópia de ficheiros de vários clientes, vários threads. Leia [mover dados para o cluster vFXT](avere-vfxt-data-ingest.md) para obter detalhes.

## <a name="next-steps"></a>Passos Seguintes

Continuar a [preparar para criar o vFXT Avere](avere-vfxt-prereqs.md) para concluir as tarefas preliminares para implementar o vFXT Avere para o Azure. 