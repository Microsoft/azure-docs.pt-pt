---
title: 'Tutorial: Adicione armazenamento a um cluster de filetes de borda Azure FXT'
description: Como configurar o armazenamento back-end e o pseudonamespace voltado para o cliente para o Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79239218"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Tutorial: Adicione armazenamento em back-end e configuure o espaço de nome virtual 

Este tutorial explica como adicionar armazenamento de back-edge para o seu cache e como configurar o sistema de ficheiros virtual virado para o cliente. 

O cluster conecta-se a sistemas de armazenamento back-end para aceder ao pedido dos clientes de dados, e para armazenar alterações mais permanentemente do que na cache. 

O espaço de nome é o pseudo-sistema de ficheiros virado para o cliente que permite trocar o armazenamento de back-end sem alterar os fluxos de trabalho do lado do cliente. 

Neste tutorial, vai aprender: 

> [!div class="checklist"]
> * Como adicionar armazenamento back-end ao cluster Azure FXT Edge Filer 
> * Como definir o caminho voltado para o cliente para armazenamento

## <a name="about-back-end-storage"></a>Sobre o armazenamento back-end

O cluster Azure FXT Edge Filer utiliza uma definição *de ficheiro principal* para ligar um sistema de armazenamento de back-end ao cluster FXT.

O Azure FXT Edge Filer é compatível com vários sistemas de hardware nas populares, e pode usar recipientes vazios a partir de Azure Blob ou outro armazenamento em nuvem. 

Os recipientes de armazenamento em nuvem devem estar vazios quando adicionados para que o sistema operativo FXT possa gerir completamente todos os dados sobre o volume de armazenamento na nuvem. Pode mover os dados existentes para o recipiente de nuvem depois de adicionar o recipiente ao cluster como um ficheiro principal.

Utilize o Painel de Controlo para adicionar um ficheiro central ao seu sistema.

