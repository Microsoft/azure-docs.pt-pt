---
title: Configure Armazenamento VFXT - Azure
description: Como adicionar um sistema de armazenamento back-end ao seu Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372204"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Este passo configura um sistema de armazenamento traseiro para o seu cluster vFXT.

> [!TIP]
> Se criou um novo recipiente Azure Blob juntamente com o cluster Avere vFXT, esse recipiente já está configurado e pronto a ser utilizado.

Siga estas instruções se não criou um novo recipiente Blob com o seu cluster, ou se quiser adicionar um sistema adicional de armazenamento baseado em hardware ou nuvem.

Há duas tarefas principais:

1. [Crie um filer core](#create-a-core-filer), que liga o seu cluster vFXT a um sistema de armazenamento existente ou a um recipiente de conta Azure Storage.

1. [Crie uma junção espaço-nome,](#create-a-junction)que define o caminho que os clientes irão montar.

Estes passos utilizam o Painel de Controlo Avere. Leia [Aceda ao cluster vFXT](avere-vfxt-cluster-gui.md) para aprender a usá-lo.

## <a name="create-a-core-filer"></a>Criar um filer central

"Core filer" é um termo vFXT para um sistema de armazenamento de back-end. O armazenamento pode ser um aparelho NAS de hardware como netApp ou Isilon, ou pode ser uma loja de objetos em nuvem. Mais informações sobre os ficheiros centrais podem ser encontradas no guia de definições do [cluster Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um filer core, escolha um dos dois principais tipos de ficheiros principais:

* [Ficheiro principal da NAS](#nas-core-filer) - descreve como adicionar um ficheiro principal nas
* Ficheiro de núcleo de [nuvem de armazenamento Azure](#azure-blob-storage-cloud-core-filer) - descreve como adicionar um recipiente de armazenamento Azure Blob como um filedor de núcleo de nuvem

### <a name="nas-core-filer"></a>Arquivo principal da NAS

Um filete de núcleo NAS pode ser um netapp ou um aparelho Isilon no local, ou um ponto final nas na nuvem. O sistema de armazenamento deve ter uma ligação fiável de alta velocidade ao cluster Avere vFXT - por exemplo, uma ligação 1GBps ExpressRoute (não uma VPN) - e deve dar ao cluster root acesso às exportações nas que estão a ser utilizadas.

Siga estes passos para adicionar um ficheiro principal da NAS:

1. A partir do Painel de Controlo Avere, clique no separador **Definições** na parte superior.

1. Clique no **Ficheiro Core** > **gerir ficheiros core** à esquerda.

1. Clique em **Criar**.

   ![Screenshot da nova página de ficheiros core Adicionar com um cursor sobre o botão Criar](media/avere-vfxt-add-core-filer-start.png)

1. Preencha as informações necessárias no assistente:

   * Diga o nome do seu arquivo principal.
   * Forneça um nome de domínio totalmente qualificado (FQDN) se disponível. Caso contrário, forneça um endereço IP ou nome de anfitrião que se resolva ao seu ficheiro principal.
   * Escolha a sua classe de arquivo da lista. Se não tiver a certeza, escolha **Outros.**

     ![Screenshot da nova página de ficheiros core Add com o nome central do filer e o seu nome de domínio totalmente qualificado](media/avere-vfxt-add-core-filer.png)
  
   * Clique em **Seguir** e escolha uma política de cache.
   * Clique em **Adicionar Filer**.
   * Para obter informações mais detalhadas, consulte a [adição de um novo ficheiro central nasno](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) no guia de definições do cluster Avere.

Em seguida, proceda à [Criação de uma junção.](#create-a-junction)  

### <a name="azure-blob-storage-cloud-core-filer"></a>Ficheiro de núcleo de nuvem de armazenamento Azure Blob

Para utilizar o armazenamento Azure Blob como armazenamento traseiro do seu cluster vFXT, precisa de um recipiente vazio para adicionar como um filer principal.

Adicionar o armazenamento blob ao seu cluster requer estas tarefas:

* Criar uma conta de armazenamento (passo 1, abaixo)
* Criar um recipiente blob vazio (passos 2-3)
* Adicione a chave de acesso ao armazenamento como uma credencial em nuvem para o cluster vFXT (passos 4-6)
* Adicione o recipiente Blob como um filer de núcleo para o cluster vFXT (passos 7-9)
* Crie uma junção espaço-nome que os clientes usam para montar o filer central ([Criar uma junção](#create-a-junction), o mesmo para hardware e armazenamento em nuvem)

> [!TIP]
> Se criar um novo recipiente Blob quando criar um Avere vFXT para o cluster Azure, o modelo de implantação configura automaticamente o recipiente como um filer de núcleo. (Isto também é verdade se utilizar o script de criação, que está disponível a pedido.) Não precisa de configurar o ficheiro principal depois.
>
> A ferramenta de criação de cluster saem destas tarefas de configuração para si:
>
> * Cria um novo recipiente Blob na conta de armazenamento fornecida
> * Define o recipiente como um filer de núcleo
> * Cria uma junção espaço-nome para o recipiente
> * Cria um ponto final de serviço de armazenamento dentro da rede virtual do cluster

Para adicionar o armazenamento Blob depois de criar o cluster, siga estes passos.

1. Crie uma conta de armazenamento V2 de uso geral com estas definições:

   * **Subscrição** - o mesmo que o cluster vFXT
   * **Grupo de recursos** - o mesmo que o grupo de cluster vFXT (opcional)
   * **Localização** - o mesmo que o cluster vFXT
   * **Desempenho** - Standard (O armazenamento premium não suportado)
   * **Tipo de conta** - V2 de uso geral (StorageV2)
   * **Replicação** - Armazenamento localmente redundante (LRS)
   * **Nível de acesso** - Quente
   * **Transferência segura necessária** - desative esta opção (valor não predefinido)
   * **Redes virtuais** - não necessárias

   Pode utilizar o portal Azure ou clicar no botão "Implementar para Azure" abaixo.

   [![botão para criar conta de armazenamento](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Depois de criada a conta, navegue na página da conta de armazenamento.

   ![Nova conta de armazenamento no portal Azure](media/avere-vfxt-new-storage-acct.png)

1. Criar um novo recipiente Blob: Clique em **Recipientes** na página de visão geral e, em seguida, clique em **+Container**. Utilize qualquer nome de contentor e certifique-se de que o acesso está definido para **Privado**.

   ![Página de blobs de armazenamento com o botão +contentor circulado e um novo recipiente sendo criado em uma página pop-up](media/avere-vfxt-new-blob.png)

1. Obtenha a chave da conta De armazenamento Azure clicando em **teclas** de acesso em **Definições**. Copie uma das chaves fornecidas.

   ![Portal Azure GUI para copiar a chave](media/avere-vfxt-copy-storage-key.png)

1. Abra o Painel de Controlo de Avere para o seu cluster. Clique em **Definições**, em seguida, abra **o Cluster** > **Credenciais** cloud no painel de navegação esquerdo. Na página Cloud Credentials, clique em **Adicionar Credencial**.

   ![Clique no botão Adicionar Credencial na página de configuração de Credenciais cloud](media/avere-vfxt-new-credential-button.png)

1. Preencha as seguintes informações para criar uma credencial para o filedor de núcleo de nuvem:

   | Campo | Valor |
   | --- | --- |
   | Nome da credencial | qualquer nome descritivo |
   | Tipo de serviço | (selecione chave de acesso ao armazenamento Azure) |
   | Inquilino | Nome da conta de armazenamento |
   | Subscrição | ID da subscrição |
   | Chave de acesso ao armazenamento | Chave da conta de armazenamento azure (copiada no passo anterior) |

   Clique em **Submeter**.

   ![Forma credencial de nuvem completa em Painel de Controlo de Avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o ficheiro principal. No lado esquerdo do Painel de Controlo Avere, clique em **Core Filer** >  **Gerir Ficheiros Core**.

1. Clique no botão **Criar** na página de definições de **'Gerir ficheiros Core'.**

1. Preencha o assistente:

   * Selecione o tipo de ficheiro **Cloud**.
   * Nomeie o novo ficheiro principal e clique **em Seguinte**.
   * Aceite a política de cache padrão e continue para a terceira página.
   * No **tipo de serviço,** escolha **o armazenamento Azure.**
   * Escolha a credencial criada anteriormente.
   * Definir **o conteúdo do balde** para **esvaziar**
   * Alterar **a verificação** do certificado para **deficientes**
   * Alterar **o modo de compressão** para **nenhum**
   * Clique em **Seguinte**.
   * Na quarta página, introduza o nome do recipiente em **nome balde** como *storage_account_name*/*container_name*.
   * Opcionalmente, definir **o tipo de encriptação** para **Nenhum**.  O Armazenamento Azure é encriptado por padrão.
   * Clique em **Adicionar Filer**.

   Para obter informações mais detalhadas, leia [Adicionar um novo ficheiro de núcleo](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) de nuvem no guia de configuração do cluster Avere.

A página irá refrescar-se ou pode refrescar a página para exibir o seu novo ficheiro principal.

Em seguida, tens de [criar uma junção.](#create-a-junction)

## <a name="create-a-junction"></a>Criar uma junção

Uma junção é um caminho que se cria para os clientes. Os clientes montam o caminho e chegam ao destino que escolher.

Por exemplo, pode criar `/vfxt/files` para mapear o seu arquivo central NetApp `/vol0/data` exportar e o subdiretório `/project/resources`.

Mais informações sobre junções podem ser encontradas na [secção espaço de nome do guia de configuração do cluster Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Siga estes passos na interface do Painel de Controlo de Avere:

* Clique no **VServer** > **Espaço de Nome** na parte superior esquerda.
* Forneça um caminho espaço de nome a partir de /(barra para a frente), como ``/vfxt/data``.
* Escolha o seu ficheiro principal.
* Escolha a exportação de filer principal.
* Clique em **Seguinte**.

  ![Screenshot da página "Adicionar nova junção" com os campos concluídos para junção, filer de núcleo e exportação](media/avere-vfxt-add-junction.png)

A junção aparecerá após alguns segundos. Crie junções adicionais, se necessário.

Após a criação da junção, os clientes usam o caminho do espaço de nome para aceder aos ficheiros do sistema de armazenamento.

## <a name="next-steps"></a>Passos seguintes

* [Montar o cluster Avere vFXT](avere-vfxt-mount-clients.md)
* Aprenda formas eficientes [de mover dados para um novo recipiente Blob](avere-vfxt-data-ingest.md)
