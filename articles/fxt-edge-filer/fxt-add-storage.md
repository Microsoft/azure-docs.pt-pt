---
title: 'Tutorial: Adicionar armazenamento a um cluster de arquivos do Azure FXT Edge'
description: Como configurar o armazenamento de back-end e o pseudonamespace voltado para o cliente para o filer do Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551969"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Tutorial: Adicionar armazenamento de back-end e configurar o namespace virtual 

Este tutorial explica como adicionar armazenamento de back-Edge para seu cache e como configurar o sistema de arquivos virtual voltado para o cliente. 

O cluster se conecta a sistemas de armazenamento de back-end para acessar a solicitação de clientes de dados e armazenar alterações mais permanentemente do que no cache. 

O namespace é o pseudo filesystem voltado para o cliente que permite que você troque o armazenamento de back-end sem alterar os fluxos de trabalho do lado do cliente. 

Neste tutorial, vai aprender: 

> [!div class="checklist"]
> * Como adicionar armazenamento de back-end ao cluster do Filer do Azure FXT Edge 
> * Como definir o caminho voltado para o cliente para armazenamento

## <a name="about-back-end-storage"></a>Sobre o armazenamento de back-end

O cluster de arquivos do Azure FXT Edge usa uma definição de *Filer principal* para vincular um sistema de armazenamento de back-end ao cluster FXT.

O Azure FXT Edge Filer é compatível com vários sistemas de hardware NAS populares e pode usar contêineres vazios do blob do Azure ou de outro armazenamento em nuvem. 

Os contêineres de armazenamento em nuvem devem estar vazios quando adicionados para que o sistema operacional FXT possa gerenciar completamente todos os dados no volume de armazenamento em nuvem. Você pode mover os dados existentes para o contêiner de nuvem depois de adicionar o contêiner ao cluster como um Filer principal.

Use o painel de controle para adicionar um Filer principal ao seu sistema.

