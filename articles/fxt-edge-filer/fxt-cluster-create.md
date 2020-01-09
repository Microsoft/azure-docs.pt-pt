---
title: 'Tutorial: criar o cluster de cache do arquivo de borda do Azure FXT Edge'
description: Como criar um cluster de cache de armazenamento híbrido com o filer do Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bfe1d1aeeac55039acf0c7eb295001277be9cd2e
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551272"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Tutorial: criar o cluster de arquivos do Azure FXT Edge

Depois de instalar e inicializar os nós de hardware do Azure FXT Edge para o cache, use o software de cluster FXT para criar o cluster de cache. 

Este tutorial orienta você pelas etapas para configurar seus nós de hardware como um cluster. 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Quais informações são necessárias antes de começar a criar o cluster
> * A diferença entre a rede de gerenciamento do cluster, a rede de cluster e a rede voltada para o cliente
> * Como se conectar a um nó de cluster 
> * Como criar um cluster inicial usando um nó de filer do Azure FXT Edge
> * Como entrar no painel de controle do cluster para definir as configurações de cluster

Esse procedimento leva entre 15 e 45 minutos, dependendo da quantidade de pesquisa que você precisa fazer para identificar os endereços IP e os recursos de rede.

## <a name="prerequisites"></a>Pré-requisitos

Conclua estes pré-requisitos antes de iniciar este tutorial:

* Instale seus sistemas de hardware do Azure FXT Edge para o seu data center 

  Você só precisa de um nó para criar o cluster, mas precisa [adicionar pelo menos mais dois nós](fxt-add-nodes.md) antes de poder configurar o cluster e prepará-lo para uso. 

