---
title: Configure Armazenamento Avere vFXT - Azure
description: Saiba como adicionar um sistema de armazenamento back-end para um cluster em Avere vFXT para Azure. Se criou um recipiente Azure Blob com o cluster, está pronto a ser utilizado.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001529"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Este passo configura um sistema de armazenamento back-end para o seu cluster vFXT.

> [!TIP]
> Se criou um novo recipiente Azure Blob juntamente com o cluster Avere vFXT, esse recipiente já está configurado e pronto a ser utilizado.

Siga estas instruções se não criar um novo recipiente Blob com o seu cluster, ou se pretender adicionar um hardware adicional ou um sistema de armazenamento baseado na nuvem.

Há duas tarefas principais:

1. [Crie um ficheiro principal,](#create-a-core-filer)que ligue o seu cluster vFXT a um sistema de armazenamento existente ou a um recipiente de conta de armazenamento Azure.

1. [Crie uma junção de espaço de nome,](#create-a-junction)que define o caminho que os clientes vão montar.

Estes passos utilizam o Painel de Controlo avere. Leia [Aceda ao cluster vFXT](avere-vfxt-cluster-gui.md) para aprender a usá-lo.

## <a name="create-a-core-filer"></a>Criar um ficheiro central

"Core filer" é um termo vFXT para um sistema de armazenamento de back-end. O armazenamento pode ser um aparelho NAS de hardware como o NetApp ou o Isilon, ou pode ser uma loja de objetos em nuvem. Mais informações sobre ficheiros principais podem ser encontradas no guia de definições do [cluster Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um ficheiro principal, escolha um dos dois tipos principais de ficheiros principais:

* [NOS core filer](#nas-core-filer) - descreve como adicionar um ficheiro principal nas
* [Azure Storage cloud core filer](#azure-blob-storage-cloud-core-filer) - descreve como adicionar um recipiente de armazenamento Azure Blob como um filete de núcleo de nuvem

### <a name="nas-core-filer"></a>Filete de núcleo nas

Um ficheiro principal da NAS pode ser um aparelho NetApp ou Isilon no local, ou um ponto final nas na nuvem. O sistema de armazenamento deve ter uma ligação fiável de alta velocidade ao cluster Avere vFXT - por exemplo, uma ligação ExpressRoute de 1GBps (não uma VPN) - e deve dar ao cluster acesso raiz às exportações nas NASA que estão a ser utilizadas.

Siga estes passos para adicionar um ficheiro principal da NAS:

1. A partir do Painel de Controlo Avere, clique no **separador Definições** na parte superior.

1. Clique **em Ficheiros Core** Gerir  >  **Ficheiros Principais** à esquerda.

1. Clique em **Criar**.

   ![Screenshot da nova página de ficheiros do núcleo com um cursor sobre o botão Criar](media/avere-vfxt-add-core-filer-start.png)

1. Preencha as informações necessárias no assistente:

   * Diga o nome do seu ficheiro principal.
   * Forneça um nome de domínio totalmente qualificado (FQDN) se disponível. Caso contrário, forneça um endereço IP ou nome de anfitrião que se resolva para o seu ficheiro principal.
   * Escolha a sua classe de arquivador na lista. Se não tiver certeza, escolha **Outros**.

     ![Screenshot da nova página de ficheiros do núcleo com o nome do ficheiro principal e o seu nome de domínio totalmente qualificado](media/avere-vfxt-add-core-filer.png)
  
   * Clique **em Seguida** e escolha uma política de cache.
   * Clique **em Adicionar Filer**.
   * Para obter informações mais detalhadas, consulte a [Adição de um novo ficheiro principal nas](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) nas definições do cluster Avere.

Em seguida, proceda à [Criação de uma junção.](#create-a-junction)  

### <a name="azure-blob-storage-cloud-core-filer"></a>Filete de núcleo de nuvem de armazenamento de Azure Blob

Para utilizar o armazenamento Azure Blob como o armazenamento traseiro do seu cluster vFXT, precisa de um recipiente vazio para adicionar como um filete principal.

A adição de armazenamento Blob ao seu cluster requer estas tarefas:

* Criar uma conta de armazenamento (passo 1, abaixo)
* Criar um recipiente Blob vazio (passos 2-3)
* Adicione a chave de acesso ao armazenamento como uma credencial de nuvem para o cluster vFXT (passos 4-6)
* Adicione o recipiente Blob como um filete de núcleo para o cluster vFXT (passos 7-9)
* Crie uma junção de espaço de nome que os clientes usam para montar o ficheiro central[(Criar uma junção,](#create-a-junction)o mesmo para hardware e armazenamento em nuvem)

> [!TIP]
> Se criar um novo recipiente Blob quando criar um avere vFXT para cluster Azure, o modelo de implantação configura automaticamente o recipiente como um ficheiro principal. (Isto também é verdade se você usar o script de criação, que está disponível a pedido.) Não precisa de configurar o ficheiro principal depois.
>
> A ferramenta de criação de clusters faz estas tarefas de configuração para si:
>
> * Cria um novo recipiente Blob na conta de armazenamento fornecida
> * Define o recipiente como um ficheiro central
> * Cria uma junção de espaço de nome para o recipiente
> * Cria um ponto final de serviço de armazenamento dentro da rede virtual do cluster

Para adicionar o armazenamento Blob depois de criar o cluster, siga estes passos.

1. Crie uma conta de armazenamento V2 de uso geral com estas definições:

   * **Subscrição** - igual ao cluster vFXT
   * **Grupo de recursos** - o mesmo que o grupo de cluster vFXT (opcional)
   * **Localização** - igual ao cluster vFXT
   * **Desempenho** - Standard (Armazenamento premium não é suportado)
   * **Tipo de conta** - V2 de uso geral (StorageV2)
   * **Replicação** - Armazenamento localmente redundante (LRS)
   * **Nível de acesso** - Quente
   * **Transferência segura necessária** - desative esta opção (valor não padrão)
   * **Redes virtuais** - não necessárias

   Pode utilizar o portal Azure ou clicar no botão "Implementar para azul" abaixo.

   [![botão para criar conta de armazenamento](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Depois de a conta ser criada, navegue na página da conta de armazenamento.

   ![Nova conta de armazenamento no portal Azure](media/avere-vfxt-new-storage-acct.png)

1. Criar um novo recipiente Blob: Clique em **Recipientes** na página geral e, em seguida, clique em **+Contentor**. Utilize qualquer nome de recipiente e certifique-se de que o acesso está definido para **Private**.

   ![Página de bolhas de armazenamento com o botão +recipiente circulado e um novo recipiente sendo criado em uma página pop-up](media/avere-vfxt-new-blob.png)

1. Obtenha a tecla de conta de Armazenamento Azure clicando nas **teclas de acesso** em **Definições**. Copie uma das chaves fornecidas.

   ![Portal Azure GUI para copiar a chave](media/avere-vfxt-copy-storage-key.png)

1. Abra o Painel de Controlo avere para o seu cluster. Clique em **Definições** e, em seguida, abra as   >  **credenciais de nuvem** de cluster no painel de navegação à esquerda. Na página Cloud Credentials, clique **em Adicionar Credencial.**

   ![Clique no botão Adicionar Credencial na página de configuração de Credenciais de Nuvem](media/avere-vfxt-new-credential-button.png)

1. Preencha as seguintes informações para criar uma credencial para o ficheiro do núcleo de nuvem:

   | Campo | Valor |
   | --- | --- |
   | Nome da credencial | qualquer nome descritivo |
   | Tipo de serviço | (selecione chave de acesso ao armazenamento Azure) |
   | Inquilino | Nome da conta de armazenamento |
   | Subscrição | ID da subscrição |
   | Chave de acesso ao armazenamento | Chave da conta de armazenamento Azure (copiada no passo anterior) |

   Clique **em Submeter.**

   ![Formulário de credencial de nuvem preenchido no Painel de Controlo de Avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o ficheiro principal. No lado esquerdo do Painel de Controlo Avere, clique em Ficheiros Core **Filer**  >   **Manager**.

1. Clique no botão **Criar** na página **'Gerir ficheiros'.**

1. Preencha o assistente:

   * Selecione o tipo de **ficheiro Cloud**.
   * Nomeie o novo ficheiro principal e clique em **Seguinte**.
   * Aceite a política de cache padrão e continue para a terceira página.
   * No **tipo de Serviço,** escolha **o armazenamento Azure.**
   * Escolha a credencial criada anteriormente.
   * Definir **conteúdo de balde** para **esvaziar**
   * Alterar **verificação do certificado** para **deficientes**
   * Alterar **o modo de compressão** para **Nenhum**
   * Clique em **Seguinte**.
   * Na quarta página, insira o nome do recipiente em **nome balde** como *storage_account_name* / *container_name*.
   * Opcionalmente, desacrição **do tipo de encriptação** a **Nenhum**.  O Azure Storage é encriptado por padrão.
   * Clique **em Adicionar Filer**.

   Para obter informações mais detalhadas, leia [Adicionando um novo filete de núcleo](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) de nuvem no guia de configuração do cluster Avere.

A página irá refrescar-se ou pode refrescar a página para exibir o seu novo ficheiro principal.

Em seguida, é necessário [criar uma junção.](#create-a-junction)

## <a name="create-a-junction"></a>Criar uma junção

Uma junção é um caminho que se cria para os clientes. Os clientes montam o caminho e chegam ao destino que escolher.

Por exemplo, pode criar `/vfxt/files` para mapear para a exportação do seu ficheiro core NetApp `/vol0/data` e para a `/project/resources` subdiretório.

Mais informações sobre junções podem ser encontradas na [secção de espaço de nome do guia de configuração do cluster Avere.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)

Siga estes passos na interface do Painel de Controlo avere:

* Clique no espaço de nome **VServer**  >   na parte superior esquerda.
* Providenciar um caminho de espaço de nome começando com / (barra para a frente), como ``/vfxt/data`` .
* Escolha o seu ficheiro principal.
* Escolha a exportação de filetes principais.
* Clique em **Seguinte**.

  ![Screenshot da página "Adicionar nova junção" com os campos concluídos para junção, filete de núcleo e exportação](media/avere-vfxt-add-junction.png)

A junção aparecerá após alguns segundos. Crie junções adicionais conforme necessário.

Após a junção ter sido criada, os clientes usam o caminho do espaço de nome para aceder aos ficheiros a partir do sistema de armazenamento.

## <a name="next-steps"></a>Passos seguintes

* [Montar o cluster Avere vFXT](avere-vfxt-mount-clients.md)
* Aprenda formas eficientes de [mover dados para um novo recipiente Blob](avere-vfxt-data-ingest.md)
