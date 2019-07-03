---
title: Adicionar armazenamento de back-end para o cluster de ficheiros do Microsoft Azure FXT Edge
description: Como configurar o armazenamento de back-end e o pseudonamespace com clientes para ficheiros do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 4a69aa7838e08c83b47c5f0248e821edf86b3990
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543258"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Tutorial: Adicionar armazenamento de back-end e configurar o espaço de nomes virtual 

Este tutorial explica como adicionar armazenamento de back-edge para a sua cache e como configurar o sistema de ficheiros virtual com clientes. 

O cluster liga-se aos sistemas de armazenamento de back-end para aceder a pedido de dados de clientes e para armazenar as alterações mais permanentemente do que na cache. 

O espaço de nomes é o sistema de ficheiros de pseudo-autenticação com clientes que pode alternar o armazenamento de back-end sem alteração fluxos de trabalho do lado do cliente. 

Neste tutorial, vai aprender: 

> [!div class="checklist"]
> * Como adicionar armazenamento de back-end para o cluster de ficheiros do Azure FXT Edge 
> * Como definir o caminho de com clientes de armazenamento

## <a name="about-back-end-storage"></a>Sobre o armazenamento de back-end

O cluster de ficheiros do Azure FXT Edge utiliza um *núcleos de filtro de* definição para ligar um sistema de armazenamento de back-end para o cluster FXT.

Filtro de borda FXT do Azure é compatível com vários sistemas de hardware NAS populares e pode utilizar vazios contentores de Blobs do Azure ou outro armazenamento na cloud. 

Contentores de armazenamento na cloud pode estar vazios quando adicionados para que o sistema de operativo FXT completamente pode gerir todos os dados no volume de armazenamento na cloud. Pode mover os dados existentes para o contentor de cloud depois de adicionar o contentor para o cluster como um filtro de núcleo.

Utilize o painel de controle para adicionar um filtro de núcleo para seu sistema.

