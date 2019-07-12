---
title: Clientes de montagem no cluster de ficheiros do Microsoft Azure FXT Edge
description: Como máquinas de cliente NFS podem montar o cache de ficheiros do Azure FXT Edge de armazenamento híbrida
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 5471bf4041275d5988414def99dd2130f51fbb80
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828021"
---
# <a name="tutorial-mount-the-cluster"></a>Tutorial: Montar o cluster

Este tutorial ensina a montar os clientes NFS para o cluster de ficheiros do Azure FXT Edge. Os clientes montagem os caminhos de virtual de espaço de nomes que atribuiu quando foi adicionada ao armazenamento de back-end. 

Esse tutorial ensina: 

> [!div class="checklist"]
> * Estratégias para clientes de balanceamento de carga entre o intervalo de endereços IP com clientes
> * Como construir um caminho de montagem de uma junção de endereço e o espaço de nomes IP com clientes
> * Os argumentos para utilizar um comando de montagem

Este tutorial demora, aproximadamente 45 minutos a concluir.

## <a name="steps-to-mount-the-cluster"></a>Passos para montar o cluster

Siga estes passos para ligar computadores cliente para o cluster de ficheiros do Azure FXT Edge.

1. Decidir como tráfego de cliente de balanceamento de carga entre os nós do cluster. Leia [carga de cliente do saldo](#balance-client-load), abaixo, para obter detalhes. 
1. Identifique o caminho de endereço e a junção IP de cluster para montar.
1. Determine o caminho de com clientes para a montagem.
1. Problema do [comando de montagem](#use-recommended-mount-command-options), com argumentos adequados.

## <a name="balance-client-load"></a>Carga de cliente de saldo

Para ajudar a pedidos de cliente de equilíbrio entre todos os nós do cluster, deve montar os clientes a gama completa de endereços IP com clientes. Existem várias formas para automatizar esta tarefa.

Para saber mais sobre o round robin DNS balanceamento de carga para o cluster, leia [configurar o DNS para o cluster de ficheiros do Azure FXT Edge](fxt-configure-network.md#configure-dns-for-load-balancing). Para utilizar este método tem de manter um servidor DNS, que não é explicado nestes artigos.

Um método mais simples para instalações de pequeno é usar um script para atribuir endereços IP em todo o intervalo em tempo de montagem do cliente. 

Outros métodos de balanceamento de carga podem ser adequados para sistemas de grandes porte ou complicados. Consulte o seu Microsoft representativo ou abra um [pedido de suporte](fxt-support-ticket.md) para obter ajuda. (Balanceador de carga do azure está atualmente *nepodporuje* com filtro de borda do Azure FXT.)

## <a name="create-the-mount-command"></a>Criar o comando de montagem 

A partir do cliente, o ``mount`` mapas do comando do virtual server (vserver) no cluster de ficheiros do Azure FXT Edge para um caminho no sistema de ficheiros local. 

O formato é ``mount <FXT cluster path> <local path> {options}``

Existem três elementos para o comando de montagem: 

* caminho de cluster - uma combinação de IP endereço e espaço de nomes junção do caminho descrito abaixo
* caminho local - o caminho no cliente 
* Opções de comando - de montagem (listados na [utilização recomendada opções do comando de montagem](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Criar o caminho de cluster

O caminho de cluster é uma combinação da vserver *endereço IP* além do caminho para um *junção de espaço de nomes*. A junção de espaço de nomes é um caminho virtual que definiu quando [adicionado o sistema de armazenamento](fxt-add-storage.md#create-a-junction).

Por exemplo, se utilizou ``/fxt/files`` como o caminho de espaço de nomes, os clientes se montaria *IP_address*: / fxt/ficheiros para o ponto de montagem local. 

![Caixa de diálogo "Adicionar novo junção" com os ficheiros/avere/no campo do caminho de espaço de nomes](media/fxt-mount/fxt-junction-example.png)

O endereço IP é um dos endereços IP com clientes definidos para o vserver. Pode encontrar o intervalo de IPs em dois locais do painel de controlo do cluster de com de clientes:

* **VServers** tabela (separador Dashboard) - 

  ![Separador dashboard do painel de controle com o separador de VServer selecionado na tabela de dados abaixo o gráfico e a secção de endereço IP com círculos](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Rede com acesso do cliente** página de definições - 

  ![Definições > VServer > página de configuração de rede com acesso do cliente com um círculo à volta a secção de intervalo de endereços da tabela para um determinado vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Combine o endereço IP e o caminho de espaço de nomes para formar o caminho de cluster para o comando de montagem. 

Comando de montagem de cliente de exemplo: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Criar o caminho local

O caminho local para o comando de montagem cabe a. Pode definir qualquer estrutura de caminho que queira como parte do espaço de nomes virtual. Crie um espaço de nomes e o caminho local, que é conveniente para seu fluxo de trabalho do cliente. 

Para obter mais informações sobre o espaço de nomes com clientes, leia o guia de configuração de Cluster [descrição geral do espaço de nomes](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html).

Os caminhos, além de incluir o [opções de comando de montagem](#use-recommended-mount-command-options) descrito a seguir ao montar a cada cliente.

### <a name="use-recommended-mount-command-options"></a>Utilize as opções de comando de montagem recomendada

Para garantir uma montagem de cliente totalmente integrado, passa estas definições e os argumentos do comando de montagem: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Definições necessárias | |
--- | --- 
``hard`` | Monta de forma recuperável para o cluster de ficheiros do Azure FXT Edge está associadas com falhas da aplicação e possível perda de dados. 
``proto=netid`` | Esta opção suporta manipulação adequada de erros de rede NFS.
``mountproto=netid`` | Esta opção suporta manipulação adequada de erros de rede para operações de montagem.
``retry=n`` | Definir ``retry=30`` para evitar falhas de montagem transitório. (Um valor diferente é recomendado em primeiro plano monta.)

| Definições preferenciais  | |
--- | --- 
``nointr``            | Se os clientes utilizam mais antigos kernels do SO (antes de Abril de 2008) que suportam esta opção, utilizá-lo. A opção "intr" é a predefinição.

## <a name="next-steps"></a>Passos Seguintes

Após ter montado clientes, pode testar o seu fluxo de trabalho e começar a utilizar com o seu cluster.

Se precisar de mover dados para um novo filtro de núcleos na cloud, tirar partido da estrutura de cache através da utilização de dados paralelos de ingestão. Algumas estratégias descritas [mover dados para um cluster de vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT para o Azure é um produto com base na cloud que utiliza tecnologia colocação em cache, muito parecida com o filtro de borda de FXT do Azure.)

Leia [estado do hardware de filtro de borda do Monitor do Azure FXT](fxt-monitor.md) se precisar de resolver quaisquer problemas de hardware. 
