---
title: 'Quickstart: Construa um classificador com o website Da Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você vai aprender a usar o site Da Visão Personalizada para criar, treinar e testar um modelo de classificação de imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconhecimento de imagem, app de reconhecimento de imagem, visão personalizada
ms.openlocfilehash: d25b226f5dfff34aa572b40d1e4de142cf8e0b7b
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221249"
---
# <a name="quickstart-build-a-classifier-with-the-custom-vision-website"></a>Quickstart: Construa um classificador com o website Da Visão Personalizada

Neste arranque rápido, você vai aprender a usar o site Da Visão Personalizada para criar um modelo de classificação de imagem. Uma vez construído um modelo, pode testá-lo com novas imagens e eventualmente integrá-lo na sua própria app de reconhecimento de imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um conjunto de imagens com as quais treinar o seu classificador. Veja abaixo as dicas sobre a escolha das imagens.

## <a name="create-custom-vision-resources"></a>Criar recursos de visão personalizada

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Criar um novo projeto

No seu navegador web, navegue na página web da [Visão Personalizada](https://customvision.ai) e selecione __Iniciar sôs.o.__ Inscreva-se com a mesma conta que usou para assinar no portal Azure.

![Imagem da página de inscrição](./media/browser-home.png)


1. Para criar o seu primeiro projeto, selecione **New Project**. Aparecerá a nova caixa de diálogo do **projeto Create.**

    ![A nova caixa de diálogo do projeto tem campos para nome, descrição e domínios.](./media/getting-started-build-a-classifier/new-project.png)

1. Insira um nome e uma descrição para o projeto. Em seguida, selecione um Grupo de Recursos. Se a sua conta de súmus estiver associada a uma conta Azure, o dropdown do Grupo de Recursos apresentará todos os seus Grupos de Recursos Azure que incluem um Recurso de Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se não houver nenhum grupo de recursos disponível, por favor confirme que iniciou [sessão](https://customvision.ai) em customvision.ai com a mesma conta que usou para iniciar sessão no [portal Azure](https://portal.azure.com/). Além disso, confirme que selecionou o mesmo "Diretório" no website da Visão Personalizada que o diretório no portal Azure onde estão localizados os seus recursos de Visão Personalizada. Em ambos os sites, pode selecionar o seu diretório a partir do menu de conta drop down no canto superior direito do ecrã. 

1. Selecione __classificação__ em __tipos de projeto.__ Em seguida, nos __Tipos de Classificação,__ escolha **multilabel** ou **multiclasse,** dependendo da sua caixa de utilização. A classificação multilabel aplica qualquer número das suas tags a uma imagem (zero ou mais), enquanto a classificação multiclasse classifica as imagens em categorias únicas (cada imagem que submeter será classificada na etiqueta mais provável). Poderá alterar o tipo de classificação mais tarde, se quiser.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o classificador para tipos específicos de imagens, conforme descrito na tabela seguinte. Poderá alterar o domínio mais tarde, se desejar.

    |Domínio|Objetivo|
    |---|---|
    |__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagem. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico. |
    |__Comida__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Se quiser classificar fotografias de frutas ou vegetais individuais, utilize o domínio Food.|
    |__Pontos de referência__|Otimizado para marcos reconhecíveis, naturais e artificiais. Este domínio funciona melhor quando o marco é claramente visível na fotografia. Este domínio funciona mesmo que o marco seja ligeiramente obstruído por pessoas à sua frente.|
    |__Retail__|Otimizado para imagens que são encontradas em um catálogo de compras ou site de compras. Se quiser classificar de alta precisão entre vestidos, calças e camisas, use este domínio.|
    |__Domínios compactos__| Otimizado para os constrangimentos da classificação em tempo real em dispositivos móveis. Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente.|

1. Por fim, __selecione Criar projeto.__

## <a name="choose-training-images"></a>Escolha imagens de treino

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Nesta secção, você carregará e marcará manualmente imagens para ajudar a treinar o classificador. 

1. Para adicionar imagens, __selecione Adicionar imagens__ e, em seguida, selecione __procurar ficheiros locais__. Selecione __Open__ para mover-se para a marcação. A sua seleção de etiquetas será aplicada a todo o grupo de imagens que selecionou para carregar, por isso é mais fácil fazer upload de imagens em grupos separados de acordo com as suas etiquetas aplicadas. Também pode alterar as etiquetas para imagens individuais depois de serem carregadas.

    ![O controlo de imagens adicionais é mostrado na parte superior esquerda, e como um botão no centro inferior.](./media/getting-started-build-a-classifier/add-images01.png)


1. Para criar uma etiqueta, introduza texto no campo __'As etiquetas'__ e prima Enter. Se a etiqueta já existir, aparecerá num menu suspenso. Num projeto multilabel, pode adicionar mais do que uma etiqueta às suas imagens, mas num projeto multiclasse pode adicionar apenas uma. Para terminar o upload das imagens, utilize o botão __de ficheiros Upload [número].__ 

    ![Imagem da etiqueta e página de upload](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecione __Feito__ uma vez que as imagens tenham sido carregadas.

    ![A barra de progresso mostra todas as tarefas concluídas.](./media/getting-started-build-a-classifier/add-images04.png)

Para carregar outro conjunto de imagens, volte ao topo desta secção e repita os passos.

## <a name="train-the-classifier"></a>Preparar o classificador

Para treinar o classificador, selecione o botão **Train.** O classificador utiliza todas as imagens atuais para criar um modelo que identifique as qualidades visuais de cada tag.

![O botão do comboio no topo direito da barra de ferramentas do cabeçalho da página web](./media/getting-started-build-a-classifier/train01.png)

O processo de treino deve demorar apenas alguns minutos. Durante este tempo, as informações sobre o processo de treino são apresentadas no **separador Performance.**

![A janela do navegador com um diálogo de treino na secção principal](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Avaliar o classificador

Após o treino ter terminado, o desempenho do modelo é estimado e exibido. O Serviço de Visão Personalizada utiliza as imagens que submeteu para treino para calcular a precisão e a recuperação, utilizando um processo chamado [validação cruzada k-fold](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precisão e recordação são duas medições diferentes da eficácia de um classificador:

- **Precisão** indica a fração de classificações identificadas que estavam corretas. Por exemplo, se o modelo identificasse 100 imagens como cães, e 99 delas fossem realmente de cães, então a precisão seria de 99%.
- **A recolha** indica a fração de classificações reais que foram corretamente identificadas. Por exemplo, se houvesse realmente 100 imagens de maçãs, e o modelo identificasse 80 como maçãs, a recuperação seria de 80%.

![Os resultados da formação mostram a precisão e a recuperação globais, e a precisão e a recuperação de cada etiqueta no classificador.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Limiar de probabilidade

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gerir iterações de formação

Cada vez que treina o seu classificador, cria uma nova _iteração_ com as suas próprias métricas de desempenho atualizadas. Pode ver todas as suas iterações no painel esquerdo do **separador Performance.** Também encontrará o botão **Eliminar,** que pode utilizar para eliminar uma iteração se for obsoleto. Quando elimina uma iteração, apaga-se quaisquer imagens que estejam exclusivamente associadas a ela.

Consulte [o seu modelo com a previsão API](./use-prediction-api.md) para aprender a aceder programaticamente aos seus modelos treinados.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar e treinar um modelo de classificação de imagem utilizando o website Da Visão Personalizada. Em seguida, obtenha mais informações sobre o processo iterativo de melhorar o seu modelo.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](./overview.md)