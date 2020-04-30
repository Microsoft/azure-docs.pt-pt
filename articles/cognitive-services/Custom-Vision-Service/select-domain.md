---
title: Selecione um domínio para um projeto de Visão Personalizada - Visão computorizada
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
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82127779"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selecione um domínio para um projeto de Visão Personalizada

A partir da lâmina de definições para o seu projeto Custom Vision, pode selecionar um domínio para o seu projeto. Escolha o domínio mais próximo do seu cenário.

## <a name="image-classification"></a>Classificação de Imagens

|Domain|Objetivo|
|---|---|
|__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagem. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Comida__|Otimizado para fotografias de pratos como você os veria em um menu de restaurante. Se quiser classificar fotografias de frutas ou legumes individuais, utilize o domínio alimentar.|
|__Pontos de referência__|Otimizado para marcos reconhecíveis, tanto naturais como artificiais. Este domínio funciona melhor quando o marco é claramente visível na fotografia. Este domínio funciona mesmo que o marco seja ligeiramente obstruído por pessoas à sua frente.|
|__Retalho__|Otimizado para imagens que são encontradas num catálogo de compras ou site de compras. Se quiser classificação de alta precisão entre vestidos, calças e camisas, use este domínio.|
|__Domínios compactos__| Otimizado para os constrangimentos da classificação em tempo real em dispositivos de borda.|

## <a name="object-detection"></a>Deteção de Objetos

|Domain|Objetivo|
|---|---|
|__General__| Otimizado para uma ampla gama de tarefas de deteção de objetos. Se nenhum dos outros domínios for apropriado, ou se não tiver a certeza de qual domínio escolher, selecione o domínio Genérico.|
|__Logótipo__|Otimizado para encontrar logotipos da marca em imagens.|
|__Produtos nas prateleiras__|Otimizado para detetar e classificar produtos nas prateleiras.|
|__Domínios compactos__| Otimizado para os constrangimentos da deteção de objetos em tempo real em dispositivos de borda.|

## <a name="compact-domains"></a>Domínios compactos

Os modelos gerados por domínios compactos podem ser exportados para funcionar localmente. O desempenho do modelo varia por domínio selecionado. Na tabela abaixo, reportamos o tamanho do modelo e o tempo de \[\]inferência no CpU intel desktop e nVidia GPU 1 . 

> [!NOTE]
> Estes números não incluem o pré-processamento e o tempo de pós-processamento.

|Tarefa|Domain|Tamanho do modelo|Tempo de inferência do CPU|Tempo de inferência da GPU|
|---|---|---|---|---|
|Classificação|General (compact) (Geral [compacto])|5 MB|13 ms|5 ms|
|Deteção de Objetos|General (compact) (Geral [compacto])|45 MB|35 ms|5 ms|
|Deteção de Objetos|Geral (compacto) [S1]|14 MB|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Quando um domínio compacto é selecionado, é fornecida uma opção extra "Capacidades de Exportação" que permite distinguir entre "Plataformas Básicas" e "Vision AI Dev Kit".

No âmbito _das Capacidades de Exportação,_ as duas opções são:

- Plataformas básicas (Tensorflow, CoreML, ONNX, etc.)
- Vision AI Dev Kit.

Quando o _Vision AI Dev Kit_ é selecionado o _Genérico,_ _Marcos_e _Retalho,_ mas não _os_ domínios compactos alimentares estão disponíveis para classificação de imagem enquanto tanto o _General (compacto)_ como o _General [S1] estão_ disponíveis para deteção de objetos.

>[!NOTE]
>O domínio __geral (compacto)__ para deteção de objetos requer uma lógica especial de pós-processamento. Para os detalhes, consulte um exemplo no pacote zip exportado. Se precisar de um modelo sem a lógica de pós-processamento, utilize __o General (compacto) [S1]__.

>[!IMPORTANT]
>Não há garantias de que os modelos exportados dêem exatamente o mesmo resultado que a previsão da API na nuvem. Uma ligeira diferença na plataforma de execução ou na implementação do pré-processamento pode causar uma maior diferença nas saídas do modelo. Para obter o detalhe da lógica de pré-processamento, consulte [este documento](quickstarts/image-classification.md).

\[1\] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
