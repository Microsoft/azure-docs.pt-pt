---
title: 'Quickstart: Construir, implementar e usar um modelo personalizado - Tradutor Personalizado'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, passa-se por um processo passo a passo de construção de um sistema de tradução utilizando o Tradutor Personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: c86a387e66cce914f9d0b92793893b0cba08e7bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587103"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Quickstart: Construir, implementar e usar um modelo personalizado para tradução

Este artigo fornece instruções passo a passo para construir um sistema de tradução com tradutor personalizado.

## <a name="prerequisites"></a>Pré-requisitos

1. Para utilizar o Portal tradutor [personalizado,](https://portal.customtranslator.azure.ai) necessitará de uma [conta Microsoft](https://signup.live.com) ou [conta Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (conta de organização hospedada no Azure) para iniciar sessão.

2. Uma subscrição ao Tradutor através do portal Azure. Você precisará da chave de subscrição tradutor para se associar ao seu espaço de trabalho em Tradutor Personalizado. Veja [como se inscrever no Tradutor.](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup)

3. Quando tiver ambos os acima, inscreva-se no portal [Tradutor Personalizado](https://portal.customtranslator.azure.ai) para criar espaços de trabalho, projetos, carregar ficheiros e criar/implementar modelos.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Se for utilizador pela primeira vez, será-lhe pedido que concorde com os Termos de Serviço, que crie um espaço de trabalho e associe o seu espaço de trabalho à subscrição do Tradutor.

![Criar espaço de trabalho ](media/quickstart/terms-of-service.png)
 ![ Criar espaço de trabalho Criar ](media/quickstart/create-workspace-1.png)
 ![ espaço de trabalho ](media/quickstart/create-workspace-2.png)
 ![ Criar espaço de trabalho Criar ](media/quickstart/create-workspace-3.png)
 ![ espaço de trabalho Criar ](media/quickstart/create-workspace-4.png)
 ![ espaço de trabalho Criar espaço ](media/quickstart/create-workspace-5.png)
 ![ de trabalho](media/quickstart/create-workspace-6.png)

Nas visitas subsequentes ao portal Tradutor Personalizado, navegue para a página Definições onde pode gerir o seu espaço de trabalho, criar mais espaços de trabalho, associar a sua chave de subscrição do Tradutor aos seus espaços de trabalho, adicionar coproprietários e alterar uma chave de subscrição.

## <a name="create-a-project"></a>Criar um projeto

Na página de aterragem do portal Custom Tradutor, clique em New Project. No diálogo pode introduzir o nome de projeto, par de idiomas e categoria pretendidos, bem como outros campos relevantes. Então, salve o seu projeto. Para mais detalhes, visite [o Projeto Criar.](how-to-create-project.md)

![Criar o projeto](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Carregar documentos

Em seguida, fazer upload de [treino,](training-and-model.md#training-document-type-for-custom-translator) [afinação](training-and-model.md#tuning-document-type-for-custom-translator) e conjuntos de documentos de [teste.](training-and-model.md#testing-dataset-for-custom-translator) Pode fazer o upload de documentos [paralelos](what-are-parallel-documents.md) e combinados. Também pode carregar [o dicionário.](what-is-dictionary.md)

Pode fazer o upload de documentos a partir do separador de documentos ou da página de um projeto específico.

![Carregar documentos](media/quickstart/ct-how-to-upload.png)

Ao fazer o upload de documentos, escolha o tipo de documento (treino, afinação ou teste) e o par de idiomas. Ao enviar documentos paralelos, terá de especificar adicionalmente um nome de documento. Para mais detalhes, visite o [documento Upload](how-to-upload-document.md).

## <a name="create-a-model"></a>Criar um modelo

Quando todos os documentos necessários forem carregados, o próximo passo é construir o seu modelo.

Selecione o projeto que criou. Verá todos os documentos que carregou que partilham um par de línguas com este projeto. Selecione os documentos que deseja incluídos no seu modelo. Pode selecionar [treinos,](training-and-model.md#training-document-type-for-custom-translator) [afinação](training-and-model.md#tuning-document-type-for-custom-translator)e [testar](training-and-model.md#testing-dataset-for-custom-translator) dados ou selecionar apenas dados de treino e deixar o Custom Tradutor construir automaticamente conjuntos de afinação e teste para o seu modelo.

![Criar um modelo](media/quickstart/ct-how-to-train.png)

Quando terminar de selecionar os documentos pretendidos, clique no botão Criar Modelo para criar o seu modelo e começar a treinar. Você pode ver o estado do seu treino, e detalhes para todos os modelos que você treinou, no separador Modelos.

Para mais detalhes, visite [Criar um Modelo.](how-to-train-model.md)

## <a name="analyze-your-model"></a>Analise o seu modelo

Uma vez concluído o seu treino com sucesso, inspecione os resultados. A pontuação BLEU é uma métrica que indica a qualidade da sua tradução. Também pode comparar manualmente as traduções feitas com o seu modelo personalizado com as traduções fornecidas no seu teste definidos navegando para o separador "Test" e clicando em "Resultados do Sistema". Inspecionar manualmente algumas destas traduções dar-lhe-á uma boa ideia da qualidade da tradução produzida pelo seu sistema. Para mais detalhes, visite [os Resultados do Teste do Sistema.](how-to-view-system-test-results.md)

## <a name="deploy-a-trained-model"></a>Implementar um modelo treinado

Quando estiver pronto para implementar o seu modelo treinado, clique no botão "Implementar". Pode ter um modelo implantado por projeto e pode ver o estado da sua implantação na coluna 'Estado'. Para mais detalhes, visite Model [Deployment](how-to-view-system-test-results.md#deploy-a-model)

![Implementar um modelo treinado](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Use um modelo implantado

Os modelos implantados podem ser acedidos através do Microsoft Tradutor [Text API V3 especificando o CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Mais informações sobre o Tradutor podem ser encontradas na página web da [API Reference.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="next-steps"></a>Passos seguintes

- Aprenda a navegar no espaço de trabalho do [Tradutor Personalizado e gerencie os seus projetos.](workspace-and-project.md)
