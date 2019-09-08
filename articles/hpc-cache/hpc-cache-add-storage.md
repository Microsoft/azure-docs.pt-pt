---
title: Adicionar armazenamento a um cache HPC do Azure
description: Como definir destinos de armazenamento para que o cache HPC do Azure possa usar seu sistema NFS local ou contêineres de blob do Azure para o armazenamento de arquivos de longo prazo
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 4554214b74b4d09fa40e355270208bebda4076b7
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775263"
---
# <a name="add-storage"></a>Adicionar armazenamento

Os *destinos de armazenamento* são armazenamento de back-end para arquivos que são acessados por meio de uma instância de cache do Azure HPC. Você pode adicionar armazenamento NFS, como um sistema de hardware local, ou armazenar dados no blob do Azure.

Você pode definir até dez destinos de armazenamento diferentes para um cache. O cache apresenta todos os destinos de armazenamento em um namespace agregado.

Lembre-se de que as exportações de armazenamento devem ser acessíveis da rede virtual do seu cache. Para o armazenamento de hardware local, talvez seja necessário configurar um servidor DNS que possa resolver nomes de host para acesso de armazenamento NFS. Leia mais em [acesso DNS](hpc-cache-prereqs.md#dns-access).

Você pode adicionar destinos de armazenamento ao criar o cache do HPC do Azure ou posteriormente. O procedimento é ligeiramente diferente dependendo se você está adicionando o armazenamento de BLOBs do Azure ou uma exportação de NFS. Os detalhes de cada um estão abaixo.

## <a name="add-storage-targets-while-creating-the-cache"></a>Adicionar destinos de armazenamento ao criar o cache

Use a guia **destinos de armazenamento** do assistente de criação de cache para definir o armazenamento ao mesmo tempo em que cria a instância de cache.

![captura de tela da página de destinos de armazenamento](media/create-targets.png)

Clique no link **Adicionar destino de armazenamento** para adicionar armazenamento.

## <a name="add-storage-targets-from-the-cache"></a>Adicionar destinos de armazenamento do cache

No portal do Azure, abra sua instância de cache e clique em **destinos de armazenamento** na barra lateral esquerda. A página destino de armazenamento lista todos os destinos existentes e fornece um link para adicionar um novo.

## <a name="add-a-new-azure-blob-storage-target"></a>Adicionar um novo destino de armazenamento de BLOBs do Azure

Um novo destino de armazenamento de BLOBs precisa de um contêiner de blob vazio ou um contêiner que é preenchido com dados no formato de sistema de arquivos de nuvem do cache HPC do Azure. Leia mais sobre pré-carregando um contêiner de BLOBs em [mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md).

Para definir um contêiner de blob do Azure, insira essas informações.

![captura de tela da página Adicionar destino de armazenamento, preenchida com informações para um novo destino de armazenamento de BLOBs do Azure](media/hpc-cache-add-blob.png)

* **Nome do destino de armazenamento** -defina um nome que identifique esse destino de armazenamento no cache do HPC do Azure.
* **Tipo de destino** -escolha **blob**.
* **Conta de armazenamento** -selecione a conta com o contêiner para referenciar.

  Você precisará autorizar a instância de cache a acessar a conta de armazenamento, conforme descrito em [Adicionar as funções de acesso](#add-the-access-control-roles-to-your-account).
* **Contêiner de armazenamento** -selecione o contêiner de BLOB para este destino.

* **Caminho do namespace virtual** -defina o filePath voltado para o cliente para este destino de armazenamento. Leia [Configurar namespace agregado](hpc-cache-namespace.md) para saber mais sobre o recurso de namespace virtual.

<!--  The namespace path value must end with a slash (``/``) and should not start with one.  -->

Quando terminar, clique em **OK** para adicionar o destino de armazenamento.

### <a name="add-the-access-control-roles-to-your-account"></a>Adicionar as funções de controle de acesso à sua conta

O cache HPC do Azure usa o [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/index) para autorizar o aplicativo de cache a acessar sua conta de armazenamento para destinos do armazenamento de BLOBs do Azure.

O proprietário da conta de armazenamento deve adicionar explicitamente o colaborador da [conta de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) de acúmulo e o colaborador de dados do [blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) para o usuário "provedor de recursos StorageCache".

Você pode fazer isso antecipadamente ou clicando em um link na página em que você adiciona um destino de armazenamento de BLOBs.

Etapas para adicionar as funções de RBAC:

1. Abra a página **controle de acesso (iam)** para a conta de armazenamento. (O link na página **Adicionar destino de armazenamento** abre automaticamente esta página para a conta selecionada.)

1. Clique no na parte superior da página e escolha **Adicionar uma atribuição de função.** **+**

1. Selecione a função "colaborador da conta de armazenamento" na lista.

1. No campo **atribuir acesso a** , deixe o valor padrão selecionado ("usuário do Azure AD, grupo ou entidade de serviço").  

1. No campo **selecionar** , procure "storagecache".  Essa cadeia de caracteres deve corresponder a uma entidade de segurança, denominada "provedor de recursos de cache do HPC". Clique nessa entidade para selecioná-la.

1. Clique no botão **salvar** para adicionar a atribuição de função à conta de armazenamento.

1. Repita esse processo para atribuir a função "colaborador de dados de blob de armazenamento".  

![captura de tela de adicionar GUI de atribuição de função](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Adicionar um novo destino de armazenamento NFS

Um destino de armazenamento NFS tem alguns campos adicionais para especificar como acessar a exportação de armazenamento e como armazenar seus dados em cache de forma eficiente. Além disso, você pode criar vários caminhos de namespace de um host NFS, se ele tiver mais de uma exportação disponível.

![Captura de tela da página Adicionar destino de armazenamento com o destino NFS definido](media/hpc-cache-add-nfs-target.png)

Forneça essas informações para um destino de armazenamento com backup em NFS:

* **Nome do destino de armazenamento** -defina um nome que identifique esse destino de armazenamento no cache do HPC do Azure.

* **Tipo de destino** -escolha **NFS**.

* **Hostname** – Insira o endereço IP ou o nome de domínio totalmente qualificado para o sistema de armazenamento NFS. (Use um nome de domínio somente se o cache tiver acesso a um servidor DNS que possa resolver o nome.)

* **Modelo de uso** – escolha um dos perfis de cache de dados com base em seu fluxo de trabalho, descrito em [escolher um modelo de uso, abaixo](#choose-a-usage-model).

Você pode criar vários caminhos de namespace para representar exportações diferentes no mesmo sistema de armazenamento NFS, mas deve criá-los todos de um destino de armazenamento.

Para cada exportação, preencha estes valores:

* **Caminho do namespace virtual** -defina o filePath voltado para o cliente para este destino de armazenamento. Leia [Configurar namespace agregado](hpc-cache-namespace.md) para saber mais sobre o recurso de namespace virtual.

<!--  The virtual path should start with a slash ``/``. -->

* **Caminho de exportação de NFS** -Insira o caminho para a exportação de NFS.

* **Caminho do subdiretório** -se você quiser montar um subdiretório específico da exportação, insira-o aqui. Caso contrário, deixe esse campo em branco. 

Quando terminar, clique em **OK** para adicionar o destino de armazenamento.

### <a name="choose-a-usage-model"></a>Escolher um modelo de uso 
<!-- link in GUI to this heading -->

Ao criar um destino de armazenamento que aponta para um sistema de armazenamento NFS, você precisa escolher o *modelo de uso* para esse destino. Esse modelo determina como os dados são armazenados em cache.

* Leitura intensa – se você usar principalmente o cache para acelerar o acesso de leitura de dados, escolha essa opção. 

* Leitura/gravação – se os clientes usarem o cache para leitura e gravação, escolha essa opção.

* Clientes ignoram o cache-escolha esta opção se seus clientes gravarem dados diretamente no sistema de armazenamento sem primeiro gravar no cache.

## <a name="next-steps"></a>Passos Seguintes

Depois de criar destinos de armazenamento, considere uma destas tarefas:

* [Montar o cache HPC do Azure](hpc-cache-mount.md)
* [Mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md)