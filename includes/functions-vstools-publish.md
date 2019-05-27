---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3ac7d8cc4705fe1b6e80f1e0c7e26d847d761cf6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131828"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

2. Selecione **Aplicação de Função do Azure**, escolha **Criar Nova**  e selecione **Publicar**.

    ![Escolher um destino de publicação](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    Quando ativa **execute a partir de Zip**, a aplicação de funções no Azure entra em modo só de leitura e é executada diretamente a partir do pacote de implementação. Para mais informações, veja [Executar as suas Funções do Azure a partir de um ficheiro de pacote](../articles/azure-functions/run-functions-from-deployment-package.md).
     
    >[!CAUTION]
    >Quando escolhe **Selecionar Existente**, todos os ficheiros na aplicação de funções existentes no Azure são substituídos pelos ficheiros do projeto local. Utilize esta opção apenas ao voltar a publicar atualizações numa aplicação de funções existente.

3. Se ainda não tiver associado o Visual Studio à sua conta do Azure, selecione **Adicionar uma conta...**.

4. Na caixa de diálogo **Criar Serviço de Aplicações**, utilize as definições de **Anfitrião**, conforme especificadas na tabela abaixo da imagem:

    ![Caixa de diálogo Criar App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da Aplicação** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. Escolha **Novo** para criar um grupo de recursos novo.|
    | **[Plano do Serviço de Aplicações](../articles/azure-functions/functions-scale.md)** | Plano de consumo | Certifique-se de que escolhe **Consume** por baixo de **Tamanho** depois de clicar em **Novo** para criar um plano sem servidor. Escolha também uma **Localização** numa [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. Quando executa um plano diferente de **Consumo**, deve gerir o [dimensionamento da sua aplicação de funções](../articles/azure-functions/functions-scale.md).  |
    | **[Conta de Armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** | Conta de armazenamento para fins gerais | O runtime das Funções precisa de uma conta de armazenamento do Azure. Clique em **Novo** para criar uma conta de armazenamento para fins gerais. Também pode utilizar uma conta existente que cumpra os [requisitos de conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Clique em **Criar** para criar uma aplicação de função e os recursos relacionados no Azure com estas definições e implemente o código do projeto de função. 

6. Quando a implementação estiver concluída, aponte o valor **URL do Site**, que é o endereço da sua aplicação de função no Azure.

    ![Mensagem de publicação bem-sucedida](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
