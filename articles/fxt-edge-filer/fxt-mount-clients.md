---
title: Monte clientes no cluster Microsoft Azure FXT Edge Filer
description: Como as máquinas de clientes NFS podem montar a cache de armazenamento híbrido Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: c9ee50c146271106e2b9c693702827b2e1b52199
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219552"
---
# <a name="tutorial-mount-the-cluster"></a>Tutorial: Monte o aglomerado

Este tutorial ensina-o a montar clientes NFS no cluster Azure FXT Edge Filer. Os clientes montam os caminhos virtuais do espaço de nome que atribuiu quando adicionamos armazenamento de back-end.

Este tutorial ensina:

> [!div class="checklist"]
>
> * Estratégias para equilibrar os clientes em toda a gama de endereços IP voltados para o cliente
> * Como construir um caminho de montagem a partir de um endereço IP virado para o cliente e junção de espaço de nome
> * Que argumentos usar num comando de montagem

Este tutorial leva aproximadamente 45 minutos para ser concluído.

## <a name="steps-to-mount-the-cluster"></a>Passos para montar o cluster

Siga estes passos para ligar as máquinas clientes ao seu cluster Azure FXT Edge Filer.

1. Decida como equilibrar o tráfego de clientes entre os seus nós de cluster. Leia [a carga do cliente balance,](#balance-client-load)abaixo, para mais detalhes.
1. Identifique o endereço IP do cluster e o caminho de junção para montar.
1. Determine o caminho virado para o cliente para o monte.
1. Emite o [comando de montagem,](#use-recommended-mount-command-options)com argumentos apropriados.

## <a name="balance-client-load"></a>Equilibrar carga do cliente

Para ajudar a equilibrar os pedidos dos clientes entre todos os nós do cluster, deverá montar os clientes na gama completa de endereços IP voltados para o cliente. Há várias formas de automatizar esta tarefa.

Para saber mais sobre o equilíbrio de carga de DNS redondo para o cluster, leia [Configure DNS para o cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing). Para utilizar este método deve manter um servidor DNS, o que não é explicado nestes artigos.

Um método mais simples para pequenas instalações é usar um script para atribuir endereços IP em toda a gama no tempo de montagem do cliente.

Outros métodos de equilíbrio de carga podem ser adequados para sistemas grandes ou complicados. Consulte o seu representante da Microsoft ou abra um pedido de ajuda de [apoio.](fxt-support-ticket.md) (AZure Load Balancer *não* é suportado atualmente com O Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>Criar o comando de montagem

A partir do seu cliente, o ``mount`` comando mapeia o servidor virtual (vserver) no cluster Azure FXT Edge Filer para um caminho no sistema de ficheiros local.

O formato é ``mount <FXT cluster path> <local path> {options}``

Há três elementos no comando de montagem:

* cluster path - uma combinação de endereço IP e caminho de junção namespace descrito abaixo
* caminho local - o caminho no cliente
* opções de comando de montagem - (listado em [Opções de comando de montagem recomendadas](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Criar o caminho do cluster

O caminho do cluster é uma combinação do *endereço IP* vserver mais o caminho para uma *junção de espaço de nome*. A junção do espaço de nome é um caminho virtual que definiu quando [adicionou o sistema de armazenamento.](fxt-add-storage.md#create-a-junction)

Por exemplo, se usasse ``/fxt/files`` como caminho do espaço de identificação, os seus clientes montariam *IP_address*:/fxt/ficheiros para o seu ponto de montagem local.

![Diálogo "Adicionar nova junção" com /avere/ficheiros no campo do caminho do espaço de nome](media/fxt-mount/fxt-junction-example.png)

O endereço IP é um dos endereços IP virados para o cliente definidos para o vserver. Pode encontrar a gama de IPs voltados para o cliente em dois lugares no Painel de Controlo do cluster:

* **Tabela VServers** (separador dashboard) -

  ![Separador do painel de controlo do Painel de Controlo com o separador VServer selecionado na tabela de dados abaixo do gráfico e a secção de endereço IP circulou](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Página de** definições de rede virada para o cliente -

  ![As definições > VServer > página de configuração de rede virada para o cliente com um círculo em torno da secção de alcance de endereço da tabela para um vserver particular](media/fxt-mount/fxt-ip-addresses-settings.png)

Combine o endereço IP e o caminho do espaço de nome para formar o caminho do cluster para o comando de montagem.

Exemplo de comando de montagem de clientes: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Criar o caminho local

O caminho local para o comando do monte é consigo. Pode definir qualquer estrutura de caminho que queira como parte do espaço de nome virtual. Desenhe um espaço de nome e um caminho local que seja conveniente para o fluxo de trabalho do seu cliente.

Para obter mais informações sobre o espaço de nome virado para o cliente, leia a visão geral do [espaço de identificação](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)do Guia de Configuração do Cluster .

Além dos caminhos, inclua as [opções de comando de montagem descritas](#use-recommended-mount-command-options) abaixo ao montar cada cliente.

### <a name="use-recommended-mount-command-options"></a>Utilize opções recomendadas de comando de montagem

Para garantir uma montagem de cliente sem emenda, passe estas definições e argumentos no seu comando de montagem:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Configurações necessárias | Descrição |
--- | ---
``hard`` | Os suportes macios para o cluster Azure FXT Edge Filer estão associados a falhas de aplicação e possíveis perda de dados.
``proto=netid`` | Esta opção suporta o manuseamento adequado de erros de rede NFS.
``mountproto=netid`` | Esta opção suporta o manuseamento adequado de erros de rede para operações de montagem.
``retry=n`` | Preparado ``retry=30`` para evitar falhas de montagem transitórias. (Recomenda-se um valor diferente em suportes de primeiro plano.)

| Definições preferenciais  | Descrição |
--- | ---
``nointr``            | Se os seus clientes utilizarem núcleos de OS mais antigos (antes de abril de 2008) que suportem esta opção, utilize-a. A opção "intr" é o padrão.

## <a name="next-steps"></a>Passos seguintes

Depois de ter clientes montados, pode testar o seu fluxo de trabalho e começar com o seu cluster.

Se precisar de mover dados para um novo ficheiro de núcleo de nuvem, aproveite a estrutura da cache utilizando a ingestão de dados paralelos. Algumas estratégias são descritas em [Mover dados para um cluster vFXT.](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest) (Avere vFXT for Azure é um produto baseado na nuvem que utiliza tecnologia de caching muito semelhante ao Azure FXT Edge Filer.)

Leia [o estado do hardware do Monitor Azure FXT Edge Filer](fxt-monitor.md) se precisar de resolver problemas de hardware.
