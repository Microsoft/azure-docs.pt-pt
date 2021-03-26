---
title: Selecione um domínio para um projeto de visão personalizada - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como selecionar um domínio para o seu projeto no Serviço de Visão Personalizada.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 8aba6f13957d37f843114572f001029baf41ded6
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889353"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selecione um domínio para um projeto de Visão Personalizada

A partir do separador de definições do seu projeto Visão Personalizada, pode selecionar um domínio para o seu projeto. Escolha o domínio mais próximo do seu cenário. Se estiver a aceder à Visão Personalizada através de uma biblioteca de clientes ou da REST API, terá de especificar um ID de domínio ao criar o projeto. Pode obter uma lista de IDs de domínio com [O Domínio Get](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)ou utilizar a tabela abaixo.

## <a name="image-classification"></a>Classificação de Imagens

|Domínio|Objetivo|
|---|---|
|__Geral__| Otimizado para uma ampla gama de tarefas de classificação de imagem. Se nenhum dos outros domínios específicos for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione um dos domínios gerais. ID: `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__General [A1]__| Otimizado para uma maior precisão com tempo de inferência comparável como domínio geral. Recomendado para conjuntos de dados maiores ou cenários de utilizador mais difíceis. Este domínio requer mais tempo de treino. ID: `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__General [A2]__| Otimizado para uma melhor precisão com tempo de inferência mais rápido do que os domínios Geral[A1] e Geral. Recomendado para a maioria dos conjuntos de dados. Este domínio requer menos tempo de treino do que os domínios Geral e Geral [A1]. ID: `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Comida__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Se quiser classificar fotografias de frutas ou vegetais individuais, utilize o domínio Food. ID: `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Pontos de referência__|Otimizado para marcos reconhecíveis, naturais e artificiais. Este domínio funciona melhor quando o marco é claramente visível na fotografia. Este domínio funciona mesmo que o marco seja ligeiramente obstruído por pessoas à sua frente. ID: `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Retail__|Otimizado para imagens que são encontradas em um catálogo de compras ou site de compras. Se quiser classificar de alta precisão entre vestidos, calças e camisas, use este domínio. ID: `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Domínios compactos__| Otimizado para os constrangimentos da classificação em tempo real em dispositivos de borda.|


> [!NOTE]
> Os domínios General[A1] e General[A2] podem ser utilizados para um conjunto alargado de cenários e otimizados para uma precisão. Utilize o modelo General[A2] para uma melhor velocidade de inferência e um tempo de treino mais curto. Para conjuntos de dados maiores, pode querer utilizar o General[A1] para obter uma maior precisão do que o General[A2], embora requeira mais tempo de treino e inferência. O modelo geral requer mais tempo de inferência do que o General[A1] e o General[A2].

## <a name="object-detection"></a>Deteção de Objetos

|Domínio|Objetivo|
|---|---|
|__Geral__| Otimizado para uma ampla gama de tarefas de deteção de objetos. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Geral. ID: `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__General [A1]__| Otimizado para uma maior precisão com tempo de inferência comparável como domínio geral. Recomendado para necessidades de localização da região mais precisas, conjuntos de dados maiores ou cenários de utilizador mais difíceis. Este domínio requer mais tempo de treino, e os resultados não são determinísticos: espere uma diferença média de precisão média de +-1% com os mesmos dados de formação fornecidos. ID: `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logótipo__|Otimizado para encontrar logotipos da marca em imagens. ID: `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Produtos nas prateleiras__|Otimizado para detetar e classificar produtos nas prateleiras. ID: `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Domínios compactos__| Otimizado para os constrangimentos da deteção de objetos em tempo real em dispositivos de borda.|

## <a name="compact-domains"></a>Domínios compactos

Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente. Na API de pré-visualização pública Custom Vision 3.4, pode obter uma lista das plataformas exportáveis para domínios compactos, ligando para a API GetDomains.

O desempenho do modelo varia de acordo com o domínio selecionado. Na tabela abaixo, reportamos o tamanho do modelo e o tempo de inferência no Intel Desktop CPU e no NVidia GPU \[ 1 \] . Estes números não incluem tempo de pré-processamento e pós-processamento.

|Tarefa|Domínio|ID|Tamanho do Modelo|Tempo de inferência do CPU|Tempo de inferência da GPU|
|---|---|---|---|---|---|
|Classificação|General (compact) (Geral [compacto])|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 ms|5 ms|
|Classificação|Geral (compacto) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 ms|5 ms|
|Deteção de Objetos|General (compact) (Geral [compacto])|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 ms|5 ms|
|Deteção de Objetos|Geral (compacto) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 ms|7 ms|

>[!NOTE]
>O domínio __geral (compacto)__ para deteção de objetos requer uma lógica especial de pós-processamento. Para obter os detalhes, consulte um roteiro de exemplo no pacote zip exportado. Se precisar de um modelo sem a lógica de pós-processamento, utilize __o General (compacto) [S1]__.

>[!IMPORTANT]
>Não há garantias de que os modelos exportados dêem exatamente o mesmo resultado que a previsão da API na nuvem. Uma ligeira diferença na plataforma de execução ou na implementação do pré-processamento pode causar uma maior diferença nas saídas do modelo. Para obter os detalhes da lógica de pré-processamento, consulte [este documento](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