> [!NOTE]
> 
> Se você quiser usar o Amazon AWS ou o Google Cloud Storage, deverá instalar uma licença de recurso do FlashCloud<sup>TM</sup> . Entre em contato com seu representante da Microsoft para obter uma chave de licença e siga as instruções no guia de configuração herdado para [Adicionar ou remover licenças de recurso](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> O suporte para o armazenamento de BLOBs do Azure está incluído na licença de software do Azure FXT Edge. 

Para obter informações mais detalhadas sobre como adicionar os principais filers, leia estas seções do guia de configuração do cluster:

* Para obter mais informações sobre como escolher e preparar para adicionar um Filer principal, leia [trabalhando com os principais Filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Para obter pré-requisitos detalhados e instruções passo a passo, leia estes artigos:

  * [Adicionando um novo Filer do NAS Core](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Adicionando um novo Filer do Cloud Core](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Depois de adicionar um Filer principal, você pode atualizar suas configurações na página de configurações de detalhes do Filer Core.

## <a name="add-a-core-filer"></a>Adicionar um Filer principal

Defina um Filer principal clicando no botão **criar** na página **principal do Filer** > **gerenciar os principais arquivos** de configurações.

![Clicando no botão criar acima da lista de principais Filers na página Gerenciar Filers principais](media/fxt-cluster-config/create-core-filer-button.png)

O assistente **Adicionar novo Filer principal** orienta você pelo processo de criação de um Filer principal que se vincula ao armazenamento de back-end. O guia de configuração de cluster apresenta descrições passo a passo do processo, que é diferente para armazenamento NFS/NAS e armazenamento em nuvem (os links estão acima). 

As subtarefas incluem:

* Especificar o tipo de filer principal (NAS ou Cloud)

  ![Primeira página do assistente de novo Filer do NAS de hardware. A opção "Cloud Core Filer" está desabilitada e mostra uma mensagem de erro sobre a licença ausente.](media/fxt-cluster-config/new-nas-1.png)

* Defina o nome do Filer principal. Escolha um nome que ajude os administradores de cluster a entender qual sistema de armazenamento ele representa.

* Para os filers do NAS Core, forneça o nome de domínio totalmente qualificado (FQDN) ou o endereço IP. O FQDN é recomendado para todos os Filers principais e é necessário para acesso SMB.

* Selecionar uma política de cache – a segunda página do assistente lista as políticas de cache disponíveis para o novo Filer principal. Para obter detalhes, leia a [seção políticas de cache do guia de configuração do cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Segunda página de um assistente de novo Filer do NAS de hardware; o menu suspenso política de cache está aberto, mostrando várias opções desabilitadas e três opções de política de cache válidas (ignorar, cache de leitura e cache de leitura/gravação).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Para o armazenamento em nuvem, você deve especificar o serviço de nuvem e as credenciais de acesso, entre outros parâmetros. Para obter detalhes, leia [serviço de nuvem e protocolo](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) no guia de configuração de cluster.

  ![Informações do Filer Core do clouder no assistente do novo Filer principal](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Se você já tiver adicionado credenciais de acesso à nuvem para esse cluster, elas aparecerão na lista. Atualize e adicione credenciais no **Cluster** > página Configurações de **credenciais de nuvem** . 

Depois de preencher todas as configurações necessárias no assistente, clique no botão **Adicionar Filer** para enviar a alteração.

Depois de alguns instantes, o sistema de armazenamento aparece na lista de arquivos do **Dashboard** Core e pode ser acessado por meio das páginas de configurações do Filer principal.

![Filer principal "monte-NAS" na página de configurações gerenciar Filers principais, com a exibição de detalhes do Filer expandida](media/fxt-cluster-config/core-filer-in-manage-page.png)

O filer principal nesta captura de tela não tem um VServer. Você deve vincular o filer principal a um vserver e criar uma junção para que os clientes possam acessar o armazenamento. Essas etapas são descritas abaixo em [Configurar o namespace](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Configurar o namespace

O cluster de arquivos do Azure FXT Edge cria um sistema de arquivos virtual chamado de *namespace do cluster* que simplifica o acesso do cliente aos dados armazenados em sistemas back-end diversificados. Como os clientes solicitam arquivos usando um caminho virtual, os sistemas de armazenamento podem ser adicionados ou substituídos sem a necessidade de alterar o fluxo de trabalho do cliente. 

O namespace de cluster também permite apresentar sistemas de armazenamento em nuvem e NAS em uma estrutura de arquivos semelhante. 

O vservers do cluster mantém o namespace e serve conteúdo aos clientes. Há duas etapas para criar o namespace do cluster: 

1. Criar um VServer 
1. Configurar junções entre os sistemas de armazenamento back-end e os caminhos do sistema de arquivos voltados para o cliente 

### <a name="create-a-vserver"></a>Criar um VServer

VServers são servidores de arquivos virtuais que controlam como os dados fluem entre o cliente e os principais filers do cluster:

* Endereços IP voltados para o cliente do host VServers
* VServers criar o namespace e definir junções que mapeiam a estrutura de diretório virtual voltada para o cliente para exportações no armazenamento de back-end
* VServers impor controles de acesso a arquivos, incluindo políticas de exportação do Filer principal e sistemas de autenticação de usuário
* VServers fornecer infraestrutura SMB

Antes de começar a configurar um VServer de cluster, leia a documentação vinculada e consulte seu representante da Microsoft para ajudar a entender o namespace e o vservers. Se estiver usando VLANs, [crie-](fxt-configure-network.md#adjust-network-settings) as antes de criar o vserver. 

Essas seções do guia de configuração de cluster ajudarão você a se familiarizar com os recursos do FXT vserver e namespace global:

* [Criando e trabalhando com VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Usando um namespace global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Criando um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Você precisa de pelo menos um VServer para o cluster. 

Para criar um novo vServer, você precisará das seguintes informações:

* O nome a ser definido para o vserver

* O intervalo de endereços IP voltados para o cliente que o vserver manipulará

  Você deve fornecer um único intervalo de endereços IP contíguos ao criar o vserver. Você pode adicionar mais endereços posteriormente usando a página de configurações de **rede voltada** para o cliente.

* Se sua rede tiver VLANs, qual VLAN usar para este vserver

Use a página **VServer** > gerenciar configurações do **VServers** para criar um novo vserver. Para obter detalhes, leia [criando um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) no guia de configuração do cluster. 

![janela pop-up para criar um novo vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Criar uma junção

Uma *junção* mapeia um caminho de armazenamento de back-end para o namespace visível do cliente.

Você pode usar esse sistema para simplificar o caminho usado em pontos de montagem de cliente e dimensionar a capacidade diretamente, pois um caminho virtual pode acomodar o armazenamento de vários Filers principais.

![Página de assistente Adicionar nova junção com as configurações preenchidas](media/fxt-cluster-config/add-junction-full.png)

Consulte [ **vserver** > **namespace** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) no guia de configuração do cluster para obter detalhes completos sobre como criar uma junção de namespace.

![A página de configurações do namespace > VServer mostrando detalhes de uma junção](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Configurar regras de exportação

Depois de ter um vserver e um Filer principal, você deve personalizar as regras de exportação e exportar políticas que controlam como os clientes podem acessar arquivos nas exportações do Filer principal.

Primeiro, use a página **VServer** > **exportar regras** para adicionar novas regras, para modificar a política padrão ou para criar sua própria política de exportação personalizada.

Em segundo lugar, use a página **VServer** > **Exportar políticas** para aplicar a política personalizada às exportações do seu Filer principal quando acessadas por meio desse vserver.

Leia o artigo guia de configuração do cluster [controlando o acesso às exportações do principal Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) para obter detalhes.


## <a name="next-steps"></a>Passos seguintes

Depois de adicionar o armazenamento e configurar o namespace voltado para o cliente, conclua a configuração inicial do cluster: 

> [!div class="nextstepaction"]
> [Configurar as definições de rede do cluster](fxt-configure-network.md)
