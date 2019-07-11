---
title: Tutorial - dados de partilha com clientes e parceiros através da pré-visualização do compartilhamento de dados do Azure
description: Tutorial - dados de partilha com clientes e parceiros através da pré-visualização do compartilhamento de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: ebd60607f9a24074e0fa985973dfc35674f0d66c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788228"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Tutorial: Partilhar os seus dados com a pré-visualização de partilha de dados do Azure

Neste tutorial, aprenderá como configurar uma nova partilha de dados do Azure e comece a partilhar os seus dados com clientes e parceiros fora da sua organização do Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma partilha de dados.
> * Adicione conjuntos de dados para a partilha de dados.
> * Ative uma agenda de sincronização para a sua partilha de dados. 
> * Adicione os destinatários para a partilha de dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de armazenamento do Azure: Se ainda não tiver uma, pode criar um [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Criar uma conta de partilha de dados

Crie um recurso de partilha de dados do Azure num grupo de recursos do Azure.

1. Selecione o botão (+) **Criar um recurso**, no canto superior esquerdo do portal.

1. Procure *partilha de dados*.

1. Selecione a partilha de dados (pré-visualização) e selecione **criar**.

1. Preencha os detalhes básicos do seu recurso de partilha de dados do Azure com as seguintes informações. 

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome | *datashareacount* | Especifique um nome para a sua conta de partilha de dados. |
    | Subscription | A sua subscrição | Selecione a subscrição do Azure que pretende utilizar para a sua conta de partilha de dados.|
    | Resource group | *test-resource-group* | Utilizar um grupo de recursos existente ou criar um novo grupo de recursos. |
    | Location | *E.U.A. Leste 2* | Selecione uma região para a sua conta de partilha de dados.
    | | |

1. Selecione **criar** para aprovisionar a sua conta de partilha de dados. Aprovisionar uma nova conta de partilha de dados normalmente demora cerca de 2 minutos ou menos. 

1. Quando a implementação estiver concluída, selecione **Ir para recurso**.

## <a name="create-a-data-share"></a>Criar uma partilha de dados

1. Navegue até à sua página de descrição geral da partilha de dados.

    ![Partilhar os seus dados](./media/share-receive-data.png "partilhar os seus dados") 

1. Selecione **comece a partilhar os seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes para a sua partilha de dados. Especifique um nome, descrição da partilha de conteúdo bem como os termos de utilização (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "detalhes da partilha introduza") 

1. Selecione **continuar**

1. Para adicionar conjuntos de dados para a partilha de dados, selecione **adicionar conjuntos de dados**. 

    ![Conjuntos de dados](./media/datasets.png "conjuntos de dados")

1. Selecione o tipo de conjunto de dados que pretende adicionar. 

    ![AddDatasets](./media/add-datasets.png "adicionar conjuntos de dados")    

1. Navegue para o objeto que pretende partilhar e selecione "Adicionar conjuntos de dados". 

    ![SelectDatasets](./media/select-datasets.png "selecione conjuntos de dados")    

1. No separador de destinatários, introduza os endereços de e-mail de consumidor de dados ao selecionar "+ Adicionar destinatário". 

    ![AddRecipients](./media/add-recipient.png "adicionar destinatários") 

1. Selecione **continuar**

1. Se desejar que o consumidor de dados para conseguir obter atualizações incrementais dos seus dados, ative o agendamento de instantâneo. 

    ![EnableSnapshots](./media/enable-snapshots.png "ativar instantâneos") 

1. Selecione um intervalo de tempo e a periodicidade do início. 

1. Selecione **continuar**

1. A revisão + criar separador, reveja o conteúdo do pacote, definições, os destinatários e as definições de sincronização. Selecione **Criar**

A partilha de dados do Azure agora foi criada e o destinatário da sua partilha de dados agora está pronto para aceitar o convite. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, com aprendizagem como criar uma partilha de dados do Azure e convidar os destinatários. Para saber mais sobre como um consumidor de dados pode aceitar e receber uma partilha de dados, avance para o [aceite e receber dados](subscribe-to-data-share.md) tutorial. 