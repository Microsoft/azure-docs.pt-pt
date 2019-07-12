---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1a8ecd0e53d28aed66546c41f95cc3855d1783a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608323"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

2. Selecione **Aplicação de Função do Azure**, escolha **Criar Nova**  e selecione **Publicar**.

    ![Escolher um destino de publicação](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png) 

    Quando clica em **execute a partir de um ficheiro de pacote (recomendado)** , a aplicação de funções será implementada utilizar [implementar Zip](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) com [execução do pacote](../articles/azure-functions/run-functions-from-deployment-package.md) modo ativado. Esta é a forma recomendada de executar as suas funções e irá resultar num melhor desempenho.

    >[!CAUTION]
    >Quando escolhe **Selecionar Existente**, todos os ficheiros na aplicação de funções existentes no Azure são substituídos pelos ficheiros do projeto local. Utilize esta opção apenas ao voltar a publicar atualizações numa aplicação de funções existente.

3. Se ainda não tiver associado o Visual Studio à sua conta do Azure, selecione **Adicionar uma conta...** .

4. Na caixa de diálogo **Criar Serviço de Aplicações**, utilize as definições de **Anfitrião**, conforme especificadas na tabela abaixo da imagem:

    ![Caixa de diálogo Criar App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da Aplicação** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. Escolha **Novo** para criar um grupo de recursos novo.|
    | **[Plano do Serviço de Aplicações](../articles/azure-functions/functions-scale.md)** | Plano de consumo | Certifique-se de que escolhe **Consume** por baixo de **Tamanho** depois de clicar em **Novo** para criar um plano sem servidor. Escolha também uma **Localização** numa [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. Quando executa um plano diferente de **Consumo**, deve gerir o [dimensionamento da sua aplicação de funções](../articles/azure-functions/functions-scale.md).  |
    | **[Conta de Armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** | Conta de armazenamento para fins gerais | O runtime das Funções precisa de uma conta de armazenamento do Azure. Clique em **New** para criar uma conta de armazenamento para fins gerais. Também pode utilizar uma conta existente que cumpra os [requisitos de conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Clique em **Criar** para criar uma aplicação de função e os recursos relacionados no Azure com estas definições e implemente o código do projeto de função. 

6. Quando a implementação estiver concluída, aponte o valor **URL do Site**, que é o endereço da sua aplicação de função no Azure.

    ![Mensagem de publicação bem-sucedida](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
