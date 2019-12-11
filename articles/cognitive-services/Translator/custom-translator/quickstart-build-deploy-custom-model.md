---
title: 'Início rápido: criar, implantar e usar um modelo personalizado-Tradutor personalizado'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você passa pelo processo passo a passo de criação de um sistema de tradução usando o tradutor personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: d93c6333b811885eec37e8401bee27afeedf1475
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975759"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Início rápido: criar, implantar e usar um modelo personalizado para tradução

Este artigo fornece instruções passo a passo para criar um sistema de tradução com o tradutor personalizado.

## <a name="prerequisites"></a>Pré-requisitos

1. Para usar o portal do [Tradutor personalizado](https://portal.customtranslator.azure.ai) , você precisará de uma [conta do conta Microsoft ou do Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (conta da organização hospedada no Azure) para entrar. [](https://signup.live.com)

2. Uma assinatura do API de Tradução de Texto por meio do portal do Azure. Você precisará da chave de assinatura API de Tradução de Texto para associar ao seu espaço de trabalho no Tradutor personalizado. Consulte [como se inscrever para o API de tradução de texto](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Quando você tiver ambos os itens acima, entre no portal do [Tradutor personalizado](https://portal.customtranslator.azure.ai) . Uma vez no portal do tradutor personalizado, navegue até a página configurações, na qual você pode associar sua chave de assinatura do Microsoft API de Tradução de Texto ao seu espaço de trabalho.

## <a name="create-a-project"></a>Criar um projeto

Na página de aterrissagem do portal do tradutor personalizado, clique em novo projeto. Na caixa de diálogo, você pode inserir o nome do projeto, o par de idiomas e a categoria desejados, bem como outros campos relevantes. Em seguida, salve seu projeto. Para obter mais detalhes, visite [criar projeto](how-to-create-project.md).

![Criar o projeto](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Carregar documentos

Em seguida, carregue os conjuntos de documentos de [treinamento](training-and-model.md#training-dataset-for-custom-translator), [ajuste](training-and-model.md#tuning-dataset-for-custom-translator) e [teste](training-and-model.md#testing-dataset-for-custom-translator) . Você pode carregar documentos [paralelos](what-are-parallel-documents.md) e de combinação. Você também pode carregar o [dicionário](what-is-dictionary.md).

Você pode carregar documentos da guia documentos ou da página de um projeto específico.

![Carregar documentos](media/quickstart/ct-how-to-upload.png)

Ao carregar documentos, escolha o tipo de documento (treinamento, ajuste ou teste) e o par de idiomas. Ao carregar documentos paralelos, você precisará especificar também um nome de documento. Para obter mais detalhes, visite [carregar documento](how-to-upload-document.md).

## <a name="create-a-model"></a>Criar um modelo

Quando todos os documentos necessários forem carregados, a próxima etapa será criar seu modelo.

Selecione o projeto que você criou. Você verá todos os documentos que carregou e que compartilham um par de idiomas com este projeto. Selecione os documentos que você deseja incluir em seu modelo. Você pode selecionar [treinamento](training-and-model.md#training-dataset-for-custom-translator), [ajuste](training-and-model.md#tuning-dataset-for-custom-translator)e [teste](training-and-model.md#testing-dataset-for-custom-translator) de dados ou selecionar apenas dados de treinamento e permitir que o tradutor personalizado crie automaticamente conjuntos de ajuste e teste para seu modelo.

![Criar um modelo](media/quickstart/ct-how-to-train.png)

Quando terminar de selecionar os documentos desejados, clique no botão criar modelo para criar seu modelo e iniciar o treinamento. Você pode ver o status do treinamento e os detalhes de todos os modelos treinados na guia modelos.

Para obter mais detalhes, visite [criar um modelo](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analisar seu modelo

Depois que o treinamento for concluído com êxito, inspecione os resultados. A pontuação BLEU é uma métrica que indica a qualidade da tradução. Você também pode comparar manualmente as conversões feitas com seu modelo personalizado com as traduções fornecidas no conjunto de teste navegando até a guia "teste" e clicando em "resultados do sistema". A inspeção manual de algumas dessas traduções dará a você uma boa ideia da qualidade da tradução produzida pelo seu sistema. Para obter mais detalhes, visite [resultados de teste do sistema](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Implantar um modelo treinado

Quando estiver pronto para implantar seu modelo treinado, clique no botão "implantar". Você pode ter um modelo implantado por projeto e pode exibir o status de sua implantação na coluna status. Para obter mais detalhes, visite [implantação de modelo](how-to-view-system-test-results.md#deploy-a-model)

![Implantar um modelo treinado](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Usar um modelo implantado

Modelos implantados podem ser acessados por meio da API de texto do Microsoft Translator [v3 especificando o categoryId](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Mais informações sobre o API de Tradução de Texto podem ser encontradas na página da Web de [referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) .

## <a name="next-steps"></a>Passos seguintes

- Saiba como navegar no [espaço de trabalho do tradutor personalizado e gerenciar seus projetos](workspace-and-project.md).
