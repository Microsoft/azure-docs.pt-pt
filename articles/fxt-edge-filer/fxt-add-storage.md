---
title: 'Tutorial: Adicione armazenamento a um cluster De Arquivo de Borda Azure FXT'
description: Como configurar o armazenamento back-end e o pseudonamespace virado para o cliente para O Ficheiro de Borda Azure FXT
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239218"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Tutorial: Adicione armazenamento de back-end e configure o espaço de nome virtual 

Este tutorial explica como adicionar armazenamento de back-edge para a sua cache e como configurar o sistema de ficheiros virtuais virado para o cliente. 

O cluster liga-se aos sistemas de armazenamento back-end para aceder ao pedido dos clientes de dados e para armazenar alterações mais permanentemente do que na cache. 

O espaço de nome é o sistema de ficheiros pseudo voltado para o cliente que permite trocar o armazenamento de back-end sem alterar fluxos de trabalho do lado do cliente. 

Neste tutorial, vai aprender: 

> [!div class="checklist"]
> * Como adicionar armazenamento de back-end ao cluster Azure FXT Edge Filer 
> * Como definir o caminho virado para o cliente para armazenamento

## <a name="about-back-end-storage"></a>Sobre o armazenamento de back-end

O cluster Azure FXT Edge Filer utiliza uma definição *de ficheiro core* para ligar um sistema de armazenamento back-end ao cluster FXT.

O Azure FXT Edge Filer é compatível com vários sistemas de hardware NAS populares, e pode usar recipientes vazios a partir de Azure Blob ou outro armazenamento em nuvem. 

Os recipientes de armazenamento em nuvem devem estar vazios quando adicionados para que o sistema operativo FXT possa gerir completamente todos os dados no volume de armazenamento em nuvem. Pode mover os dados existentes para o recipiente de nuvem depois de adicionar o recipiente ao cluster como um filete de núcleo.

Utilize o Painel de Controlo para adicionar um ficheiro core ao seu sistema.

> [!NOTE]
> 
> Se pretender utilizar o Amazon AWS ou o armazenamento do Google Cloud, tem de instalar uma licença de funcionalidade<sup>FlashCloud TM.</sup> Contacte o seu representante da Microsoft para obter uma chave de licença e, em seguida, siga as instruções no guia de configuração do legado para [adicionar ou remover as licenças](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)de funcionalidade .
> 
> O suporte para armazenamento Azure Blob está incluído na licença de software Azure FXT Edge Filer. 

Para obter informações mais detalhadas sobre a adição de ficheiros centrais, leia estas secções do Guia de Configuração do Cluster:

