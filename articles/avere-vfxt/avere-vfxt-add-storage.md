---
title: Configurar o armazenamento de vFXT Avere - Azure
description: Como adicionar um sistema de armazenamento de back-end para seu vFXT Avere para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 6d35d5cdeafb80a36f910d71393802a3affb4df8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078821"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Este passo configura um sistema de armazenamento de back-end para o seu cluster vFXT.

> [!TIP]
> Se tiver criado um novo contentor de Blobs do Azure, juntamente com o cluster de vFXT Avere, que o contentor já está configurado para utilização e não é necessário adicionar armazenamento.

Siga estas instruções se não tiver criado um novo contentor de Blobs com o seu cluster, ou se pretender adicionar um hardware adicional ou um sistema de armazenamento com base na cloud.

Existem duas tarefas principais:

1. [Criar um filtro de núcleo](#create-a-core-filer), que liga o seu cluster vFXT para um sistema de armazenamento existente ou uma conta de armazenamento do Azure.

1. [Criar uma junção de espaço de nomes](#create-a-junction), que define o caminho que irão montar os clientes.

Estes passos utilizam o painel de controle de Avere. Leia [aceder ao cluster vFXT](avere-vfxt-cluster-gui.md) para saber como utilizá-lo.

## <a name="create-a-core-filer"></a>Criar um filtro de núcleo

"Filtro de principal" é um termo de vFXT para um sistema de armazenamento de back-end. O armazenamento pode ser um dispositivo de de hardware, como NetApp ou Isilon ou pode ser um arquivo de objeto na cloud. Podem encontrar mais informações sobre se filtram de núcleo [no Avere guia de configurações de cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um filtro de núcleos, escolha um dos dois tipos principais de se filtram de núcleo:

  * [Filtro de núcleo de NAS](#nas-core-filer) -descreve como adicionar um filtro de núcleos NAS 
  * [Filtro de núcleo de cloud de armazenamento do Azure](#azure-storage-cloud-core-filer) -descreve como adicionar uma conta de armazenamento do Azure como um filtro de núcleos na cloud

### <a name="nas-core-filer"></a>Filtro de núcleo NAS

Um filtro de núcleos NAS pode ser um NetApp no local ou Isilon ou um ponto de extremidade na cloud. O sistema de armazenamento tem de ter uma ligação fiável de alta velocidade ao Avere vFXT cluster - por exemplo, um 1 Gbps ligação do ExpressRoute (não uma VPN) – e tem de dar o acesso de raiz do cluster para as exportações na ser utilizadas.

Os seguintes passos, adicione um filtro de núcleos NAS:

1. No painel de controlo Avere, clique nas **definições** separador na parte superior.

1. Clique em **filtro de núcleo** > **gerir se filtram de núcleo** à esquerda.

1. Clique em **Criar**.

   ![Captura de ecrã da página Adicionar novo-filtro core com um cursor sobre o botão Criar](media/avere-vfxt-add-core-filer-start.png)

1. Preencha as informações necessárias no assistente: 

   * Nome de seu filtro de núcleo.
   * Forneça um nome de domínio completamente qualificado (FQDN) se estiver disponível. Caso contrário, fornece um endereço IP ou nome de anfitrião que resolva para o seu filtro de núcleo.
   * Escolha a sua classe de filtro na lista. Se não souber, escolha **outros**.

     ![Captura de ecrã da página Adicionar novo-filtro core com o nome do filtro de núcleos e o respetivo nome de domínio completamente qualificado](media/avere-vfxt-add-core-filer.png)
  
   * Clique em **seguinte** e escolha uma política de cache. 
   * Clique em **Adicionar filtro de**.
   * Para obter mais informações, consulte [adicionando uma novo NAS principais filtro](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) no Avere guia de configurações de cluster.

Em seguida, avance para [criar uma junção](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Filtro de núcleo de cloud de armazenamento do Azure

Para utilizar o armazenamento de Blobs do Azure como armazenamento de back-end do seu cluster vFXT, terá de um contêiner vazio para adicionar como um filtro de núcleo.

> [!TIP] 
> Se optar por criar um contentor de BLOBs ao mesmo tempo, que criar o cluster de vFXT Avere, o modelo de implementação ou o script cria um contentor de armazenamento, define-lo como um filtro de núcleos e cria a junção de espaço de nomes como parte da criação do cluster vFXT. O modelo também cria um ponto de extremidade de serviço de armazenamento dentro da rede de virtual do cluster. 

Adicionar armazenamento de BLOBs para o cluster requer estas tarefas:

* Criar uma conta de armazenamento (etapa 1, abaixo)
* Criar um contentor de Blob vazio (passos 2 a 3)
* Adicionar a chave de acesso de armazenamento como uma credencial de nuvem para o cluster de vFXT (passos 4 a 6)
* Adicionar o contentor de BLOBs como um filtro de núcleo para o cluster de vFXT (passos 7 a 9)
* Criar uma junção de espaço de nomes que os clientes utilizam para montar o filtro de núcleo ([criar uma junção](#create-a-junction), mesmo para o armazenamento de hardware e na cloud)

Para adicionar armazenamento de BLOBs depois de criar o cluster, siga estes passos. 

1. Crie uma conta de armazenamento para fins gerais V2 com estas definições:

   * **Subscrição** – mesmo que o cluster vFXT
   * **Grupo de recursos** – mesmo que o grupo de cluster vFXT (opcional)
   * **Localização** – mesmo que o cluster vFXT
   * **Desempenho** - Standard (armazenamento Premium é suportado)
   * **Tipo de conta** -General-purpose V2 (StorageV2)
   * **Replicação** -armazenamento localmente redundante (LRS)
   * **Camada de acesso** - acesso frequente
   * **Transferência segura necessária** -desativar esta opção (valor não predefinido)
   * **Redes virtuais** - não é necessário

   Pode utilizar o portal do Azure ou clique no botão "Implementar no Azure" abaixo.

   [![botão para criar a conta de armazenamento](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Depois da conta é criada, navegue para a página de conta de armazenamento.

   ![Nova conta de armazenamento no portal do Azure](media/avere-vfxt-new-storage-acct.png)

1. Crie um contentor de BLOBs, clicando em **Blobs** na página de descrição geral e clique em **+ contentor**. Utilizar qualquer nome de contentor e certifique-se de acesso é definido como **privada**.

   ![Página de blobs de armazenamento com não existem contentores existentes](media/avere-vfxt-blob-no-container.png)

1. Obter a chave de conta de armazenamento do Azure ao clicar em **chaves de acesso** sob **definições**:

   ![Portal do Azure GUI para copiar a chave](media/avere-vfxt-copy-storage-key.png) 

1. Abra o painel de controle de Avere para o seu cluster. Clique em **configurações**, em seguida, abra **Cluster** > **credênciais de Cloud** no painel de navegação esquerdo. Na página de credenciais na nuvem, clique em **adicionar credencial**.

   ![Clique no botão Adicionar credencial na página de configuração de credenciais de Cloud](media/avere-vfxt-new-credential-button.png)

1. Preencha as seguintes informações para criar uma credencial para o filtro de núcleos na cloud: 

   | Campo | Value |
   | --- | --- |
   | Nome da credencial | qualquer nome descritivo |
   | Tipo de serviço | (selecione a chave de acesso de armazenamento do Azure) |
   | Inquilino | Nome da conta de armazenamento |
   | Subscrição | ID de subscrição |
   | Chave de acesso de armazenamento | Chave de conta de armazenamento do Azure (copiado no passo anterior) | 

   Clique em **Submit** (Submeter).

   ![Concluído o formulário de credencial de nuvem no painel de controlo de Avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o filtro de núcleo. No lado esquerdo do painel de controle Avere, clique em **filtro de núcleo** >  **gerir se filtram de núcleo**. 

1. Clique no **Create** botão a **gerir se filtram de núcleo** página de definições.

1. Preencha o assistente:

   * Selecione o tipo de filtro **Cloud**. 
   * Nomeie o novo filtro de núcleos e clique em **seguinte**.
   * Aceite a política de cache padrão e avançar para a terceira página.
   * Na **tipo de serviço**, escolha **o armazenamento do Azure**. 
   * Escolha a credencial que criou anteriormente.
   * Definir **conteúdo de registo** para **vazio**
   * Alteração **verificação de certificado** para **desativado**
   * Alteração **modo de compressão** para **None**  
   * Clique em **Seguinte**.
   * Na quarta página, introduza o nome do contentor no **nome do Bucket** como *storage_account_name*/*container_name*.
   * Opcionalmente, defina **tipo de encriptação** ao **nenhum**.  O armazenamento do Azure é encriptado por predefinição.
   * Clique em **Adicionar filtro de**.

   Para obter mais informações, leia [adicionando um novo filtro de núcleos na cloud](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) no guia de configuração de cluster Avere. 

A página irá atualizar ou, pode atualizar a página para apresentar o novo filtro de núcleos.

Em seguida, precisa [criar uma junção](#create-a-junction).

## <a name="create-a-junction"></a>Criar uma junção

Uma junção é um caminho que criar para os clientes. Os clientes o caminho de montagem e chegarem ao destino que escolher.

Por exemplo, poderia criar `/avere/files` para mapear para o seu filtro de núcleo de NetApp `/vol0/data` exportar e o `/project/resources` subdiretório.

Podem encontrar mais informações sobre junções no [espaço de nomes seção do guia de configuração de cluster Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Siga estes passos na interface de definições do painel de controlo de Avere:

* Clique em **VServer** > **espaço de nomes** no canto superior esquerdo.
* Forneça um início do caminho de espaço de nomes com / (reencaminhar barra invertida), como ``/avere/data``.
* Escolha o seu filtro de núcleo.
* Escolha a exportação de filtro de núcleo.
* Clique em **Seguinte**.

  ![Captura de ecrã da página "Adicionar novo junção" com os campos concluída para a junção, filtro de núcleos e exportação](media/avere-vfxt-add-junction.png)

A junção irá aparecer após alguns segundos. Crie junções adicionais conforme necessário.

Depois de ter sido criada a junção, os clientes poderão [montar o cluster de vFXT Avere](avere-vfxt-mount-clients.md) para acessar o sistema de ficheiros.
