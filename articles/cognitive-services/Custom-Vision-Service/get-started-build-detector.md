---
title: Início rápido criar um detector de objeto-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a usar o site Visão Personalizada para criar um modelo de classificação de imagem.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: 0d9c175db1370fe07b3278a46d910c59d81df860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969926"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Início rápido: como criar um detector de objeto com Visão Personalizada

Neste guia de início rápido, você aprenderá a criar um detector de objeto por meio do site Visão Personalizada. Depois de criar um modelo de detector, você pode usar o serviço de Visão Personalizada para detecção de objeto.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um conjunto de imagens com o qual treinar seu modelo de detector. Você pode usar o conjunto de [imagens de exemplo](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) no github. Ou, você pode escolher suas próprias imagens usando as dicas abaixo.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar Visão Personalizada recursos no portal do Azure

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Criar um novo projeto

No navegador da Web, navegue até a [página da Web do visão personalizada](https://customvision.ai) e selecione __entrar__. Entre com a mesma conta usada para entrar no portal do Azure.

![Imagem da página de entrada](./media/browser-home.png)


1. Para criar seu primeiro projeto, selecione **novo projeto**. A caixa de diálogo **criar novo projeto** será exibida.

    ![A caixa de diálogo novo projeto tem campos para nome, descrição e domínios.](./media/get-started-build-detector/new-project.png)

1. Insira um nome e uma descrição para o projeto. Em seguida, selecione um grupo de recursos. Se sua conta conectada estiver associada a uma conta do Azure, a lista suspensa grupo de recursos exibirá todos os seus grupos de recursos do Azure que incluem um recurso Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se nenhum grupo de recursos estiver disponível, confirme se você fez logon no [customvision.ai](https://customvision.ai) com a mesma conta usada para fazer logon no [portal do Azure](https://portal.azure.com/). Além disso, confirme que você selecionou o mesmo "diretório" no portal de Visão Personalizada como o diretório no portal do Azure em que os recursos do Visão Personalizada estão localizados. Em ambos os sites, você pode selecionar o diretório no menu suspenso da conta no canto superior direito da tela. 

1. Selecione __detecção de objeto__ em __tipos de projeto__.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o detector para tipos específicos de imagens, conforme descrito na tabela a seguir. Você poderá alterar o domínio mais tarde, se desejar.

    |Domain|Finalidade|
    |---|---|
    |__Geral__| Otimizado para uma ampla variedade de tarefas de detecção de objetos. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio genérico. |
    |__Logo__|Otimizado para localizar logotipos de marca em imagens.|
    |__Domínios compactados__| Otimizado para as restrições de detecção de objetos em tempo real em dispositivos móveis. Os modelos gerados por domínios do Compact podem ser exportados para serem executados localmente.|

1. Por fim, selecione __criar projeto__.

## <a name="choose-training-images"></a>Escolher imagens de treinamento

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Nesta seção, você carregará e marcará manualmente as imagens para ajudar a treinar o detector. 

1. Para adicionar imagens, clique no botão __Adicionar imagens__ e selecione __procurar arquivos locais__. Selecione __abrir__ para carregar as imagens.

    ![O controle adicionar imagens é mostrado no canto superior esquerdo e como um botão no centro inferior.](./media/get-started-build-detector/add-images.png)

1. Você verá suas imagens carregadas na seção não **marcada** da interface do usuário. A próxima etapa é marcar manualmente os objetos que você deseja que o detector Aprenda a reconhecer. Clique na primeira imagem para abrir a janela da caixa de diálogo de marcação. 

    ![Imagens carregadas, na seção não marcada](./media/get-started-build-detector/images-untagged.png)

1. Clique e arraste um retângulo em volta do objeto em sua imagem. Em seguida, insira um novo nome de marca com o botão **+** ou selecione uma marca existente na lista suspensa. É muito importante marcar todas as instâncias dos objetos que você deseja detectar, pois o detector usa a área de plano de fundo não marcada como um exemplo negativo no treinamento. Quando terminar de marcar, clique na seta à direita para salvar as marcas e passar para a próxima imagem.

    ![Marcação de um objeto com uma seleção retangular](./media/get-started-build-detector/image-tagging.png)

Para carregar outro conjunto de imagens, retorne para a parte superior desta seção e repita as etapas.

## <a name="train-the-detector"></a>Treinar o detector

Para treinar o modelo de detector, selecione o botão **treinar** . O detector usa todas as imagens atuais e suas marcas para criar um modelo que identifica cada objeto marcado.

![O botão de treinamento no canto superior direito da barra de ferramentas do cabeçalho da página da Web](./media/getting-started-build-a-classifier/train01.png)

O processo de treinamento deve levar apenas alguns minutos. Durante esse tempo, as informações sobre o processo de treinamento são exibidas na guia **desempenho** .

![A janela do navegador com uma caixa de diálogo de treinamento na seção principal](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Avaliar o detector

Após a conclusão do treinamento, o desempenho do modelo é calculado e exibido. O serviço de Visão Personalizada usa as imagens que você enviou para treinamento para calcular precisão, Recall e a precisão média da média. Precision e RECALL são duas medidas diferentes da eficácia de um detector:

- **Precisão** indica a fração de classificações identificadas que estavam corretas. Por exemplo, se o modelo identificou 100 imagens como cachorros e 99 delas eram realmente de cachorros, a precisão seria 99%.
- **Recall** indica a fração de classificações reais que foram identificadas corretamente. Por exemplo, se havia, na verdade, 100 imagens de maçãs e o modelo identificado 80 como maçãs, o cancelamento seria 80%.

![Os resultados de treinamento mostram a precisão e a RECALL geral e a precisão média mediana.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Limiar de Probabilidade

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gerenciar iterações de treinamento

Cada vez que você treina o detector, cria uma nova _iteração_ com suas próprias métricas de desempenho atualizadas. Você pode exibir todas as iterações no painel esquerdo da guia **desempenho** . No painel esquerdo, você também encontrará o botão **excluir** , que pode ser usado para excluir uma iteração se ela estiver obsoleta. Ao excluir uma iteração, você exclui todas as imagens que estão associadas exclusivamente a ela.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar e treinar um modelo de detector de objetos usando o site Visão Personalizada. Em seguida, obtenha mais informações sobre o processo iterativo de aprimorar seu modelo.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)

