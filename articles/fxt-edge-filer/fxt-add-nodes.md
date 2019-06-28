---
title: Configuração de cluster de ficheiros de borda do Microsoft Azure FXT - adicionar nós
description: Como adicionar nós para a cache de armazenamento de ficheiros do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 970639eec8c16540d8d7653f1d8bb01e4a397080
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450500"
---
# <a name="tutorial-add-cluster-nodes"></a>Tutorial: Adicionar nós de cluster 

Um novo cluster de ficheiros do Azure FXT Edge é criado com apenas um nó. Deve adicionar pelo menos dois nós mais e ativar a elevada disponibilidade antes de fazer outra configuração. 

Este tutorial explica como adicionar nós de cluster e ativar a funcionalidade de elevada disponibilidade (HA). 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Como adicionar nós ao FXT cluster
> * Como ativar a HA

Os passos neste tutorial demorar aproximadamente 45 minutos a concluir.

Antes de começar este tutorial, ligar os nós que pretende adicionar e [definir as respetivas palavras-passe inicial](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Carregar a página de nós de Cluster

Abra o painel de controlo do cluster num navegador da web e iniciar sessão como administrador. (Instruções detalhadas são o artigo de descrição geral, em [abra as páginas de definições](fxt-cluster-create.md#open-the-settings-pages).)

O painel de controlo mostra o **Dashboard** separador quando é aberta. 

![Dashboard de painel de controle (primeiro separador)](media/fxt-cluster-config/dashboard-1-node.png)

Esta imagem mostra o dashboard de um cluster recém-criado, com um único nó.

## <a name="2-locate-the-node-to-add"></a>2. Localize o nó para adicionar

Para adicionar nós, clique a **definições** separador e escolha o **FXT nós** página no **Cluster** secção.

![Definições do painel de controlo separador (segunda guia) com o Cluster > nós FXT carregado](media/fxt-cluster-config/settings-fxt-nodes.png)

O **nós FXT - desassociar** lista mostra todos os não atribuídas FXT nós (a maioria dos centros de dados tem apenas alguns. Encontre os nós FXT que pretende adicionar ao cluster.

> [!Tip] 
> Se não é possível localizar o nó pretende ter na **Unjoined** lista, verifique se ele cumpre estes requisitos:
> 
> * Ele é ativado e tenha tido uma [conjunto de palavra-passe de raiz](fxt-node-password.md).
> * Está ligado a uma rede que pode aceder. Se utilizar VLANs, tem de ser estão na mesma VLAN que o cluster.
> * Ele pode ser detetado com o protocolo de Bonjour. 
>
>   Algumas definições de firewall bloqueiam as portas TCP/UDP usadas pelo Bonjour, o que impede que o sistema de operativo FXT detetar automaticamente os nós.
> 
> Se o nó que pretende adicionar não estiver na lista, experimente estas soluções: 
> 
> * Clique nas **Manual detetar** botão para encontrá-lo ao endereço IP.
> 
> * Atribua manualmente endereços IP temporários. Isso é raro, mas pode ser necessário se utiliza VLANs marcados e os nós não estão na rede correta ou sua rede não permite que os endereços IP personalizada atribuídos. Siga as instruções na versão deste documento para legado [definir um endereço IP estático manualmente](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

O nome de nó, o endereço IP, a versão do software e o estado de elegibilidade são apresentados na lista. Normalmente, o **estado** coluna seja diz "Pretende aderir ao" ou descreve um problema de sistema ou do hardware, que faz com que o nó não é elegível para aderirem ao cluster.

O **ações** coluna tem botões que permitem-lhe adicionar o nó ao cluster ou atualizar o software. O botão de atualização instala automaticamente a versão de software que corresponde à já os nós do cluster.

Todos os nós num cluster tem de utilizar a mesma versão do sistema operacional, mas não precisa de atualização de software antes de adicionar um nó. Depois de clicar no **permitir a associação** botão, o processo de ingresso cluster verifica e instala automaticamente o software de sistema operacional que corresponde à versão no cluster.

Para saber mais sobre as opções desta página, leia [ **Cluster** > **FXT nós** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) no guia de configuração de Cluster.

## <a name="3-click-the-allow-to-join-button"></a>3. Clique no botão "Permitir associar" 

Clique nas **permitir para aderir*** botão no **ações** coluna para o nó que pretende adicionar.

Depois de clicar no botão, o estado do nó poderá alterar à medida que o software é atualizado em preparação para adicioná-lo para o cluster. 

A imagem abaixo mostra um nó que está no processo de associar o cluster (muito provavelmente, está a receber uma atualização de SO antes de a ser adicionado). Não existem botões aparecem no **ações** coluna para nós que estão no processo que está a ser adicionado ao cluster.

![uma linha da tabela de nó, que mostra o nome de um nó, endereço IP, versão do software, a mensagem "Autorizado a aderir" e uma última coluna em branco](media/fxt-cluster-config/node-join-in-process.png)

Após alguns instantes, o novo nó deve aparecer na lista de nós de cluster na parte superior a **FXT nós** página de definições. 

Repita este processo para adicionar outros nós ao cluster. Não precisa de esperar por um nó concluir a associar ao cluster antes de iniciar outra.

## <a name="enable-high-availability"></a>Ativar a elevada disponibilidade

Depois de adicionar um segundo nó ao cluster, poderá ver uma mensagem de aviso no painel de controlo do Dashboard que a funcionalidade de elevada disponibilidade não está configurado. 

Elevada disponibilidade (HA) permite que os nós do cluster compensar entre si se um for desativado. HA não está ativada por predefinição.

![Separador dashboard com a mensagem "o cluster tem mais de um nó, mas HA não está ativada..." na tabela de condições](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Não ative a HA até ter, pelo menos, três nós no cluster.

Siga este procedimento para ativar a HA: 

1. Carga a **elevada disponibilidade** página no **Cluster** seção o **definições** separador.

   ![Página de configuração de HA (Cluster > elevada disponibilidade). A "Ativar a HA" caixa de seleção está na parte superior e o botão submit é na parte inferior.](media/fxt-cluster-config/enable-ha.png)

2. Clique na caixa de com o nome **ativar a HA** e clique nas **submeter** botão. 

Será apresentado um alerta sobre o **Dashboard** para confirmar que a HA está ativada.

![Tabela de dashboard que mostra a mensagem "HA é agora totalmente configurada"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Passos Seguintes

Depois de adicionar todos os nós do cluster, continue a configuração, configurar o armazenamento de longo prazo do seu cluster.

> [!div class="nextstepaction"]
> [Adicionar armazenamento de back-end e configurar o espaço de nomes virtual](fxt-add-storage.md)