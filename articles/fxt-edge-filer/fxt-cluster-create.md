---
title: 'Tutorial: Criar o cluster de cache de arquivo de borda Azure FXT'
description: Como criar um cluster de cache de armazenamento híbrido com o Ficheiro de Borda Azure FXT
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bfe1d1aeeac55039acf0c7eb295001277be9cd2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79239211"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Tutorial: Criar o cluster Azure FXT Edge Filer

Depois de instalar e inicializar os nós de hardware do Ficheiro de Borda Azure FXT para a sua cache, utilize o software de cluster FXT para criar o cluster de cache. 

Este tutorial acompanha-o através dos passos para configurar os seus nós de hardware como um cluster. 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Que informação é necessária antes de começar a criar o cluster
> * A diferença entre a rede de gestão do cluster, a rede de clusters e a rede voltada para o cliente
> * Como ligar a um nó de cluster 
> * Como criar um cluster inicial usando um nó de Filer de Borda Azure FXT
> * Como iniciar sessão no painel de controlo do cluster para configurar as definições do cluster

Este procedimento demora entre 15 e 45 minutos, dependendo da pesquisa que precisa fazer para identificar endereços IP e recursos de rede.

## <a name="prerequisites"></a>Pré-requisitos

Complete estes pré-requisitos antes de iniciar este tutorial:

* Instale os seus sistemas de hardware De Ficheiros De Borda Azure FXT no seu centro de dados 

  Você só precisa de um nó para criar o cluster, mas você precisa [adicionar pelo menos mais dois nós](fxt-add-nodes.md) antes de configurar o cluster e prepará-lo para o usar. 

