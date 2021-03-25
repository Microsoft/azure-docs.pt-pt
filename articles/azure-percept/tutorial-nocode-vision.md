---
title: Criar uma solução de visão sem código no Azure Percept Studio
description: Aprenda a criar uma solução de visão sem código no Azure Percept Studio e implemente-a no seu Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023134"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Criar uma solução de visão sem código no Azure Percept Studio

O Azure Percept Studio permite-lhe construir e implementar soluções de visão computacional personalizadas, sem necessidade de codificação. Neste artigo, vai:

- Criar um projeto de visão no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Colete imagens de treino com o seu devkit
- Rotule as suas imagens de treino em [Visão Personalizada](https://www.customvision.ai/)
- Treine o seu modelo de deteção ou classificação de objetos personalizados
- Implemente o seu modelo no seu devkit
- Melhore o seu modelo configurando a reconversão

Este tutorial é adequado para desenvolvedores com pouca ou nenhuma experiência em IA e aqueles que apenas começam com Azure Percept.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de configuração Azure Percept DK: ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub

## <a name="create-a-vision-prototype"></a>Criar um protótipo de visão

1. Inicie o seu navegador e vá ao [Azure Percept Studio.](https://go.microsoft.com/fwlink/?linkid=2135819)

1. Na página geral, clique no separador **Demos & tutoriais.**  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Ecrã geral do Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. Sob **tutoriais e demonstrações da Visão,** clique em **Criar um protótipo de visão.**

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Azure Percept Studio demos e tutoriais." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Na página do **protótipo new Azure Percept Custom Vision,** faça o seguinte:

    1. Na caixa de **nomes do Projeto,** insira um nome para o seu protótipo de visão.

    1. Introduza uma descrição do protótipo de visão na caixa **de descrição** do projeto.

    1. Selecione **Azure Percept DK** no menu drop-down **do tipo dispositivo.**

    1. Selecione um recurso no menu de entrega de **recursos** ou clique em **Criar um novo recurso.** Se optar por criar um novo recurso, faça o seguinte na janela **Criar:**
        1. Insira um nome para o seu novo recurso.
        1. Selecione a sua subscrição do Azure.
        1. Selecione um grupo de recursos ou crie um novo.
        1. Selecione o seu país/região preferencial.
        1. Selecione o seu nível de preços (recomendamos S0).
        1. Clique em **Criar** na parte inferior da janela.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Criar janela de recursos.":::

    1. Para **o tipo de projeto,** escolha se o seu projeto de visão irá realizar a deteção de objetos ou classificação de imagem. Para obter mais informações sobre os tipos de projeto, clique em **Ajuda-me a escolher.**

    1. Para **otimização,** selecione se pretende otimizar o seu projeto para precisão, baixa latência da rede ou um equilíbrio de ambos.

    1. Clique no botão **Criar**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Crie um ecrã de protótipo de visão personalizado.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Ligue um dispositivo ao seu projeto e capture imagens

Depois de criar uma solução de visão, deve adicionar-lhe o seu devkit e o seu IoT Hub correspondente.

1. Energia no seu devkit.

1. No menu dropdown **IoT Hub,** selecione o hub IoT ao que o seu devkit estava ligado durante o OOBE.

1. No menu de downdown **dos Dispositivos,** selecione o seu devkit.

Em seguida, deve carregar imagens ou capturar imagens para treinar o seu modelo de IA. Recomendamos o upload de pelo menos 30 imagens por tipo de etiqueta. Por exemplo, se você quiser construir um detetor de cães e gatos, você deve carregar pelo menos 30 imagens de cães e 30 imagens de gatos. Para capturar imagens com a visão SoM do seu devkit, faça o seguinte:

1. Na janela de captura de **imagem,** selecione **Ver o fluxo do dispositivo** para visualizar o fluxo de vídeo SoM da visão.

1. Verifique o fluxo de vídeo para garantir que a câmara SoM da sua visão está corretamente alinhada para tirar as fotos de treino. Faça os ajustes conforme necessário.

1. Na janela de captura de **imagem,** clique em **Tirar fotografia**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Ecrã de captura de imagem.":::

1. Em alternativa, crie uma captura de imagem automatizada para recolher uma grande quantidade de imagens de cada vez, verificando a caixa **de captura automática de imagens.** Selecione a sua taxa de imagem preferida na **taxa de Captura** e o número total de imagens que pretende recolher no **Target**. Clique **em Definir a captura automática** para iniciar o processo automático de captura de imagem.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Menu de captura automática de imagem.":::

Quando tiver fotografias suficientes, clique em **Seguinte: Marque imagens e modele o treino** na parte inferior do ecrã. Todas as imagens serão guardadas na [Visão Personalizada.](https://www.customvision.ai/)

> [!NOTE]
> Se optar por carregar imagens de treino diretamente para a Visão Personalizada, tenha em atenção que o tamanho do ficheiro de imagem não pode exceder 6MB.

## <a name="tag-images-and-train-your-model"></a>Marque imagens e treine o seu modelo

Antes de treinar o seu modelo, adicione etiquetas às suas imagens.

1. Na **página de treinamento de imagens tag e modelo,** clique **em Open project in Custom Vision**.

1. No lado esquerdo da página **Visão Personalizada,** clique em **'Não marcar'** em Tags para visualizar as **imagens** que acabou de recolher no passo anterior. Selecione uma ou mais das suas imagens não gravadas.

1. Na janela **Image Detail,** clique na imagem para começar a marcar. Se selecionou a deteção de objetos como o seu tipo de projeto, também deve desenhar uma [caixa de delimitação](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) em torno de objetos específicos que gostaria de etiquetar. Ajuste a caixa de delimitação conforme necessário. Digite a etiqueta do seu objeto e clique **+** para aplicar a etiqueta. Por exemplo, se estivesse a criar uma solução de visão que o notificasse quando uma prateleira de loja precisa de ser reposta, adicione a etiqueta "Prateleira Vazia" a imagens de prateleiras vazias e adicione a etiqueta "Prateleira Cheia" a imagens de prateleiras totalmente abastecidas. Repita para todas as imagens não gravadas.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Tela de marcação de imagem em Visão Personalizada.":::

1. Depois de marcar as suas imagens, clique no ícone **X** no canto superior direito da janela. Clique em **Etiquetas** para ver todas as suas imagens recentemente marcadas. 

1. Depois de as suas imagens serem rotuladas, está pronto para treinar o seu modelo de IA. Para tal, clique em **Train** perto do topo da página. Deve ter pelo menos 15 imagens por tipo de etiqueta para treinar o seu modelo (recomendamos a utilização de pelo menos 30). O treino normalmente demora cerca de 30 minutos, mas pode demorar mais tempo se o seu conjunto de imagens for extremamente grande.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Seleção de imagem de treino com botão de comboio em destaque.":::

1. Quando o treino estiver concluído, o ecrã mostrará o desempenho do seu modelo. Para obter mais informações sobre a avaliação destes resultados, consulte a documentação de avaliação do [modelo.](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector) Após o treino, pode também desejar [testar o seu modelo](../cognitive-services/custom-vision-service/test-your-model.md) em imagens adicionais e treinar se necessário. Cada vez que treinar o seu modelo, será salvo como uma nova iteração. Consulte a [documentação da Visão Personalizada](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) para obter informações adicionais sobre como melhorar o desempenho do modelo.

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Resultados de treino de modelo.":::

    > [!NOTE]
    > Se optar por testar o seu modelo em imagens adicionais no Custom Vision, tenha em atenção que o tamanho do ficheiro de imagem de teste não pode exceder 4MB.

Assim que estiver satisfeito com o desempenho do seu modelo, feche a Visão Personalizada fechando o separador do navegador.

## <a name="deploy-your-ai-model"></a>Implemente o seu modelo de IA

1. Volte para o separador Azure Percept Studio e clique em **Seguinte: Avalie e implemente** na parte inferior do seu ecrã.

1. A janela **avaliar e implantar** mostrará o desempenho da iteração do seu modelo selecionado. Selecione a iteração que pretende implantar no seu devkit sob o menu de **iteração modelo** e clique em Implementar o **modelo** na parte inferior do ecrã.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Ecrã de implementação do modelo." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Depois de implementar o seu modelo, veja o fluxo de vídeo do seu dispositivo para ver o seu modelo a inferencê-lo em ação.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Fluxo de dispositivo mostrando o detetor de auscultadores em ação.":::

Depois de fechar esta janela, poderá voltar a editar o seu projeto de visão a qualquer momento clicando em **Vision** in **AI Projects** na página inicial do Azure Percept Studio e selecionando o nome do seu projeto de visão.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Página do projeto de visão." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Melhore o seu modelo configurando a reconversão

Depois de ter treinado o seu modelo e o ter implantado no dispositivo, pode melhorar o desempenho do modelo, configurando parâmetros de reconversão para capturar mais dados de treino. Esta funcionalidade é usada para melhorar o desempenho de um modelo treinado, dando-lhe a capacidade de capturar imagens com base numa gama de probabilidades. Por exemplo, pode configurar o seu dispositivo apenas para capturar imagens de treino quando a probabilidade é baixa. Aqui está uma [orientação adicional](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) para adicionar mais imagens e equilibrar dados de treino.

1. Para configurar a reconversão, volte ao seu **Projeto,** em seguida, para **o Projeto Resumo**
1. No separador **captura de imagem,** selecione **a captura automática de imagens** e **crie a reconversão**.
1. Configurar a captura de imagem automatizada para recolher uma grande quantidade de imagens de cada vez, verificando a caixa **de captura automática de imagens.**
1. Selecione a sua taxa de imagem preferida na **taxa de Captura** e o número total de imagens que pretende recolher no **Target**.
1. Na secção **de reconversão configurada,** selecione a iteração para a quais gostaria de capturar mais dados de treino e, em seguida, selecione o intervalo de probabilidades. Apenas as imagens que satisfaçam a taxa de probabilidade serão enviadas para o seu projeto.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="captura de imagem.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se criou um novo recurso Azure para este tutorial e já não deseja desenvolver ou utilizar a sua solução de visão, execute os seguintes passos para eliminar o seu recurso:

1. Aceda ao [Portal do Azure](https://ms.portal.azure.com/).
1. Clique em **Todos os recursos.**
1. Clique na caixa de verificação ao lado do recurso criado durante este tutorial. O tipo de recurso será listado como **Serviços Cognitivos.**
1. Clique no ícone **Eliminar** perto da parte superior do ecrã.

## <a name="video-walkthrough"></a>Instruções de vídeo

Para uma passagem visual dos passos acima descritos, consulte o seguinte vídeo:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Passos Seguintes

Em seguida, confira a visão como-a-ver artigos para obter informações sobre funcionalidades adicionais de solução de visão no Azure Percept Studio.

<!--
Add links to how-to articles and oobe article.
-->