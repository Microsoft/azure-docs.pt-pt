---
title: Partilhar e receber dados do Armazenamento de Blobs do Azure e do Azure Data Lake Storage
description: Saiba como partilhar e receber dados do Azure Blob Storage e do Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: dc309e85373193e4f5d431f543ff3e59ea5bebc7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739267"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Partilhar e receber dados do Armazenamento de Blobs do Azure e do Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

O Azure Data Share suporta a partilha baseada em instantâneos a partir de uma conta de armazenamento. Este artigo explica como partilhar e receber dados de Azure Blob Storage, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2.

O Azure Data Share suporta a partilha de ficheiros, pastas e sistemas de ficheiros da Azure Data Lake Gen1 e da Azure Data Lake Gen2. Também suporta a partilha de bolhas, pastas e recipientes do Azure Blob Storage. Apenas bolhas de blocos são suportadas atualmente. Os dados partilhados destas fontes podem ser recebidos pelo Azure Data Lake Gen2 ou pelo Azure Blob Storage.

Quando os sistemas de ficheiros, contentores ou pastas são partilhados na partilha baseada em instantâneos, os consumidores de dados podem optar por fazer uma cópia completa dos dados da partilha. Ou podem usar a capacidade de instantâneo incremental para copiar apenas ficheiros novos ou atualizados. A capacidade de instantâneo incremental baseia-se no último tempo modificado dos ficheiros. 

Os ficheiros existentes com o mesmo nome são substituídos durante uma snapshot. Um ficheiro que é eliminado da fonte não é eliminado no alvo. As sub-dobradeiras vazias na fonte não são copiadas para o alvo. 

## <a name="share-data"></a>Partilhar dados

