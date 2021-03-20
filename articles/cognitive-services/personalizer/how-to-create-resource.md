---
title: Criar recurso personalizador
description: Neste artigo, aprenda a criar um recurso personalizador no portal Azure para cada ciclo de feedback.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c75d917f1abe72af2f4aa56b0f67dbb7bcd24a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91303561"
---
# <a name="create-a-personalizer-resource"></a>Criar um recurso personalizante

Um recurso personalizer é a mesma coisa que um ciclo de aprendizagem personalizer. Um único recurso, ou ciclo de aprendizagem, é criado para cada domínio ou área de conteúdo que você tem. Não utilize várias áreas de conteúdo no mesmo ciclo, pois isso irá confundir o ciclo de aprendizagem e fornecer más previsões.

Se quiser que o Personalizer selecione o melhor conteúdo para mais do que uma área de conteúdo de uma página web, utilize um ciclo de aprendizagem diferente para cada um.


## <a name="create-a-resource-in-the-azure-portal"></a>Criar um recurso no portal do Azure

Crie um recurso Personalizer para cada ciclo de feedback.

1. Inscreva-se no [portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). O link anterior leva-o à página **Criar** para o serviço Personalizar.
1. Insira o seu nome de serviço, selecione uma subscrição, localização, nível de preços e grupo de recursos.

    > [!div class="mx-imgBorder"]
    > ![Use o portal Azure para criar recurso Personalizer, também chamado de ciclo de aprendizagem.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selecione **Criar** para criar o recurso.

1. Depois de o seu recurso ter sido implantado, selecione o botão **Ir ao Recurso** para ir ao seu recurso Personalizer.

1. Selecione a página **de início rápido** para o seu recurso e, em seguida, copie os valores para o seu ponto final e chave. Precisa tanto do ponto final do recurso como da chave para utilizar as APIs rank e reward.

1. Selecione a página **de Configuração** para o novo recurso para [configurar o ciclo de aprendizagem](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Criar um recurso com o Azure CLI

1. Inscreva-se no Azure CLI com o seguinte comando:

    ```azurecli-interactive
    az login
    ```

1. Crie um grupo de recursos, um agrupamento lógico para gerir todos os recursos Azure que pretende utilizar com o recurso Personalizer.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Crie um novo recurso Personalizer, _ciclo de aprendizagem,_ com o seguinte comando para um grupo de recursos existente.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Isto devolve um objeto JSON, que inclui o seu **ponto final de recurso**.

1. Utilize o seguinte comando Azure CLI para obter a sua **chave de recursos**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Precisa tanto do ponto final do recurso como da chave para utilizar as APIs rank e reward.

## <a name="next-steps"></a>Passos seguintes

* [Configuração](how-to-settings.md) Ciclo de aprendizagem personalizado