* Conectar cabos de energia e de rede apropriados ao sistema  
* Ligue pelo menos um nó de filer do Azure FXT Edge e [defina sua senha raiz](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Coletar informações para o cluster 

Você precisa das seguintes informações para criar o cluster de arquivos do Azure FXT Edge:

* Nome do cluster

* Senha administrativa a ser definida para o cluster

* Endereços IP:

  * Um único endereço IP para gerenciamento de cluster e a máscara de rede e o roteador a serem usados para o gerenciamento de redes
  * O primeiro e último endereços IP em um intervalo contíguo de endereços IP para comunicação de cluster (nó para nó). Consulte [distribuição de endereço IP](#ip-address-distribution), abaixo, para obter detalhes. 
  * (Os endereços IP voltados para o cliente são definidos após a criação do cluster.)

* Informações de infraestrutura de rede:

  * O endereço IP de um servidor DNS para o cluster
  * O nome do domínio DNS para o cluster
  * O nome ou endereço IP para os servidores NTP de cluster (um servidor ou três ou mais) 
  * Se você deseja habilitar a agregação de link IEEE 802.1 AX-2008 nas interfaces do cluster
  * Se você habilitar a agregação de links, se usará ou não a agregação dinâmica de LACP do IEEE 802.3

Você pode configurar esses itens de infraestrutura de rede depois de criar o cluster, mas é melhor fazê-lo no momento da criação. 

### <a name="ip-address-distribution"></a>Distribuição de endereço IP

O cluster de cache de armazenamento híbrido do Filer do Azure FXT Edge usa endereços IP em três categorias:

* IP de gerenciamento: um único endereço IP para gerenciamento de cluster

  Esse endereço serve como ponto de entrada para acessar os utilitários de configuração de cluster (o painel de controle baseado na Web ou a API XML-RPC). Esse endereço é atribuído automaticamente ao nó primário no cluster e é movido automaticamente se o nó primário for alterado.

  Outros endereços IP podem ser usados para acessar o painel de controle, mas o endereço IP de gerenciamento é projetado para fornecer acesso mesmo se nós individuais fizerem failover.

* Rede de cluster: um intervalo de endereços IP para comunicação de cluster

  A rede de cluster é usada para comunicação entre nós de cluster e para recuperar arquivos do armazenamento de back-end (principais Filers).

  **Prática recomendada:** Aloque um endereço IP por porta física usada para comunicação do cluster em cada nó do Filer do Azure FXT Edge. O cluster atribui automaticamente os endereços no intervalo especificado a nós individuais.

* Rede voltada para o cliente: o intervalo de endereços IP que os clientes usam para solicitar e gravar arquivos

  Os endereços de rede do cliente são usados por clientes para acessar os dados do Filer principal por meio do cluster. Por exemplo, um cliente NFS pode montar um desses endereços.

  **Prática recomendada:** Aloque um endereço IP por porta física usada para comunicação do cliente em cada nó da série FXT.

  O cluster distribui endereços IP voltados para o cliente em seus nós constituintes da maneira mais uniforme possível.

  Para simplificar, muitos administradores configuram um único nome DNS com a configuração de DNS Round Robin (RRDNS) para facilitar a distribuição de solicitações do cliente no intervalo de endereços. Essa configuração também permite que todos os clientes usem o mesmo comando de montagem para acessar o cluster. Leia [Configurar DNS](fxt-configure-network.md#configure-dns-for-load-balancing) para obter mais informações.

O endereço IP de gerenciamento e um intervalo de endereços de rede de cluster devem ser especificados para criar um novo cluster. Os endereços voltados para o cliente são especificados após a criação do cluster.

## <a name="connect-to-the-first-node"></a>Conectar-se ao primeiro nó

Você pode se conectar a qualquer um dos nós FXT instalados e usar seu software de so para configurar o cluster.

Se você ainda não tiver feito isso, ligue pelo menos um dos nós FXT para o cluster e verifique se ele tem uma conexão de rede e um endereço IP. Você deve definir uma nova senha de raiz para ativar o nó, portanto, siga as etapas em [definir senhas de hardware](fxt-node-password.md) , se você ainda não tiver feito isso.

Para verificar a conexão de rede, verifique se os LEDs de link de rede do nó estão acesos (e, se necessário, os indicadores no comutador de rede ao qual ele está anexado). Os LEDs indicadores são descritos em [monitorar o status de hardware do arquivo do Azure FXT Edge](fxt-monitor.md).

Quando o nó for inicializado, ele solicitará um endereço IP. Se ele estiver conectado a um servidor DHCP, ele aceitará o endereço IP fornecido pelo DHCP. (Esse endereço IP é temporário. Ele será alterado quando você criar o cluster.)

Se ele não estiver conectado a um servidor DHCP ou não receber uma resposta, o nó usará o Bonjour software para definir um endereço IP atribuído automaticamente no formato 169,254.\*.\*. No entanto, você deve definir um endereço IP estático temporário em uma das placas de rede do nó antes de usá-lo para criar um cluster. As instruções estão incluídas neste documento herdado; Contate o serviço da Microsoft e suporte para obter informações atualizadas: [Apêndice a: Configurando um endereço IP estático em um nó FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Localizar o endereço IP

Conecte-se ao nó Filer do Azure FXT Edge para localizar seu endereço IP. Você pode usar um cabo serial, conexão direta com as portas USB e VGA, ou conectar-se por meio de um comutador KVM. (Para obter detalhes de conexão de porta, consulte [definir senhas iniciais](fxt-node-password.md).)

Depois de se conectar, entre com o nome de usuário `root` e a senha que você definiu ao inicializar o nó pela primeira vez.  

Depois de entrar, você precisa determinar o endereço IP do nó.

Use o `ifconfig` de comando para ver os endereços atribuídos a este sistema.

Por exemplo, o comando `ifconfig | grep -B5 inet` procura portas com endereços de Internet e fornece cinco linhas de contexto para mostrar o identificador de porta.

Anote qualquer endereço IP mostrado no relatório ifconfig. Os endereços listados com nomes de porta como e0a ou e0b são boas opções. Não use nenhum endereço IP listado com nomes E7 *, já que esses nomes são usados apenas para portas de serviço iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Carregar o assistente de configuração de cluster

Use a ferramenta de configuração de cluster com base em navegador para criar o cluster. 

Insira o endereço IP para o nó em um navegador da Web. Se o navegador fornecer uma mensagem sobre o site que está sendo não confiável, prossiga para o site mesmo assim. (Nós individuais do Azure FXT Edge Filer não têm certificados de segurança fornecidos pela autoridade de certificação.)

![Página de entrada do painel de controle na janela do navegador](media/fxt-cluster-create/unconfigured-node-gui.png)

Deixe os campos **nome de usuário** e **senha** em branco. Clique em **logon** para carregar a página de criação de cluster.

![Tela inicial de instalação para um nó não configurado no painel de controle de GUI baseado em navegador. Ele mostra opções para atualizar o software, configurar um cluster manualmente ou configurar um cluster a partir de um arquivo de instalação.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Criar o cluster

A ferramenta de configuração de cluster orienta você por meio de um conjunto de telas para criar o cluster de arquivos do Azure FXT Edge. Verifique se você tem as [informações necessárias](#gather-information-for-the-cluster) prontas antes de iniciar. 

### <a name="creation-options"></a>Opções de criação

A primeira tela fornece três opções. Use a opção de configuração manual, a menos que você tenha instruções especiais da equipe de suporte.

Clique em **Configurar o cluster manualmente** para carregar a tela opções de configuração do novo cluster. 

As outras opções raramente são usadas:

* "Atualizar a imagem do sistema" solicita que você instale um novo software de sistema operacional antes de criar o cluster. (A versão do software atualmente instalada está listada na parte superior da tela.) Você deve fornecer o arquivo de pacote de software-uma URL e nome de usuário/senha ou carregando um arquivo do seu computador. 

* A opção de arquivo de configuração de cluster às vezes é usada pelo suporte e atendimento ao cliente da Microsoft. 

## <a name="cluster-options"></a>Opções de cluster

A próxima tela solicitará que você configure as opções para o novo cluster.

A página é dividida em duas seções principais, configuração **básica** e **configuração de rede**. A seção de configuração de rede também tem subseções: uma para a rede de **Gerenciamento** e outra para a rede de **cluster** .

### <a name="basic-configuration"></a>Configuração básica

Na seção superior, preencha as informações básicas para o novo cluster.

![Detalhes da seção "configuração básica" na página GUI do navegador. Ele mostra três campos (nome do cluster, senha do administrador, confirmar senha)](media/fxt-cluster-create/basic-configuration.png) 

* **Nome do cluster** – Insira um nome exclusivo para o cluster.

  O nome do cluster deve atender a estes critérios:
  
  * Comprimento de 1 a 16 caracteres
  * Pode incluir letras, números e os caracteres traço (-) e sublinhado (_) 
  * Não deve incluir outra Pontuação ou caracteres especiais
  
  Você pode alterar esse nome posteriormente no **Cluster** > página de configuração de **instalação geral** . (Para obter mais informações sobre configurações de cluster, leia o [Guia de configuração de cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), que não faz parte deste conjunto de documentação.)

  > [!NOTE] 
  > O nome do cluster é usado para identificar as informações do sistema carregadas para dar suporte ao monitoramento ou à solução de problemas, portanto, é útil incluir o nome da empresa.

* **Senha do administrador** – defina a senha para o usuário administrativo padrão, `admin`.
  
  Você deve configurar contas de usuário individuais para cada pessoa que administra o cluster, mas não pode remover o usuário `admin`. Entre como `admin` se precisar criar usuários adicionais.
 
  Você pode alterar a senha para `admin` na página de configurações **usuários** do > de **Administração** no painel de controle do cluster. Para obter detalhes, leia a documentação **dos usuários** no [Guia de configuração do cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Configuração da rede

A seção **rede** solicita que você especifique a infraestrutura de rede que o cluster usará. 

Há duas redes separadas a serem configuradas:

* A *rede de gerenciamento* fornece acesso de administrador ao cluster para configuração e monitoramento. O endereço IP especificado aqui é usado ao conectar-se ao painel de controle ou para acesso SSH. 

  A maioria dos clusters usa apenas um único endereço IP de gerenciamento, mas se você quiser adicionar interfaces, poderá fazer isso depois de criar o cluster.

* A *rede de cluster* é usada para comunicação entre nós de cluster e entre nós de cluster e armazenamento de back-end (Filers de núcleo).

A rede voltada para o cliente é configurada posteriormente, depois que o cluster tiver sido criado.

Esta seção também inclui a configuração para servidores DNS e NTP que são usados por ambas as redes.

### <a name="configure-the-management-network"></a>Configurar a rede de gerenciamento

As configurações na seção de **Gerenciamento** são para a rede que fornece acesso de administrador ao cluster.

![detalhe da seção "gerenciamento", com campos para cinco opções e uma caixa de seleção para rede de gerenciamento de 1 GB](media/fxt-cluster-create/management-network-filled.png)

* **IP de gerenciamento** – especifique o endereço IP que será usado para acessar o painel de controle do cluster. Esse endereço será reivindicado pelo nó primário do cluster, mas ele se moverá automaticamente para um nó íntegro se o nó primário original ficar indisponível.

  A maioria dos clusters usa apenas um endereço IP de gerenciamento. Se você quiser mais de um, poderá adicioná-los depois de criar o cluster usando a página de configurações de **rede administrativa** do **cluster** > . Leia mais no [Guia de configuração do cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Máscara** de rede – especifique a máscara de redes para o Management Network.

* **Roteador** -Insira o endereço de gateway padrão usado pela rede de gerenciamento.

* **Marca VLAN (opcional)** – se o cluster usar marcas de VLAN, especifique a marca para a rede de gerenciamento.

  Configurações de VLAN adicionais são definidas na página de configurações de **VLAN** de > de **cluster** . Leia [trabalhando com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) e [cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) no guia de configuração do cluster para saber mais.

* **MTU** – se necessário, ajuste a MTU (unidade máxima de transmissão) para a rede de gerenciamento do cluster.

* **Usar rede de gerenciamento de 1GB** -Marque esta caixa se você quiser atribuir as duas portas de rede 1 GbE em seus nós FXT somente à rede de gerenciamento. (Você deve ter 25GbE/10Gbe portas disponíveis para todos os outros tráfegos.) Se você não marcar essa caixa, a rede de gerenciamento usará a porta de velocidade mais alta disponível. 

### <a name="configure-the-cluster-network"></a>Configurar a rede de cluster 

As configurações de rede de cluster se aplicam ao tráfego entre os nós de cluster e entre os nós de cluster e os principais Filers.

![detalhe da seção "cluster", com campos para inserir seis valores](media/fxt-cluster-create/cluster-network-filled.png)

* **Primeiro IP** e **último IP** -Insira os endereços IP que definem o intervalo a ser usado para comunicação de cluster interno. Os endereços IP usados aqui devem ser contíguos e não atribuídos pelo DHCP.

  Você pode adicionar mais endereços IP depois de criar o cluster. Use a **página Configurações do cluster > ** redes de **cluster** (guia de[configuração do cluster documentação](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  O valor em **número de IPs no intervalo** é calculado e mostrado automaticamente.

* **Máscara de rede não gerenciada (opcional)** – especifique a máscara de sub-rede para a sub-rede do cluster. 

  O sistema sugere automaticamente o valor de máscara de rede que você inseriu para o Management Network; Altere-o se necessário.

* **Roteador de cluster (opcional)** -especifique o endereço de gateway padrão usado pela rede de cluster. 

  O sistema sugere automaticamente o mesmo endereço de gateway que você forneceu para a rede de gerenciamento.

* **Marca de VLAN de cluster (opcional)** – se o cluster usar marcas de VLAN, especifique a marca para a rede de cluster.

* **MTU não MGMT (opcional)** -se necessário, ajuste a unidade de transmissão máxima (MTU) para a rede do cluster.

### <a name="configure-cluster-dns-and-ntp"></a>Configurar o DNS do cluster e o NTP 

Abaixo da seção de **cluster** , há campos para especificar servidores DNS e NTP, e para habilitar a agregação de links. Essas configurações se aplicam a todas as redes que o cluster usa.

![Detalhe da seção para configuração de DNS/NTP, com três campos para servidores DNS, campos para pesquisa de DNS e de domínio DNS, três campos para servidores NTP e um menu suspenso para opções de agregação de link](media/fxt-cluster-create/dns-ntp-filled.png)

* Servidores **DNS** – Insira o endereço IP de um ou mais servidores DNS (sistema de nomes de domínio).

  O DNS é recomendado para todos os clusters e é necessário se você quiser usar SMB, AD ou Kerberos. 
  
  Para obter o desempenho ideal, configure o servidor DNS do cluster para balanceamento de carga de Round-Robin, conforme descrito em [Configurar o DNS para o cluster de arquivos do Azure FXT Edge](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domínio DNS** – Insira o nome de domínio de rede que o cluster usará.

* **Pesquisa de DNS** -opcionalmente, insira os nomes de domínio adicionais que o sistema deve pesquisar para resolver consultas DNS. Você pode adicionar até seis nomes de domínio, separados por espaços.

* **Servidor (es) NTP** – especifique um ou três servidores NTP (protocolo de tempo de rede) nos campos fornecidos. Você pode usar nomes de host ou endereços IP.

* **Agregação de link** – a agregação de link permite que você personalize como as portas Ethernet no cluster FXT nós lidam com vários tipos de tráfego. Para saber mais, leia [agregação de links](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) no guia de configuração do cluster.

### <a name="click-the-create-button"></a>Clique no botão criar

Depois de fornecer todas as configurações necessárias no formulário, clique no botão **criar cluster** .

![parte inferior do formulário concluído com o cursor sobre o botão criar no canto inferior direito](media/fxt-cluster-create/create-cluster.png)

O sistema exibe uma mensagem ao criar o cluster.

![mensagem de status de configuração de cluster no navegador: "o nó FXT agora está criando o cluster. Isso levará vários minutos. Quando o cluster for criado, visite este link para concluir a configuração. " com o hiperlink "visite este link"](media/fxt-cluster-create/creating-message.png)

Depois de alguns instantes, você pode clicar no link na mensagem para ir para o painel de controle do cluster. (Esse link leva você para o endereço IP que você especificou no **IP de gerenciamento**.) Leva 15 segundos a um minuto para que o link se torne ativo depois de clicar no botão criar. Se a interface da Web não for carregada, espere mais alguns segundos e clique no link novamente. 

A criação do cluster leva um minuto ou mais, mas você pode entrar no painel de controle enquanto o processo está em andamento. É normal que a página painel do painel de controle mostre avisos até que o processo de criação do cluster seja concluído. 

## <a name="open-the-settings-pages"></a>Abrir as páginas de configurações 

Depois de criar o cluster, você precisa personalizar sua configuração para sua rede e fluxo de trabalho. 

Use a interface da Web do painel de controle para configurar o novo cluster. Siga o link da tela de status de criação do cluster ou navegue até o endereço IP de gerenciamento que você definiu no cluster.

Entre na interface da Web com o nome de usuário `admin` e a senha que você definiu ao criar o cluster.

![navegador da Web mostrando campos de logon do painel de controle](media/fxt-cluster-create/admin-login.png)

O painel de controle é aberto e mostra a página **painel** . À medida que a criação do cluster for concluída, as mensagens de aviso deverão ser limpas da exibição.

Clique na guia **configurações** para configurar o cluster.

Na guia **configurações** , a barra lateral esquerda mostra um menu de páginas de configuração. As páginas são organizadas por categoria. Clique no controle + ou-na parte superior do nome da categoria para expandir ou ocultar as páginas individuais.

![Guia Configurações do painel de controle (no navegador) com o cluster > página Instalação geral carregada](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Etapas de configuração do cluster

Neste ponto do processo, o cluster existe, mas ele tem apenas um nó, nenhum endereço IP voltado para o cliente e nenhum armazenamento de back-end. São necessárias etapas de configuração adicionais para ir de um cluster recém-criado para um sistema de cache que esteja pronto para lidar com seu fluxo de trabalho.

### <a name="required-configuration"></a>Configuração necessária

Essas etapas são necessárias para a maioria ou todos os clusters. 

* Adicionar nós ao cluster 

  Três nós são padrão, mas muitos clusters de produção têm mais de um máximo de 24 nós.

  Leia [adicionar nós de cluster](fxt-add-nodes.md) para saber como adicionar outras unidades de arquivo de borda do Azure FXT ao cluster e para habilitar a alta disponibilidade.

* Especificar armazenamento de back-end

  Adicione definições de *Filer de núcleo* para cada sistema de armazenamento de back-end que o cluster usará. Leia [Adicionar armazenamento de back-end e configurar o namespace virtual](fxt-add-storage.md#about-back-end-storage) para saber mais.

* Configurar o acesso do cliente e o namespace virtual 

  Crie pelo menos um servidor virtual (vserver) e atribua a ele um intervalo de endereços IP para que os computadores cliente usem. Você também deve configurar o namespace do cluster (às vezes chamado de namespace global ou GNS), um recurso de sistema de arquivos virtual que permite mapear exportações de armazenamento de back-end para caminhos virtuais. O namespace de cluster fornece aos clientes uma estrutura de sistema de arquivos consistente e acessível mesmo que você alterne a mídia de armazenamento de back-end. O namespace também pode fornecer uma hierarquia de armazenamento virtual amigável para os contêineres de blob do Azure ou outro armazenamento de objeto de nuvem com suporte.

  Leia [Configurar o namespace](fxt-add-storage.md#configure-the-namespace) para obter detalhes. Esta etapa inclui:
  * Criando vservers
  * Configurando junções entre a exibição de rede do cliente e o armazenamento de back-end 
  * Definindo quais endereços IP do cliente são atendidos por cada vserver

  > [!Note] 
  > É recomendável um planejamento significativo antes de começar a configurar o GNS do cluster. Leia o [usando um namespace global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) e [criando e trabalhando com seções VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) no guia de configuração de cluster para obter ajuda.

* [Ajustar as configurações de rede](fxt-configure-network.md)

  Há várias configurações relacionadas à rede que devem ser verificadas ou personalizadas para um novo cluster. Leia [ajustar as configurações de rede](fxt-configure-network.md) para obter detalhes sobre estes itens:

  * Verificando a configuração de DNS e NTP 
  * Configurando serviços de diretório, se necessário 
  * Configurando VLANs
  * Configurando servidores proxy
  * Adicionando endereços IP à rede de cluster
  * Armazenando certificados de criptografia

* [Configurar o monitoramento de suporte](#enable-support)

  Você deve aceitar a política de privacidade para a ferramenta de configuração do e deve configurar suas configurações de carregamento de suporte ao mesmo tempo.

  O cluster pode carregar automaticamente os dados de solução de problemas sobre o cluster, incluindo estatísticas e arquivos de depuração. Esses carregamentos permitem que o atendimento ao cliente e o suporte da Microsoft forneçam o melhor serviço possível. Você pode personalizar o que é monitorado e, opcionalmente, habilitar o suporte proativo e o serviço de solução de problemas remoto.  

### <a name="optional-configuration"></a>Configuração opcional

Essas etapas não são necessárias para todos os clusters. Eles são necessários para alguns tipos de fluxos de trabalho ou para determinados estilos de gerenciamento de cluster. 

* Personalizar configurações de nó

  Você pode definir nomes de nós e configurar portas IPMI de nó em um nível de todo o cluster ou individualmente. Se você definir essas configurações antes de adicionar nós ao cluster, os novos nós poderão selecionar as configurações automaticamente ao ingressarem. As opções são descritas no documento criação de cluster herdado [Personalizando as configurações de nó](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Algumas documentações deste produto ainda não estão disponíveis no site de documentação Microsoft Azure. Links para o [Guia de configuração de cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) e a versão herdada do [Guia de criação de cluster](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) levarão você para um site separado hospedado no github. 

* Configurar SMB

  Se você quiser permitir o acesso SMB ao cluster, bem como ao NFS, deverá configurar o SMB e ativá-lo. O SMB (às vezes chamado de CIFS) é normalmente usado para dar suporte a clientes do Microsoft Windows.

  Planejar e configurar o SMB envolve mais do que clicar em alguns botões no painel de controle. Dependendo dos requisitos do seu sistema, o SMB pode influenciar como você define os principais filers, quantas vservers você cria, como você configura as junções e o namespace, permissões de acesso e outras configurações.

  Para obter mais informações, leia o guia de configuração do cluster [Configurando a seção acesso SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) .

* Instalar licenças adicionais

  Se você quiser usar o armazenamento em nuvem diferente do blob do Azure, deverá instalar uma licença de recurso adicional. Entre em contato com seu representante da Microsoft para obter detalhes sobre como comprar uma licença do FlashCloud<sup>TM</sup> . Os detalhes são explicados em [Adicionar armazenamento de back-end e configurar o namespace virtual](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Habilitar suporte

O cluster de arquivos do Azure FXT Edge pode carregar automaticamente os dados de suporte sobre o cluster. Esses uploads permitem que a equipe forneça o melhor serviço possível ao cliente.

Siga estas etapas para configurar os carregamentos de suporte.

1. Navegue até a página de configurações de **suporte** do **cluster** > . Aceite a política de privacidade. 

   ![Captura de tela mostrando o painel de controle e a janela pop-up com o botão confirmar para aceitar a política de privacidade](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Clique no triângulo à esquerda das **informações do cliente** para expandir a seção.
1. Clique no botão **revalidar informações de carregamento** .
1. Definir o nome de suporte do cluster em **nome de cluster exclusivo** -Verifique se ele identifica exclusivamente o cluster para dar suporte à equipe.
1. Marque as caixas de **monitoramento de estatísticas**, **carregamento de informações gerais**e **carregamento de informações de falha**.
1. Clique em **Submit** (Submeter).  

   ![Captura de tela contendo a seção de informações do cliente concluída da página de configurações de suporte](media/fxt-cluster-create/fxt-support-info.png)

1. Clique no triângulo à esquerda da **SPS (suporte proativo seguro)** para expandir a seção.
1. Marque a caixa **habilitar o link do SPS**.
1. Clique em **Submit** (Submeter).

   ![Captura de tela que contém a seção de suporte proativo seguro completa na página Configurações de suporte](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Passos seguintes

Depois de criar o cluster básico e aceitar a política de privacidade, adicione o restante dos nós de cluster. 

> [!div class="nextstepaction"]
> [Adicionar nós de cluster](fxt-add-nodes.md)
