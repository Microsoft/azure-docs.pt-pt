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
ms.openlocfilehash: a0cf557cec0f20d257c3c70290e2d74f077d881a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68593277"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

2. Na caixa de diálogo **escolher um destino de publicação** , use as opções de publicação conforme especificado na tabela abaixo da imagem: 

    ![Escolher um destino de publicação](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    | Opção      | Descrição                                |
    | ------------ |  -------------------------------------------------- |
    | **Plano de consumo de Azure Functions** |   Quando você publica seu projeto em um aplicativo de funções que é executado em um [plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan), você paga apenas pelas execuções do seu aplicativo de funções. Outros planos de hospedagem incorrem em custos mais altos. Para saber mais, consulte [Azure Functions escala e hospedagem](../articles/azure-functions/functions-scale.md). | 
    | **Criar novo** | Um novo aplicativo de funções, com recursos relacionados, é criado no Azure. Quando escolhe **Selecionar Existente**, todos os ficheiros na aplicação de funções existentes no Azure são substituídos pelos ficheiros do projeto local. Utilize esta opção apenas ao voltar a publicar atualizações numa aplicação de funções existente. |
    | **Executar do arquivo de pacote** | Seu aplicativo de funções é implantado usando a [implantação de zip](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) com o modo [de execução do pacote](../articles/azure-functions/run-functions-from-deployment-package.md) habilitado. Essa é a maneira recomendada de executar suas funções, o que resulta em um melhor desempenho. |


3. Selecione **Publicar**. Se você ainda não entrou em sua conta do Azure por meio do Visual Studio, selecione **entrar**. Você também pode criar uma conta gratuita do Azure.

4. No serviço de aplicativo:  **Criar nova** caixa de diálogo, use as configurações de **hospedagem** conforme especificado na tabela abaixo da imagem:

    ![Caixa de diálogo Criar App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. Os carateres válidos são `a-z`, `0-9` e `-`. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. Escolha **Novo** para criar um grupo de recursos novo.|
    | **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** | Plano de consumo | Certifique-se de escolher o **consumo** em **tamanho** depois de selecionar **novo** para criar um plano sem servidor. Escolha também uma **Localização** numa [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. Quando executa um plano diferente de **Consumo**, deve gerir o [dimensionamento da sua aplicação de funções](../articles/azure-functions/functions-scale.md).  |
    | **[Armazenamento do Microsoft Azure](../articles/storage/common/storage-quickstart-create-account.md)** | Conta de armazenamento de uso geral | O runtime das Funções precisa de uma conta de armazenamento do Azure. Selecione **novo** para criar uma conta de armazenamento de uso geral. Também pode utilizar uma conta existente que cumpra os [requisitos de conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Selecione **criar** para criar um aplicativo de funções e recursos relacionados no Azure com essas configurações e implantar o código do projeto de função. 

6. Quando a implementação estiver concluída, aponte o valor **URL do Site**, que é o endereço da sua aplicação de função no Azure.

    ![Mensagem de publicação bem-sucedida](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
