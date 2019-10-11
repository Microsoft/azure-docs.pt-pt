---
title: Microsoft Azure configuração do cluster Filer do FXT Edge-adicionar nós
description: Como adicionar nós ao cache de armazenamento do Filer do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 85ab9aaa3e184af7aa71a31eb3d8de1a20639c2a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254924"
---
# <a name="tutorial-add-cluster-nodes"></a>Tutorial: adicionar nós de cluster 

Um novo cluster de arquivos do Azure FXT Edge é criado com apenas um nó. Você deve adicionar pelo menos mais dois nós e habilitar a alta disponibilidade antes de fazer outras configurações. 

Este tutorial explica como adicionar nós de cluster e habilitar o recurso de alta disponibilidade (HA). 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Como adicionar nós ao cluster FXT
> * Como habilitar a HA

As etapas neste tutorial levam aproximadamente 45 minutos para serem concluídas.

Antes de iniciar este tutorial, ligue os nós que você deseja adicionar e [defina suas senhas iniciais](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. carregar a página de nós de cluster

Abra o painel de controle do cluster em um navegador da Web e entre como administrador. (As instruções detalhadas estão no artigo de visão geral, em [abrir as páginas de configurações](fxt-cluster-create.md#open-the-settings-pages).)

O painel de controle mostra a guia **painel** quando ele é aberto. 

![Painel do painel de controle (primeira guia)](media/fxt-cluster-config/dashboard-1-node.png)

Esta imagem mostra o painel de um cluster recém-criado, com um único nó.

## <a name="2-locate-the-node-to-add"></a>2. Localize o nó a ser adicionado

Para adicionar nós, clique na guia **configurações** e escolha a página **nós FXT** na seção **cluster** .

![Guia Configurações do painel de controle (segunda guia) com cluster > nós do FXT carregados](media/fxt-cluster-config/settings-fxt-nodes.png)

A lista **nós FXT-Unassociated** mostra todos os nós FXT não atribuídos (a maioria dos data centers tem apenas alguns. Localize os nós FXT que você deseja adicionar ao cluster.

> [!Tip] 
> Se você não conseguir localizar o nó desejado na lista não **unida** , verifique se ele atende a estes requisitos:
> 
> * Ele está ligado e teve uma [senha raiz definida](fxt-node-password.md).
> * Ele é conectado a uma rede que você pode acessar. Se você usar VLANs, ela deverá estar na mesma VLAN que o cluster.
> * Ele pode ser detectado com o protocolo Bonjour. 
>
>   Algumas configurações de firewall bloqueiam as portas TCP/UDP usadas pelo Bonjour, o que impede que o sistema operacional FXT detecte automaticamente os nós.
> 
> Se o nó que você deseja adicionar não estiver na lista, tente estas soluções: 
> 
> * Clique no botão de **descoberta manual** para encontrá-lo por endereço IP.
> 
> * Atribua manualmente endereços IP temporários. Isso é raro, mas pode ser necessário se você usar VLANs marcadas e se os nós não estiverem na rede correta, ou se a sua rede não permitir endereços IP atribuídos automaticamente. Siga as instruções na versão herdada deste documento para [definir manualmente um endereço IP estático](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

O nome do nó, o endereço IP, a versão do software e o status de qualificação são exibidos na lista. Normalmente, a coluna **status** diz "quer ingressar" ou descreve um problema de sistema ou de hardware que torna o nó inelegível para ingressar no cluster.

A coluna **ações** tem botões que permitem adicionar o nó ao cluster ou atualizar seu software. O botão atualizar instala automaticamente a versão do software que corresponde aos nós que já estão no cluster.

Todos os nós em um cluster devem usar a mesma versão do sistema operacional, mas você não precisa atualizar o software antes de adicionar um nó. Depois de clicar no botão **permitir ingresso** , o processo de ingresso no cluster verifica e instala automaticamente o software do sistema operacional que corresponde à versão no cluster.

Para saber mais sobre as opções nesta página, leia nós do [ **cluster** > **FXT** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) no guia de configuração do cluster.

## <a name="3-click-the-allow-to-join-button"></a>3. Clique no botão "permitir junção" 

Clique no botão **permitir para ingressar*** na coluna **ações** para o nó que você deseja adicionar.

Depois que você clicar no botão, o status do nó poderá mudar à medida que seu software for atualizado na preparação para adicioná-lo ao cluster. 

A imagem abaixo mostra um nó que está no processo de ingressar no cluster (muito provavelmente, está obtendo uma atualização do sistema operacional antes de ser adicionada). Nenhum botão aparece na coluna **ações** para os nós que estão no processo de serem adicionados ao cluster.

![uma linha da tabela node, mostrando o nome de um nó, o endereço IP, a versão do software, a mensagem "permissão para ingressar" e uma última coluna em branco](media/fxt-cluster-config/node-join-in-process.png)

Após alguns instantes, o novo nó deve aparecer na lista de nós de cluster na parte superior da página de configurações de **nós do FXT** . 

Repita esse processo para adicionar os outros nós ao cluster. Você não precisa esperar que um nó termine de ingressar no cluster antes de iniciar outro.

## <a name="enable-high-availability"></a>Habilitar alta disponibilidade

Depois de adicionar um segundo nó ao cluster, você poderá ver uma mensagem de aviso no painel do painel de controle que o recurso de alta disponibilidade não está configurado. 

HA (alta disponibilidade) permite que os nós de cluster compensam uns aos outros se um falhar. A HA não está habilitada por padrão.

![Guia painel com a mensagem "o cluster tem mais de um nó, mas a HA não está habilitada..." na tabela condições](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Não habilite a HA até que você tenha pelo menos três nós no cluster.

Siga este procedimento para ativar a HA: 

1. Carregue a página **alta disponibilidade** na seção **cluster** da guia **configurações** .

   ![Página de configuração de HA (cluster > alta disponibilidade). A caixa de seleção "Habilitar HA" está na parte superior e o botão enviar está na parte inferior.](media/fxt-cluster-config/enable-ha.png)

2. Clique na caixa rotulada **habilitar ha** e clique no botão **Enviar** . 

Um alerta é exibido no **painel** para confirmar se a ha está habilitada.

![Tabela do painel mostrando a mensagem "a HA agora está totalmente configurada"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Passos seguintes

Depois de adicionar todos os nós no cluster, continue a instalação Configurando o armazenamento de longo prazo do cluster.

> [!div class="nextstepaction"]
> [Adicionar armazenamento de back-end e configurar o namespace virtual](fxt-add-storage.md)