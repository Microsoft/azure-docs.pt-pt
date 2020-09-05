---
title: Partilhe fora do seu org (portal Azure) - Azure Data Share quickstart
description: Saiba como partilhar dados com clientes e parceiros usando a Azure Data Share neste quickstart.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 41598c04af78d4366435259357d8f897ac178942
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489940"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Quickstart: Partilhar dados usando a Azure Data Share no portal Azure

Neste arranque rápido, você vai aprender a configurar uma nova Azure Data Share usando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="create-a-data-share-account"></a>Criar uma conta de partilha de dados

Crie um recurso Azure Data Share num grupo de recursos Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione o botão **De recurso** (+) no canto superior esquerdo do portal.

1. Procurar *por Partilha de Dados.*

1. Selecione **partilha de dados** e selecione **Criar**.

1. Preencha os detalhes básicos do seu recurso Azure Data Share com as seguintes informações. 

   **Definição** | **Valor sugerido** | **Descrição do campo**
   |---|---|---|
   | Subscrição | A sua subscrição | Selecione a subscrição Azure que pretende utilizar para a sua conta de partilha de dados.|
   | Grupo de recursos | *grupo de recursos de teste* | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
   | Localização | *E.U.A. Leste 2* | Selecione uma região para a sua conta de partilha de dados.
   | Nome | *conta de datashare* | Especifique um nome para a sua conta de partilha de dados. |

1. Selecione **Rever + criar**e, em seguida, **criar** para obter a sua conta de partilha de dados. O fornecimento de uma nova conta de partilha de dados normalmente demora cerca de 2 minutos ou menos.

1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

## <a name="create-a-share"></a>Criar uma partilha

1. Navegue para a sua página de visão geral da partilha de dados.

   ![Partilhar os seus dados](./media/share-receive-data.png "Partilhar os seus dados") 

1. Selecione **Comece a partilhar os seus dados.**

1. Selecione **Criar**.

1. Preencha os detalhes da sua parte. Especifique um nome, tipo de partilha, descrição do conteúdo da partilha e termos de utilização (opcional). 

   ![EnterShareDetails](./media/enter-share-details.png "Insira detalhes de partilha") 

1. **Selecione Continuar**.

1. Para adicionar Conjuntos de Dados à sua parte, selecione **Adicionar Conjuntos de Dados**. 

   ![Adicione conjuntos de dados à sua parte](./media/datasets.png "Conjuntos de dados")

1. Selecione o tipo de conjunto de dados que gostaria de adicionar. Verá uma lista diferente de tipos de conjuntos de dados dependendo do tipo de partilha (instantâneo ou no local) que selecionou no passo anterior. Se partilhar de uma Base de Dados Azure SQL ou Azure Synapse Analytics, você será solicitado para algumas credenciais SQL. Autenticar utilizando o utilizador que criou como parte dos pré-requisitos.

   ![AdicionarDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Navegue para o objeto que pretende partilhar e selecione 'Adicionar conjuntos de dados'. 

   ![SelecioneDatasets](./media/select-datasets.png "Selecione conjuntos de dados")    

1. No separador Destinatários, insira nos endereços de e-mail do seu Consumidor de Dados selecionando '+ Adicionar Destinatário'.

   ![AdicionarRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. **Selecione Continuar**.

1. Se tiver selecionado o tipo de partilha snapshot, pode configurar o calendário de instantâneos para fornecer atualizações dos seus dados ao seu consumidor de dados. 

   ![Ativar Fotos](./media/enable-snapshots.png "Ativar instantâneos") 

1. Selecione um intervalo de tempo de início e recorrência. 

1. **Selecione Continuar**.

1. No separador 'Rever + Criar', rever os conteúdos, definições, destinatários e definições de sincronização do pacote. Selecione **Criar**.

A sua Azure Data Share foi agora criada e o destinatário da sua Partilha de Dados está agora pronto para aceitar o seu convite.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o recurso já não for necessário, vá à página de Visão Geral do **Conjunto de Partilha de Dados** e selecione **Eliminar** para o remover.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma Azure Data Share. Para saber como um Consumidor de Dados pode aceitar e receber uma partilha de dados, continue a aceitar e receber o tutorial [de dados.](subscribe-to-data-share.md) 