Utilize as informações nas seguintes secções para partilhar dados utilizando a Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Pré-requisitos para a partilha de dados

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Encontre o endereço de e-mail de inscrição do Azure do seu destinatário. O pseudónimo do destinatário não funcionará para os seus propósitos.
* Se a loja de dados Azure de origem estiver numa subscrição Azure diferente daquela em que irá criar o recurso Data Share, registe o [fornecedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na subscrição onde está localizada a loja de dados Azure. 

### <a name="prerequisites-for-the-source-storage-account"></a>Pré-requisitos para a conta de armazenamento de fontes

* Uma conta do Armazenamento do Azure. Se ainda não tem uma conta, [crie uma.](../storage/common/storage-account-create.md)
* Permissão para escrever na conta de armazenamento. A permissão de escrita está em *Microsoft.Storage/storageAcounts/write*. Faz parte do papel do Contribuinte.
* Permissão para adicionar atribuição de função à conta de armazenamento. Esta permissão está em *Microsoft.Authorization/role assignments/write*. Faz parte do papel do Proprietário. 

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Criar uma conta de Partilha de Dados

Crie um recurso Azure Data Share num grupo de recursos Azure.

1. No canto superior esquerdo do portal, abra o menu e, em seguida, **selecione Criar um recurso** (+).

1. Procurar *por Partilha de Dados.*

1. Selecione **Partilha de Dados** e **Crie**.

1. Forneça os detalhes básicos do seu recurso Azure Data Share: 

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione uma subscrição Azure para a sua conta de partilha de dados.|
    | Grupo de recursos | *grupo de recursos de teste* | Utilize um grupo de recursos existente ou crie um grupo de recursos. |
    | Localização | *E.U.A. Leste 2* | Selecione uma região para a sua conta de partilha de dados.
    | Name | *conta de datashare* | Nomeie a sua conta de partilha de dados. |
    | | |

1. Selecione **Rever + criar** para obter a sua conta de partilha de  >   dados. O fornecimento de uma nova conta de partilha de dados normalmente demora cerca de 2 minutos. 

1. Quando a implementação estiver concluída, selecione **Ir para recurso**.

### <a name="create-a-share"></a>Criar uma parte

1. Aceda à página **geral da** partilha de dados.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Screenshot mostrando a visão geral da partilha de dados.":::

1. Selecione **Comece a partilhar os seus dados.**

1. Selecione **Criar**.   

1. Forneça os detalhes da sua parte. Especifique um nome, tipo de partilha, descrição do conteúdo da partilha e termos de utilização (opcional). 

    ![Screenshot mostrando detalhes da partilha de dados.](./media/enter-share-details.png "Introduza os dados partilhar detalhes.") 

1. Selecione **Continuar**.

1. Para adicionar conjuntos de dados à sua parte, selecione **Adicionar Conjuntos de Dados**. 

    ![Screenshot mostrando como adicionar conjuntos de dados à sua parte.](./media/datasets.png "Conjuntos de dados.")

1. Selecione um tipo de conjunto de dados para adicionar. A lista de tipos de conjuntos de dados depende se selecionou a partilha baseada em instantâneos ou a partilha no local no passo anterior. 

    ![Screenshot mostrando onde selecionar um tipo de conjunto de dados.](./media/add-datasets.png "Adicione conjuntos de dados.")    

1. Vá ao objeto que quer partilhar. Em seguida, **selecione Adicionar Conjuntos de Dados**. 

    ![Screenshot mostrando como selecionar um objeto para partilhar.](./media/select-datasets.png "Selecione conjuntos de dados.")    

1. No **separador Destinatários,** adicione o endereço de e-mail do seu consumidor de dados selecionando **Add Recipient**. 

    ![Screenshot mostrando como adicionar endereços de e-mail do destinatário.](./media/add-recipient.png "Adicione os destinatários.") 

1. Selecione **Continuar**.

1. Se selecionar um tipo de partilha instantânea, pode configurar o calendário de instantâneos para atualizar os seus dados para o consumidor de dados. 

    ![Screenshot mostrando as definições do horário do instantâneo.](./media/enable-snapshots.png "Ativar as fotos.") 

1. Selecione um intervalo de tempo de início e recorrência. 

1. Selecione **Continuar**.

1. No **separador 'Rever + Criar',** revê os conteúdos, configurações, destinatários e definições de sincronização do seu pacote. Em seguida, selecione **Criar**.

Criou agora a sua partilha de dados Azure. O destinatário da sua partilha de dados pode aceitar o seu convite. 

## <a name="receive-data"></a>Receber dados

As secções seguintes descrevem como receber dados partilhados.
### <a name="prerequisites-to-receive-data"></a>Pré-requisitos para receber dados
Antes de aceitar um convite para partilha de dados, certifique-se de que tem os seguintes pré-requisitos: 

* Uma subscrição do Azure. Se não tiver uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Um convite de Azure. O assunto do e-mail deve ser "Azure Data Share convite de *\<yourdataprovider\@domain.com>* ".
* Um fornecedor de [recursos registado microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) em:
    * A subscrição Azure onde irá criar um recurso Data Share.
    * A subscrição Azure onde estão localizadas as lojas de dados Azure alvo.

### <a name="prerequisites-for-a-target-storage-account"></a>Pré-requisitos para uma conta de armazenamento alvo

* Uma conta do Armazenamento do Azure. Se ainda não tem uma, [crie uma conta.](../storage/common/storage-account-create.md) 
* Permissão para escrever na conta de armazenamento. Esta permissão está em *Microsoft.Storage/storageAcounts/write*. Faz parte do papel do Contribuinte. 
* Permissão para adicionar atribuição de função à conta de armazenamento. Esta atribuição está em *Microsoft.Authorization/role assignments/write*. Faz parte do papel do Proprietário.  

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Abrir um convite

Pode abrir um convite a partir de e-mail ou diretamente do portal Azure.

1. Para abrir um convite a partir de e-mail, consulte a sua caixa de entrada para obter um convite do seu fornecedor de dados. O convite da Microsoft Azure é intitulado "Azure Data Share invitation *\<yourdataprovider\@domain.com>* from". Selecione **Ver convite** para ver o seu convite em Azure. 

   Para abrir um convite do portal Azure, procure convites para *partilhar dados.* Você vê uma lista de convites de partilha de dados.

   ![Screenshot mostrando a lista de convites no portal Azure.](./media/invitations.png "Lista de convites.") 

1. Selecione a partilha que pretende visualizar. 

### <a name="accept-an-invitation"></a>Aceite um convite
1. Reveja todos os campos, incluindo os **Termos de utilização.** Se concordar com os termos, selecione a caixa de verificação. 

   ![Screenshot mostrando a área de termos de utilização.](./media/terms-of-use.png "Termos de uso.") 

1. Na **conta 'Partilha de Dados-Alvo',** selecione o grupo de subscrição e recursos onde irá implementar a sua Partilha de Dados. Em seguida, preencha os seguintes campos:

   * No campo da **conta de partilha de dados,** selecione Criar **novo** se não tiver uma conta De partilha de dados. Caso contrário, selecione uma conta de Partilha de Dados existente que aceitará a sua partilha de dados. 

   * No campo **de nome de partilha Recebido,** ou deixa o padrão especificado pelo fornecedor de dados ou especifica um novo nome para a parte recebida. 

1. **Selecione Aceitar e configurar**. É criada uma subscrição de ações. 

   ![Screenshot mostrando onde aceitar as opções de configuração.](./media/accept-options.png "Aceitar opções") 

    A parte recebida aparece na sua conta De partilha de dados. 

    Se não quiser aceitar o convite, **selecione Rejeitar**. 

### <a name="configure-a-received-share"></a>Configurar uma parte recebida
Siga os passos nesta secção para configurar um local para receber dados.

1. No **separador Datasets,** selecione a caixa de verificação ao lado do conjunto de dados onde pretende atribuir um destino. Selecione **Mapa para o alvo** para escolher uma loja de dados alvo. 

   ![Screenshot mostrando como mapear para um alvo.](./media/dataset-map-target.png "Mapa para o alvo.") 

1. Selecione uma loja de dados-alvo para os dados. Os ficheiros na loja de dados-alvo que tenham o mesmo caminho e nome que os ficheiros nos dados recebidos serão substituídos. 

   ![Screenshot mostrando onde selecionar uma conta de armazenamento alvo.](./media/map-target.png "Armazenamento de alvo.") 

1. Para a partilha baseada em instantâneos, se o fornecedor de dados utilizar um calendário instantâneo para atualizar regularmente os dados, pode ativar o calendário a partir do separador **Agenda instantânea.** Selecione a caixa ao lado do horário de instantâneo. Em seguida, **selecione Ativar**.

   ![Screenshot mostrando como ativar um horário de instantâneo.](./media/enable-snapshot-schedule.png "Ativar o horário do instantâneo.")

### <a name="trigger-a-snapshot"></a>Desencadear um instantâneo
Os passos desta secção aplicam-se apenas à partilha baseada em instantâneos.

1. Pode ativar uma fotografia a partir do separador **Detalhes.** No separador, selecione **Trigger snapshot**. Pode optar por desencadear uma imagem instantânea completa ou incremental dos seus dados. Se estiver a receber dados do seu fornecedor de dados pela primeira vez, selecione **Full copy**. Quando um instantâneo está a ser executado, as imagens subsequentes não começam até que a anterior esteja completa.

   ![Screenshot mostrando a seleção de instantâneo do Gatilho.](./media/trigger-snapshot.png "Trigger snapshot.") 

1. Quando o estado da última execução for *bem sucedido,* vá à loja de dados-alvo para ver os dados recebidos. Selecione **Conjuntos de dados** e, em seguida, selecione a ligação do caminho-alvo. 

   ![Screenshot mostrando um mapeamento de conjunto de dados do consumidor.](./media/consumer-datasets.png "Mapeamento do conjunto de dados do consumidor.") 

### <a name="view-history"></a>Ver histórico
Pode ver a história das suas fotos apenas na partilha baseada em instantâneos. Para ver a história, abra o **separador História.** Aqui você vê a história de todos os instantâneos que foram gerados nos últimos 30 dias. 

## <a name="storage-snapshot-performance"></a>Desempenho do instantâneo de armazenamento
O desempenho do snapshot de armazenamento é impactado por uma série de fatores, além do número de ficheiros e tamanho dos dados partilhados. É sempre recomendado realizar o seu próprio teste de desempenho. Abaixo estão alguns fatores de exemplo que impactam o desempenho.

* Acesso simultâneo às lojas de dados de origem e alvo.  
* Localização das lojas de dados de origem e alvo. 
* Para uma snapshot incremental, o número de ficheiros no conjunto de dados partilhados pode ter impacto no tempo necessário para encontrar a lista de ficheiros com o último tempo modificado após o último instantâneo bem sucedido. 


## <a name="next-steps"></a>Passos seguintes
Aprendeu a partilhar e a receber dados de uma conta de armazenamento utilizando o serviço Azure Data Share. Para saber mais sobre a partilha de outras fontes de dados, consulte lojas de [dados suportadas.](supported-data-stores.md)