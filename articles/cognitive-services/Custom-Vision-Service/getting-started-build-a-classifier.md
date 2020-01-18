---
title: Início rápido criar um classificador-Serviço de Visão Personalizada
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
ms.openlocfilehash: b664a586398e297a00ea9cd8fe68dc65e6ade5c8
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170015"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Início rápido: como criar um classificador com Visão Personalizada

Neste guia de início rápido, você aprenderá a criar um classificador por meio do site Visão Personalizada. Depois de criar um modelo de classificador, você pode usar o serviço de Visão Personalizada para classificação de imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um conjunto de imagens com o qual treinar o classificador. Consulte abaixo para obter dicas sobre como escolher imagens.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar Visão Personalizada recursos no portal do Azure

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Criar um novo projeto

No navegador da Web, navegue até a [página da Web do visão personalizada](https://customvision.ai) e selecione __entrar__. Entre com a mesma conta usada para entrar no portal do Azure.

![Imagem da página de entrada](./media/browser-home.png)


1. Para criar seu primeiro projeto, selecione **novo projeto**. A caixa de diálogo **criar novo projeto** será exibida.

    ![A caixa de diálogo novo projeto tem campos para nome, descrição e domínios.](./media/getting-started-build-a-classifier/new-project.png)

1. Insira um nome e uma descrição para o projeto. Em seguida, selecione um grupo de recursos. Se sua conta conectada estiver associada a uma conta do Azure, a lista suspensa grupo de recursos exibirá todos os seus grupos de recursos do Azure que incluem um recurso Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se nenhum grupo de recursos estiver disponível, confirme se você fez logon no [customvision.ai](https://customvision.ai) com a mesma conta usada para fazer logon no [portal do Azure](https://portal.azure.com/). Além disso, confirme que você selecionou o mesmo "diretório" no portal de Visão Personalizada como o diretório no portal do Azure em que os recursos do Visão Personalizada estão localizados. Em ambos os sites, você pode selecionar o diretório no menu suspenso da conta no canto superior direito da tela. 

1. Selecione __classificação__ em __tipos de projeto__. Em seguida, em __tipos de classificação__, escolha **multirótulo** ou **multiclasse**, dependendo de seu caso de uso. A classificação de vários rótulos aplica qualquer número de marcas a uma imagem (zero ou mais), enquanto a classificação multiclasse classifica as imagens em categorias únicas (cada imagem que você enviar será classificada na marca mais provável). Você poderá alterar o tipo de classificação posteriormente, se desejar.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o classificador para tipos específicos de imagens, conforme descrito na tabela a seguir. Você poderá alterar o domínio mais tarde, se desejar.

    |Domain|Finalidade|
    |---|---|
    |__Genérica__| Otimizado para uma ampla gama de tarefas de classificação de imagem. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio genérico. |
    |__Alimentos__|Otimizado para fotografias de pratos como você os veria em um menu de restaurante. Se você quiser classificar fotografias de frutas individuais ou de refeições, use o domínio de alimentos.|
    |__Pontos__|Otimizado para pontos de referência reconhecíveis, tanto naturais quanto artificiais. Esse domínio funciona melhor quando o ponto de referência está claramente visível na fotografia. Esse domínio funciona mesmo que o ponto de referência seja ligeiramente obstruído por pessoas na frente dele.|
    |__Mercado__|Otimizado para imagens encontradas em um catálogo de compras ou site de compras. Se você quiser classificação de alta precisão entre Dresses, Pants e camisas, use esse domínio.|
    |__Domínios compactados__| Otimizado para as restrições de classificação em tempo real em dispositivos móveis. Os modelos gerados por domínios do Compact podem ser exportados para serem executados localmente.|

1. Por fim, selecione __criar projeto__.

## <a name="choose-training-images"></a>Escolher imagens de treinamento

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Nesta seção, você carregará e marcará manualmente as imagens para ajudar a treinar o classificador. 

1. Para adicionar imagens, clique no botão __Adicionar imagens__ e selecione __procurar arquivos locais__. Selecione __abrir__ para mover para a marcação. Sua seleção de marca será aplicada a todo o grupo de imagens que você selecionou para carregar, portanto, é mais fácil carregar imagens em grupos separados de acordo com as marcas desejadas. Você também pode alterar as marcas de imagens individuais depois que elas tiverem sido carregadas.

    ![O controle adicionar imagens é mostrado no canto superior esquerdo e como um botão no centro inferior.](./media/getting-started-build-a-classifier/add-images01.png)


1. Para criar uma marca, digite o texto no campo __minhas marcas__ e pressione Enter. Se a marca já existir, ela será exibida em um menu suspenso. Em um projeto multirótulo, você pode adicionar mais de uma marca às suas imagens, mas em um projeto multiclasse, você pode adicionar apenas uma. Para concluir o carregamento das imagens, use o botão __carregar [número] arquivos__ . 

    ![Imagem da página de marcação e carregamento](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecione __concluído__ depois que as imagens forem carregadas.

    ![A barra de progresso mostra todas as tarefas concluídas.](./media/getting-started-build-a-classifier/add-images04.png)

Para carregar outro conjunto de imagens, retorne para a parte superior desta seção e repita as etapas.

## <a name="train-the-classifier"></a>Preparar o classificador

Para treinar o classificador, selecione o botão **treinar** . O classificador usa todas as imagens atuais para criar um modelo que identifica as qualidades visuais de cada marca.

![O botão de treinamento no canto superior direito da barra de ferramentas do cabeçalho da página da Web](./media/getting-started-build-a-classifier/train01.png)

O processo de treinamento deve levar apenas alguns minutos. Durante esse tempo, as informações sobre o processo de treinamento são exibidas na guia **desempenho** .

![A janela do navegador com uma caixa de diálogo de treinamento na seção principal](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Avaliar o classificador

Após a conclusão do treinamento, o desempenho do modelo será estimado e exibido. O Serviço de Visão Personalizada usa as imagens que você enviou para treinamento para calcular a precisão e a RECALL, usando um processo chamado [validação cruzada de k-fold](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precision e RECALL são duas medidas diferentes da eficácia de um classificador:

- **Precisão** indica a fração de classificações identificadas que estavam corretas. Por exemplo, se o modelo identificou 100 imagens como cachorros e 99 delas eram realmente de cachorros, a precisão seria 99%.
- **Recall** indica a fração de classificações reais que foram identificadas corretamente. Por exemplo, se havia, na verdade, 100 imagens de maçãs e o modelo identificado 80 como maçãs, o cancelamento seria 80%.

![Os resultados de treinamento mostram a precisão e a RECALL geral e a precisão e a recall para cada marca no classificador.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Limiar de Probabilidade

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Gerenciar iterações de treinamento

Cada vez que você treina o classificador, cria uma nova _iteração_ com suas próprias métricas de desempenho atualizadas. Você pode exibir todas as iterações no painel esquerdo da guia **desempenho** . Você também encontrará o botão **excluir** , que pode ser usado para excluir uma iteração se ela estiver obsoleta. Ao excluir uma iteração, você exclui todas as imagens que estão associadas exclusivamente a ela.

Consulte [usar seu modelo com a API de previsão](./use-prediction-api.md) para saber como acessar seus modelos treinados programaticamente.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar e treinar um modelo de classificação de imagem usando o site Visão Personalizada. Em seguida, obtenha mais informações sobre o processo iterativo de aprimorar seu modelo.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)

