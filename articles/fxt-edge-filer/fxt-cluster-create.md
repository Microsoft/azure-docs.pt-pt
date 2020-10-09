---
title: 'Tutorial: Criar o cluster de cache do Azure FXT Edge Filer'
description: Aprenda a criar um cluster de cache de armazenamento híbrido com o Azure FXT Edge Filer e como iniciar sessão no painel de controlo do cluster para configurar as definições do cluster.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7a471868bac8f5e0623942c0cc1dc4af4e3881e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185354"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Tutorial: Criar o cluster Azure FXT Edge Filer

Depois de instalar e rubricar os nós de hardware Azure FXT Edge Filer para a sua cache, utilize o software de cluster FXT para criar o cluster de cache. 

Este tutorial leva-o através dos passos para configurar os seus nós de hardware como um cluster. 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Que informação é necessária antes de começar a criar o cluster
> * A diferença entre a rede de gestão do cluster, a rede de clusters e a rede virada para o cliente
> * Como ligar-se a um nó de cluster 
> * Como criar um cluster inicial usando um nó de Filete de Borda Azure FXT
> * Como iniciar seduca no painel de controlo do cluster para configurar as definições do cluster

Este procedimento demora entre 15 e 45 minutos, dependendo da quantidade de pesquisa que precisa fazer para identificar endereços IP e recursos de rede.

## <a name="prerequisites"></a>Pré-requisitos

Complete estes pré-requisitos antes de iniciar este tutorial:

* Instale os seus sistemas de hardware Azure FXT Edge Filer no seu centro de dados 

  Só precisa de um nó para criar o cluster, mas precisa [adicionar pelo menos mais dois nós](fxt-add-nodes.md) antes de configurar o cluster e prepará-lo para o uso. 