* Para mais informações sobre a escolha e preparação para adicionar um filer core, leia [Working With Core Filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Para pré-requisitos detalhados e instruções passo a passo, leia estes artigos:

  * [Adicionar um novo arquivo de núcleo nas nas](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Adicionar um novo ficheiro de núcleo de nuvem](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Depois de adicionar um ficheiro principal, pode atualizar as definições na página de definições de Detalhes do Ficheiro Core.

## <a name="add-a-core-filer"></a>Adicione um filer principal

Defina um ficheiro core clicando no botão **Criar** na página de definições de Ficheiros **Core** > Gerir o **Núcleo.**

![Clicar no botão criar acima da lista de ficheiros core na página 'Gerir Ficheiros Core'](media/fxt-cluster-config/create-core-filer-button.png)

O assistente **add New Core Filer** acompanha-o através do processo de criação de um ficheiro core que se liga ao seu armazenamento traseiro. O Guia de Configuração do Cluster tem descrições passo-a-passo do processo, o que é diferente para armazenamento NFS/NAS e para armazenamento em nuvem (as ligações estão acima). 

As subtarefas incluem:

* Especifique o tipo de filer principal (NAS ou nuvem)

  ![Primeira página do novo assistente de ficheiro seledores da NAS. A opção para "cloud core filer" é desativada e mostra uma mensagem de erro sobre a licença em falta.](media/fxt-cluster-config/new-nas-1.png)

* Defino o nome do ficheiro principal. Escolha um nome que ajude os administradores do cluster a entender qual o sistema de armazenamento que representa.

* Para os ficheiros centrais da NAS, forneça o nome de domínio totalmente qualificado (FQDN) ou endereço IP. FQDN é recomendado para todos os filetes principais, e necessário para o acesso SMB.

* Selecione uma política de cache - A segunda página do assistente lista as políticas de cache disponíveis para o novo ficheiro principal. Para mais detalhes, leia a secção de [políticas de cache do Guia de Configuração do Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Segunda página de um novo assistente de ficheiro seletiva nas do hardware; o menu de entrega da Política cache está aberto, mostrando várias opções de incapacidade e três opções de política de cache válidas (bypass, read caching, e ler/escrever cacheching).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Para armazenamento em nuvem, deve especificar o serviço de nuvem e credenciais de acesso, entre outros parâmetros. Para mais detalhes, leia o [serviço cloud e](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) o protocolo no Guia de Configuração do Cluster.

  ![Informações de ficheiros de núcleo de nuvem no novo assistente de Ficheiro seleção](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Se já adicionou credenciais de acesso à nuvem para este cluster, elas aparecem na lista. Atualizar e adicionar credenciais na página de definições de **Credenciais** de Nuvem do **Cluster** > . 

Depois de preencher todas as definições necessárias no assistente, clique no botão **Adicionar Filer** para submeter a alteração.

Após alguns momentos, o sistema de armazenamento aparece na lista de ficheiros core **do Dashboard** e pode ser acedido através de páginas de definições de ficheiros core.

![ficheiro "Flurry-NAS" na página de definições de 'Gerir Ficheiros Core', com a visão de dados do filer expandida](media/fxt-cluster-config/core-filer-in-manage-page.png)

O ficheiro principal desta imagem está a faltar um vserver. Deve ligar o ficheiro principal a um servidor de vserver e criar uma junção para que os clientes possam aceder ao armazenamento. Estes passos são descritos abaixo na [Configuração do espaço de nome](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Configure o espaço de nome

O cluster Azure FXT Edge Filer cria um sistema de ficheiros virtual chamado *espaço de nome* do cluster que simplifica o acesso do cliente aos dados armazenados em diversos sistemas back-end. Como os clientes solicitam ficheiros utilizando um caminho virtual, os sistemas de armazenamento podem ser adicionados ou substituídos sem terem de alterar o fluxo de trabalho do cliente. 

O espaço de nome do cluster também permite apresentar sistemas de armazenamento de nuvem e NAS numa estrutura de ficheiro semelhante. 

Os vservers do cluster mantêm o espaço de nome e servem conteúdo aos clientes. Existem dois passos para criar o espaço de nome do cluster: 

1. Criar um vserver 
1. Instale entroncamentos entre os sistemas de armazenamento de back-end e os caminhos do sistema de ficheiros virado para o cliente 

### <a name="create-a-vserver"></a>Criar um vserver

Os VServers são servidores de ficheiros virtuais que controlam a forma como os dados fluem entre o cliente e os ficheiros centrais do cluster:

* VServers hospedam endereços IP voltados para o cliente
* Os VServers criam o espaço de nome e definem as junções que mapeiam a estrutura de diretório virtual virada para o cliente para exportações em armazenamento back-end
* VServers impõem controlos de acesso a ficheiros, incluindo políticas de exportação de ficheiros fundamentais e sistemas de autenticação do utilizador
* VServers fornecem infraestrutura SMB

Antes de começar a configurar um servidor de cluster, leia a documentação ligada e consulte o seu representante da Microsoft para ajudar a compreender o espaço de nome e os servidores de vservers. Se utilizar VLANs, [crie-os](fxt-configure-network.md#adjust-network-settings) antes de criar o vserver. 

Estas secções do Guia de Configuração do Cluster irão ajudá-lo a familiarizar-se com o vserver FXT e as funcionalidades globais do espaço de nome:

* [Criação e trabalho com VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Usando um espaço de nome global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Criação de um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Precisa de pelo menos um vserver para o seu cluster. 

Para criar um novo servidor de servidores, necessita das seguintes informações:

* O nome a definir para o vserver

* O leque de endereços IP virados para o cliente que o vserver irá manusear

  Deve fornecer uma única gama de endereços IP contíguos quando criar o vserver. Pode adicionar mais endereços mais tarde utilizando a página de definições da Rede De Volta ao **Cliente.**

* Se a sua rede tiver VLANs, que vLAN usar para este vserver

Utilize a página de definições **do VServer** > **Gerir os VServers** para criar um novo servidor de vserver. Para mais detalhes leia [Criar um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) no Guia de Configuração do Cluster. 

![janela pop-up para criar um novo vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Criar uma junção

Uma *junção* mapeia um caminho de armazenamento de costas para o espaço de nome visível do cliente.

Você pode usar este sistema para simplificar o caminho usado nos pontos de montagem do cliente, e para escalar a capacidade perfeitamente porque um caminho virtual pode acomodar armazenamento de vários filetes de núcleo.

![Adicione a página do assistente de junção nova com as definições preenchidas](media/fxt-cluster-config/add-junction-full.png)

Consulte o [ **VServer** > **Namespace** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) no Guia de Configuração do Cluster para obter detalhes completos sobre a criação de uma junção espaço de nome.

![A página de definições do VServer > Namespace mostrando detalhes para uma junção](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Configure regras de exportação

Depois de ter um vserver e um filer principal, deve personalizar as regras de exportação e as políticas de exportação que controlam como os clientes podem aceder a ficheiros sobre as exportações de filer seletiva.

Em primeiro lugar, utilize a página **Do VServer** > **Regras de Exportação** para adicionar novas regras, para modificar a política de incumprimento ou para criar a sua própria política de exportação personalizada.

Em segundo lugar, utilize a página **VServer** > **Export Policies** para aplicar a política personalizada às exportações do seu ficheiro principal quando acedida através desse servidor.

Leia o artigo do Guia de Configuração do Cluster [controlando o acesso às exportações de filer seleções](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) para mais detalhes.


## <a name="next-steps"></a>Passos seguintes

Depois de adicionar armazenamento e configurar o espaço de nome virado para o cliente, complete a configuração inicial do seu cluster: 

> [!div class="nextstepaction"]
> [Configurar as definições de rede do cluster](fxt-configure-network.md)