* Ligue os cabos de alimentação e rede adequados ao sistema  
* Potência em pelo menos um nó de Ficheiro selada azure FXT Edge e [definir a sua palavra-passe de raiz](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Recolher informações para o cluster 

Precisa das seguintes informações para criar o cluster Azure FXT Edge Filer:

* Nome do cluster

* Senha administrativa para definir para o cluster

* Endereços IP:

  * Um único endereço IP para gestão de clusters, e o netmask e router para usar para a rede de gestão
  * Os primeiros e últimos endereços IP numa gama contígua de endereços IP para comunicação de cluster (nó ao nó). Consulte a distribuição de [endereçoip,](#ip-address-distribution)abaixo, para mais detalhes. 
  * (Os endereços IP voltados para o cliente são definidos após a criação do cluster.)

* Informação sobre infraestruturas de rede:

  * O endereço IP de um servidor DNS para o cluster
  * O nome do domínio DNS para o cluster
  * O nome ou endereço IP para os servidores NTP do cluster (um servidor ou três ou mais) 
  * Se pretende ativar a agregação de ligação IEEE 802.1AX-2008 nas interfaces do cluster
  * Se ativar a agregação de ligações, quer utilize ou não a agregação dinâmica DoIEEE 802.3ad (LACP)

Pode configurar estes itens de infraestrutura de rede depois de criar o cluster, mas é melhor fazê-lo no momento da criação. 

### <a name="ip-address-distribution"></a>Distribuição de endereços IP

O cluster de cache híbrido Azure FXT Edge Filer utiliza endereços IP em três categorias:

* IP de gestão: um único endereço IP para gestão de clusters

  Este endereço serve como ponto de entrada para aceder aos utilitários de configuração do cluster (o painel de controlo baseado na Web ou a API XML-RPC). Este endereço é automaticamente atribuído ao nó principal do cluster, e move-se automaticamente se o nó primário mudar.

  Outros endereços IP podem ser usados para aceder ao painel de controlo, mas o endereço IP de gestão foi concebido para fornecer acesso mesmo que os nós individuais falhem.

* Cluster Network: Uma gama de endereços IP para comunicação de cluster

  A rede de clusters é utilizada para a comunicação entre nós de cluster e para recuperar ficheiros do armazenamento de backend (filetes de núcleo).

  **Boas práticas:** Alocar um endereço IP por porta física utilizado para a comunicação do cluster em cada nó de Ficheiro sinuoso Azure FXT Edge. O cluster atribui automaticamente os endereços na gama especificada a nós individuais.

* Rede voltada para o cliente: O leque de endereços IP que os clientes usam para solicitar e escrever ficheiros

  Os endereços da rede de clientes são utilizados pelos clientes para aceder aos dados de ficheiros principais através do cluster. Por exemplo, um cliente nFS pode montar um destes endereços.

  **Boas práticas:** Alocar um endereço IP por porta física utilizado para a comunicação do cliente em cada nó da Série FXT.

  O cluster distribui endereços IP virados para o cliente através dos seus nós constituintes da forma mais uniforme possível.

  Para a simplicidade, muitos administradores configuram um único nome DNS com configuração dNS redonda (RRDNS) para facilitar a distribuição dos pedidos dos clientes através da gama de endereços. Esta configuração também permite que todos os clientes utilizem o mesmo comando de montagem para aceder ao cluster. Leia o [DNS da Configuração](fxt-configure-network.md#configure-dns-for-load-balancing) para mais informações.

O endereço IP de gestão e uma série de endereços de rede de cluster devem ser especificados para criar um novo cluster. Os endereços voltados para o cliente são especificados após a criação do cluster.

## <a name="connect-to-the-first-node"></a>Ligue-se ao primeiro nó

Pode ligar-se a qualquer um dos nós FXT instalados e utilizar o seu software OS para configurar o cluster.

Se ainda não o fez, desligue pelo menos um dos nós FXT para o seu cluster e certifique-se de que tem uma ligação de rede e um endereço IP. Tem de definir uma nova palavra-passe de raiz para ativar o nó, por isso siga os passos em [Definir palavras-passe](fxt-node-password.md) de hardware se ainda não o tiver feito.

Para verificar a ligação de rede, certifique-se de que os LEDs de ligação de rede do nó estão iluminados (e, se necessário, os indicadores no interruptor de rede a que está ligado). Os LED indicadores são descritos no estado do hardware do Ficheiro de [Borda Monitor Azure FXT](fxt-monitor.md).

Quando o nó arrancar, solicitará um endereço IP. Se estiver ligado a um servidor DHCP, aceita o endereço IP fornecido pelo DHCP. (Este endereço IP é temporário. Mudará quando criar o cluster.)

Se não estiver ligado a um servidor DHCP ou não receber uma resposta, o nó utilizará o software Bonjour para definir um endereço IP autoatribuído no formulário 169.254. \*. \*. No entanto, deve definir um endereço IP estático temporário num dos cartões de rede do nó antes de o utilizar para criar um cluster. As instruções estão incluídas neste documento antigo; contacte o Serviço e suporte da Microsoft para obter informações atualizadas: [Apêndice A: Definição de um endereço IP estático num nó FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Encontre o endereço IP

Ligue-se ao nó de Filer de Borda Azure FXT para encontrar o seu endereço IP. Pode utilizar um cabo em série, ligação direta às portas USB e VGA, ou ligar através de um interruptor KVM. (Para obter detalhes de ligação à porta, consulte [definir senhas iniciais](fxt-node-password.md).)

Depois de se ligar, inscreva-se com o nome `root` de utilizador e a palavra-passe que definiu quando iniciou o nó pela primeira vez.  

Depois de iniciar sessão, tem de determinar o endereço IP do nó.

Utilize o `ifconfig` comando para ver os endereços atribuídos a este sistema.

Por exemplo, `ifconfig | grep -B5 inet` o comando procura portos com endereços de internet e dá cinco linhas de contexto para mostrar o identificador de porta.

Escreva qualquer endereço IP mostrado no relatório ifconfig. Endereços listados com nomes de porta como e0a ou e0b são boas opções. Não utilize nenhum endereço IP listado com nomes e7*, uma vez que esses nomes são utilizados apenas para portas de serviço iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Carregue o assistente de configuração do cluster

Utilize a ferramenta de configuração do cluster baseada no navegador para criar o cluster. 

Introduza o endereço IP para o nó num navegador web. Se o navegador der uma mensagem sobre o site não ser confiável, dirija-se ao site de qualquer forma. (Os nós individuais de filete sulista Azure FXT Edge Filer não dispõem de certificados de segurança fornecidos pela CA.)

![Página de sinal de painel de controlo na janela do navegador](media/fxt-cluster-create/unconfigured-node-gui.png)

Deixe os campos **username** e **password** em branco. Clique **em Login** para carregar a página de criação do cluster.

![Ecrã de configuração inicial para um nó não configurado no painel de controlo GUI baseado no navegador. Mostra opções para atualizar o software, configurar um cluster manualmente ou configurar um cluster a partir de um ficheiro de configuração.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Criar o cluster

A ferramenta de configuração do cluster guia-o através de um conjunto de ecrãs para criar o cluster Azure FXT Edge Filer. Certifique-se de que tem as [informações necessárias](#gather-information-for-the-cluster) prontas antes de começar. 

### <a name="creation-options"></a>Opções de criação

O primeiro ecrã dá três opções. Utilize a opção de configuração manual a menos que tenha instruções especiais do pessoal de suporte.

Clique **em configurar o cluster manualmente** para carregar o novo ecrã de opções de configuração do cluster. 

As outras opções raramente são usadas:

* "Atualizar a imagem do sistema" leva-o a instalar um novo software OS antes de criar o cluster. (A versão de software atualmente instalada está listada na parte superior do ecrã.) Deve fornecer o ficheiro de pacote de software - seja um URL e um nome de utilizador/palavra-passe, ou fazendo o upload de um ficheiro do seu computador. 

* A opção de ficheiro de configuração do cluster é por vezes utilizada pelo Microsoft Customer Service and Support. 

## <a name="cluster-options"></a>Opções de cluster

O ecrã seguinte leva-o a configurar opções para o novo cluster.

A página está dividida em duas secções principais, **Configuração Básica** e **Configuração de Rede.** A secção de configuração de rede também tem subsecções: uma para a rede **de Gestão** e outra para a rede **Cluster.**

### <a name="basic-configuration"></a>Configuração básica

Na secção superior, preencha informações básicas para o novo cluster.

![Detalhe da secção "Configuração básica" na página GUI do navegador. Mostra três campos (nome do cluster, senha de administração, confirmar palavra-passe)](media/fxt-cluster-create/basic-configuration.png) 

* **Nome do cluster** - Introduza um nome único para o cluster.

  O nome do cluster deve satisfazer estes critérios:
  
  * Comprimento de 1 a 16 caracteres
  * Pode incluir letras, números e o traço (-) e sublinhar (_) caracteres 
  * Não deve incluir outras pontuações ou caracteres especiais
  
  Pode alterar este nome mais tarde na página de configuração de Configuração do **Cluster** > **General Setup.** (Para obter mais informações sobre as definições do cluster, leia o Guia de Configuração do [Cluster,](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)que não faz parte deste conjunto de documentação.)

  > [!NOTE] 
  > O nome do seu cluster é usado para identificar informações do sistema enviadas para suporte para monitorização ou resolução de problemas, por isso é útil incluir o nome da sua empresa.

* **Palavra-passe de administrador** - Desloque a palavra-passe para o utilizador administrativo predefinido, `admin`.
  
  Deve configurar contas individuais de utilizador para cada pessoa que administra `admin`o cluster, mas não pode remover o utilizador . Inscreva-se `admin` como se precisasse de criar utilizadores adicionais.
 
  Pode alterar a `admin` palavra-passe na página de definições dos**Utilizadores** de **Administração** > no Painel de Controlo do Cluster. Para mais informações, leia a documentação dos **Utilizadores** no Guia de [Configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html)do Cluster .

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Configuração da rede

A secção **de Networking** solicita-lhe que especifique a infraestrutura de rede que o cluster irá utilizar. 

Existem duas redes separadas para configurar:

* A *rede de gestão* proporciona ao administrador acesso ao cluster para configuração e monitorização. O endereço IP aqui especificado é utilizado quando se conecta ao Painel de Controlo ou para acesso sSH. 

  A maioria dos clusters usa apenas um único endereço IP de gestão, mas se quiser adicionar interfaces pode fazê-lo depois de criar o cluster.

* A *rede de clusters* é utilizada para a comunicação entre nós de cluster e entre nós de cluster e armazenamento de back-end (filetes core).

A rede voltada para o cliente é configurada mais tarde, após a criação do cluster.

Esta secção também inclui configuração para servidores DNS e NTP que são usados por ambas as redes.

### <a name="configure-the-management-network"></a>Configure a rede de gestão

As definições na secção **Gestão** são para a rede que proporciona ao administrador acesso ao cluster.

![detalhe da secção "Gestão", com campos para 5 opções e uma caixa de verificação para rede de gestão de 1Gb](media/fxt-cluster-create/management-network-filled.png)

* **IP de gestão** - Especifique o endereço IP que utilizará para aceder ao Painel de Controlo do Cluster. Este endereço será reivindicado pelo nó principal do cluster, mas automaticamente move-se para um nó saudável se o nó primário original ficar indisponível.

  A maioria dos clusters usa apenas um endereço IP de gestão. Se quiser mais do que um, pode adicioná-los depois de criar o cluster utilizando a página de definições da**Rede Administrativa** **cluster.** >  Leia mais na Guia de [Configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html)do Cluster .

* **Netmask** - Especifique a máscara de rede para a rede de gestão.

* **Router** - Introduza o endereço de gateway predefinido utilizado pela rede de gestão.

* **Etiqueta VLAN (opcional)** - Se o seu cluster utilizar etiquetas VLAN, especifique a etiqueta para a rede de gestão.

  As definições vLAN adicionais estão configuradas na página de definições do **Cluster** > **VLAN.** Ler [Trabalhar com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) e [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) no Guia de Configuração do Cluster para saber mais.

* **MTU** - Se necessário, ajuste a unidade de transmissão máxima (MTU) para a rede de gestão do seu cluster.

* **Utilize a rede 1Gb mgmt** - Verifique esta caixa se pretende atribuir as duas portas de rede 1GbE nos seus nós FXT apenas à rede de gestão. (Deve ter portas de 25GbE/10GbE disponíveis para todo o tráfego.) Se não verificar esta caixa, a rede de gestão utiliza a porta de maior velocidade disponível. 

### <a name="configure-the-cluster-network"></a>Configure a rede de clusters 

As definições da rede de cluster aplicam-se ao tráfego entre os nós do cluster e entre nós de cluster e filetes de núcleo.

![detalhe da secção "Cluster", com campos para entrar em seis valores](media/fxt-cluster-create/cluster-network-filled.png)

* **Primeiro IP** e **Último IP** - Introduza os endereços IP que definem a gama a utilizar para a comunicação interna do cluster. Os endereços IP aqui utilizados devem ser contíguos e não atribuídos pela DHCP.

  Pode adicionar mais endereços IP depois de criar o cluster. Utilize **Cluster** > a página de definições de cluster**networks** [(documentação do Guia](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)de Configuração do Cluster).

  O valor em **Número de IPs no Range** é calculado e mostrado automaticamente.

* **Máscara de rede não mgmt (opcional)** - Especifique o máscara de rede para a rede de clusters. 

  O sistema sugere automaticamente o valor da máscara de rede que introduziu para a rede de gestão; mudá-lo, se necessário.

* **Cluster router (opcional)** - Especifique o endereço de gateway predefinido utilizado pela rede de clusters. 

  O sistema sugere automaticamente o mesmo endereço de gateway que forneceu para a rede de gestão.

* **Etiqueta VLAN do cluster (opcional)** - Se o seu cluster utilizar etiquetas VLAN, especifique a etiqueta para a rede de clusters.

* **MTU não mgmt (opcional)** - Se necessário, ajuste a unidade de transmissão máxima (MTU) para a sua rede de clusters.

### <a name="configure-cluster-dns-and-ntp"></a>Configure cluster DNS e NTP 

Abaixo da secção **Cluster** existem campos para especificar servidores DNS e NTP, e para permitir a agregação de ligações. Estas definições aplicam-se a todas as redes que o cluster utiliza.

![Detalhe da secção para configuração DNS/NTP, com três campos para servidores DNS, campos para domínio DNS e pesquisa de DNS, três campos para servidores NTP, e um menu de drop-down para opções de agregação de ligações](media/fxt-cluster-create/dns-ntp-filled.png)

* **Servidor(s) DNS** - Introduza o endereço IP de um ou mais servidores do sistema de nome de domínio (DNS).

  O DNS é recomendado para todos os clusters, e necessário se quiser utilizar SMB, AD ou Kerberos. 
  
  Para um desempenho ótimo, configure o servidor DNS do cluster para equilibrar a carga de robin redondo, conforme descrito no [Configure DNS para o cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domínio DNS** - Introduza o nome de domínio de rede que o cluster utilizará.

* **Pesquisa dNS** - Opcionalmente, introduza nomes de domínio adicionais que o sistema deve procurar para resolver consultas de DNS. Pode adicionar seis nomes de domínio, separados por espaços.

* **Servidores NTP (s)** - Especifique um ou três servidores de protocolo de tempo de rede (NTP) nos campos fornecidos. Pode utilizar nomes de anfitriões ou endereços IP.

* **Agregação** de ligações - A agregação de ligações permite-lhe personalizar como as portas ethernet nos nós FXT do cluster lidam com vários tipos de tráfego. Para saber mais, leia a [Agregação](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) de Ligações no Guia de Configuração do Cluster.

### <a name="click-the-create-button"></a>Clique no botão criar

Depois de fornecer todas as definições necessárias no formulário, clique no botão **Criar Cluster.**

![fundo de forma completacom cursor sobre o botão criar no canto inferior direito](media/fxt-cluster-create/create-cluster.png)

O sistema exibe uma mensagem enquanto cria o cluster.

![mensagem de estado de configuração de cluster no navegador: "O nó FXT está agora a criar o cluster. Isto vai levar vários minutos. Quando o cluster for criado, visite este link para completar a configuração." com hiperligação em "visite este link"](media/fxt-cluster-create/creating-message.png)

Após alguns momentos, pode clicar no link na mensagem para ir ao Painel de Controlo do Cluster. (Este link leva-o ao endereço IP que especificou no IP de **Gestão**.) Leva 15 segundos a um minuto para que o link se torne ativo depois de clicar no botão criar. Se a interface web não carregar, aguarde mais alguns segundos e, em seguida, clique novamente no link. 

A criação do cluster demora um minuto ou mais, mas pode iniciar sessão no Painel de Controlo enquanto o processo está em curso. É normal que a página do painel de controlo mostre avisos até que o processo de criação do cluster termine. 

## <a name="open-the-settings-pages"></a>Abra as páginas Definições 

Depois de criar o cluster, tem de personalizar a sua configuração para a sua rede e fluxo de trabalho. 

Utilize a interface web do Painel de Controlo para configurar o seu novo cluster. Siga o link do ecrã do estado de criação do cluster ou navegue até ao endereço IP de gestão que definiu no cluster.

Inscreva-se na interface web `admin` com o nome de utilizador e a palavra-passe que definiu ao criar o cluster.

![navegador web mostrando campos de login de painel de controlo](media/fxt-cluster-create/admin-login.png)

O Painel de Controlo abre e mostra a página **do Dashboard.** À medida que a criação do cluster termina, quaisquer mensagens de aviso devem ser retiradas do visor.

Clique no separador **Definições** para configurar o cluster.

No separador **Definições,** a barra lateral esquerda mostra um menu de páginas de configuração. As páginas são organizadas por categoria. Clique no + ou - controle no topo do nome da categoria para expandir ou ocultar as páginas individuais.

![Separador de definições do painel de controlo (no navegador) com a página de Configuração geral do Cluster > carregada](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Passos de configuração do cluster

Nesta altura do processo, o seu cluster existe, mas tem apenas um nó, nenhum endereço IP virado para o cliente e nenhum armazenamento de back-end. São necessários passos adicionais de configuração para ir de um cluster recém-criado para um sistema de cache que esteja pronto para lidar com o seu fluxo de trabalho.

### <a name="required-configuration"></a>Configuração necessária

Estes passos são necessários para a maioria ou todos os aglomerados. 

* Adicione nós ao cluster 

  Três nós é normal, mas muitos aglomerados de produção têm mais - até um máximo de 24 nós.

  Leia Adicionar nós de cluster para aprender a adicionar [outras](fxt-add-nodes.md) unidades azure FXT Edge Filer ao seu cluster e para ativar a Alta Disponibilidade.

* Especificar armazenamento final

  Adicione definições *de filer de núcleo* para cada sistema de armazenamento de back-end que o cluster irá utilizar. Leia [Adicionar armazenamento de back-end e configurar espaço](fxt-add-storage.md#about-back-end-storage) de nome virtual para saber mais.

* Configurar o acesso ao cliente e o espaço de nome virtual 

  Crie pelo menos um servidor virtual (vserver) e atribua-lhe um intervalo de endereçoIP para as máquinas clientes usarem. Também deve configurar o espaço de nome do cluster (por vezes chamado de Global Namespace ou GNS), uma funcionalidade de sistema de ficheiros virtual que permite mapear as exportações de armazenamento de back-end para caminhos virtuais. O espaço de nome do cluster proporciona aos clientes uma estrutura consistente e acessível do sistema de ficheiros, mesmo que altere os suportes de armazenamento de back-end. O espaço de nome também pode fornecer uma hierarquia de armazenamento virtual amigável para recipientes Azure Blob ou outro armazenamento de objetos em nuvem suportado.

  Leia [configurar o espaço de nomes](fxt-add-storage.md#configure-the-namespace) para mais detalhes. Este passo inclui:
  * Criação de servidores de vservers
  * Configuração de junções entre a vista da rede de clientes e o armazenamento back-end 
  * Definição de quais endereços IP do cliente são servidos por cada vserver

  > [!Note] 
  > Recomenda-se um planeamento significativo antes de começar a configurar o GNS do cluster. Leia o Uso de [um Espaço](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) de Nome Global e criação e trabalho com secções [vServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) no Guia de Configuração do Cluster para obter ajuda.

* [Ajustar as definições da rede](fxt-configure-network.md)

  Existem várias definições relacionadas com a rede que devem ser verificadas ou personalizadas para um novo cluster. Ler [Ajuste as definições da rede](fxt-configure-network.md) para obter detalhes sobre estes itens:

  * Verificação da configuração dNS e NTP 
  * Configurar serviços de diretório, se necessário 
  * Criação de VLANs
  * Configurar servidores proxy
  * Adicionar endereços IP à rede de clusters
  * Armazenar certificados de encriptação

* [Configurar a monitorização do suporte](#enable-support)

  Deve aceitar a política de privacidade da ferramenta de configuração e configurar as definições de upload de suporte ao mesmo tempo.

  O cluster pode enviar automaticamente dados de resolução de problemas sobre o seu cluster, incluindo estatísticas e ficheiros de depuração. Estes uploads permitem que o Microsoft Customer Service e o Support forneçam o melhor serviço possível. Pode personalizar o que é monitorizado e, opcionalmente, ativar o suporte proactivo e o serviço de resolução remota de problemas.  

### <a name="optional-configuration"></a>Configuração opcional

Estes passos não são necessários para todos os aglomerados. São necessários para alguns tipos de fluxos de trabalho ou para certos estilos de gestão de clusters. 

* Personalize as definições do nó

  Pode definir nomes de nós e configurar as portas IPMI do nó em um nível de cluster em todo o cluster, ou individualmente. Se configurar estas definições antes de adicionar nós ao cluster, os novos nós podem captar automaticamente as definições quando se juntam. As opções são descritas no documento de criação de clusters personalizador definições do [nó.](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html)

  > [!TIP]
  > Alguma documentação para este produto ainda não está disponível no site de documentação do Microsoft Azure. As ligações ao Guia de [Configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) do Cluster e à versão antiga do Guia de Criação de [Clusters](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) irão levá-lo a um website separado hospedado pelo GitHub. 

* Configure SMB

  Se pretender permitir o acesso da SMB ao seu cluster, bem como ao NFS, tem de configurar o SMB e ligá-lo. O SMB (às vezes chamado CIFS) é normalmente utilizado para suportar clientes do Microsoft Windows.

  Planear e configurar sMB envolve mais do que clicar em alguns botões no Painel de Controlo. Dependendo dos requisitos do seu sistema, o SMB pode influenciar a forma como define os ficheiros centrais, quantos servidores cria, como configura as suas junções e espaço de nome, permissões de acesso e outras definições.

  Para mais informações, leia a secção de configuração do cluster [Configurando o Acesso SMB.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html)

* Instalar licenças adicionais

  Se pretender utilizar o armazenamento em nuvem para além do Azure Blob, tem de instalar uma licença adicional de funcionalidade. Contacte o seu representante da Microsoft para obter mais informações sobre a aquisição de uma licença<sup>FlashCloud TM.</sup> Os detalhes são explicados em [Adicionar armazenamento final e configurar espaço](fxt-add-storage.md#about-back-end-storage)de nome virtual .


### <a name="enable-support"></a>Ativar suporte

O cluster Azure FXT Edge Filer pode enviar automaticamente dados de suporte sobre o seu cluster. Estes uploads permitem que o pessoal forneça o melhor atendimento ao cliente possível.

Siga estes passos para configurar uploads de suporte.

1. Navegue na página de definições de**Suporte** de **Cluster.** >  Aceite a política de privacidade. 

   ![Screenshot mostrando Painel de Controlo e janela pop-up com botão Confirmar para aceitar a política de privacidade](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Clique no triângulo à esquerda da **Informação** do Cliente para expandir a secção.
1. Clique no botão de informação de **upload Revalida.**
1. Deite o nome de suporte do cluster em **Nome único** de Cluster - certifique-se de que identifica exclusivamente o seu cluster para apoiar a equipa.
1. Verifique as caixas para **monitorização de estatísticas,** upload de **informações gerais**e upload de informações sobre **acidentes**.
1. Clique em **Submeter**.  

   ![Screenshot contendo secção de informação completa do cliente da página de definições de suporte](media/fxt-cluster-create/fxt-support-info.png)

1. Clique no triângulo à esquerda do **Suporte Proactivo Seguro (SPS)** para expandir a secção.
1. Verifique a caixa para **ativar a ligação SPS**.
1. Clique em **Submeter**.

   ![Screenshot contendo secção de suporte proactivo seguro completado na página de definições de suporte](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Passos seguintes

Depois de ter criado o cluster básico e aceitado a política de privacidade, adicione o resto dos nós do cluster. 

> [!div class="nextstepaction"]
> [Adicionar nós de cluster](fxt-add-nodes.md)
