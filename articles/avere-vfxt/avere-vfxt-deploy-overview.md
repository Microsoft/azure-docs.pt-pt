---
title: Visão geral da implantação – avere vFXT for Azure
description: Visão geral da implantação do avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 46e6828710c5951cdd7ec3a029272a0e3d68c477
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415411"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure – visão geral da implantação

Este artigo fornece uma visão geral das etapas necessárias para obter um avere vFXT para o cluster do Azure em funcionamento.

Várias tarefas são necessárias antes e depois de você criar o cluster vFXT do Azure Marketplace. Ter uma noção clara do processo de início a término ajudará você a definir o escopo do esforço necessário.

## <a name="deployment-steps"></a>Passos da implementação

Depois de [planejar o sistema](avere-vfxt-deploy-plan.md), você pode começar a criar seu cluster avere vFXT.

Um modelo de Azure Resource Manager no Azure Marketplace coleta as informações necessárias e implanta automaticamente todo o cluster.

Depois que o cluster vFXT estiver em execução, você desejará saber como conectar clientes a ele e (opcionalmente) como mover seus dados para o novo contêiner de armazenamento de BLOBs. Se você usar um sistema de armazenamento NAS, será necessário adicioná-lo após a criação do cluster.

Aqui está uma visão geral de todas as etapas.

1. Configurar pré-requisitos

   Antes de criar uma VM, você deve criar uma nova assinatura para o projeto avere vFXT, configurar a propriedade da assinatura, verificar cotas e solicitar um aumento, se necessário, e aceitar os termos para usar o software avere vFXT. Leia [preparar para criar o avere vFXT](avere-vfxt-prereqs.md) para obter instruções detalhadas.

1. Criar o cluster avere vFXT

   Use o Azure Marketplace para criar o avere vFXT para o cluster do Azure. Um modelo coleta as informações necessárias e executa scripts para criar o produto final.

   A criação do cluster envolve essas etapas, que são todas feitas pelo modelo do Marketplace:

   * Criar nova infraestrutura de rede e grupos de recursos, se necessário
   * Criar um *controlador de cluster*  

     O controlador de cluster é uma VM simples que reside na mesma rede virtual que o cluster avere vFXT e tem o software personalizado necessário para criar e gerenciar o cluster. O controlador cria os nós vFXT e forma o cluster e também fornece uma interface de linha de comando para gerenciar o cluster durante seu tempo de vida.

     Se você criar uma nova rede virtual ou sub-rede durante a implantação, o controlador terá um endereço IP público. Isso significa que o controlador pode servir como um host de salto para se conectar ao cluster de fora da sub-rede.

   * Criar as VMs do nó de cluster

   * Configurar as VMs do nó de cluster para formar o cluster

   * Opcionalmente, crie um novo contêiner de BLOB e configure-o como armazenamento de back-end para o cluster

1. Configurar o cluster

   Conecte-se à interface de configuração do avere vFXT (painel de controle do avere) para personalizar as configurações do cluster. Aceite o monitoramento de suporte e adicione seu sistema de armazenamento se você estiver usando um data center local.

   * [Aceder ao cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Habilitar suporte](avere-vfxt-enable-support.md)
   * [Configurar o armazenamento](avere-vfxt-add-storage.md) (se necessário)

1. Montar clientes

   Siga as diretrizes em [montar o cluster avere vFXT](avere-vfxt-mount-clients.md) para saber mais sobre o balanceamento de carga e como os computadores cliente devem montar o cluster.

1. Adicionar dados (se necessário)

   Como o avere vFXT é um cache de vários clientes escalonáveis, a melhor maneira de mover dados para um novo contêiner de armazenamento de back-end é com uma estratégia de cópia de arquivos multifuncional de vários clientes.
   
   Se você precisar mover os dados do conjunto de trabalho para um novo contêiner de BLOB ou outro sistema de armazenamento de back-end, siga as instruções em [movendo dados para o cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Passos seguintes

Continue a [preparar para criar o avere vFXT](avere-vfxt-prereqs.md) para concluir as tarefas de pré-requisito.
