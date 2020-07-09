---
title: 'Quickstart: Construa, implemente e use um modelo personalizado - Custom Tradutor'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você passa por um processo passo a passo de construção de um sistema de tradução usando o Tradutor Personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: b0992c4d18fdb9cb5201ab3ef52fba8ee3feb7a2
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964384"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Quickstart: Construa, implemente e use um modelo personalizado para tradução

Este artigo fornece instruções passo a passo para construir um sistema de tradução com o Tradutor Personalizado.

## <a name="prerequisites"></a>Pré-requisitos

1. Para utilizar o Portal [do Tradutor Personalizado,](https://portal.customtranslator.azure.ai) necessitará de uma [conta Microsoft](https://signup.live.com) ou de uma [conta AD Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (conta de organização hospedada no Azure) para iniciar sessão.

2. Uma assinatura do Tradutor através do portal Azure. Você precisará de uma chave de subscrição de Tradutor para associar ao seu espaço de trabalho em Custom Tradutor. Veja [como se inscrever para Tradutor.](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup)

3. Quando tiver ambos os dois acima, inscreva-se no portal [Do Tradutor Personalizado](https://portal.customtranslator.azure.ai) para criar espaços de trabalho, projetos, carregar ficheiros e criar/implementar modelos.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Se for o utilizador pela primeira vez, será solicitado que concorde com os Termos de Serviço, que crie um espaço de trabalho e que associe o seu espaço de trabalho à subscrição do Tradutor.

![Criar espaço de trabalho ](media/quickstart/terms-of-service.png)
 ![ Criar espaço de trabalho Criar espaço ](media/quickstart/create-workspace-1.png)
 ![ de trabalho Criar ](media/quickstart/create-workspace-2.png)
 ![ espaço de trabalho ](media/quickstart/create-workspace-3.png)
 ![ Criar espaço de trabalho ](media/quickstart/create-workspace-4.png)
 ![ Criar espaço de trabalho ](media/quickstart/create-workspace-5.png)
 ![ Criar espaço de trabalho Criar espaço de trabalho](media/quickstart/create-workspace-6.png)

Nas visitas subsequentes ao portal Do Tradutor Personalizado, navegue na página Definições onde pode gerir o seu espaço de trabalho, criar mais espaços de trabalho, associar a sua chave de subscrição de Tradutor aos seus espaços de trabalho, adicionar coproprietários e alterar uma chave de subscrição.

## <a name="create-a-project"></a>Criar um projeto

Na página de aterragem do portal Do Tradutor Personalizado, clique em New Project. No diálogo pode introduzir o nome do projeto pretendido, par de idiomas e categoria, bem como outros campos relevantes. Então, salve o seu projeto. Para mais detalhes, visite [o Create Project.](how-to-create-project.md)

![Criar o projeto](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Carregar documentos

Em seguida, carre faça [o upload dos](training-and-model.md#training-document-type-for-custom-translator)conjuntos de [testes, afinação](training-and-model.md#tuning-document-type-for-custom-translator) e [teste.](training-and-model.md#testing-dataset-for-custom-translator) Pode carregar documentos [paralelos](what-are-parallel-documents.md) e combinados. Também pode carregar [o dicionário.](what-is-dictionary.md)

Pode fazer upload de documentos a partir do separador documentos ou da página de um projeto específico.

![Carregar documentos](media/quickstart/ct-how-to-upload.png)

Ao carregar documentos, escolha o tipo de documento (treino, afinação ou teste) e o par de idiomas. Ao carregar documentos paralelos, terá de especificar ainda mais um nome de documento. Para mais detalhes, visite [o documento do Upload](how-to-upload-document.md).

## <a name="create-a-model"></a>Criar um modelo

Quando todos os documentos necessários são carregados, o próximo passo é construir o seu modelo.

Selecione o projeto que criou. Verá todos os documentos que carregou que partilham um par de línguas com este projeto. Selecione os documentos que pretende incluídos no seu modelo. Pode selecionar [os dados de treino,](training-and-model.md#training-document-type-for-custom-translator) [afinação](training-and-model.md#tuning-document-type-for-custom-translator)e [testar](training-and-model.md#testing-dataset-for-custom-translator) ou selecionar apenas dados de treino e permitir que o Custom Tradutor construa automaticamente afinações e conjuntos de testes para o seu modelo.

![Criar um modelo](media/quickstart/ct-how-to-train.png)

Quando terminar de selecionar os documentos pretendidos, clique no botão Criar Modelo para criar o seu modelo e começar a treinar. Pode ver o estado do seu treino e detalhes para todos os modelos que treinou, no separador Modelos.

Para mais detalhes, visite [Criar um Modelo.](how-to-train-model.md)

## <a name="analyze-your-model"></a>Analise o seu modelo

Uma vez concluído o seu treino com sucesso, inspecione os resultados. A pontuação BLEU é uma métrica que indica a qualidade da sua tradução. Também pode comparar manualmente as traduções feitas com o seu modelo personalizado com as traduções fornecidas no seu conjunto de testes navegando no separador "Teste" e clicando em "Resultados do Sistema". Inspecionar manualmente algumas destas traduções vai dar-lhe uma boa ideia da qualidade da tradução produzida pelo seu sistema. Para mais detalhes, visite [os Resultados dos Testes do Sistema.](how-to-view-system-test-results.md)

## <a name="deploy-a-trained-model"></a>Implementar um modelo treinado

Quando estiver pronto para implementar o seu modelo treinado, clique no botão "Implementar". Pode ter um modelo implantado por projeto e pode ver o estado da sua implantação na coluna Status. Para mais detalhes, visite [a Implementação do Modelo](how-to-view-system-test-results.md#deploy-a-model)

![Implementar um modelo treinado](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Utilize um modelo implantado

Os modelos implantados podem ser acedidos através do Tradutor especificando a https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) categoriaID. Mais informações sobre o Tradutor podem ser encontradas na página de referência da [API.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="next-steps"></a>Passos seguintes

- Saiba como navegar no espaço de trabalho do [Tradutor Personalizado e gerir os seus projetos.](workspace-and-project.md)
