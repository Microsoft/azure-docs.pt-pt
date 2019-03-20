---
title: Criar um classificador - serviço de visão personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar o site de visão personalizada para criar um modelo de classificação de imagem.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: f2cd8f5074f815e84caaedb01335406657f29088
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088014"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Como criar um classificador com visão personalizada

Para utilizar o serviço de visão personalizada para classificação de imagens, primeiro tem de criar um modelo de classificador. Neste guia, aprenderá como criar um classificador através do site de visão personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure válida. [Criar uma conta](https://azure.microsoft.com/free/) gratuitamente.
- Um conjunto de imagens com a qual treinar o classificador. Veja abaixo para obter dicas sobre a escolha de imagens.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de visão personalizada no Portal do Azure
Para utilizar o serviço de visão personalizada, terá de criar recursos de formação de visão personalizada e predição no do [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). Esta ação irá criar recursos de formação e predição. 

## <a name="create-a-new-project"></a>Criar um novo projeto

No seu browser, navegue para o [página da web de visão personalizada](https://customvision.ai) e selecione __iniciar sessão__. Inicie sessão com a mesma conta utilizada para iniciar sessão no Portal do Azure.

![Imagem da página de início de sessão](./media/browser-home.png)


1. Para criar seu primeiro projeto, selecione **novo projeto**. O **criar novo projeto** será apresentada a caixa de diálogo.

    ![A caixa de diálogo novo projeto tem campos de nome, descrição e domínios.](./media/getting-started-build-a-classifier/new-project.png)

1. Introduza um nome e uma descrição para o projeto. Em seguida, selecione um grupo de recursos. Se a sua conta com sessão iniciada estiver associada uma conta do Azure, a lista pendente do grupo de recursos irá apresentar todos os grupos de recursos do Azure que incluem um recurso de serviço de visão personalizada. 

> [!NOTE]
> Se nenhum grupo de recursos estiver disponível, confirme que iniciou sessão na [customvision.ai](https://customvision.ai) com a mesma conta que utilizou para iniciar sessão para o [Portal do Azure](https://portal.azure.com/). Além disso, confirme que selecionou o mesmo "diretório" no portal de visão personalizada como o diretório no portal do Azure, onde se encontram os seus recursos de visão personalizada. Em ambos os sites, pode selecionar o seu diretório no menu pendente menu da conta no canto superior direito da tela. 

1. Selecione __classificação__ sob __tipos de projetos__. Em seguida, em __tipos de classificação__, escolha o **Multilabel** ou **várias classes**, dependendo do seu caso de utilização. Classificação multilabel aplica-se um número qualquer de suas marcas para uma imagem (zero ou mais), enquanto a classificação multiclasses ordena imagens em únicas categorias (cada imagem que submeter serão também ser ordenada a marca mais provável). Será capaz de alterar o tipo de classificação mais tarde se desejar.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o classificador para tipos específicos de imagens, conforme descrito na tabela seguinte. Será capaz de alterar o domínio mais tarde se desejar.

    |Domain|Objetivo|
    |---|---|
    |__Genérico__| Otimizado para uma ampla variedade de tarefas de classificação de imagens. Se nenhum dos outros domínios são adequadas ou se tiver a certeza de qual escolher o domínio, selecione o domínio genérico. |
    |__Comida__|Otimizado para fotos de pratos conforme os veria num menu do restaurante. Se quiser classificar fotografias de frutas individuais ou vegetables, utilize o domínio de alimento.|
    |__Pontos de referência__|Otimizado para pontos de referência reconhecíveis, naturais e artificiais. Este domínio funciona melhor quando o ponto de referência é claramente visível na fotografia. Este domínio funciona mesmo que o ponto de referência é um pouco obstructed por pessoas na sua frente.|
    |__Revenda__|Otimizado para imagens que se encontram num catálogo de compra ou o Web site de compra. Se pretender que o serviço de alta precisão classificar entre dresses, intuição e shirts, utilize este domínio.|
    |__Adulto__|Otimizado para definir melhor o conteúdo para adultos e conteúdo não adulto. Por exemplo, se deseja bloquear imagens de pessoas em bathing de encriptação, este domínio permite-lhe criar um classificador personalizado para fazer isso.|
    |__Domínios compactos__| Otimizado para as restrições de classificação em tempo real em dispositivos móveis. Os modelos de gerados por domínios compactos podem ser exportados para executar localmente.|
    
1. Por fim, selecione __criar projeto__.

## <a name="choose-training-images"></a>Escolha as imagens de formação

Como mínimo, recomendamos que utilize, pelo menos, 30 imagens por etiqueta no conjunto de treinamento inicial. Também vai querer recolher algumas imagens Extras para testar o seu modelo uma vez que está preparado.

Para preparar o seu modelo com eficiência, utilize imagens com variedade visual. Selecionadas imagens com que variam de acordo com:
* ângulo da câmara
* iluminação
* background
* Estilo Visual
* subject(s) indivíduo/agrupados
* tamanho
* tipo

Além disso, certifique-se de que todas as imagens de formação cumpram os seguintes critérios:
* formato. jpg,. png ou. bmp
* não superior a 6MB de tamanho (4MB para imagens de predição)
* Não é inferior a 256 pixels na extremidade mais curta; quaisquer imagens mais curtas do que isso será automaticamente ajustado pelo serviço de visão personalizada

## <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Nesta secção irá carregar e marque manualmente as imagens para ajudar a treinar o classificador. 

1. Para adicionar imagens, clique a __adicionar imagens__ e, em seguida, selecione __navegar nos ficheiros locais__. Selecione __aberto__ para mover para a marcação. A seleção de etiqueta será aplicada para o grupo inteiro de imagens que selecionou para carregar, pelo que é mais fácil carregar imagens em grupos separados de acordo com as respetivas etiquetas pretendidos. Também pode alterar as etiquetas de imagens individuais após eles foram carregados.

    ![O controle de imagens de add é mostrado no canto superior esquerdo e, como um botão no centro na parte inferior.](./media/getting-started-build-a-classifier/add-images01.png)


1. Para criar uma etiqueta, introduza o texto no __minhas marcas__ campo e prima Enter. Se a etiqueta já existe, ele será exibido num menu suspenso. Num projeto multilabel, pode adicionar mais do que uma etiqueta para suas imagens, mas num projeto de várias classes pode adicionar apenas um. Para concluir a carregar as imagens, utilize o __carregar ficheiros [número]__ botão. 

    ![Imagem da etiqueta e o carregamento de página](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecione __feito__ assim que as imagens foram carregadas.

    ![A barra de progresso mostra todas as tarefas concluídas.](./media/getting-started-build-a-classifier/add-images04.png)

Para carregar outro conjunto de imagens, regresse à parte superior desta secção e repita os passos. Em algum momento no seu projeto, poderá ter de adicionar _negativo exemplos_ para ajudar a tornar o seu classificador mais precisas. Exemplos negativos são aqueles que não correspondam a qualquer uma das outras etiquetas. Ao carregar estas imagens, aplicam-se especial **negativo** Etiquetar às mesmas.

> [!NOTE]
> O serviço de visão personalizada oferece suporte a alguns manipulação de imagem negativo automática. Por exemplo, se estiver criando um grape versus classificador banana e enviar uma imagem de um calçado para predição, o classificador deve classificar essa imagem como próximo de 0% para grape e banana.
> 
> Por outro lado, nos casos em que as imagens negativas são apenas uma variação das imagens utilizadas na formação, é provável que o modelo classifica as imagens negativas como uma classe etiquetada devido às semelhanças excelentes. Por exemplo, se tiver uma cor de laranja versus classificador grapefruit e alimentar numa imagem de um clementine, ele pode classificar o clementine como uma cor de laranja porque muitos recursos do clementine assemelhar-se do laranjas. Se as imagens negativas dessa natureza, é recomendável criar uma ou mais etiquetas adicionais (como **outros**) e identificar as imagens negativas com esta etiqueta durante o treinamento para permitir que o modelo para melhor distinção entre essas classes .

## <a name="train-the-classifier"></a>Preparar o classificador

Para treinar o classificador, selecione o **treinar** botão. O classificador utiliza todas as imagens atuais para criar um modelo que identifica as qualidades visual de cada etiqueta.

![O botão de train no canto superior direito da barra de ferramentas de cabeçalho de página da web](./media/getting-started-build-a-classifier/train01.png)

O processo de treinamento deverá demorar apenas alguns minutos. Durante este período, informações sobre o processo de treinamento são apresentadas na **desempenho** separador.

![A janela do browser com uma caixa de diálogo de treinamento na secção principal](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Avaliar o classificador

Depois de concluída a formação, desempenho do modelo é estimado e apresentado. O serviço de visão personalizada utiliza as imagens que submetidas para formação para calcular a precisão e lembre-se, através de um processo chamado [fases k validação cruzada](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precisão e a solicitação de recolhimento são duas medidas diferentes da eficácia de um classificador:

- **Precisão** indica a fração de classificações identificadas que estavam correto. Por exemplo, se o modelo identificado 100 imagens como cães e 99 deles foram, na verdade, de cães, em seguida, a precisão seria 99%.
- **Lembre-se de** indica a fração de classificações reais que foram identificadas corretamente. Por exemplo, se houve realmente 100 imagens de maçãs e o modelo identificado 80 como maçãs, o recolhimento seria 80%.

![Os resultados de treinamento mostram a precisão geral e lembre-se e a precisão e lembre-se para cada etiqueta no classificador.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Limiar de probabilidade

Tenha em atenção a **limiar de probabilidade** controlo de deslize no painel esquerdo do **desempenho** separador. Este é o limiar para uma probabilidade prevista ser considerado correto ao computar a precisão e lembre-se.

Chamadas de previsão com um limiar de probabilidade elevada de interpretar tende a devolver resultados com precisão elevada em detrimento de recolhimento (as classificações encontradas estão corretas, mas muitos não foram encontrados); um limiar de baixa probabilidade faz exatamente o oposto (a maioria das classificações reais foram encontrada, mas existem falsos positivos nesse conjunto). Com isso em mente, deve definir o limiar de probabilidade, de acordo com as necessidades específicas do seu projeto. Mais tarde, no lado do cliente, deve usar o mesmo valor de limiar de probabilidade como um filtro ao receber resultados de predição do modelo.

## <a name="manage-training-iterations"></a>Gerir iterações de treinamento

Treinar o seu classificador de cada vez, cria uma nova _iteração_ com suas próprias métricas de desempenho atualizado. Pode ver todas as iterações no painel esquerdo dos **desempenho** separador. Quando seleciona um, tem a opção de torná-lo a _predefinido iteração_ ao clicar o **predefinir** botão na parte superior. O _predefinido iteração_ é o modelo que irá ser utilizado por predefinição, quando consulta por meio da API de predição (numa aplicação, por exemplo). Se recusar a atualizar o _predefinido iteração_, pode continuar a preparar o seu modelo sem afetar o comportamento da aplicação atual; então, quando estiver satisfeito com o modelo aprimorado, pode atualizar a predefinição.

No painel esquerdo irá também encontrar os **eliminar** botão, que pode utilizar para eliminar uma iteração se ele está obsoleto. Quando elimina uma iteração, elimine quaisquer imagens que estão associadas exclusivamente com o mesmo.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu a criar e formar um modelo de classificação de imagem usando o Web site de visão personalizada. Em seguida, obter mais informações sobre o processo interativo de melhorar o seu modelo.

[Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)

