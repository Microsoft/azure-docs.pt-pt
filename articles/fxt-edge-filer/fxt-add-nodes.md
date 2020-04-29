---
title: 'Tutorial: Adicione nós a um cluster de filetes de borda Azure FXT'
description: Como adicionar nódosos ao cache de armazenamento Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6251fe8f88b7db25e3c09898540e07754d72fb0d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75551952"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Tutorial: Adicione nós de cluster a um cluster Azure FXT Edge Filer

Um novo cluster Azure FXT Edge Filer é criado com apenas um nó. Deve adicionar pelo menos mais dois nós e permitir uma alta disponibilidade antes de fazer outra configuração. 

Este tutorial explica como adicionar nós de cluster e ativar a funcionalidade de Alta Disponibilidade (HA). 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Como adicionar nós ao cluster FXT
> * Como ativar ha

Os passos deste tutorial demoram aproximadamente 45 minutos a ser concluídos.

Antes de iniciar este tutorial, ligue os nós que pretende adicionar e [definir as suas palavras-passe iniciais](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Carregue a página de nós do cluster

Abra o Painel de Controlo do cluster num navegador web e inscreva-se como administrador. (Instruções detalhadas estão no artigo de visão geral, sob [as páginas De definições abertas](fxt-cluster-create.md#open-the-settings-pages).)

O Painel de Controlo mostra o **separador dashboard** quando abre. 

![Painel de controlo painel painel (primeiro separador)](media/fxt-cluster-config/dashboard-1-node.png)

Esta imagem mostra o painel de instrumentos de um cluster recém-criado, com um único nó.

## <a name="2-locate-the-node-to-add"></a>2. Localizar o nó para adicionar

Para adicionar nós, clique no separador **Definições** e escolha a página **de nós FXT** na secção **Cluster.**

![Separador de definições do painel de controlo (segundo separador) com nódos de cluster > FXT carregados](media/fxt-cluster-config/settings-fxt-nodes.png)

Os **nós FXT - Lista não unida** mostra todos os nós FXT não atribuídos (a maioria dos centros de dados tem apenas alguns. Encontre os nós FXT que pretende adicionar ao cluster.

> [!Tip] 
> Se não encontrar o nó que deseja na lista **não unida,** verifique se satisfaz estes requisitos:
> 
> * É ligado e tem um conjunto de [palavra-passe raiz.](fxt-node-password.md)
> * Está ligado a uma rede a que pode aceder. Se utilizar VLANs, deve estar no mesmo VLAN que o cluster.
> * Pode ser detetado com o protocolo Bonjour. 
>
>   Algumas definições de firewall bloqueiam as portas TCP/UDP utilizadas pela Bonjour, o que impede que o sistema operativo FXT detete automaticamente os nós.
> 
> Se o nó que pretende adicionar não estiver na lista, experimente estas soluções: 
> 
> * Clique no botão **Desconhecer manualmente** para o encontrar por endereço IP.
> 
> * Atribua manualmente endereços IP temporários. Isto é raro, mas pode ser necessário se utilizar VLANs marcados e os nós não estiverem na rede correta, ou se a sua rede não permitir endereços IP autoatribuídos. Siga as instruções na versão antiga deste documento para [definir manualmente um endereço IP estático](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

O nome do nó, endereço IP, versão de software e estado de elegibilidade são apresentados na lista. Normalmente, a coluna **Status** ou diz "Quer aderir" ou descreve um problema de sistema ou hardware que torna o nó inelegível para aderir ao cluster.

A coluna **Ações** tem botões que permitem adicionar o nó ao cluster ou atualizar o seu software. O botão de atualização instala automaticamente a versão do software que corresponde aos nós já no cluster.

Todos os nós de um cluster devem usar a mesma versão do SISTEMA, mas não precisa de atualizar o software antes de adicionar um nó. Depois de clicar no botão **Permitir Juntar,** o processo de adesão do cluster verifica automaticamente e instala o software OS que corresponde à versão do cluster.

Para saber mais sobre as opções nesta página, leia os nós do [ **Cluster** > FXT](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) no Guia de Configuração do Cluster.

## <a name="3-click-the-allow-to-join-button"></a>3. Clique no botão "Permitir juntar-se" 

Clique no botão **Permitir juntar*na**coluna **Ações** para o nó que pretende adicionar.

Depois de clicar no botão, o estado do nó pode mudar à medida que o seu software é atualizado em preparação para adicioná-lo ao cluster. 

A imagem abaixo mostra um nó que está em processo de juntar o cluster (provavelmente, está recebendo uma atualização de OS antes de ser adicionado). Não aparecem botões na coluna **Ações** para nós que estão em processo de ser adicionados ao cluster.

![uma linha da mesa do nó, mostrando o nome de um nó, endereço IP, versão de software, a mensagem "Autorizado a aderir", e uma última coluna em branco](media/fxt-cluster-config/node-join-in-process.png)

Após alguns momentos, o novo nó deve aparecer na lista de nós de cluster no topo da página de definições de **nós fxt.** 

Repita este processo para adicionar os outros nós ao seu cluster. Não é preciso esperar que um nó termine de se juntar ao agrupamento antes de começar outro.

## <a name="enable-high-availability"></a>Ativar alta disponibilidade

Depois de adicionar um segundo nó ao seu cluster, pode ver uma mensagem de aviso no painel de controlo Dashboard de que a funcionalidade de alta disponibilidade não está configurada. 

Alta disponibilidade (HA) permite que os nós do cluster compensem uns aos outros se um descer. Ha não é ativado por padrão.

![Tab dashboard com a mensagem "O cluster tem mais do que um nó, mas HA não está ativado..." na tabela Condições](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Não ative ha até ter pelo menos três nós no aglomerado.

Siga este procedimento para ligar ha: 

1. Carregue a página **de Alta Disponibilidade** na secção **Cluster** do separador **Definições.**

   ![Página de configuração HA (Cluster > Alta Disponibilidade). A caixa de verificação "Enable HA" está na parte superior e o botão de apresentação está na parte inferior.](media/fxt-cluster-config/enable-ha.png)

2. Clique na caixa rotulada **Ativação HA** e clique no botão **Enviar.** 

Um alerta aparece no **Dashboard** para confirmar que ha está ativado.

![Tabela do painel de instrumentos mostrando a mensagem "HA está agora totalmente configurada"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Passos seguintes

Depois de adicionar todos os nós do seu cluster, continue a configurar configurando o armazenamento a longo prazo do seu cluster.

> [!div class="nextstepaction"]
> [Adicione armazenamento de back-end e instale o espaço de nome virtual](fxt-add-storage.md)