---
title: Criação do cluster de ficheiros do Microsoft Azure FXT Edge
description: Como criar um cluster de cache de armazenamento híbrido com o filtro de borda de FXT do Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 1bfe8f0efce0a844263fc65df0ad927114886769
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450542"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Tutorial: Criar o cluster de ficheiros do Azure FXT Edge

Depois de instalar e iniciar os nós de hardware de ficheiros do Azure FXT Edge para a sua cache, utilize o software de cluster FXT para criar o cluster de cache. 

Este tutorial explica os passos para configurar os nós de hardware como um cluster. 

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * As informações que é necessária antes de começar a criar o cluster
> * A diferença entre a rede de gestão do cluster, a rede de cluster e a rede com clientes
> * Como ligar a um nó de cluster 
> * Como criar um cluster inicial usando um nó de filtro de borda de FXT do Azure
> * Como iniciar sessão no painel de controle de cluster para configurar as definições de cluster

Este procedimento demora entre 15 e 45 minutos, dependendo da quantidade pesquisa que precisa fazer para identificar o IP endereços e os recursos de rede.

## <a name="prerequisites"></a>Pré-requisitos

Conclua estes pré-requisitos antes de começar este tutorial:

* Instale, pelo menos, três sistemas de hardware de ficheiros do Azure FXT Edge no seu centro de dados 
* Ligar cabos de energia e rede apropriados para o sistema  
* Power em, pelo menos, um nó de filtro de borda do Azure FXT e [defina a respetiva palavra-passe de raiz](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Recolher informações para o cluster 

Terá das seguintes informações para criar o cluster de ficheiros do Azure FXT Edge:

* Nome do cluster

* Palavra-passe administrativa para definir para o cluster

* Endereços IP:

  * Um único endereço IP para gestão de clusters e a máscara de rede e o router a utilizar para a rede de gestão
  * Os primeiros e últimos endereços IP num contíguas intervalo de endereços IP para comunicação de cluster (nó para nó). Ver [distribuição de endereço IP](#ip-address-distribution), abaixo, para obter detalhes. 
  * (Os endereços IP com clientes são definidos após a criação do cluster.)

* Informações de infraestrutura de rede:

  * O endereço IP de um servidor DNS para o cluster
  * O nome do domínio DNS para o cluster
  * O nome ou endereço IP para os servidores NTP de cluster (um servidor ou três ou mais) 
  * Se pretende ativar o IEEE 802.1AX-2008 ligar agregação nas interfaces do cluster
  * Se ativar a agregação de ligação, se deve ou não utilizar IEEE 802.3ad (LACP) agregação dinâmica

Pode configurar estes itens de infraestrutura de rede depois de criar o cluster, mas é melhor fazê-lo no momento da criação. 

### <a name="ip-address-distribution"></a>Distribuição de endereço IP

O cluster de cache de armazenamento do Azure FXT Edge filtro híbrida utiliza endereços IP em três categorias:

* Gestão de IP: Um único endereço IP para gestão de clusters

  Este endereço funciona como o ponto de entrada para acessar os utilitários de configuração de cluster (o painel de controle baseado na web ou a API de XML-RPC). Este endereço é atribuído automaticamente para o nó principal do cluster e move automaticamente se o nó principal é alterado.

  Outros endereços IP podem ser utilizados para aceder ao painel de controle, mas o endereço IP de gestão foi concebido para fornecer acesso, mesmo se nós individuais efetuar a ativação pós-falha.

* Rede de cluster: Um intervalo de endereços IP para comunicação de cluster

  A rede de cluster é utilizada para comunicação entre nós de cluster e para recuperar ficheiros do armazenamento de back-end (se filtram os núcleos).

  **Prática recomendada:** Alocar um endereço IP por porta física utilizada para comunicação de cluster em cada nó de filtro de borda de FXT do Azure. O cluster atribui automaticamente os endereços no intervalo especificado para nós individuais.

* Cliente com acesso à rede: O intervalo de endereços IP que utilizam os clientes a pedido e de escrita de ficheiros

  Os endereços de rede do cliente são utilizados pelos clientes para acessar os dados de filtro de núcleos por meio do cluster. Por exemplo, um cliente NFS pode montar um destes endereços.

  **Prática recomendada:** Alocar um endereço IP por porta física utilizada para comunicação de cliente em cada nó de série de FXT.

  O cluster distribui endereços IP com clientes entre os nós de constituintes mais uniformemente possível.

  Para simplificar, muitos administradores configuram um único nome DNS com a configuração de DNS (RRDNS) de round robin para torná-lo mais fácil de distribuir os pedidos de cliente entre o intervalo de endereços. Esta configuração também permite que todos os clientes utilizem o mesmo comando de montagem para aceder ao cluster. Leia [configurar DNS](fxt-configure-network.md#configure-dns-for-load-balancing) para obter mais informações.

O endereço IP de gestão e um intervalo de endereços de rede de cluster tem de ser especificados para criar um novo cluster. Com clientes endereços são especificados após a criação de cluster.

## <a name="connect-to-the-first-node"></a>Ligar ao primeiro nó

Pode ligar a qualquer um de nós FXT instalados e usar o software de sistema operacional para configurar o cluster.

Se ainda não tiver feito, pelo menos um de nós FXT para o cluster de ativação e certificar-se de que tem uma ligação de rede e um endereço IP. Tem de definir uma nova palavra-passe de raiz para ativar o nó, por isso, siga os passos em [definir palavras-passe de hardware](fxt-node-password.md) se ainda não o tiver feito.

Para verificar a ligação de rede, verifique se são iluminado ligação de rede do nó LEDs (e, se necessário, os indicadores na rede mudar para o qual está ligado). Indicador LEDs descritas [estado do hardware de filtro de borda do Monitor do Azure FXT](fxt-monitor.md).

Quando o nó é inicializado, irá pedir um endereço IP. Se estiver ligado a um servidor DHCP, ela aceita o endereço IP fornecido pelo DHCP. (Este endereço IP é temporário. Ele será alterado ao criar o cluster.)

Se ele não está ligado a um servidor DHCP ou não recebeu uma resposta, o nó irá utilizar o software de Bonjour para definir um endereço IP personalizada atribuído na forma 169.254. \*. \*. No entanto, deve definir um endereço IP estático temporário em uma das placas de rede do nó antes de o utilizar para criar um cluster. As instruções estão incluídas neste documento herdados; Contacte a Microsoft Service e o suporte para obter informações atualizadas: [Apêndice a: Definir um endereço IP estático num nó FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Encontrar o endereço IP

Ligar ao nó de filtro de borda de FXT do Azure para localizar o respetivo endereço IP. Pode utilizar um cabo de série, uma ligação direta para as portas USB e VGA, ou se conectar por meio de um comutador KVM. (Para a ligação da porta detalhes, veja [definir palavras-passe inicial](fxt-node-password.md).)

Depois de ligar, iniciar sessão com o nome de utilizador `root` e a palavra-passe que definiu quando arranca o nó pela primeira vez.  

Depois de entrar, precisa determinar o endereço IP do nó.

Utilize o comando `ifconfig` para ver os endereços atribuídos a este sistema.

Por exemplo, o comando `ifconfig | grep -B5 inet` procura as portas com endereços de internet e fornece cinco linhas de contexto para mostrar o identificador de porta.

Anote qualquer endereço IP mostrado no relatório ifconfig. Endereços listados com nomes de porta como e0a ou e0b são boas opções. Não utilizar quaisquer endereços IP listados com e7 * nomes, uma vez que esses nomes são apenas utilizados para portas IPMI, portas de rede não regular.  

## <a name="load-the-cluster-configuration-wizard"></a>Carregar o Assistente de configuração de cluster

Utilize a ferramenta de configuração de cluster baseado no browser para criar o cluster. 

Introduza o endereço IP para o nó num navegador da web. Se o navegador fornece uma mensagem sobre o site que está a ser não fidedigno, de qualquer forma, avance para o site. (Nós de filtro de borda de FXT do individuais do Azure não tem certificados de segurança de AC fornecidos.)

![Controlo painel início de sessão página numa janela do browser](media/fxt-cluster-create/unconfigured-node-gui.png)

Deixe o **nome de utilizador** e **palavra-passe** campos em branco. Clique em **início de sessão** para carregar a página de criação do cluster.

![Ecrã de configuração inicial para um nó não configurado no painel de controle de GUI baseada no browser. Mostra as opções de atualização de software, configurar um cluster manualmente ou configurar um cluster de um ficheiro de configuração.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Criar o cluster

A ferramenta de configuração de cluster orienta-o através de um conjunto de telas para criar o cluster de ficheiros do Azure FXT Edge. Certifique-se de que tem o [informações necessárias](#gather-information-for-the-cluster) pronto antes de iniciar. 

### <a name="creation-options"></a>Opções de criação

A primeira tela oferece três opções. Utilize a opção de configuração manual, a menos que tenha instruções especiais da equipe de suporte.

Clique em **vou configurar o cluster manualmente** para carregar a nova tela de opções de configuração de cluster. 

As outras opções são raramente utilizadas:

* "Atualizar a imagem do sistema" pede-lhe instalar novo software de sistema operacional antes de criar o cluster. (A versão atualmente instalada do software é listada na parte superior do ecrã.) Tem de fornecer o ficheiro de pacote de software - optar por um URL e o nome de utilizador/palavra-passe, ou ao carregar um ficheiro do seu computador. 

* A opção de ficheiro de configuração de cluster, às vezes, é utilizada pelo suporte ao cliente da Microsoft. 

## <a name="cluster-options"></a>Opções de cluster

A próxima tela pede-lhe configurar as opções para o novo cluster.

A página é dividida em duas seções principais, **configuração básica** e **configuração de rede**. A seção de configuração de rede também tem subsecções: um para o **gerenciamento** rede e outro para o **Cluster** rede.

### <a name="basic-configuration"></a>Configuração básica

Na seção superior, preencha as informações básicas para o novo cluster.

![Detalhes da secção "Configuração básica", na página do browser GUI. Ele mostra três campos (nome do cluster, palavra-passe de administrador, confirme a palavra-passe)](media/fxt-cluster-create/basic-configuration.png) 

* **Nome do cluster** -introduza um nome exclusivo para o cluster.

  O nome do cluster tem de cumprir estes critérios:
  
  * Comprimento de 1 a 16 carateres
  * Pode incluir letras, números e o travessão (-) e carateres de sublinhado (_) 
  * Não pode incluir outros pontuação ou carateres especiais
  
  Pode alterar esse nome posteriormente os **Cluster** > **configuração geral** página de configuração. (Para obter mais informações sobre as definições do cluster, leia os [guia de configuração de Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), que não faz parte deste conjunto de documentação.)

  > [!NOTE] 
  > O nome do cluster é utilizado para identificar informações do sistema carregadas para o suporte para monitorização ou resolução de problemas, por isso é útil incluir o nome da sua empresa.

* **Palavra-passe de administrador** -defina a palavra-passe para o utilizador administrativo de padrão, `admin`.
  
  Deve configurar contas de utilizador individuais para cada pessoa que administra o cluster, mas não é possível remover o utilizador `admin`. Inicie sessão como `admin` se precisar de criar utilizadores adicionais.
 
  Pode alterar a palavra-passe `admin` no **administração** > **utilizadores** página de definições do painel de controlo do cluster. Para obter detalhes, leia os **usuários** documentação na [guia de configuração de Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Configuração da rede

O **redes** secção solicita que especifique a infraestrutura de rede que o cluster irá utilizar. 

Existem duas redes distintas para configurar:

* O *rede de gestão* fornece acesso de administrador para o cluster para configuração e monitorização. O endereço IP especificado aqui é utilizado ao ligar-se para o painel de controlo ou para o acesso SSH. 

  A maioria dos clusters utilizam apenas um endereço IP de gestão único, mas se quiser adicionar interfaces pode fazê-lo Depois de criar o cluster.

* O *rede de cluster* é utilizada para comunicação entre nós de cluster e entre nós de cluster e o armazenamento de back-end (se filtram os núcleos).

A rede voltado para o cliente está configurada mais tarde, depois do cluster ter sido criado.

Esta secção também inclui a configuração para servidores DNS e NTP que são utilizadas por ambas as redes.

### <a name="configure-the-management-network"></a>Configurar a rede de gestão

As definições no **gestão** secção destinam-se na rede que fornece acesso de administrador para o cluster.

![Detalhes da seção de "Gestão", com campos para 5 opções e uma caixa de verificação para a rede de gestão de 1Gb](media/fxt-cluster-create/management-network-filled.png)

* **Gestão IP** -especifique o endereço IP que irá utilizar para aceder ao painel de controlo do cluster. Este endereço será ser solicitado por nó principal do cluster, mas ele é automaticamente movido para um nó de bom estado de funcionamento se o nó primário original ficar indisponível.

  A maioria dos clusters, utilize apenas um endereço IP de gestão. Se quiser mais do que um, pode adicioná-los depois de criar o cluster utilizando o **Cluster** > **rede administrativas** página de definições. Leia mais no [guia de configuração de Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Máscara de rede** -especifique a máscara de rede para a rede de gestão.

* **Router** -introduza o endereço de gateway predefinido utilizado pela rede de gestão.

* **Etiqueta VLAN (opcional)** - se o cluster utiliza etiquetas VLAN, especifique a etiqueta para a rede de gestão.

  Definições de VLAN adicionais são configuradas no **Cluster** > **VLAN** página de definições. Leia [trabalhando com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) e [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) no guia de configuração de Cluster para saber mais.

* **O MTU** - se necessário, ajuste a unidade de transmissão máxima (MTU) para a rede de gestão do seu cluster.

* **Rede de gestão de 1Gb de utilização** -Selecione esta caixa se de que pretende atribuir a rede de 1 duas gbe portas nos seus nós FXT à apenas a rede de gestão. Se não selecionar esta caixa, a rede de gestão utiliza a porta de velocidade mais elevada disponível. 

### <a name="configure-the-cluster-network"></a>Configurar a rede de cluster 

Aplicam as definições de rede de cluster para o tráfego entre os nós de cluster e entre nós de cluster e se filtram de núcleo.

![Detalhes da seção de "Cluster", com campos para introduzir os valores de seis](media/fxt-cluster-create/cluster-network-filled.png)

* **IP primeiro** e **IP último** -introduza os endereços IP que definem o intervalo a utilizar para comunicação de clusters internos. Os endereços IP utilizados aqui tem de ser contíguo e não atribuído pelo DHCP.

  Pode adicionar mais endereços IP depois de criar o cluster. Utilize o **Cluster** > **redes de Cluster** página de definições ([documentação de guia de configuração de Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  O valor na **número de IPs no intervalo** é calculada e apresentada automaticamente.

* **(Opcional) da máscara de rede não-mgmt** -especifique a máscara de rede para a rede de cluster. 

  O sistema sugere automaticamente o valor de máscara de rede que introduziu para a rede de gestão; Altere se necessário.

* **Router de cluster (opcional)** -especifique o endereço de gateway predefinido utilizado pela rede de cluster. 

  O sistema sugere automaticamente o mesmo endereço de gateway que especificou para a rede de gestão.

* **Etiqueta VLAN (opcional) do cluster** - se o cluster utiliza etiquetas VLAN, especifique a etiqueta para a rede de cluster.

* **(Opcional) o MTU não-mgmt** - se necessário, ajuste a unidade de transmissão máxima (MTU) para a sua rede de cluster.

### <a name="configure-cluster-dns-and-ntp"></a>Configurar DNS e NTP do cluster 

Abaixo da **Cluster** secção lá são campos para especificar os servidores DNS e NTP e para ativar a agregação de ligação. Estas definições aplicam-se a todas as redes que o cluster utiliza.

![Detalhes da seção de configuração de DNS/NTP, com três campos para servidores DNS, campos para o domínio DNS e pesquisa DNS, três campos para os servidores NTP e um menu de lista pendente para opções de agregação de ligação](media/fxt-cluster-create/dns-ntp-filled.png)

* **Servidor (es) DNS** – introduza o endereço IP de um ou mais domínio servidores de nomes system (DNS).

  DNS é recomendado para todos os clusters e necessário se pretender utilizar o SMB, AD, ou Kerberos. 
  
  Para um desempenho ideal, configurar o servidor DNS do cluster para o balanceamento de carga round robin, conforme descrito em [configurar o DNS para o cluster de ficheiros do Azure FXT Edge](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domínio DNS** -introduza o nome de domínio de rede do cluster irá utilizar.

* **Pesquisa DNS** - como opção, introduza os nomes de domínio adicional que o sistema deve procurar para resolver as consultas DNS. Pode adicionar até seis nomes de domínio, separados por espaços.

* **Servidor (es) NTP** -especificar um ou três servidores de protocolo (NTP) de tempo de rede nos campos fornecidos. Pode utilizar nomes de anfitrião ou endereços IP.

* **Agregação de ligação** -agregação de ligação permite-lhe personalizar a forma como as portas de ethernet em nós FXT do cluster processam vários tipos de tráfego. Para saber mais, leia [agregação de ligação](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) no guia de configuração de Cluster.

### <a name="click-the-create-button"></a>Clique no botão Criar

Depois de fornecer todas as configurações necessárias no formulário, clique nas **criar clusters** botão.

![na parte inferior do formulário completo com o cursor sobre o botão Criar na parte inferior direita](media/fxt-cluster-create/create-cluster.png)

O sistema exibe uma mensagem ao criar o cluster.

![mensagem de estado de configuração de cluster no browser: "O nó FXT agora está a criar o cluster. Esta ação irá demorar vários minutos. Quando o cluster é criado, visite este link para concluir a configuração." com o hiperlink em "visite este link"](media/fxt-cluster-create/creating-message.png)

Após alguns instantes, pode clicar na ligação na mensagem para ir para o painel de controlo do cluster. (Esta ligação direciona-o para o endereço IP que especificou no **IP de gestão**.) Demora 15 segundos para um minuto para a ligação fique ativa depois de clicar no botão criar. Se a interface da web não carregar, aguarde vários segundos mais e, em seguida, clique na ligação novamente. 

Criação do cluster demora um minuto ou mais, mas pode iniciar sessão painel de controlo enquanto o processo está acontecendo. É normal para a página do dashboard do painel de controle Mostrar avisos até termina o processo de criação do cluster. 

## <a name="open-the-settings-pages"></a>Abra as páginas de definições 

Depois de criar o cluster, terá de personalizar a sua configuração para a sua rede e o fluxo de trabalho. 

Utilize a interface de web do painel de controlo para configurar o novo cluster. Siga a ligação a partir do seu ecrã de estado de criação de cluster ou navegue para o endereço IP de gestão que definir no cluster.

Entrar para a interface da web com o nome de utilizador `admin` e a palavra-passe que definiu quando criou o cluster.

![Mostrar campos de início de sessão do painel de controle de navegador da Web](media/fxt-cluster-config/admin-login.png)

O painel de controle é aberto e mostra os **Dashboard** página. Medida que conclui a criação do cluster, devem limpar quaisquer mensagens de aviso do monitor.

Clique nas **definições** separador para configurar o cluster.

Sobre o **definições** separador, a barra lateral esquerda mostra um menu de páginas de configuração. As páginas são organizadas por categoria. Clique a + ou - na parte superior do nome de categoria para expandir ou ocultar as páginas individuais.

![Separador Definições do painel de controlo (no browser) com o Cluster > página de configuração geral carregada](media/fxt-cluster-config/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Passos de configuração de cluster

Neste momento no processo, o cluster existe, mas tem apenas um nó, não existem endereços IP com clientes e nenhum armazenamento de back-end. São necessários passos de configuração adicionais para ir de um cluster recém-criado para um sistema de cache que está pronto para lidar com seu fluxo de trabalho.

### <a name="required-configuration"></a>Configuração necessária

Estes passos são necessários para a maioria ou todos os clusters. 

* Adicionar nós ao cluster 

  Três nós é padrão, mas muitos clusters de produção têm mais - até um máximo de 24 nós.

  Leia [adicionar nós de cluster](fxt-add-nodes.md) para saber como adicionar outras unidades de ficheiros do Azure FXT Edge ao seu cluster e ativar a elevada disponibilidade.

* Especificar o armazenamento de back-end

  Adicione *núcleos de filtro de* definições para cada sistema de armazenamento de back-end que o cluster irá utilizar. Leia [adicionar armazenamento de back-end e configurar o espaço de nomes virtual](fxt-add-storage.md#about-back-end-storage) para saber mais.

* Configurar o acesso para cliente e o espaço de nomes virtual 

  Crie pelo menos um servidor virtual (vserver) e atribuí-la um intervalo de endereços IP para máquinas de cliente utilizar. Também tem de configurar o espaço de nomes de cluster (às vezes chamado de Global Namespace ou GNS), uma funcionalidade do sistema de ficheiros virtual que lhe permite mapear exportações de armazenamento de back-end para caminhos virtuais. O espaço de nomes do cluster fornece clientes uma estrutura de sistema de ficheiros consistente e acessível, mesmo se alternar o suporte de dados do armazenamento de back-end. O espaço de nomes também pode fornecer uma hierarquia de armazenamento virtual amigável de utilizador para os contentores de Blobs do Azure ou outro armazenamento de objeto de nuvem suportada.

  Leia [configurar o espaço de nomes](fxt-add-storage.md#configure-the-namespace) para obter detalhes. Este passo inclui:
  * Criar vservers
  * Configuração de junções entre o armazenamento de modo de exibição e o back-end da rede de cliente 
  * Definir o IP de cliente endereços são servidos por cada vserver

  > [!Note] 
  > Bastante planejamento é recomendado antes de começar a configurar GNS do cluster. Leitura a [com um espaço de nomes Global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) e [criar e trabalhar com VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) secções no guia de configuração de Cluster para obter ajuda.

* [Ajustar as definições de rede](fxt-configure-network.md)

  Há várias configurações relacionadas à rede que devem ser verificadas ou personalizadas para um novo cluster. Leia [ajustar as definições de rede](fxt-configure-network.md) para obter detalhes sobre estes itens:

  * Verificar configuração DNS e NTP 
  * Configurar os serviços de diretório, se necessário 
  * Configurar as VLANs
  * Configurar servidores do proxy
  * Adicionar endereços IP para a rede de cluster
  * Armazenamento de certificados de encriptação

* [Configurar a monitorização de suporte](#enable-support)

  Tem de aceitar a política de privacidade da ferramenta de configuração e configurar as definições de carregamento de suporte ao mesmo tempo.

  O cluster automaticamente pode carregar dados de resolução de problemas sobre o cluster, incluindo estatísticas e a depuração de ficheiros. Esses carregamentos de deixar que Microsoft Customer Service and Support fornecem o melhor serviço possíveis. Pode personalizar o que está a ser monitorizado e, opcionalmente, ativar o suporte pró-ativo e o serviço de resolução de problemas remoto.  

### <a name="optional-configuration"></a>Configuração opcional

Estes passos não são necessários para todos os clusters. Eles são necessários para alguns tipos de fluxos de trabalho ou para alguns estilos de gerenciamento de cluster. 

* Personalize as configurações de nó

  Pode definir os nomes de nó e configurar portas IPMI de nó numa ampla de cluster, nível ou individualmente. Se configurar estas definições antes de adicionar nós ao cluster, os novos nós podem recolher as definições automaticamente quando eles forem associados. As opções são descritas no documento de criação do cluster legado [personalizar definições de nó](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Alguma documentação para este produto ainda não está disponível no site de documentação do Microsoft Azure. Contém ligações para o [guia de configuração de Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) e a versão herdada da [guia de criação do Cluster](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) leva-o para um site alojado no GitHub separado. 

* Configurar o SMB

  Se pretender permitir o acesso SMB para o seu cluster, bem como NFS, tem de configurar o SMB e ativá-la. SMB (por vezes denominado CIFS) é normalmente utilizado para suportar clientes do Microsoft Windows.

  Planear e configurar o SMB envolvem mais do que clicar em alguns botões no painel de controlo. Dependendo das necessidades do seu sistema, SMB pode influenciar a forma como define se filtram de núcleos, quantos vservers criar, como configurar a sua junções e espaço de nomes, permissões de acesso e outras definições.

  Para obter mais informações, leia o guia de configuração de Cluster [configurar o acesso de SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) secção.

* Instalar licenças adicionais

  Se pretender utilizar o armazenamento na cloud, além de Blobs do Azure, tem de instalar uma licença de funcionalidades adicionais. Contacte o seu representante da Microsoft para obter detalhes sobre a aquisição de um FlashCloud<sup>TM</sup> licença. Detalhes são explicados em [adicionar armazenamento de back-end e configurar o espaço de nomes virtual](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Ativar o suporte

O cluster de ficheiros do Azure FXT Edge automaticamente pode carregar dados de suporte sobre o cluster. Esses carregamentos permitem que a equipe garantir o melhor serviço de cliente possíveis.

Siga estes passos para configurar os carregamentos de suporte.

1. Navegue para o **Cluster** > **suporte** página de definições. Aceite a política de privacidade. 

   ![Captura de ecrã que mostra o painel de controlo e a janela de pop-up com o botão confirmar para aceitar a política de privacidade](media/fxt-cluster-config/fxt-privacy-policy.png)

1. Clique no triângulo à esquerda da vírgula **Customer Info** para expandir a secção.
1. Clique nas **Revalide informações de carregamento** botão.
1. Definir o nome do suporte do cluster no **nome de Cluster exclusivo** -Certifique-se de que identifica exclusivamente o seu cluster para o suporte técnico.
1. As caixas de verificação **estatísticas de monitorização**, **carregar de informações gerais**, e **carregar de informações de falhas**.
1. Clique em **Submit** (Submeter).  

   ![Captura de ecrã que contém concluído a secção de informações de cliente da página de definições de suporte](media/fxt-cluster-config/fxt-support-info.png)

1. Clique no triângulo à esquerda da vírgula **Secure Proativa suporte (SPS)** para expandir a secção.
1. Marque a caixa **ativar a ligação do SPS**.
1. Clique em **Submit** (Submeter).

   ![Captura de ecrã que contém concluído a secção de proteger o suporte Proativo de mensagens em fila na página de definições de suporte](media/fxt-cluster-config/fxt-support-sps.png)

## <a name="next-steps"></a>Passos Seguintes

Depois de ter criado o cluster básico e aceite a política de privacidade, o resto de nós do cluster. 

> [!div class="nextstepaction"]
> [Adicionar nós de cluster](fxt-add-nodes.md)
