---
title: 'Quickstart: Construa, implemente e use um modelo personalizado - Custom Tradutor'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você passa por um processo passo a passo de construção de um sistema de tradução usando o Tradutor Personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: 4fbac2c2d77d838ac7fc4f3caaa73983633c3e03
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366065"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Quickstart: Construa, implemente e use um modelo personalizado para tradução

Este artigo fornece instruções passo a passo para construir um sistema de tradução com o Tradutor Personalizado.

## <a name="prerequisites"></a>Pré-requisitos

1. Para utilizar o Portal [do Tradutor Personalizado,](https://portal.customtranslator.azure.ai) necessitará de uma [conta Microsoft](https://signup.live.com) ou de uma [conta AD Azure](../../../active-directory/fundamentals/active-directory-whatis.md) (conta de organização hospedada no Azure) para iniciar sessão.

2. Uma subscrição da API de Texto tradutor através do portal Azure. Você precisará da chave de subscrição de Texto de Tradutor API para associar-se ao seu espaço de trabalho no Tradutor Personalizado. Veja [como se inscrever para a API de Texto do Tradutor](../translator-how-to-signup.md).

3. Quando tiver ambos os dois acima, inscreva-se no portal  [Do Tradutor Personalizado](https://portal.customtranslator.azure.ai) para criar espaços de trabalho, projetos, carregar ficheiros e criar/implementar modelos.

>[!Note]
>O Tradutor Personalizado não suporta a criação de espaço de trabalho para o recurso API de texto de tradutor que foi criado dentro [do VNET Ativado.](../../../api-management/api-management-using-with-vnet.md)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Se for o utilizador pela primeira vez, será solicitado que concorde com os Termos de Serviço, que crie um espaço de trabalho e que associe o seu espaço de trabalho à subscrição API do Texto de Texto do Microsoft Tradutor.

![Criar espaço de trabalho ](media/quickstart/terms-of-service.png)
 ![ Criar imagem de espaço de trabalho 1 ](media/quickstart/create-workspace-1.png)
 ![ Criar imagem de espaço de trabalho 2 ](media/quickstart/create-workspace-2.png)
 ![ Criar imagem de espaço de trabalho 3 ](media/quickstart/create-workspace-3.png)
 ![ Criar imagem de espaço de trabalho 4 ](media/quickstart/create-workspace-4.png)
 ![ Criar imagem de espaço de trabalho 5 ](media/quickstart/create-workspace-5.png)
 ![ Criar imagem de espaço de trabalho 6](media/quickstart/create-workspace-6.png)

Nas visitas subsequentes ao portal Do Tradutor Personalizado, navegue na página Definições onde pode gerir o seu espaço de trabalho, criar mais espaços de trabalho, associar a sua chave de subscrição de Texto API do Microsoft Tradutor com os seus espaços de trabalho, adicionar coproprietários e alterar uma chave de subscrição.

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

## <a name="swap-deployed-model"></a>Trocar modelo implantado

Para trocar um modelo implantado com outro dentro de um projeto clique no botão "Swap" exibido ao lado do modelo pretendido. Durante o processo de troca, o modelo implementado continuará disponível para atender pedidos de tradução. 

![Trocar modelo implantado](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>Utilize um modelo implantado

Os modelos implantados podem ser acedidos através do Texto API V3 do Texto do Tradutor [da Microsoft, especificando a categoriaID](../reference/v3-0-translate.md?tabs=curl). Mais informações sobre a API de Texto de Tradutor podem ser encontradas na página de Referência da [API.](../reference/v3-0-reference.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como navegar no espaço de trabalho do [Tradutor Personalizado e gerir os seus projetos.](workspace-and-project.md)