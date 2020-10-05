---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89411761"
---
## <a name="get-the-training-and-prediction-keys"></a>Obter as chaves de preparação e de predição

O projeto necessita de um conjunto válido de chaves de subscrição para interagir com o serviço. Pode encontrar os itens no site da [Visão Personalizada.](https://customvision.ai) Faça sessão com a conta associada à conta Azure que utilizou para criar os seus recursos de Visão Personalizada. Na página inicial (a página com a opção de adicionar um novo projeto), selecione o ícone de __engrenagem__ no canto superior direito. Encontre os seus recursos de treino e previsão na lista e expanda-os. Aqui pode encontrar a sua chave de treino, a chave de previsão e os valores de identificação de recursos de previsão. Guarde estes valores para um local temporário.

> [!NOTE]
> Se estiver a utilizar uma chave tudo-em-um dos Serviços Cognitivos para aceder à Visão Personalizada, então só verá uma chave no ecrã das definições. Neste caso, usará a mesma chave para operações de treino e previsão.

![Imagem da IU de chaves](../media/csharp-tutorial/training-prediction-keys.png)

Ou, pode obter estas chaves e ID a partir do [portal Azure,](https://www.portal.azure.com) visualizando os seus recursos de Treino e Previsão de Visão Personalizada e navegando no separador __Chaves.__ Lá encontrará a chave de treino e a chave de previsão. Navegue no separador __Propriedades__ do seu recurso Prediction para obter o seu ID de recurso de previsão.

