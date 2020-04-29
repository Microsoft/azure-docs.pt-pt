---
title: Quickstart Construa um detetor de objetos - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você vai aprender a usar o site Da Visão Personalizada para criar um modelo de classificação de imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ba121400368f37c4a562a9c34e209c59d15b173c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404115"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Quickstart: Como construir um detetor de objetos com Visão Personalizada

Neste arranque rápido, você vai aprender a construir um detetor de objetos através do site Custom Vision. Assim que construir um modelo de detetor, pode utilizar o serviço Custom Vision para deteção de objetos.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um conjunto de imagens para treinar o seu modelo de detetor. Pode utilizar o conjunto de [imagens](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) de amostra no GitHub. Ou, pode escolher as suas próprias imagens usando as dicas abaixo.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de Visão Personalizada no portal Azure

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Criar um novo projeto

No seu navegador web, navegue para a [página web](https://customvision.ai) da Visão Personalizada e selecione Iniciar __sessão__. Inscreva-se na mesma conta que usou para assinar no portal Azure.

![Imagem da página de inscrição](./media/browser-home.png)


1. Para criar o seu primeiro projeto, selecione **New Project**. Aparecerá a nova caixa de diálogo do **projeto Create.**

    ![A nova caixa de diálogo do projeto tem campos de nome, descrição e domínios.](./media/get-started-build-detector/new-project.png)

1. Insira um nome e uma descrição para o projeto. Em seguida, selecione um Grupo de Recursos. Se a sua conta de entrada está associada a uma conta Azure, o dropdown do Grupo de Recursos apresentará todos os seus Grupos de Recursos Azure que incluem um Recurso de Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se não houver nenhum grupo de recursos disponível, confirme que entrou em [customvision.ai](https://customvision.ai) com a mesma conta que usou para entrar no [portal Azure](https://portal.azure.com/). Além disso, confirme que selecionou o mesmo "Diretório" no portal Visão Personalizada que o diretório no portal Azure onde estão localizados os seus recursos de Visão Personalizada. Em ambos os sites, pode selecionar o seu diretório a partir do menu de conta drop down no canto superior direito do ecrã. 

1. Selecione __Deteção de Objetos__ sob tipos de __projeto__.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o detetor para tipos específicos de imagens, conforme descrito na tabela seguinte. Poderá mudar o domínio mais tarde, se desejar.

    |Domain|Objetivo|
    |---|---|
    |__General__| Otimizado para uma ampla gama de tarefas de deteção de objetos. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico. |
    |__Logótipo__|Otimizado para encontrar logotipos da marca em imagens.|
    |__Domínios compactos__| Otimizado para os constrangimentos da deteção de objetos em tempo real em dispositivos móveis. Os modelos gerados por domínios compactos podem ser exportados para funcionar localmente.|

1. Finalmente, selecione __Criar projeto__.

## <a name="choose-training-images"></a>Escolha imagens de treino

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Nesta secção, irá carregar e marcar manualmente imagens para ajudar a treinar o detetor. 

1. Para adicionar imagens, clique no botão __Adicionar imagens__ e, em seguida, selecione __Navegar nos ficheiros locais__. Selecione __Abrir__ para carregar as imagens.

    ![O controlo de imagens adicionais é mostrado na parte superior esquerda e como um botão no centro inferior.](./media/get-started-build-detector/add-images.png)

1. Verá as suas imagens carregadas na secção **Untagged** da UI. O próximo passo é marcar manualmente os objetos que pretende que o detetor aprenda a reconhecer. Clique na primeira imagem para abrir a janela de diálogo de marcação. 

    ![Imagens enviadas, na secção Untagged](./media/get-started-build-detector/images-untagged.png)

1. Clique e arraste um retângulo em torno do objeto na sua imagem. Em seguida, introduza um **+** novo nome de etiqueta com o botão ou selecione uma etiqueta existente na lista de lançamentos. É muito importante marcar todas as instâncias do(s) objeto que você quer detetar, porque o detetor usa a área de fundo não marcada como um exemplo negativo no treino. Quando terminar de marcar, clique na seta à direita para guardar as suas etiquetas e siga para a próxima imagem.

    ![Marcação de um objeto com uma seleção retangular](./media/get-started-build-detector/image-tagging.png)

Para fazer o upload de mais um conjunto de imagens, volte ao topo desta secção e repita os passos.

## <a name="train-the-detector"></a>Treine o detetor

Para treinar o modelo do detetor, selecione o botão **Train.** O detetor utiliza todas as imagens e etiquetas atuais para criar um modelo que identifique cada objeto marcado.

![O botão do comboio na parte superior direita da barra de ferramentas do cabeçalho da página web](./media/getting-started-build-a-classifier/train01.png)

O processo de treino deve demorar apenas alguns minutos. Durante este período, as informações sobre o processo de treino são apresentadas no separador **Performance.**

![A janela do navegador com um diálogo de treino na secção principal](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Avaliar o detetor

Após o treino ter terminado, o desempenho do modelo é calculado e exibido. O serviço Custom Vision utiliza as imagens que submeteu para treino para calcular precisão, recordação e precisão média média. Precisão e recordação são duas medições diferentes da eficácia de um detetor:

- **A precisão** indica a fração de classificações identificadas que estavam corretas. Por exemplo, se o modelo identificasse 100 imagens como cães, e 99 delas fossem na verdade de cães, então a precisão seria de 99%.
- **A recordação** indica a fração das classificações reais que foram corretamente identificadas. Por exemplo, se houvesse realmente 100 imagens de maçãs, e o modelo identificasse 80 como maçãs, a recolha seria de 80%.

![Os resultados do treino mostram a precisão e a recuperação globais, e a precisão média média.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Limiar de Probabilidade

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gerir iterações de treino

Cada vez que treina o seu detetor, cria uma nova _iteração_ com as suas próprias métricas de desempenho atualizadas. Pode ver todas as suas iterações no painel esquerdo do separador **Performance.** No painel esquerdo também encontrará o botão **Apagar,** que pode utilizar para eliminar uma iteração se for obsoleto. Ao eliminar uma iteração, elimina-se quaisquer imagens que estejam exclusivamente associadas a ela.

Consulte [Utilize o seu modelo com a previsão API](./use-prediction-api.md) para aprender a aceder aos seus modelos treinados programáticamente.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar e treinar um modelo de detetor de objetos usando o website Custom Vision. Em seguida, obtenha mais informações sobre o processo iterativo de melhorar o seu modelo.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)

