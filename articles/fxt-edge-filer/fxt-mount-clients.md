---
title: Monte clientes no cluster Microsoft Azure FXT Edge Filer
description: Como as máquinas de cliente NFS podem montar o cache de armazenamento híbrido Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 43223db298e4ad170ea6d0687a342b3aee35500e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130773"
---
# <a name="tutorial-mount-the-cluster"></a>Tutorial: Monte o cluster

Este tutorial ensina-o a montar clientes NFS no cluster Azure FXT Edge Filer. Os clientes montam os caminhos de espaço de nome virtual que atribuiu quando adicionou armazenamento de back-end.

Este tutorial ensina:

> [!div class="checklist"]
> * Estratégias para equilibrar a carga dos clientes em toda a gama de endereços IP voltados para o cliente
> * Como construir um caminho de montagem a partir de um endereço IP virado para o cliente e junção de espaço de nome
> * Que argumentos usar num comando de montagem

Este tutorial leva aproximadamente 45 minutos para ser concluído.

## <a name="steps-to-mount-the-cluster"></a>Passos para montar o cluster

Siga estes passos para ligar as máquinas do cliente ao seu cluster Azure FXT Edge Filer.

1. Decida como equilibrar o tráfego de clientes entre os seus nós de cluster. Leia [Balance a carga do cliente,](#balance-client-load)abaixo, para mais detalhes.
1. Identifique o endereço IP do cluster e o caminho de junção para montar.
1. Determine o caminho virado para o cliente para o monte.
1. Emita o comando do [monte,](#use-recommended-mount-command-options)com argumentos apropriados.

## <a name="balance-client-load"></a>Equilibrar a carga do cliente

Para ajudar a equilibrar os pedidos dos clientes entre todos os nós do cluster, deverá montar os clientes para toda a gama de endereços IP voltados para o cliente. Há várias formas de automatizar esta tarefa.

Para saber mais sobre o equilíbrio de carga dNS de robin redondo para o cluster, leia [Configure DNS para o cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing). Para utilizar este método, deve manter um servidor DNS, o que não é explicado nestes artigos.

Um método mais simples para pequenas instalações é usar um script para atribuir endereços IP em toda a gama no tempo de montagem do cliente.

Outros métodos de equilíbrio de carga podem ser adequados para sistemas grandes ou complicados. Consulte o seu representante da Microsoft ou abra um pedido de ajuda de [suporte.](fxt-support-ticket.md) (O Azure Load Balancer não é atualmente *suportado* com o Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>Criar o comando do suporte

Do seu cliente, o ``mount`` comando mapeia o servidor virtual (vserver) no cluster Azure FXT Edge Filer para um caminho no sistema de ficheiros local.

O formato é``mount <FXT cluster path> <local path> {options}``

Há três elementos no comando do monte:

* cluster path - uma combinação de endereço IP e caminho de junção espaço de nome descrito abaixo
* caminho local - o caminho no cliente
* montar opções de comando - (listadas em [Utilização recomendadas opções](#use-recommended-mount-command-options)de comando de montagem )

### <a name="create-the-cluster-path"></a>Criar o caminho do cluster

O caminho do cluster é uma combinação do *endereço IP* do vserver mais o caminho para uma *junção espaço-nome*. A junção espaço de nome é um caminho virtual que definiu quando [adicionou o sistema de armazenamento.](fxt-add-storage.md#create-a-junction)

Por exemplo, se ``/fxt/files`` usasse como caminho do espaço de nome, os seus clientes montariam *IP_address*:/fxt/ficheiros até ao ponto de montagem local.

![Diálogo "Adicionar nova junção" com /avere/ficheiros no campo de caminho espaço de nome](media/fxt-mount/fxt-junction-example.png)

O endereço IP é um dos endereços IP orientados para o cliente definidos para o servidor de vserver. Pode encontrar a gama de IPs voltados para o cliente em dois locais do Painel de Controlo do Cluster:

* **Tabela VServers** (separador dashboard) -

  ![Separador de painel de instrumentos do Painel de Controlo com o separador VServer selecionado na tabela de dados abaixo do gráfico, e a secção de endereçoIP circulou](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Página** de definições de rede virada para o cliente -

  ![Definições > Página de configuração da Rede de Localização do Cliente > VServer com um círculo em torno da secção gama de endereços da tabela para um servidor de servidor específico](media/fxt-mount/fxt-ip-addresses-settings.png)

Combine o endereço IP e o caminho do espaço de nome para formar o caminho do cluster para o comando do suporte.

Exemplo de comando de montagem de cliente:``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Criar o caminho local

O caminho local para o comando do monte depende de si. Pode definir qualquer estrutura de caminho que quiser como parte do espaço de nome virtual. Desenhe um espaço de nome e um caminho local que seja conveniente para o fluxo de trabalho do seu cliente.

Para mais informações sobre o espaço de nome sonorizador esquelético do Guia de Configuração [do](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)Cluster.

Além dos caminhos, inclua as [opções](#use-recommended-mount-command-options) de comando de montagem descritas abaixo ao montar cada cliente.

### <a name="use-recommended-mount-command-options"></a>Utilize opções de comando de montagem recomendadas

Para garantir uma montagem perfeita do cliente, passe estas configurações e argumentos no seu comando de montagem:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Definições necessárias | |
--- | ---
``hard`` | Os suportes macios para o cluster Azure FXT Edge Filer estão associados a falhas de aplicação e possível perda de dados.
``proto=netid`` | Esta opção suporta o tratamento adequado dos erros de rede NFS.
``mountproto=netid`` | Esta opção suporta o tratamento adequado dos erros de rede para as operações de montagem.
``retry=n`` | Preparar ``retry=30`` para evitar falhas transitórias de montagem. (Recomenda-se um valor diferente em suportes de primeiro plano.)

| Definições preferenciais  | |
--- | ---
``nointr``            | Se os seus clientes utilizarem núcleos oss oficiais mais antigos (antes de abril de 2008) que suportem esta opção, use-o. A opção "intr" é o padrão.

## <a name="next-steps"></a>Passos seguintes

Depois de ter clientes montados, pode testar o seu fluxo de trabalho e começar com o seu cluster.

Se precisar de mover dados para um novo filer de núcleo de nuvem, aproveite a estrutura da cache utilizando a ingerir dados paralelos. Algumas estratégias são descritas em [mover dados para um cluster vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT para Azure é um produto baseado na nuvem que utiliza tecnologia de cache muito semelhante ao Azure FXT Edge Filer.)

Leia o estado de [hardware do Monitor Azure FXT Edge Filer](fxt-monitor.md) se precisar de resolver problemas de hardware.