* Ligar cabos de alimentação e rede adequados ao sistema  
* Ligue pelo menos um nó de Filer de Borda Azure FXT e [desemconfie da sua senha de raiz](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Recolher informações para o cluster 

Precisa das seguintes informações para criar o cluster Azure FXT Edge Filer:

* Nome do cluster

* Senha administrativa para definir para o cluster

* Endereços IP:

  * Um único endereço IP para gestão de clusters, e o netmask e router para usar para a rede de gestão
  * O primeiro e último endereço IP aborda uma gama contígua de endereços IP para comunicação cluster (nó a nó). Consulte [a distribuição do endereço IP,](#ip-address-distribution)abaixo, para mais informações. 
  * (Os endereços IP voltados para o cliente são definidos após a criação do cluster.)

* Informações sobre infraestruturas de rede:

  * O endereço IP de um servidor DNS para o cluster
  * O nome do domínio DNS para o cluster
  * O nome ou endereço IP para os servidores NTP do cluster (um servidor ou três ou mais) 
  * Quer pretenda ativar a agregação de ligações IEEE 802.1AX-2008 nas interfaces do cluster
  * Se ativar a agregação de ligações, quer utilize ou não a agregação dinâmica do IEEE 802.3ad (LACP)

Pode configurar estes itens de infraestrutura de rede depois de criar o cluster, mas é melhor fazê-lo no momento da criação. 

### <a name="ip-address-distribution"></a>Distribuição de endereços IP

O cluster de cache híbrido Azure FXT Edge Filer utiliza endereços IP em três categorias:

* ID de gestão: Um único endereço IP para gestão de clusters

  Este endereço serve como ponto de entrada para aceder aos utilitários de configuração do cluster (o painel de controlo baseado na Web ou o XML-RPC API). Este endereço é automaticamente atribuído ao nó primário no cluster, e move-se automaticamente se o nó primário mudar.

  Outros endereços IP podem ser usados para aceder ao painel de controlo, mas o endereço IP de gestão foi concebido para fornecer acesso mesmo que os nós individuais falhem.

* Rede de Cluster: Uma gama de endereços IP para comunicação de clusters

  A rede de cluster é utilizada para a comunicação entre os gêmes de cluster e para recuperar ficheiros do armazenamento de backend (ficheiros principais).

  **Melhores práticas:** Aloque um endereço IP por porta física utilizado para a comunicação do cluster em cada nó de Filete de Borda Azure FXT. O cluster atribui automaticamente os endereços no intervalo especificado a nós individuais.

* Rede virada para o cliente: A gama de endereços IP que os clientes usam para solicitar e escrever ficheiros

  Os endereços de rede de clientes são utilizados pelos clientes para aceder aos dados do ficheiro principal através do cluster. Por exemplo, um cliente NFS pode montar um destes endereços.

  **Melhores práticas:** Aloque um endereço IP por porta física utilizado para a comunicação do cliente em cada nó da Série FXT.

  O cluster distribui os endereços IP voltados para o cliente através dos seus nós constituintes da forma mais uniforme possível.

  Para simplificar, muitos administradores configuram um único nome DNS com configuração de DNS (RRDNS) para facilitar a distribuição de pedidos de clientes em toda a gama de endereços. Esta configuração também permite que todos os clientes utilizem o mesmo comando de montagem para aceder ao cluster. Leia [o DNS configurar](fxt-configure-network.md#configure-dns-for-load-balancing) para mais informações.

O endereço IP de gestão e uma série de endereços de rede de cluster devem ser especificados para criar um novo cluster. Os endereços voltados para o cliente são especificados após a criação do cluster.

## <a name="connect-to-the-first-node"></a>Ligue-se ao primeiro nó

Pode ligar-se a qualquer um dos nós FXT instalados e utilizar o seu software DE PARA configurar o cluster.

Se ainda não o fez, ligue pelo menos um dos nós FXT para o seu cluster e certifique-se de que tem uma ligação de rede e um endereço IP. Tem de definir uma nova palavra-passe de raiz para ativar o nó, por isso siga os passos nas [palavras-passe de hardware set](fxt-node-password.md) se ainda não o tiver feito.

Para verificar a ligação à rede, certifique-se de que os LED de ligação à rede do nó estão iluminados (e, se necessário, os indicadores no interruptor de rede ao qual está ligado). Os LED indicadores são descritos no estado de [hardware do Monitor Azure FXT Edge Filer](fxt-monitor.md).

Quando o nó arrancar, solicitará um endereço IP. Se estiver ligado a um servidor DHCP, aceita o endereço IP fornecido pela DHCP. (Este endereço IP é temporário. Mudará quando criar o cluster.)

Se não estiver ligado a um servidor DHCP ou não receber uma resposta, o nó utilizará o software Bonjour para definir um endereço IP autoatribuído no formulário 169.254. \* . . . . . . . . . . . . . . . . . . \* . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . No entanto, deverá definir um endereço IP estático temporário num dos cartões de rede do nó antes de o utilizar para criar um cluster. As instruções estão incluídas neste documento legado; contacte o Microsoft Service and Support para obter informações atualizadas: [Apêndice A: Definição de um endereço IP estático num nó FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Encontre o endereço IP

Ligue-se ao nó de Ficheiro de Borda Azure FXT para encontrar o seu endereço IP. Pode utilizar um cabo de série, ligação direta às portas USB e VGA, ou ligar através de um interruptor KVM. (Para detalhes de ligação à porta consulte [definir senhas iniciais](fxt-node-password.md).)

Depois de ligar, faça o sômis com o nome de utilizador `root` e a palavra-passe que definiu quando iniciou o nó pela primeira vez.  

Depois de iniciar sessão, tem de determinar o endereço IP do nó.

Utilize o comando `ifconfig` para ver os endereços atribuídos a este sistema.

Por exemplo, o comando `ifconfig | grep -B5 inet` procura portas com endereços de internet e dá cinco linhas de contexto para mostrar o identificador de porta.

Anote qualquer endereço IP mostrado no relatório ifconfig. Endereços listados com nomes portuários como e0a ou e0b são boas opções. Não utilize quaisquer endereços IP listados com nomes e7*, uma vez que esses nomes são utilizados apenas para portas de serviço iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Carregue o assistente de configuração do cluster

Utilize a ferramenta de configuração do cluster baseada no navegador para criar o cluster. 

Insira o endereço IP para o nó num navegador web. Se o navegador der uma mensagem sobre o site não ser fidedificado, dirija-se ao site de qualquer maneira. (Os nós individuais do Azure FXT Edge Filer não têm certificados de segurança fornecidos pela AC.)

![Página de sinal de controlo na janela do navegador](media/fxt-cluster-create/unconfigured-node-gui.png)

Deixe os campos **username** e **Password** em branco. Clique em **Iniciar sessão** para carregar a página de criação de cluster.

![Ecrã de configuração inicial para um nó não configurado no painel de controlo GUI baseado no navegador. Mostra opções para atualizar software, configurar um cluster manualmente ou configurar um cluster a partir de um ficheiro de configuração.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Criar o cluster

A ferramenta de configuração do cluster guia-o através de um conjunto de ecrãs para criar o cluster Azure FXT Edge Filer. Certifique-se de que tem as [informações necessárias](#gather-information-for-the-cluster) prontas antes de começar. 

### <a name="creation-options"></a>Opções de criação

O primeiro ecrã dá três opções. Utilize a opção de configuração manual, a menos que tenha instruções especiais do pessoal de suporte.

Clique **em Configurar manualmente o cluster** para carregar o ecrã de novas opções de configuração do cluster. 

As outras opções raramente são utilizadas:

* "Atualizar a imagem do sistema" pede-lhe para instalar um novo software DE antes de criar o cluster. (A versão de software atualmente instalada está listada no topo do ecrã.) Tem de fornecer o ficheiro de pacote de software - um URL e username/password, ou carregando um ficheiro a partir do seu computador. 

* A opção de ficheiro de configuração do cluster é por vezes utilizada pelo Serviço e Suporte ao Cliente da Microsoft. 

## <a name="cluster-options"></a>Opções de cluster

O ecrã seguinte leva-o a configurar opções para o novo cluster.

A página é dividida em duas secções principais, **Configuração Básica** e **Configuração de Rede**. A secção de configuração de rede também tem subsecções: uma para a rede **de Gestão** e outra para a rede **Cluster.**

### <a name="basic-configuration"></a>Configuração básica

Na secção superior, preencha informações básicas para o novo cluster.

![Detalhe da secção "Configuração Básica" na página GUI do navegador. Mostra três campos (nome de cluster, palavra-passe de administrador, confirmar senha)](media/fxt-cluster-create/basic-configuration.png) 

* **Nome cluster** - Introduza um nome único para o cluster.

  O nome do cluster deve satisfazer estes critérios:
  
  * Comprimento de 1 a 16 caracteres
  * Pode incluir letras, números e o traço (-) e sublinhar (_) caracteres 
  * Não deve incluir outros caracteres de pontuação ou caracteres especiais
  
  Pode alterar este nome **Cluster**mais tarde na página de configuração de  >  **configuração geral** do cluster. (Para obter mais informações sobre as definições do cluster, leia o Guia de Configuração do [Cluster,](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)que não faz parte deste conjunto de documentação.)

  > [!NOTE] 
  > O seu nome de cluster é usado para identificar informações do sistema enviadas para suporte para monitorização ou resolução de problemas, por isso é útil incluir o nome da sua empresa.

* **Senha de administração** - Desa estade a palavra-passe para o utilizador administrativo predefinido, `admin` .
  
  Deve configurar contas individuais de utilizador para cada pessoa que administra o cluster, mas não pode remover o utilizador `admin` . Faça sessão como `admin` se precisasse de criar utilizadores adicionais.
 
  Pode alterar a palavra-passe na `admin` página de definições de Utilizadores de **Administração**  >  **Users** no Painel de Controlo do cluster. Para mais informações, leia a documentação **dos Utilizadores** no Guia de Configuração do [Cluster.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html)

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Configuração de rede

A secção **de Networking** solicita-lhe que especifique a infraestrutura de rede que o cluster utilizará. 

Existem duas redes separadas para configurar:

* A *rede de gestão* proporciona ao administrador acesso ao cluster para configuração e monitorização. O endereço IP aqui especificado é utilizado na ligação ao Painel de Controlo ou para acesso SSH. 

  A maioria dos clusters usam apenas um único endereço IP de gestão, mas se quiser adicionar interfaces, pode fazê-lo depois de criar o cluster.

* A *rede de cluster* é utilizada para a comunicação entre os nós de cluster e entre os nós de cluster e o armazenamento de back-end (filetes principais).

A rede virada para o cliente é configurada mais tarde, após a criação do cluster.

Esta secção também inclui a configuração para servidores DNS e NTP que são utilizados por ambas as redes.

### <a name="configure-the-management-network"></a>Configurar a rede de gestão

As definições na secção **Gestão** são para a rede que proporciona acesso ao cluster.

![detalhe da secção "Gestão", com campos para 5 opções e uma caixa de verificação para rede de gestão de 1Gb](media/fxt-cluster-create/management-network-filled.png)

* **Gestão IP** - Especifique o endereço IP que utilizará para aceder ao Painel de Controlo do cluster. Este endereço será reivindicado pelo nó primário do cluster, mas move-se automaticamente para um nó saudável se o nó primário original ficar indisponível.

  A maioria dos clusters usa apenas um endereço IP de gestão. Se quiser mais do que um, pode adicioná-los depois de criar o cluster utilizando a página de definições da Rede Administrativa do **Cluster.**  >  **Administrative Network** Leia mais no Guia de Configuração do [Cluster.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html)

* **Netmask** - Especifique o bímask para a rede de gestão.

* **Router** - Introduza o endereço de gateway predefinido utilizado pela rede de gestão.

* **Tag VLAN (opcional)** - Se o seu cluster utilizar tags VLAN, especifique a etiqueta para a rede de gestão.

  As definições VLAN adicionais estão configuradas na página de definições **do Cluster**  >  **VLAN.** Leia [Trabalhar com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) e [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) no Guia de Configuração do Cluster para saber mais.

* **MTU** - Se necessário, ajuste a unidade de transmissão máxima (MTU) para a rede de gestão do seu cluster.

* **Use a rede mgmt de 1Gb** - Verifique esta caixa se pretende atribuir as duas portas de rede de 1GbE nos seus nós FXT apenas para a rede de gestão. (Você deve ter portas de 25GbE/10GbE disponíveis para todo o tráfego.) Se não verificar esta caixa, a rede de gestão utiliza a porta de maior velocidade disponível. 

### <a name="configure-the-cluster-network"></a>Configure a rede de clusters 

As definições de rede de cluster aplicam-se ao tráfego entre os nós do cluster, e entre os nós de cluster e os filetes principais.

![detalhe da secção "Cluster", com campos para introduzir seis valores](media/fxt-cluster-create/cluster-network-filled.png)

* **Primeiro IP** e **Última IP** - Introduza os endereços IP que definem o intervalo a utilizar para comunicação interna de clusters. Os endereços IP utilizados aqui devem ser contíguos e não atribuídos pela DHCP.

  Pode adicionar mais endereços IP depois de criar o cluster. Utilize **a**página de  >  definições de redes**de cluster** [(documentação do Guia de Configuração do Cluster).](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)

  O valor do **Número de IPs no Alcance** é calculado e mostrado automaticamente.

* **Não-mgmt netmask (opcional)** - Especifique o conjunto de líquidos para a rede de cluster. 

  O sistema sugere automaticamente o valor de caixa líquida que inseriu para a rede de gestão; mudá-lo, se necessário.

* **Router cluster (opcional)** - Especifique o endereço de gateway predefinido utilizado pela rede de cluster. 

  O sistema sugere automaticamente o mesmo endereço de gateway que forneceu para a rede de gestão.

* **Tag Cluster VLAN (opcional)** - Se o seu cluster utilizar tags VLAN, especifique a etiqueta para a rede de cluster.

* **NÃO MGMT MTU (opcional)** - Se necessário, ajuste a unidade de transmissão máxima (MTU) para a sua rede de cluster.

### <a name="configure-cluster-dns-and-ntp"></a>Configurar o cluster DNS e o NTP 

Abaixo da secção **Cluster** existem campos para especificar servidores DNS e NTP e para permitir a agregação de ligações. Estas definições aplicam-se a todas as redes que o cluster utiliza.

![Detalhe da secção para configuração DNS/NTP, com três campos para servidores DNS, campos para domínio DNS e pesquisa de DNS, três campos para servidores NTP e um menu suspenso para opções de agregação de ligações](media/fxt-cluster-create/dns-ntp-filled.png)

* **Servidor(s) DNS** - Introduza o endereço IP de um ou mais servidores do sistema de nomes de domínio (DNS).

  O DNS é recomendado para todos os clusters, e é necessário se quiser utilizar SMB, AD ou Kerberos. 
  
  Para obter um melhor desempenho, configuure o servidor DNS do cluster para o equilíbrio de carga de robin redondo, conforme descrito no [Configure DNS para o cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domínio DNS** - Introduza o nome de domínio da rede que o cluster utilizará.

* **Pesquisa de DNS** - Opcionalmente, introduza nomes de domínio adicionais que o sistema deve pesquisar para resolver consultas dns. Pode adicionar até seis nomes de domínio, separados por espaços.

* **Servidor(s) NTP** - Especifique servidores de um ou três servidores de protocolo de tempo de rede (NTP) nos campos fornecidos. Pode utilizar os hostnames ou endereços IP.

* **Agregação de ligações** - A agregação de ligações permite-lhe personalizar como as portas ethernet nos nós FXT do cluster lidam com vários tipos de tráfego. Para saber mais, leia [a agregação de link](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) no Guia de Configuração do Cluster.

### <a name="click-the-create-button"></a>Clique no botão criar

Depois de fornecer todas as definições necessárias no formulário, clique no botão **Criar Cluster.**

![fundo de forma preenchida com cursor sobre o botão criar no canto inferior direito](media/fxt-cluster-create/create-cluster.png)

O sistema exibe uma mensagem enquanto cria o cluster.

![Mensagem de estado de configuração do cluster no navegador: "O nó FXT está agora a criar o cluster. Isto vai levar vários minutos. Quando o cluster for criado, visite este link para completar a configuração." com hiperligação em "visite este link"](media/fxt-cluster-create/creating-message.png)

Após alguns momentos, pode clicar no link da mensagem para ir ao Painel de Controlo do cluster. (Este link leva-o ao endereço IP especificado no **IP de Gestão**.) Demora 15 segundos a um minuto para que o link fique ativo depois de clicar no botão criar. Se a interface web não carregar, aguarde mais alguns segundos e, em seguida, clique no link novamente. 

A criação de clusters demora um minuto ou mais, mas pode inscrever-se no Painel de Controlo enquanto o processo está a decorrer. É normal que a página do painel de controlo mostre avisos até que o processo de criação do cluster termine. 

## <a name="open-the-settings-pages"></a>Abra as páginas Definições 

Depois de criar o cluster, tem de personalizar a sua configuração para a sua rede e fluxo de trabalho. 

Utilize a interface web do Painel de Controlo para configurar o seu novo cluster. Siga o link do ecrã de estado da criação do cluster ou navegue para o endereço IP de gestão que definiu no cluster.

Faça o sômis à interface web com o nome de utilizador `admin` e a palavra-passe que definiu ao criar o cluster.

![navegador web mostrando campos de login de painel de controlo](media/fxt-cluster-create/admin-login.png)

O Painel de Controlo abre e mostra a página **do Painel de Instrumentos.** À medida que a criação do cluster termina, quaisquer mensagens de aviso devem sair do visor.

Clique no **separador Definições** para configurar o cluster.

No **separador Definições,** a barra lateral esquerda mostra um menu de páginas de configuração. As páginas são organizadas por categoria. Clique no + ou - controle no topo do nome da categoria para expandir ou ocultar as páginas individuais.

![Separador de configurações do painel de controlo (no navegador) com o Cluster > página de Configuração Geral carregada](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Etapas de configuração do cluster

Nesta altura do processo, o seu cluster existe, mas tem apenas um nó, nenhum endereço IP virado para o cliente e nenhum armazenamento back-end. São necessários passos de configuração adicionais para passar de um cluster recém-criado para um sistema de cache que está pronto para lidar com o seu fluxo de trabalho.

### <a name="required-configuration"></a>Configuração necessária

Estes passos são necessários para a maioria ou todos os aglomerados. 

* Adicione os nó no cluster 

  Três nós é padrão, mas muitos clusters de produção têm mais - até um máximo de 24 nós.

  Leia [Adicione nós de cluster](fxt-add-nodes.md) para aprender a adicionar outras unidades de Filetes de Borda Azure FXT ao seu cluster e para ativar a Alta Disponibilidade.

* Especificar o armazenamento na parte de trás

  Adicione definições *de ficheiro* de núcleo para cada sistema de armazenamento de back-end que o cluster irá utilizar. Leia [Adicionar armazenamento de back-end e configurar o espaço de nome virtual](fxt-add-storage.md#about-back-end-storage) para saber mais.

* Configurar o acesso ao cliente e o espaço de nome virtual 

  Crie pelo menos um servidor virtual (vserver) e atribua-lhe um intervalo de endereços IP para as máquinas clientes utilizarem. Também deve configurar o espaço de nome de cluster (às vezes chamado de Global Namespace ou GNS), uma funcionalidade de sistema de ficheiros virtual que permite mapear as exportações de armazenamento back-end para caminhos virtuais. O espaço de nome cluster proporciona aos clientes uma estrutura consistente e acessível do sistema de ficheiros, mesmo que altere os suportes de armazenamento back-end. O espaço de nome também pode fornecer uma hierarquia de armazenamento virtual amigável para recipientes Azure Blob ou outro armazenamento de objetos de nuvem suportado.

  Leia [Configurar o espaço de nomes](fxt-add-storage.md#configure-the-namespace) para mais detalhes. Este passo inclui:
  * Criação de vservers
  * Criação de junções entre a vista da rede de clientes e o armazenamento de back-end 
  * Definindo quais endereços IP cliente são servidos por cada vserver

  > [!Note] 
  > Recomenda-se um planeamento significativo antes de começar a configurar o GNS do cluster. Leia as [secções Usando um espaço de nome global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) e [criando e trabalhando com vServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) no Guia de Configuração do Cluster para obter ajuda.

* [Ajustar as definições de rede](fxt-configure-network.md)

  Existem várias definições relacionadas com a rede que devem ser verificadas ou personalizadas para um novo cluster. Leia [Ajustar as definições de rede](fxt-configure-network.md) para mais detalhes sobre estes itens:

  * Verificação da configuração de DNS e NTP 
  * Configurar serviços de diretório, se necessário 
  * Criação de VLANs
  * Configurar servidores de procuração
  * Adicionar endereços IP à rede de cluster
  * Armazenar certificados de encriptação

* [Configurar monitorização de suporte](#enable-support)

  Tem de aceitar a política de privacidade da ferramenta de configuração e deve configurar as definições de upload de suporte ao mesmo tempo.

  O cluster pode carregar automaticamente dados de resolução de problemas sobre o seu cluster, incluindo estatísticas e depuração de ficheiros. Estes uploads permitem que o Serviço e Suporte ao Cliente da Microsoft forneça o melhor serviço possível. Pode personalizar o que é monitorizado e opcionalmente ativar o suporte proactivo e o serviço de resolução remota de problemas.  

### <a name="optional-configuration"></a>Configuração opcional

Estes passos não são necessários para todos os aglomerados. São necessários para alguns tipos de fluxos de trabalho ou para certos estilos de gestão de clusters. 

* Personalize as definições do nó

  Pode definir nomes de nós e configurar portas IPMI de nó num nível de cluster, ou individualmente. Se configurar estas definições antes de adicionar nós ao cluster, os novos nós podem recolher automaticamente as definições quando se juntam. As opções são descritas no documento de criação de cluster legado [que personaliza as definições do nó.](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html)

  > [!TIP]
  > Alguma documentação para este produto ainda não está disponível no site de documentação do Microsoft Azure. As ligações ao Guia de [Configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) do Cluster e à versão antiga do Guia de Criação de [Clusters](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) irão levá-lo a um site separado do GitHub. 

* Configurar SMB

  Se quiser permitir o acesso do SMB ao seu cluster, bem como ao NFS, tem de configurar o SMB e ligá-lo. O SMB (por vezes chamado CIFS) é normalmente utilizado para suportar clientes do Microsoft Windows.

  Planear e configurar o SMB envolve mais do que clicar em alguns botões no Painel de Controlo. Dependendo dos requisitos do seu sistema, o SMB pode influenciar a forma como define os ficheiros centrais, quantos vservers cria, como configura as suas junções e espaço de nome, permissões de acesso e outras definições.

  Para mais informações, leia a secção [de Acesso ao SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) do Guia de Configuração do Cluster.

* Instalar licenças adicionais

  Se pretender utilizar o armazenamento em nuvem que não o Azure Blob, tem de instalar uma licença adicional de recursos. Contacte o seu representante da Microsoft para obter mais detalhes sobre a compra de uma licença<sup>FlashCloud TM.</sup> Os detalhes são explicados no [armazenamento de back-end e configurar o espaço de nome virtual](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Permitir o suporte

O cluster Azure FXT Edge Filer pode carregar automaticamente dados de suporte sobre o seu cluster. Estes uploads permitem que o pessoal forneça o melhor serviço de apoio ao cliente possível.

Siga estes passos para configurar uploads de suporte.

1. Navegue **Cluster**para a  >  página de definições de Suporte**ao** Cluster. Aceite a política de privacidade. 

   ![Screenshot mostrando Painel de Controlo e janela pop-up com botão Confirmar para aceitar a política de privacidade](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Clique no triângulo à esquerda da **Informação** do Cliente para expandir a secção.
1. Clique no botão **de informação de upload de Revalidate.**
1. Desagure o nome de suporte do cluster em **Unique Cluster Name** - certifique-se de que identifica exclusivamente o seu cluster para apoiar o pessoal.
1. Consulte as caixas para **monitorização de estatísticas,** **upload de informações gerais**e **upload de informações de colisão**.
1. Clique **em Submeter.**  

   ![Screenshot contendo secção de informações completas do cliente da página de definições de suporte](media/fxt-cluster-create/fxt-support-info.png)

1. Clique no triângulo à esquerda do **Secure Proactive Support (SPS)** para expandir a secção.
1. Verifique a caixa para **ativar a ligação SPS**.
1. Clique **em Submeter.**

   ![Screenshot contendo a secção de suporte proactivo seguro concluída na página de definições de suporte](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Passos seguintes

Depois de ter criado o cluster básico e aceitado a política de privacidade, adicione o resto dos nós de cluster. 

> [!div class="nextstepaction"]
> [Adicionar nós de cluster](fxt-add-nodes.md)