> [!NOTE]
> 
> Se pretender utilizar o armazenamento de AWS da Amazon ou o Google Cloud, tem de instalar um FlashCloud<sup>TM</sup> licença de funcionalidade. Contacte o seu representante da Microsoft para uma chave de licença e, em seguida, siga as instruções no guia de configuração herdadas do [adicionar ou remover licenças de funcionalidade](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Suporte para armazenamento de Blobs do Azure está incluído na licença de software de ficheiros do Azure FXT Edge. 

Para obter informações mais detalhadas sobre como adicionar se filtram de núcleos, leia estas secções do guia de configuração do Cluster:

* Para mais informações sobre escolher e preparar adicionar um filtro de núcleos, leia [trabalhar com núcleo se filtram](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Para pré-requisitos detalhados e instruções passo a passo, leia os seguintes artigos:

  * [Adicionar um novo filtro de núcleo NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Adicionar um novo filtro de núcleos na Cloud](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Depois de adicionar um filtro de núcleos, pode atualizar as definições na página de definições de detalhes do filtro de núcleo.

## <a name="add-a-core-filer"></a>Adicionar um filtro de núcleo

Definir um filtro básico ao clicar no **Create** botão o **filtro de núcleo** > **se filtram Core de gerir** página de definições.

![Clicar no botão Criar acima da lista de se filtram de núcleos na página se filtram Core de gerir](media/fxt-cluster-config/create-core-filer-button.png)

O **adicionar novo filtro de núcleo** assistente o orienta pelo processo de criação de um filtro de núcleos que liga para o armazenamento de back-end. O guia de configuração de Cluster tem descrições passo a passo do processo, o que é diferente para armazenamento NFS/NAS e de armazenamento na cloud (ligações são acima). 

Subtarefas incluem:

* Especifique o tipo de filtro de núcleo (NAS ou na cloud)

  ![Primeira página de hardware NAS principais filtro do Assistente de novo. A opção para "filtro de núcleo de cloud" está desabilitada e mostra uma mensagem de erro sobre a licença em falta.](media/fxt-cluster-config/new-nas-1.png)

* Defina nome do filtro de núcleo. Escolha um nome que ajuda os administradores de cluster compreender o que representa o sistema de armazenamento.

* Para se filtram de núcleos NAS, forneça o nome de domínio completamente qualificado (FQDN) ou o endereço IP. FQDN é recomendado para todos os se filtram de núcleos e necessárias para o acesso SMB.

* Selecione uma política de cache, a segunda página do assistente lista as políticas de cache disponíveis para o novo filtro de núcleo. Para obter detalhes, leia os [políticas de seção do guia de configuração de Cluster de cache](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Segunda página um hardware NAS principais filtro do Assistente de novo; o menu de lista pendente de política de Cache é aberto, que mostra que várias desativada opções e três opções de política de cache válida (ignorar, leia a colocação em cache e colocação em cache de leitura/escrita).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Para armazenamento na cloud, tem de especificar as cloud service credenciais de acesso e, entre outros parâmetros. Para obter detalhes, leia [Cloud service e o protocolo](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) no guia de configuração de Cluster.

  ![Informações de filtro de núcleo de nuvem no Assistente de novo filtro de núcleo](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Se já tiver adicionado na cloud de credenciais de acesso para este cluster, eles aparecem na lista. Atualizar e adicionar as credenciais no **Cluster** > **credênciais de Cloud** página de definições. 

Depois de preencher todas as definições necessárias no assistente, clique nas **Adicionar filtro** botão para submeter a alteração.

Após alguns instantes, o sistema de armazenamento aparece no **Dashboard** básicos lista se filtram e podem ser acedidos através de páginas de definições de filtro de núcleo.

![o filtro de núcleo "Flurry-NAS" na página de definições de gerir se filtram de núcleo, com a vista de detalhes de ficheiros expandidos](media/fxt-cluster-config/core-filer-in-manage-page.png)

O filtro de núcleos nesta captura de ecrã está em falta um vserver. Tem de ligar o filtro de núcleos para um vserver e criar uma junção, para que os clientes podem aceder ao armazenamento. Essas etapas são descritas abaixo num [configurar o espaço de nomes](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Configurar o espaço de nomes

O cluster de ficheiros do Azure FXT Edge cria um sistema de ficheiros virtual chamado os *espaço de nomes de cluster* que simplifica o acesso de cliente para os dados armazenados em vários sistemas de back-end. Uma vez que os clientes solicitam arquivos usando um caminho virtual, os sistemas de armazenamento podem ser adicionados ou substituídos sem ter de alterar o fluxo de trabalho do cliente. 

O espaço de nomes de cluster também permite-lhe apresentar na cloud e sistemas de armazenamento de uma estrutura de ficheiros semelhantes. 

Vservers do cluster de manter o espaço de nomes e a servir de conteúdo aos clientes. Existem dois passos para criar o espaço de nomes de cluster: 

1. Criar um vserver 
1. Configurar junções entre os sistemas de armazenamento de back-end e os caminhos do sistema de ficheiros com clientes 

### <a name="create-a-vserver"></a>Criar um vserver

VServers são servidores de arquivos virtual que controlam a forma como os dados fluem entre o cliente e se filtram de principais do cluster:

* Endereços IP do VServers anfitrião com clientes
* VServers criar o espaço de nomes e defina junções de mapeiam a estrutura de diretório virtual do cliente voltadas para exportações no armazenamento de back-end
* VServers impor controlos de acesso do ficheiro, incluindo políticas de exportação de filtro de núcleos e sistemas de autenticação de utilizador
* VServers disponibilizam a infraestrutura SMB

Antes de começar a configurar um cluster vserver, leia a documentação ligada e consulte o seu representante da Microsoft para o espaço de nomes de compreensão de ajuda e vservers. Se utilizar VLANs, [criá-los](fxt-configure-network.md#adjust-network-settings) antes de criar o vserver. 

Estas secções do guia de configuração do Cluster irão ajudá-lo a familiarizar-se com as funcionalidades de global namespace e FXT vserver:

* [Criar e trabalhar com VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Utilizar um espaço de nomes Global](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Criar um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Precisa vserver, pelo menos, um para o seu cluster. 

Para criar um novo vserver, terá das seguintes informações:

* O nome a definir para o vserver

* O intervalo de endereços IP com clientes processará o vserver

  Quando cria o vserver, deve fornecer um único intervalo de endereços IP contíguos. Pode adicionar mais endereços posteriormente utilizando o **rede com acesso do cliente** página de definições.

* Se a rede tiver VLANs, qual VLAN a utilizar para este vserver

Utilize o **VServer** > **gerir VServers** página de definições para criar um novo vserver. Para obter detalhes, leia [criando um VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) no guia de configuração de Cluster. 

![janela de pop-up para a criação de um novo vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Criar uma junção

R *junção* mapeia um caminho de armazenamento de back-end para o espaço de nomes visíveis para o cliente.

Pode utilizar este sistema para simplificar o caminho utilizado em pontos de montagem de cliente e aumentar a capacidade de forma totalmente integrada como um caminho virtual pode acomodar armazenamento a partir de vários se filtram de núcleo.

![Adicionar página do Assistente de nova junção com definições preenchidas](media/fxt-cluster-config/add-junction-full.png)

Consulte a [ **VServer** > **espaço de nomes** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) no guia de configuração de Cluster para obter detalhes completos sobre a criação de uma junção de espaço de nomes.

![O VServer > página de definições de espaço de nomes que mostra os detalhes para uma junção](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Configurar regras de exportação

Depois de ter um vserver e um filtro de núcleos, deve personalizar as regras de exportação e exportar políticas que controlam como os clientes podem aceder a ficheiros nas exportações de filtro de núcleo.

Em primeiro lugar, utilize o **VServer** > **exportar regras** página para adicionar novas regras, para modificar a política predefinida ou para criar a sua política personalizada de exportação.

Em segundo lugar, utilize o **VServer** > **exportar políticas** página para aplicar a política personalizada para exportações de seu filtro de núcleos, quando acessado por meio desse vserver.

Leia o artigo guia de configuração de Cluster [controlar o acesso para exportações de filtro de núcleo](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) para obter detalhes.


## <a name="next-steps"></a>Passos Seguintes

Depois de adicionar armazenamento e configurar o espaço de nomes com clientes, conclua a configuração inicial do seu cluster: 

> [!div class="nextstepaction"]
> [Configurar as definições de rede do cluster](fxt-configure-network.md)
