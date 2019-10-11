---
title: Configurar o armazenamento avere vFXT-Azure
description: Como adicionar um sistema de armazenamento de back-end ao avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: rohogue
ms.openlocfilehash: 86b63e6d9799387347093e469015fbd3019069d1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255053"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Esta etapa configura um sistema de armazenamento de back-end para o cluster vFXT.

> [!TIP]
> Se você tiver criado um novo contêiner de blob do Azure junto com o cluster avere vFXT, esse contêiner já estará configurado para uso e você não precisará adicionar armazenamento.

Siga estas instruções se você não criou um novo contêiner de blob com o cluster ou se deseja adicionar um hardware adicional ou sistema de armazenamento baseado em nuvem.

Há duas tarefas principais:

1. [Crie um Filer principal](#create-a-core-filer), que conecta o cluster vFXT a um sistema de armazenamento existente ou a uma conta de armazenamento do Azure.

1. [Crie uma junção de namespace](#create-a-junction), que define o caminho que os clientes montarão.

Essas etapas usam o painel de controle avere. Leia [acessar o cluster vFXT](avere-vfxt-cluster-gui.md) para saber como usá-lo.

## <a name="create-a-core-filer"></a>Criar um Filer principal

O "principal Filer" é um termo vFXT para um sistema de armazenamento de back-end. O armazenamento pode ser um dispositivo NAS de hardware como NetApp ou Isilon, ou pode ser um armazenamento de objeto de nuvem. Mais informações sobre os Filers principais podem ser encontradas [no guia de configurações do cluster avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um Filer principal, escolha um dos dois tipos principais de filers principais:

  * [Filer Core do nas](#nas-core-filer) -descreve como adicionar um Filer do nas core 
  * [Filer Core de nuvem de armazenamento do Azure](#azure-storage-cloud-core-filer) -descreve como adicionar uma conta de armazenamento do Azure como um arquivo de nuvem do Cloud Core

### <a name="nas-core-filer"></a>Filer do núcleo do NAS

Um Filer do NAS core pode ser um NetApp ou Isilon local ou um ponto de extremidade do NAS na nuvem. O sistema de armazenamento deve ter uma conexão confiável de alta velocidade com o cluster avere vFXT, por exemplo, uma conexão de ExpressRoute 1 Gbps (não uma VPN), e deve fornecer ao cluster acesso de raiz para as exportações de NAS usadas.

As etapas a seguir adicionam um Filer do NAS Core:

1. No painel de controle avere, clique na guia **configurações** na parte superior.

1. Clique em **principal filer** > **gerenciar Filers principais** à esquerda.

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

### <a name="azure-storage-cloud-core-filer"></a>Arquivador de núcleo de nuvem de armazenamento do Azure

Para usar o armazenamento de BLOBs do Azure como o armazenamento de back-end do cluster vFXT, você precisa de um contêiner vazio para adicionar como um Filer principal.

> [!TIP] 
> Se você optar por criar um contêiner de blob ao mesmo tempo em que criar o cluster avere vFXT, o modelo de implantação ou o script criará um contêiner de armazenamento, o definirá como um Filer principal e criará a junção de namespace como parte da criação do cluster vFXT. O modelo também cria um ponto de extremidade de serviço de armazenamento dentro da rede virtual do cluster. 

A adição do armazenamento de BLOBs ao cluster requer estas tarefas:

* Criar uma conta de armazenamento (etapa 1, abaixo)
* Criar um contêiner de blob Vazio (etapas 2-3)
* Adicione a chave de acesso de armazenamento como uma credencial de nuvem para o cluster vFXT (etapas 4-6)
* Adicione o contêiner de blob como um Filer principal para o cluster vFXT (etapas 7-9)
* Criar uma junção de namespace que os clientes usam para montar o filer principal ([criar uma junção](#create-a-junction), mesmo para armazenamento de hardware e de nuvem)

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

   [![button para criar a conta de armazenamento](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Depois que a conta for criada, navegue até a página da conta de armazenamento.

   ![Nova conta de armazenamento no portal do Azure](media/avere-vfxt-new-storage-acct.png)

1. Crie um contêiner de blob clicando em **BLOBs** na página Visão geral e, em seguida, clique em **+ contêiner**. Use qualquer nome de contêiner e verifique se o acesso está definido como **particular**.

   ![Página blobs de armazenamento sem contêineres existentes](media/avere-vfxt-blob-no-container.png)

1. Obtenha a chave de conta de armazenamento do Azure clicando em **chaves de acesso** em **configurações**:

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

   Clique em **Enviar**.

   ![Formulário de credencial de nuvem concluído no painel de controle do avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o filer principal. No lado esquerdo do painel de controle avere, clique em **principal filer** >  **gerenciar Filers principais**. 

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
   * Na quarta página, digite o nome do contêiner no nome do **Bucket** como *storage_account_name*/*container_name*.
   * Opcionalmente, defina o **tipo de criptografia** como **nenhum**.  O armazenamento do Azure é criptografado por padrão.
   * Clique em **Adicionar Filer**.

   Para obter informações mais detalhadas, leia [adicionando um novo Filer do Cloud Core](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) no guia de configuração do cluster avere. 

A página será atualizada ou você poderá atualizar a página para exibir o novo Filer principal.

Em seguida, você precisa [criar uma junção](#create-a-junction).

## <a name="create-a-junction"></a>Criar uma junção

Uma junção é um caminho que você cria para clientes. Os clientes montam o caminho e chegam ao destino escolhido.

Por exemplo, você pode criar `/avere/files` para mapear para o arquivo de exportação do NetApp Core `/vol0/data` e o subdiretório `/project/resources`.

Mais informações sobre junções podem ser encontradas na [seção namespace do guia de configuração do cluster avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Siga estas etapas na interface de configurações do painel de controle do avere:

* Clique em **VServer** > **namespace** no canto superior esquerdo.
* Forneça um caminho de namespace que comece com/(barra "/"), como ``/avere/data``.
* Escolha o seu arquivador principal.
* Escolha a exportação do Filer principal.
* Clique em **Seguinte**.

  ![Captura de tela da página "Adicionar nova junção" com os campos concluídos para junção, principal filer e exportar](media/avere-vfxt-add-junction.png)

A junção será exibida após alguns segundos. Crie junções adicionais conforme necessário.

Depois que a junção tiver sido criada, os clientes poderão [montar o cluster avere vFXT](avere-vfxt-mount-clients.md) para acessar o sistema de arquivos.
