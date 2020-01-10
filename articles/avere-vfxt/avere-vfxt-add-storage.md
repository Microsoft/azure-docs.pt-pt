---
title: Configurar o armazenamento avere vFXT-Azure
description: Como adicionar um sistema de armazenamento de back-end ao avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 3f7d7b5091b6439f17455b5ea66a3a23ebf79811
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75416424"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Esta etapa configura um sistema de armazenamento de back-end para o cluster vFXT.

> [!TIP]
> Se você criou um novo contêiner de blob do Azure junto com o cluster avere vFXT, esse contêiner já está configurado e pronto para uso.

Siga estas instruções se você não criou um novo contêiner de blob com o cluster ou se deseja adicionar um hardware adicional ou sistema de armazenamento baseado em nuvem.

Há duas tarefas principais:

1. [Crie um Filer principal](#create-a-core-filer), que conecta o cluster vFXT a um sistema de armazenamento existente ou a um contêiner de conta de armazenamento do Azure.

1. [Crie uma junção de namespace](#create-a-junction), que define o caminho que os clientes montarão.

Essas etapas usam o painel de controle avere. Leia [acessar o cluster vFXT](avere-vfxt-cluster-gui.md) para saber como usá-lo.

## <a name="create-a-core-filer"></a>Criar um Filer principal

O "principal Filer" é um termo vFXT para um sistema de armazenamento de back-end. O armazenamento pode ser um dispositivo NAS de hardware como NetApp ou Isilon, ou pode ser um armazenamento de objeto de nuvem. Mais informações sobre os Filers principais podem ser encontradas no [Guia de configurações do cluster avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um Filer principal, escolha um dos dois tipos principais de filers principais:

* [Filer Core do nas](#nas-core-filer) -descreve como adicionar um Filer do nas core
* [Filer Core de nuvem de armazenamento do Azure](#azure-blob-storage-cloud-core-filer) -descreve como adicionar um contêiner de armazenamento de BLOBs do Azure como um Filer do Cloud Core

### <a name="nas-core-filer"></a>Filer do núcleo do NAS

Um Filer do NAS core pode ser um dispositivo NetApp ou Isilon local ou um ponto de extremidade NAS na nuvem. O sistema de armazenamento deve ter uma conexão confiável de alta velocidade com o cluster avere vFXT, por exemplo, uma conexão de ExpressRoute 1 Gbps (não uma VPN), e deve fornecer ao cluster acesso de raiz para as exportações de NAS usadas.

Siga estas etapas para adicionar um Filer do NAS Core:

1. No painel de controle avere, clique na guia **configurações** na parte superior.

1. Clique em **principal filer** > **gerenciar os principais Filers** à esquerda.

1. Clique em **Criar**.

   ![Captura de tela da página Adicionar novo Filer principal com um cursor sobre o botão criar](media/avere-vfxt-add-core-filer-start.png)

1. Preencha as informações necessárias no assistente:

   * Nomeie o seu Filer principal.
   * Forneça um nome de domínio totalmente qualificado (FQDN), se disponível. Caso contrário, forneça um endereço IP ou nome de host que seja resolvido para o seu Filer principal.
   * Escolha a classe de filer na lista. Se não tiver certeza, escolha **outros**.

     ![Captura de tela da página Adicionar novo Filer principal com o nome do arquivo principal e seu nome de domínio totalmente qualificado](media/avere-vfxt-add-core-filer.png)
  
   * Clique em **Avançar** e escolha uma política de cache.
   * Clique em **Adicionar Filer**.
   * Para obter informações mais detalhadas, consulte [adicionando um novo Filer do nas core](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) no guia de configurações do cluster avere.

Em seguida, vá para [criar uma junção](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage Cloud principal Filer

Para usar o armazenamento de BLOBs do Azure como o armazenamento de back-end do cluster vFXT, você precisa de um contêiner vazio para adicionar como um Filer principal.

A adição do armazenamento de BLOBs ao cluster requer estas tarefas:

* Criar uma conta de armazenamento (etapa 1, abaixo)
* Criar um contêiner de blob Vazio (etapas 2-3)
* Adicione a chave de acesso de armazenamento como uma credencial de nuvem para o cluster vFXT (etapas 4-6)
* Adicione o contêiner de blob como um Filer principal para o cluster vFXT (etapas 7-9)
* Criar uma junção de namespace que os clientes usam para montar o filer principal ([criar uma junção](#create-a-junction), mesmo para armazenamento de hardware e de nuvem)

> [!TIP]
> Se você criar um novo contêiner de blob ao criar um avere vFXT para o cluster do Azure, o modelo de implantação configurará automaticamente o contêiner como um Filer central. (Isso também será verdadeiro se você usar o script de criação, que está disponível na solicitação.) Você não precisa configurar o filer principal posteriormente.
>
> A ferramenta de criação de cluster executa essas tarefas de configuração para você:
>
> * Cria um novo contêiner de blob na conta de armazenamento fornecida
> * Define o contêiner como um Filer principal
> * Cria uma junção de namespace para o contêiner
> * Cria um ponto de extremidade de serviço de armazenamento dentro da rede virtual do cluster

Para adicionar o armazenamento de blob depois de criar o cluster, siga estas etapas.

1. Crie uma conta de armazenamento de uso geral V2 com estas configurações:

   * **Assinatura** -o mesmo que o cluster vFXT
   * **Grupo de recursos** -o mesmo que o grupo de clusters vFXT (opcional)
   * **Local** -o mesmo que o cluster vFXT
   * **Desempenho** -padrão (não há suporte para o armazenamento Premium)
   * **Tipo de conta** -uso geral v2 (StorageV2)
   * **Replicação** – armazenamento com redundância local (LRS)
   * **Camada de acesso** -quente
   * **Transferência segura necessária** -desabilitar esta opção (valor não padrão)
   * **Redes virtuais** – não é necessário

   Você pode usar o portal do Azure ou clicar no botão "implantar no Azure" abaixo.

   [botão ![para criar uma conta de armazenamento](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Depois que a conta for criada, navegue até a página da conta de armazenamento.

   ![Nova conta de armazenamento no portal do Azure](media/avere-vfxt-new-storage-acct.png)

1. Criar um novo contêiner de BLOBs: clique em **contêineres** na página Visão geral e, em seguida, clique em **+ contêiner**. Use qualquer nome de contêiner e verifique se o acesso está definido como **particular**.

   ![Página blobs de armazenamento com o botão + contêiner circulado e um novo contêiner sendo criado em uma página pop-up](media/avere-vfxt-new-blob.png)

1. Obtenha a chave de conta de armazenamento do Azure clicando em **chaves de acesso** em **configurações**. Copie uma das chaves fornecidas.

   ![portal do Azure GUI para copiar a chave](media/avere-vfxt-copy-storage-key.png)

1. Abra o painel de controle do avere para o cluster. Clique em **configurações**e abra o **cluster** > **credenciais de nuvem** no painel de navegação esquerdo. Na página credenciais de nuvem, clique em **Adicionar credencial**.

   ![Clique no botão Adicionar credencial na página de configuração de credenciais de nuvem](media/avere-vfxt-new-credential-button.png)

1. Preencha as seguintes informações para criar uma credencial para o filer do Cloud Core:

   | Campo | Valor |
   | --- | --- |
   | Nome da credencial | qualquer nome descritivo |
   | Tipo de serviço | (selecione a chave de acesso de armazenamento do Azure) |
   | Inquilino | Nome da conta de armazenamento |
   | Subscrição | ID da subscrição |
   | Chave de acesso de armazenamento | Chave de conta de armazenamento do Azure (copiada na etapa anterior) |

   Clique em **Submit** (Submeter).

   ![Formulário de credencial de nuvem concluído no painel de controle do avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o filer principal. No lado esquerdo do painel de controle do avere, clique em **principal filer** >  **gerenciar os principais Filers**.

1. Clique no botão **criar** na página de configurações **gerenciar arquivos principais** .

1. Preencha o assistente:

   * Selecione Filer tipo **Cloud**.
   * Nomeie o novo Filer principal e clique em **Avançar**.
   * Aceite a política de cache padrão e continue na terceira página.
   * Em **tipo de serviço**, escolha **armazenamento do Azure**.
   * Escolha a credencial criada anteriormente.
   * Definir **conteúdo do Bucket** como **vazio**
   * Alterar **verificação de certificado** para **desabilitado**
   * Alterar o **modo de compactação** para **nenhum**
   * Clique em **Seguinte**.
   * Na quarta página, insira o nome do contêiner no nome do **Bucket** como *storage_account_name*/*container_name*.
   * Opcionalmente, defina o **tipo de criptografia** como **nenhum**.  O armazenamento do Azure é criptografado por padrão.
   * Clique em **Adicionar Filer**.

   Para obter informações mais detalhadas, leia [adicionando um novo Filer do Cloud Core](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) no guia de configuração do cluster avere.

A página será atualizada ou você poderá atualizar a página para exibir o novo Filer principal.

Em seguida, você precisa [criar uma junção](#create-a-junction).

## <a name="create-a-junction"></a>Criar uma junção

Uma junção é um caminho que você cria para clientes. Os clientes montam o caminho e chegam ao destino escolhido.

Por exemplo, você pode criar `/vfxt/files` para mapear para o seu Filer do NetApp Core `/vol0/data` exportar e o subdiretório `/project/resources`.

Mais informações sobre junções podem ser encontradas na [seção namespace do guia de configuração do cluster avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Siga estas etapas na interface do painel de controle do avere:

* Clique em **VServer** > **namespace** no canto superior esquerdo.
* Forneça um caminho de namespace que comece com/(barra "/"), como ``/vfxt/data``.
* Escolha o seu arquivador principal.
* Escolha a exportação do Filer principal.
* Clique em **Seguinte**.

  ![Captura de tela da página "Adicionar nova junção" com os campos concluídos para junção, principal filer e exportar](media/avere-vfxt-add-junction.png)

A junção será exibida após alguns segundos. Crie junções adicionais conforme necessário.

Depois que a junção tiver sido criada, os clientes usarão o caminho do namespace para acessar os arquivos do sistema de armazenamento.

## <a name="next-steps"></a>Passos seguintes

* [Montar o cluster Avere vFXT](avere-vfxt-mount-clients.md)
* Aprenda maneiras eficientes de [mover dados para um novo contêiner de BLOBs](avere-vfxt-data-ingest.md)
