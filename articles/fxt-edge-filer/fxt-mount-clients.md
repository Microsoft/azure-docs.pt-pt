---
title: Montar clientes na Microsoft Azure cluster de filer do FXT Edge
description: Como os computadores cliente NFS podem montar o cache de armazenamento híbrido do Filer do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: ac1263b352e7fdde57dfee6515a8b22400f22b06
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256035"
---
# <a name="tutorial-mount-the-cluster"></a>Tutorial: montar o cluster

Este tutorial ensina como montar clientes NFS para o cluster de arquivos do Azure FXT Edge. Os clientes montam os caminhos de namespace virtual que você atribuiu quando adicionou o armazenamento de back-end. 

Este tutorial ensina: 

> [!div class="checklist"]
> * Estratégias para clientes de balanceamento de carga na faixa de endereços IP voltados para o cliente
> * Como construir um caminho de montagem de um endereço IP voltado para o cliente e junção de namespace
> * Quais argumentos usar em um comando Mount

Este tutorial leva aproximadamente 45 minutos para ser concluído.

## <a name="steps-to-mount-the-cluster"></a>Etapas para montar o cluster

Siga estas etapas para conectar computadores cliente ao cluster de filer do Azure FXT Edge.

1. Decida como balancear a carga do tráfego do cliente entre os nós do cluster. Leia [balancear a carga do cliente](#balance-client-load), abaixo, para obter detalhes. 
1. Identifique o endereço IP do cluster e o caminho de junção a serem montados.
1. Determine o caminho voltado para o cliente para a montagem.
1. Emita o [comando Mount](#use-recommended-mount-command-options), com os argumentos apropriados.

## <a name="balance-client-load"></a>Balancear a carga do cliente

Para ajudar a balancear as solicitações do cliente entre todos os nós no cluster, você deve montar clientes para a faixa completa de endereços IP voltados para o cliente. Há várias maneiras de automatizar essa tarefa.

Para saber mais sobre o balanceamento de carga de DNS Round Robin para o cluster, leia [Configurar o DNS para o cluster de arquivos do Azure FXT Edge](fxt-configure-network.md#configure-dns-for-load-balancing). Para usar esse método, você deve manter um servidor DNS, que não é explicado nesses artigos.

Um método mais simples para instalações pequenas é usar um script para atribuir endereços IP em todo o intervalo no tempo de montagem do cliente. 

Outros métodos de balanceamento de carga podem ser apropriados para sistemas grandes ou complicados. Consulte seu representante da Microsoft ou abra uma [solicitação de suporte](fxt-support-ticket.md) para obter ajuda. (No momento, *não há suporte para* a Azure Load Balancer com o filer do Azure FXT Edge.)

## <a name="create-the-mount-command"></a>Criar o comando Mount 

No cliente, o comando ``mount`` mapeia o servidor virtual (vserver) no cluster do Azure FXT Edge para um caminho no sistema de arquivos local. 

O formato é ``mount <FXT cluster path> <local path> {options}``

Há três elementos para o comando mount: 

* caminho do cluster-uma combinação de endereço IP e caminho de junção de namespace descrita abaixo
* caminho local-o caminho no cliente 
* opções de comando de montagem-(listadas em [usar opções de comando de montagem recomendadas](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Criar o caminho do cluster

O caminho do cluster é uma combinação do *endereço IP* vserver mais o caminho para uma *junção de namespace*. A junção do namespace é um caminho virtual que você definiu quando [adicionou o sistema de armazenamento](fxt-add-storage.md#create-a-junction).

Por exemplo, se você usou ``/fxt/files`` como seu caminho de namespace, os clientes montarão *Endereço_IP*:/FXT/files em seu ponto de montagem local. 

![Caixa de diálogo "Adicionar nova junção" com/avere/files no campo caminho do namespace](media/fxt-mount/fxt-junction-example.png)

O endereço IP é um dos endereços IP voltados para o cliente definido para o vserver. Você pode encontrar o intervalo de IPs voltados para o cliente em dois locais no painel de controle do cluster:

* Tabela **VServers** (guia painel)- 

  ![Guia painel do painel de controle com a guia VServer selecionada na tabela de dados abaixo do grafo e a seção de endereço IP em círculo](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Página de configurações de **rede voltadas** para o cliente- 

  ![Configurações > VServer > página de configuração de rede voltada para o cliente com um círculo em volta da seção intervalo de endereços da tabela para um VServer específico](media/fxt-mount/fxt-ip-addresses-settings.png)

Combine o endereço IP e o caminho do namespace para formar o caminho do cluster para o comando Mount. 

Comando de montagem de cliente de exemplo: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Criar o caminho local

O caminho local para o comando Mount cabe a você. Você pode definir qualquer estrutura de caminho que desejar como parte do namespace virtual. Crie um namespace e um caminho local que seja conveniente para o fluxo de trabalho do cliente. 

Para obter mais informações sobre o namespace voltado para o cliente, leia a [visão geral do namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)do guia de configuração do cluster.

Além dos caminhos, inclua as opções de [comando de montagem](#use-recommended-mount-command-options) descritas abaixo ao montar cada cliente.

### <a name="use-recommended-mount-command-options"></a>Usar opções de comando de montagem recomendadas

Para garantir uma montagem de cliente sem interrupção, passe essas configurações e argumentos no comando mount: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Configurações necessárias | |
--- | --- 
``hard`` | As montagens suaves no cluster de arquivos do Azure FXT Edge são associadas a falhas de aplicativos e à possível perda de dados. 
``proto=netid`` | Essa opção dá suporte ao tratamento apropriado de erros de rede NFS.
``mountproto=netid`` | Essa opção dá suporte ao tratamento apropriado de erros de rede para operações de montagem.
``retry=n`` | Defina ``retry=30`` para evitar falhas de montagem transitórias. (Um valor diferente é recomendado em montagens de primeiro plano.)

| Configurações preferenciais  | |
--- | --- 
``nointr``            | Se seus clientes usam kernels de sistema operacional mais antigos (antes de abril de 2008) que dão suporte a essa opção, use-o. A opção "INTR" é o padrão.

## <a name="next-steps"></a>Passos seguintes

Depois de montar clientes, você pode testar seu fluxo de trabalho e começar a usar o cluster.

Se você precisar mover dados para um novo Filer do Cloud Core, aproveite a estrutura do cache usando a ingestão de dados paralelos. Algumas estratégias são descritas em [movendo dados para um cluster vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT for Azure é um produto baseado em nuvem que usa a tecnologia de cache muito semelhante ao Azure FXT Edge Arquivor.)

Leia [monitorar o status de hardware do arquivo de borda do Azure FXT Edge](fxt-monitor.md) se precisar solucionar problemas de hardware. 
