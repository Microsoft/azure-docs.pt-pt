---
title: Quickstart Construa um classificador - Serviço de Visão Personalizada
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
ms.openlocfilehash: 56bdaa324420bf274e7cda8ac1c6506e4bc9ad21
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404059"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Quickstart: Como construir um classificador com Visão Personalizada

Neste arranque rápido, você vai aprender a construir um classificador através do site Custom Vision. Uma vez construído um modelo de classificação, pode utilizar o serviço Custom Vision para classificação de imagem.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um conjunto de imagens com as quais treinar o seu classificador. Veja abaixo as dicas sobre a escolha das imagens.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de Visão Personalizada no portal Azure

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Criar um novo projeto

No seu navegador web, navegue para a [página web](https://customvision.ai) da Visão Personalizada e selecione Iniciar __sessão__. Inscreva-se na mesma conta que usou para assinar no portal Azure.

![Imagem da página de inscrição](./media/browser-home.png)


1. Para criar o seu primeiro projeto, selecione **New Project**. Aparecerá a nova caixa de diálogo do **projeto Create.**

    ![A nova caixa de diálogo do projeto tem campos de nome, descrição e domínios.](./media/getting-started-build-a-classifier/new-project.png)

1. Insira um nome e uma descrição para o projeto. Em seguida, selecione um Grupo de Recursos. Se a sua conta de entrada está associada a uma conta Azure, o dropdown do Grupo de Recursos apresentará todos os seus Grupos de Recursos Azure que incluem um Recurso de Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se não houver nenhum grupo de recursos disponível, confirme que entrou em [customvision.ai](https://customvision.ai) com a mesma conta que usou para entrar no [portal Azure](https://portal.azure.com/). Além disso, confirme que selecionou o mesmo "Diretório" no portal Visão Personalizada que o diretório no portal Azure onde estão localizados os seus recursos de Visão Personalizada. Em ambos os sites, pode selecionar o seu diretório a partir do menu de conta drop down no canto superior direito do ecrã. 

1. __Selecione classificação__ em tipos de __projeto__. Em seguida, em tipos de __classificação,__ escolha **multi-rótulo** ou **multiclasse,** dependendo da sua caixa de utilização. A classificação multi-etiqueta aplica qualquer número das suas etiquetas a uma imagem (zero ou mais), enquanto a classificação multiclasse classifica as imagens em categorias únicas (cada imagem que submeter será classificada na etiqueta mais provável). Poderá alterar o tipo de classificação mais tarde, se quiser.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o classificador para tipos específicos de imagens, conforme descrito na tabela seguinte. Poderá mudar o domínio mais tarde, se desejar.

    |Domain|Objetivo|
    |---|---|
    |__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagem. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico. |
    |__Comida__|Otimizado para fotografias de pratos como você os veria em um menu de restaurante. Se quiser classificar fotografias de frutas ou legumes individuais, utilize o domínio alimentar.|
    |__Pontos de referência__|Otimizado para marcos reconhecíveis, tanto naturais como artificiais. Este domínio funciona melhor quando o marco é claramente visível na fotografia. Este domínio funciona mesmo que o marco seja ligeiramente obstruído por pessoas à sua frente.|
    |__Retalho__|Otimizado para imagens que são encontradas num catálogo de compras ou site de compras. Se quiser classificação de alta precisão entre vestidos, calças e camisas, use este domínio.|
    |__Domínios compactos__| Otimizado para os constrangimentos da classificação em tempo real em dispositivos móveis. Os modelos gerados por domínios compactos podem ser exportados para funcionar localmente.|

1. Finalmente, selecione __Criar projeto__.

## <a name="choose-training-images"></a>Escolha imagens de treino

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Nesta secção, você carregará e marcará manualmente imagens para ajudar a treinar o classificador. 

1. Para adicionar imagens, clique no botão __Adicionar imagens__ e, em seguida, selecione __Navegar nos ficheiros locais__. Selecione __Open__ para passar para a marcação. A sua seleção de etiquetas será aplicada a todo o grupo de imagens que selecionou para carregar, por isso é mais fácil fazer o upload de imagens em grupos separados de acordo com as suas etiquetas desejadas. Também pode alterar as etiquetas para imagens individuais depois de terem sido carregadas.

    ![O controlo de imagens adicionais é mostrado na parte superior esquerda e como um botão no centro inferior.](./media/getting-started-build-a-classifier/add-images01.png)


1. Para criar uma etiqueta, introduza texto no campo __My Tags__ e prima Enter. Se a etiqueta já existir, aparecerá num menu de dropdown. Num projeto multi-rótulo, pode adicionar mais do que uma etiqueta às suas imagens, mas num projeto multiclasse só pode adicionar uma. Para terminar o upload das imagens, utilize o botão __de ficheiros Upload [número].__ 

    ![Imagem da página de etiquetas e upload](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecione __Feito__ uma vez que as imagens tenham sido carregadas.

    ![A barra de progresso mostra todas as tarefas concluídas.](./media/getting-started-build-a-classifier/add-images04.png)

Para fazer o upload de mais um conjunto de imagens, volte ao topo desta secção e repita os passos.

## <a name="train-the-classifier"></a>Preparar o classificador

Para treinar o classificador, selecione o botão **Train.** O classificador utiliza todas as imagens atuais para criar um modelo que identifique as qualidades visuais de cada tag.

![O botão do comboio na parte superior direita da barra de ferramentas do cabeçalho da página web](./media/getting-started-build-a-classifier/train01.png)

O processo de treino deve demorar apenas alguns minutos. Durante este período, as informações sobre o processo de treino são apresentadas no separador **Performance.**

![A janela do navegador com um diálogo de treino na secção principal](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Avaliar o classificador

Após o treino ter terminado, o desempenho do modelo é estimado e exibido. O Custom Vision Service utiliza as imagens que submeteu para treino para calcular precisão e recordação, utilizando um processo chamado [validação cruzada k-fold](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precisão e recordação são duas medições diferentes da eficácia de um classificador:

- **A precisão** indica a fração de classificações identificadas que estavam corretas. Por exemplo, se o modelo identificasse 100 imagens como cães, e 99 delas fossem na verdade de cães, então a precisão seria de 99%.
- **A recordação** indica a fração das classificações reais que foram corretamente identificadas. Por exemplo, se houvesse realmente 100 imagens de maçãs, e o modelo identificasse 80 como maçãs, a recolha seria de 80%.

![Os resultados do treino mostram a precisão e a recordação globais, e a precisão e recolha para cada etiqueta no classificador.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Limiar de Probabilidade

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gerir iterações de treino

Cada vez que treina o seu classificador, cria uma nova _iteração_ com as suas próprias métricas de desempenho atualizadas. Pode ver todas as suas iterações no painel esquerdo do separador **Performance.** Também encontrará o botão **Delete,** que pode usar para eliminar uma iteração se for obsoleto. Ao eliminar uma iteração, elimina-se quaisquer imagens que estejam exclusivamente associadas a ela.

Consulte [Utilize o seu modelo com a previsão API](./use-prediction-api.md) para aprender a aceder aos seus modelos treinados programáticamente.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar e treinar um modelo de classificação de imagem usando o website Da Visão Personalizada. Em seguida, obtenha mais informações sobre o processo iterativo de melhorar o seu modelo.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)

