---
title: 'Tutorial: Adicione os nó num cluster Azure FXT Edge Filer'
description: Aprenda a adicionar nós de cluster à cache de armazenamento Azure FXT Edge Filer e ative a função De Alta Disponibilidade (HA).
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 5b62927930212fc7e59fc4329a29ceecbe2815e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185337"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Tutorial: Adicione os nóns de cluster a um cluster Azure FXT Edge Filer

Um novo cluster Azure FXT Edge Filer é criado com apenas um nó. Deve adicionar pelo menos mais dois nós e ativar a alta disponibilidade antes de fazer outra configuração. 

Este tutorial explica como adicionar nós de cluster e ativar a funcionalidade De Alta Disponibilidade (HA). 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Como adicionar nódes ao cluster FXT
> * Como ativar o HA

Os passos neste tutorial demoram aproximadamente 45 minutos a ser concluídos.

Antes de iniciar este tutorial, ligue os nós que pretende adicionar e [desemque as suas palavras-passe iniciais](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Carregue a página de nós de cluster

Abra o Painel de Controlo do cluster num navegador web e inscreva-se como administrador. (Instruções detalhadas estão no artigo geral, em [abertura das páginas Definições](fxt-cluster-create.md#open-the-settings-pages).)

O Painel de Controlo mostra o **separador dashboard** quando abre. 

![Painel de controlo dashboard (primeira aba)](media/fxt-cluster-config/dashboard-1-node.png)

Esta imagem mostra o painel de instrumentos de um cluster recém-criado, com um único nó.

## <a name="2-locate-the-node-to-add"></a>2. Localizar o nó para adicionar

Para adicionar nós, clique no separador **Definições** e escolha a página **fxt nodes** na secção **Cluster.**

![Separador de definições do painel de controlo (segundo separador) com cluster > nos nós FXT carregados](media/fxt-cluster-config/settings-fxt-nodes.png)

A lista **FXT Nodes - Unjoined** mostra todos os nós FXT não atribuídos (a maioria dos centros de dados tem apenas alguns. Encontre os nós FXT que pretende adicionar ao cluster.

> [!Tip] 
> Se não conseguir encontrar o nó que deseja na lista **Não Unida,** verifique se cumpre estes requisitos:
> 
> * É alimentado e tem uma [senha de raiz definida](fxt-node-password.md).
> * Está ligado a uma rede a que pode aceder. Se utilizar VLANs, deve estar no mesmo VLAN que o cluster.
> * Pode ser detetado com o protocolo Bonjour. 
>
>   Algumas definições de firewall bloqueiam as portas TCP/UDP utilizadas pela Bonjour, o que impede o sistema operativo FXT de detetar automaticamente os nós.
> 
> Se o nó que pretende adicionar não estiver na lista, experimente estas soluções: 
> 
> * Clique no botão **Manual Descobrir** para encontrá-lo por endereço IP.
> 
> * Atribuir manualmente endereços IP temporários. Isto é raro, mas pode ser necessário se utilizar VLANs marcados e os nós não estiverem na rede correta, ou a sua rede não permite endereços IP autoatribuídos. Siga as instruções na versão antiga deste documento para [definir manualmente um endereço IP estático](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

O nome do nó, endereço IP, versão do software e estado de elegibilidade são apresentados na lista. Tipicamente, a coluna **Status** ou diz "Quer aderir" ou descreve um problema de sistema ou hardware que torna o nó inelegível para se juntar ao cluster.

A coluna **Ações** tem botões que permitem adicionar o nó ao cluster ou atualizar o seu software. O botão de atualização instala automaticamente a versão de software que corresponde aos nós já no cluster.

Todos os nós de um cluster devem usar a mesma versão do SO, mas não precisa de atualizar o software antes de adicionar um nó. Depois de clicar no botão **Permitir juntar-se,** o processo de ligação do cluster verifica automaticamente e instala o software OS que corresponde à versão do cluster.

Para saber mais sobre as opções desta página, leia [ **os**nós de Cluster  >  **FXT** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) no Guia de Configuração do Cluster.

## <a name="3-click-the-allow-to-join-button"></a>3. Clique no botão "Permitir aderir" 

Clique no botão **Permitir A Junção*** na coluna **Ações** para o nó que pretende adicionar.

Depois de clicar no botão, o estado do nó pode mudar à medida que o seu software é atualizado para adicioná-lo ao cluster. 

A imagem abaixo mostra um nó que está em processo de adesão ao cluster (provavelmente, está recebendo uma atualização de SO antes de ser adicionado). Não aparecem botões na coluna **Ações** para os nós que estão em vias de ser adicionados ao cluster.

![uma linha da tabela de nó, mostrando o nome de um nó, endereço IP, versão de software, a mensagem "Permitido juntar-se", e uma última coluna em branco](media/fxt-cluster-config/node-join-in-process.png)

Após alguns momentos, o novo nó deve aparecer na lista de nós de cluster no topo da página de definições **de Nó FXT.** 

Repita este processo para adicionar os outros nós ao seu cluster. Não é preciso esperar que um nó termine de se juntar ao agrupamento antes de começar outro.

## <a name="enable-high-availability"></a>Ativar alta disponibilidade

Depois de ter adicionado um segundo nó ao seu cluster, poderá ver uma mensagem de aviso no painel de controlo do painel de instrumentos que a funcionalidade de alta disponibilidade não está configurada. 

A alta disponibilidade (HA) permite que os nós de cluster compensem uns aos outros se um cair. Ha não é ativado por defeito.

![Separador do painel com a mensagem "O cluster tem mais do que um nó, mas HA não está ativado..." na tabela Condições](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Não ative ha até ter pelo menos três nós no aglomerado.

Siga este procedimento para ligar HA: 

1. Carregue a página **de Alta Disponibilidade** na secção **Cluster** do **separador Definições.**

   ![Página de configuração HA (Cluster > Alta Disponibilidade). A caixa de verificação "Enable HA" está na parte superior e o botão de submissão está na parte inferior.](media/fxt-cluster-config/enable-ha.png)

2. Clique na caixa com a etiqueta **Enable HA** e clique no botão **Enviar.** 

Aparece um alerta no **Painel de Instrumentos** para confirmar que o HA está ativado.

![Tabela do painel mostrando a mensagem "HA está agora totalmente configurada"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Passos seguintes

Depois de adicionar todos os nós no seu cluster, continue a configuração configurando o armazenamento a longo prazo do seu cluster.

> [!div class="nextstepaction"]
> [Adicione o armazenamento back-end e crie o espaço de nome virtual](fxt-add-storage.md)