> [!NOTE]
> 
> Se pretender utilizar o armazenamento Amazon AWS ou Google Cloud, tem de instalar uma licença de recurso FlashCloud<sup>TM.</sup> Contacte o seu representante da Microsoft para obter uma chave de licença e, em seguida, siga as instruções no guia de configuração legado para [adicionar ou remover licenças de funcionalidades](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> O suporte para o armazenamento Azure Blob está incluído na licença de software Azure FXT Edge Filer. 

Para obter informações mais detalhadas sobre a adição de ficheiros principais, leia estas secções do Guia de Configuração do Cluster:

* Para saber mais sobre a escolha e preparação para adicionar um ficheiro principal, leia [Working With Core Filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Para pré-requisitos detalhados e instruções passo a passo, leia estes artigos:

  * [Adicionar um novo ficheiro core nas](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Adicionar um novo filete de núcleo de nuvem](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Depois de adicionar um ficheiro central, pode atualizar as suas definições na página 'Detalhes do ficheiro'.

## <a name="add-a-core-filer"></a>Adicione um ficheiro de núcleo

Defina um ficheiro central clicando no botão **Criar** na página **Core Filer**  >  **'Ficheiros's 'Gestão de Ficheiros's'.**

![Clicar no botão de criar acima da lista de ficheiros principais na página 'Gerir ficheiros's](media/fxt-cluster-config/create-core-filer-button.png)

O assistente **de ficheiro De Novo Núcleo** adiciona-o através do processo de criação de um ficheiro central que se liga ao seu armazenamento de back-end. O Guia de Configuração do Cluster tem descrições passo a passo do processo, que é diferente para o armazenamento NFS/NAS e para armazenamento em nuvem (as ligações estão acima). 

As subtascos incluem:

* Especificar o tipo de ficheiro principal (NAS ou nuvem)

  ![Primeira página do hardware NAS novo assistente de ficheiros do núcleo. A opção para "cloud core filer" é desativada e mostra uma mensagem de erro sobre a licença em falta.](media/fxt-cluster-config/new-nas-1.png)

* Desa parte do nome do ficheiro principal. Escolha um nome que ajude os administradores do cluster a entender que sistema de armazenamento representa.

* Para os ficheiros principais da NAS, forneça o nome de domínio totalmente qualificado (FQDN) ou endereço IP. O FQDN é recomendado para todos os ficheiros principais e é necessário para o acesso ao SMB.

* Selecione uma política de cache - A segunda página do assistente lista as políticas de cache disponíveis para o novo ficheiro principal. Para mais detalhes, leia a [secção de políticas de cache do Guia de Configuração do Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Segunda página de um novo assistente de ficheiro s núcleo NAS do hardware NAS; o menu de entrega da Cache Policy está aberto, mostrando várias opções desativadas e três opções de política de cache válidas (bypass, leitura de cache e caching de leitura/escrita).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Para armazenamento em nuvem, deve especificar o serviço de nuvem e credenciais de acesso, entre outros parâmetros. Para mais detalhes, leia [o serviço cloud e](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) o protocolo no Guia de Configuração do Cluster.

  ![Informações de ficheiros de núcleo de nuvem no assistente do Novo Ficheiro Core](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Se já adicionou credenciais de acesso à nuvem para este cluster, elas aparecem na lista. Atualize e adicione **Cluster**credenciais na página  >  **de definições de Credenciais de Nuvem** de Cluster. 

Depois de preencher todas as definições necessárias no assistente, clique no botão **Add Filer** para submeter a alteração.

Após alguns momentos, o sistema de armazenamento aparece na lista de ficheiros do **núcleo do Dashboard** e pode ser acedido através das páginas de definições de ficheiros do núcleo.

![filer de núcleo "Flurry-NAS" na página 'Gerir ficheiros', com a visualização de detalhes do ficheiro expandida](media/fxt-cluster-config/core-filer-in-manage-page.png)

O ficheiro principal desta imagem está a faltar um vserver. Deve ligar o ficheiro principal a um vserver e criar uma junção para que os clientes possam aceder ao armazenamento. Estes passos são descritos abaixo em [Configure o espaço de nomes](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Configure o espaço de nome

O cluster Azure FXT Edge Filer cria um sistema de ficheiros virtual chamado *cluster namespace* que simplifica o acesso do cliente a dados armazenados em diversos sistemas back-end. Como os clientes solicitam ficheiros usando um caminho virtual, os sistemas de armazenamento podem ser adicionados ou substituídos sem terem de alterar o fluxo de trabalho do cliente. 

O espaço de nomes de cluster também permite apresentar sistemas de armazenamento de nuvem e NAS numa estrutura de arquivo semelhante. 

Os vservers do cluster mantêm o espaço de nome e servem conteúdo aos clientes. Existem dois passos para criar o espaço de nome de cluster: 

1. Criar um vserver 
1. Configurar junções entre os sistemas de armazenamento de back-end e os caminhos do sistema de ficheiros voltados para o cliente 

### <a name="create-a-vserver"></a>Criar um vserver

Os VServers são servidores de ficheiros virtuais que controlam a forma como os dados fluem entre o cliente e os ficheiros principais do cluster:

* VServers hospedam endereços IP voltados para o cliente
* VServers criam o espaço de nomes e definem junções que mapeiam a estrutura de diretório virtual voltada para o cliente para exportações em armazenamento back-end
* VServers impõem controlos de acesso a ficheiros, incluindo políticas de exportação de ficheiros centrais e sistemas de autenticação de utilizadores
* VServers fornecem infraestruturas SMB

Antes de começar a configurar um cluster vserver, leia a documentação ligada e consulte o seu representante da Microsoft para ajudar a entender o espaço de nomes e vservers. Se utilizar VLANs, [crie-os](fxt-configure-network.md#adjust-network-settings) antes de criar o vserver. 

Estas secções do Guia de Configuração do Cluster irão ajudá-lo a familiarizar-se com as funcionalidades de fxt vserver e espaço de nome global:

* [Criar e Trabalhar com VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Usando um espaço de nome global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Criar um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Precisas de pelo menos um vserver para o teu agrupamento. 

Para criar um novo vserver, precisa das seguintes informações:

* O nome a definir para o vserver

* A gama de endereços IP voltados para o cliente que o vserver irá lidar

  Deve fornecer uma única gama de endereços IP contíguos quando criar o vserver. Pode adicionar mais endereços mais tarde utilizando a página de definições de Rede De Apoio ao **Cliente.**

* Se a sua rede tem VLANs, que vlan para usar para este vserver

Utilize a página de definições **VServer**  >  **Manageers** para criar um novo vserver. Para mais detalhes, [leia-se criar um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) no Guia de Configuração do Cluster. 

![janela pop-up para criar um novo vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Criar uma junção

Uma *junção* mapeia um caminho de armazenamento de back-end para o espaço de nome visível pelo cliente.

Pode utilizar este sistema para simplificar o caminho utilizado nos pontos de montagem do cliente e para escalar a capacidade sem problemas porque um caminho virtual pode acomodar o armazenamento de vários filetes principais.

![Adicionar página de assistente de nova junção com definições preenchidas](media/fxt-cluster-config/add-junction-full.png)

Consulte o espaço de [  >  **nome** **VServer**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) no Guia de Configuração do Cluster para obter detalhes completos sobre a criação de uma junção de espaço de nome.

![A página de definições de > Namespace do VServer mostrando detalhes para uma junção](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Configure regras de exportação

Depois de ter um vserver e um ficheiro principal, deve personalizar as regras de exportação e as políticas de exportação que controlam a forma como os clientes podem aceder a ficheiros sobre as exportações de ficheiros principais.

Em primeiro lugar, utilize a página **VServer**  >  **Export Rules** para adicionar novas regras, para modificar a política de incumprimento ou para criar a sua própria política de exportação personalizada.

Em segundo lugar, utilize a página **VServer**  >  **Export Policies** para aplicar a política personalizada às exportações do seu ficheiro principal quando acedido através desse vserver.

Leia o artigo Guia de Configuração do Cluster [controlando o acesso às exportações de ficheiros principais](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) para mais detalhes.


## <a name="next-steps"></a>Passos seguintes

Depois de adicionar armazenamento e configurar o espaço de nome virado para o cliente, complete a configuração inicial do seu cluster: 

> [!div class="nextstepaction"]
> [Configurar as definições de rede do cluster](fxt-configure-network.md)
