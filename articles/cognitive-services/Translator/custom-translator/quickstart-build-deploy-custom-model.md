---
title: 'Início rápido: Criar, implementar e utilizar um modelo personalizado - Translator personalizado'
titleSuffix: Azure Cognitive Services
description: Neste início rápido que avança processo passo a passo da criação de um sistema de tradução com o Translator personalizado.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: quickstart
ms.openlocfilehash: 7875a32274ddf0ea886727cdc55a0bbb874b2296
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762998"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Início rápido: Criar, implementar e utilizar um modelo personalizado para a tradução

Este artigo fornece instruções passo a passo para criar um sistema de tradução tradutor de personalizado.

## <a name="prerequisites"></a>Pré-requisitos

1. Para utilizar o [personalizado Translator](https://portal.customtranslator.azure.ai) Portal, precisará um [conta Microsoft](https://signup.live.com) ou [conta do Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (conta de organização alojada no Azure) para iniciar sessão.

2. Uma subscrição para a API de texto do Translator através do portal do Azure. Terá a chave de subscrição de API de texto do Microsoft Translator para associar a sua área de trabalho no Translator personalizado. Ver [como inscrever-se para a API de texto do Translator](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Quando tiver ambos acima, inicie sessão para o [Translator personalizado](https://portal.customtranslator.azure.ai) portal. Uma vez no portal do Translator personalizado, navegue para a página de definições onde pode associar a sua chave de assinatura de API de texto do Microsoft Translator com sua área de trabalho.

## <a name="create-a-project"></a>Criar um projeto

Na página de aterrissagem portal personalizado Translator, clique em novo projeto. Na caixa de diálogo pode introduzir o nome do projeto desejado, par de linguagem e categoria, bem como outros campos relevantes. Em seguida, guarde o seu projeto. Para obter mais detalhes, visite [criar projeto](how-to-create-project.md).

![Criar o projeto](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Carregar documentos

Em seguida, carregue [treinamento](training-and-model.md#training-dataset-for-custom-translator), [ajuste](training-and-model.md#tuning-dataset-for-custom-translator) e [teste](training-and-model.md#testing-dataset-for-custom-translator) conjuntos de documentos. Pode carregar ambos [paralela](what-are-parallel-documents.md) e documentos de combinação. Também pode carregar [dicionário](what-is-dictionary.md).

Pode carregar documentos a partir do separador de documentos ou da página de um projeto específico.

![Carregar documentos](media/quickstart/ct-how-to-upload.png)

Ao carregar documentos, escolha o tipo de documento (treinamento, ajuste ou de teste) e o par de idioma. Ao carregar documentos paralelos, precisará para além disso, especificar um nome de documento. Para obter mais detalhes, visite [carregamento de documento](how-to-upload-document.md).

## <a name="create-a-model"></a>Criar um modelo

Quando todos os seus documentos são carregados a próxima etapa é criar o seu modelo.

Selecione o projeto que criou. Verá todos os documentos que carregou que partilham um par de idioma com este projeto. Selecione os documentos que pretende incluir no seu modelo. Pode selecionar [treinamento](training-and-model.md#training-dataset-for-custom-translator), [ajuste](training-and-model.md#tuning-dataset-for-custom-translator), e [teste](training-and-model.md#testing-dataset-for-custom-translator) dados ou selecione apenas os dados de formação e deixar o Translator personalizado automaticamente criar a Otimização e testar os conjuntos de seu modelo.

![Criar um modelo](media/quickstart/ct-how-to-train.png)

Quando terminar de selecionar os documentos pretendidos, clique em criar modelo para criar o seu modelo e iniciar o treinamento. Pode ver o estado do seu treinamento e os detalhes de todos os modelos que Treinou no separador de modelos.

Para obter mais detalhes, visite [criar um modelo](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analisar o seu modelo

Assim que o seu treinamento foi concluída com êxito, verifique os resultados. A pontuação de BLEU é uma métrica que indica a qualidade da sua tradução. Também manualmente pode comparar as traduções feitas com o seu modelo personalizado para as traduções fornecidas no seu teste definir navegando para o separador de "Teste" e clicar em "Resultados do sistema". Inspecionar manualmente alguns dessas conversões de lhe dará uma boa idéia da qualidade da tradução produzida pelo seu sistema. Para obter mais detalhes, visite [resultados de teste de sistema](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Implementar um modelo preparado

Quando estiver pronto para implementar o modelo preparado, clique no botão "Implementar". Pode ter um modelo implementado por projeto, e pode ver o estado da implementação na coluna Estado. Para obter mais detalhes, visite [modelo de implementação](how-to-view-system-test-results.md#deploy-a-model)

![Implementar um modelo preparado](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Utilizar um modelo implementado

Modelos implementados podem ser acedidos através do Microsoft Translator [V3 de API de texto, especificando o CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Obter mais informações sobre a API de texto do Translator podem ser encontradas no [referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) página Web.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como navegar a [área de trabalho do Translator personalizado e gerir os seus projetos](workspace-and-project.md).
