---
title: 'Quickstart: Construa um detetor de objetos com o website Custom Vision'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você vai aprender a usar o site Da Visão Personalizada para criar, treinar e testar um modelo de detetor de objetos.
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
ms.openlocfilehash: 5ecd5fee565a8d31e0ff05f3b234771446242d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99221300"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>Quickstart: Construa um detetor de objetos com o website Custom Vision

Neste arranque rápido, você vai aprender a usar o site Da Visão Personalizada para criar um modelo de detetor de objetos. Uma vez construído um modelo, pode testá-lo com novas imagens e eventualmente integrá-lo na sua própria app de reconhecimento de imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um conjunto de imagens para treinar o seu modelo de detetor. Pode utilizar o conjunto de [imagens](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) de amostra no GitHub. Ou, pode escolher as suas próprias imagens usando as dicas abaixo.

## <a name="create-custom-vision-resources"></a>Criar recursos de visão personalizada

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Criar um novo projeto

No seu navegador web, navegue na página web da [Visão Personalizada](https://customvision.ai) e selecione __Iniciar sôs.o.__ Inscreva-se com a mesma conta que usou para assinar no portal Azure.

![Imagem da página de inscrição](./media/browser-home.png)


1. Para criar o seu primeiro projeto, selecione **New Project**. Aparecerá a nova caixa de diálogo do **projeto Create.**

    ![A nova caixa de diálogo do projeto tem campos para nome, descrição e domínios.](./media/get-started-build-detector/new-project.png)

1. Insira um nome e uma descrição para o projeto. Em seguida, selecione um Grupo de Recursos. Se a sua conta de súmus estiver associada a uma conta Azure, o dropdown do Grupo de Recursos apresentará todos os seus Grupos de Recursos Azure que incluem um Recurso de Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se não houver nenhum grupo de recursos disponível, por favor confirme que iniciou [sessão](https://customvision.ai) em customvision.ai com a mesma conta que usou para iniciar sessão no [portal Azure](https://portal.azure.com/). Além disso, confirme que selecionou o mesmo "Diretório" no website da Visão Personalizada que o diretório no portal Azure onde estão localizados os seus recursos de Visão Personalizada. Em ambos os sites, pode selecionar o seu diretório a partir do menu de conta drop down no canto superior direito do ecrã. 

1. Selecione __a deteção de objetos__ em __tipos de projeto.__

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o detetor para tipos específicos de imagens, conforme descrito na tabela seguinte. Poderá alterar o domínio mais tarde, se desejar.

    |Domínio|Objetivo|
    |---|---|
    |__Geral__| Otimizado para uma ampla gama de tarefas de deteção de objetos. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico. |
    |__Logótipo__|Otimizado para encontrar logotipos da marca em imagens.|
    |__Produtos nas prateleiras__|Otimizado para detetar e classificar produtos nas prateleiras.|
    |__Domínios compactos__| Otimizado para os constrangimentos da deteção de objetos em tempo real em dispositivos móveis. Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente.|

1. Por fim, __selecione Criar projeto.__

## <a name="choose-training-images"></a>Escolha imagens de treino

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Nesta secção, você carregará e marcará manualmente imagens para ajudar a treinar o detetor. 

1. Para adicionar imagens, __selecione Adicionar imagens__ e, em seguida, selecione __procurar ficheiros locais__. Selecione __Open__ para carregar as imagens.

    ![O controlo de imagens adicionais é mostrado na parte superior esquerda, e como um botão no centro inferior.](./media/get-started-build-detector/add-images.png)

1. Verá as suas imagens carregadas na secção **não carregada** da UI. O próximo passo é marcar manualmente os objetos que pretende que o detetor aprenda a reconhecer. Clique na primeira imagem para abrir a janela do diálogo de marcação. 

    ![Imagens carregadas, na secção não anotado](./media/get-started-build-detector/images-untagged.png)

1. Clique e arraste um retângulo em torno do objeto na sua imagem. Em seguida, introduza um novo nome de identificação com o **+** botão ou selecione uma etiqueta existente na lista de drop-down. É importante marcar todas as instâncias do(s) objeto(s) que você pretende detetar, porque o detetor usa a área de fundo não marcada como um exemplo negativo no treino. Quando terminar de marcar, clique na seta à direita para guardar as etiquetas e passe para a imagem seguinte.

    ![Marcação de um objeto com uma seleção retangular](./media/get-started-build-detector/image-tagging.png)

Para carregar outro conjunto de imagens, volte ao topo desta secção e repita os passos.

## <a name="train-the-detector"></a>Treine o detetor

Para treinar o modelo do detetor, selecione o botão **Train.** O detetor utiliza todas as imagens atuais e as suas etiquetas para criar um modelo que identifique cada objeto marcado.

![O botão do comboio no topo direito da barra de ferramentas do cabeçalho da página web](./media/getting-started-build-a-classifier/train01.png)

O processo de treino deve demorar apenas alguns minutos. Durante este tempo, as informações sobre o processo de treino são apresentadas no **separador Performance.**

![A janela do navegador com um diálogo de treino na secção principal](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Avaliar o detetor

Após o treino ter terminado, o desempenho do modelo é calculado e exibido. O serviço Custom Vision utiliza as imagens que submeteu para treino para calcular precisão, recordação e precisão média média. Precisão e recordação são duas medições diferentes da eficácia de um detetor:

- **Precisão** indica a fração de classificações identificadas que estavam corretas. Por exemplo, se o modelo identificasse 100 imagens como cães, e 99 delas fossem realmente de cães, então a precisão seria de 99%.
- **A recolha** indica a fração de classificações reais que foram corretamente identificadas. Por exemplo, se houvesse realmente 100 imagens de maçãs, e o modelo identificasse 80 como maçãs, a recuperação seria de 80%.
- **Precisão média média** é o valor médio da precisão média (AP). AP é a área sob a curva de precisão/recordação (precisão traçada contra a recuperação para cada previsão feita).

![Os resultados do treino mostram a precisão geral e a recuperação, e a precisão média média.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Limiar de probabilidade

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>Limiar de sobreposição

O slider **do Limiar de Sobreposição** trata da forma correta como uma previsão correta de objetos deve ser considerada "correta" no treino. Define a sobreposição mínima permitida entre a caixa de delimitação de objetos prevista e a caixa de delimitação real inserida no utilizador. Se as caixas de delimitação não se sobreporem a este grau, a previsão não será considerada correta.

## <a name="manage-training-iterations"></a>Gerir iterações de formação

Cada vez que treina o detetor, cria uma nova _iteração_ com as suas próprias métricas de desempenho atualizadas. Pode ver todas as suas iterações no painel esquerdo do **separador Performance.** No painel esquerdo também encontrará o botão **Eliminar,** que pode utilizar para eliminar uma iteração se estiver obsoleto. Quando elimina uma iteração, apaga-se quaisquer imagens que estejam exclusivamente associadas a ela.

Consulte [o seu modelo com a previsão API](./use-prediction-api.md) para aprender a aceder programaticamente aos seus modelos treinados.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar e treinar um modelo de detetor de objetos utilizando o website Da Visão Personalizada. Em seguida, obtenha mais informações sobre o processo iterativo de melhorar o seu modelo.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](./overview.md)
