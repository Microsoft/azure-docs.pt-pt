---
title: Modelos personalizados - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com modelos personalizados da API do Form Recogniser API- uso e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 74ced2ecadb5ccfe5cdb7966550e469ae4f8ab31
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612267"
---
# <a name="form-recognizer-custom-models"></a>Modelos personalizados do Reconhecimento de Formulários

O Form Recogniser utiliza tecnologia avançada de aprendizagem automática para analisar e extrair dados dos seus formulários e documentos. Um modelo de Reconhecimento de Formulários é uma representação de dados extraídos que é usado como referência para analisar o seu conteúdo específico. Existem dois tipos de modelos de reconhecimento de formulários:

* **Modelos personalizados.** Os modelos personalizados Do Reconhecimento de Formulários representam dados extraídos de _formulários específicos_ para o seu negócio. Os modelos personalizados devem ser treinados para analisar os seus dados de formulário distintos.

* **Modelos pré-construídos.** ATualmente, o Form Recogniser suporta modelos pré-construídos para _recibos, cartões de visita, cartões de identificação_ e _faturas._ Os modelos pré-construídos detetam e extraem informações a partir de imagens de documentos e devolvem os dados extraídos numa saída JSON estruturada.

## <a name="what-does-a-custom-model-do"></a>O que faz um modelo personalizado?

Com o Form Recogniser, pode treinar um modelo que extrairá informações de formulários relevantes para o seu caso de utilização. Só precisa de cinco exemplos do mesmo tipo de forma para começar. O seu modelo personalizado pode ser treinado com ou sem conjuntos de dados rotulados.

## <a name="create-use-and-manage-your-custom-model"></a>Criar, usar e gerir o seu modelo personalizado

A um nível elevado, os passos para construir, treinar e usar o seu modelo personalizado são os seguintes:

> [!div class="nextstepaction"]
> [1. Monte o seu conjunto de dados de formação](build-training-data-set.md#custom-model-input-requirements)

A construção de um modelo personalizado começa por estabelecer o seu conjunto de dados de treino. Você precisará de um mínimo de cinco formas completas do mesmo tipo para o seu conjunto de dados da amostra. Podem ser de diferentes tipos de ficheiros e conter texto e caligrafia. Os seus formulários devem ser do mesmo tipo de documento e seguir os [requisitos](build-training-data-set.md#custom-model-input-requirements) de entrada para o Reconhecimento de Formulários.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2. Faça upload do conjunto de dados de treino](build-training-data-set.md#upload-your-training-data)

Terá de enviar os seus dados de treino para um recipiente de armazenamento de bolhas Azure. Se não souber como criar uma conta de armazenamento Azure com um recipiente, *consulte* [o quickstart de armazenamento Azure para o portal Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Utilize o nível de preços gratuitos (F0) para experimentar o serviço e atualize mais tarde para um nível pago para produção.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3. Treine o seu modelo personalizado](quickstarts/client-library.md#train-a-custom-model)

Pode treinar o seu modelo [sem](quickstarts/client-library.md#train-a-model-without-labels) ou [com](quickstarts/client-library.md#train-a-model-with-labels) conjuntos de dados rotulados. Os conjuntos de dados não rotulados baseiam-se exclusivamente na API de Layout para detetar e identificar informações-chave sem a entrada humana adicionada. Os conjuntos de dados rotulados também dependem da API do Layout, mas a entrada humana suplementar está incluída, tal como as suas etiquetas específicas e locais de campo. Para utilizar dados rotulados e não rotulados, comece com pelo menos cinco formas completas do mesmo tipo para os dados de formação rotulados e, em seguida, adicione dados não rotulados ao conjunto de dados necessário.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4. Analisar documentos com o seu modelo personalizado](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Teste o seu modelo recém-treinado usando um formulário que não fazia parte do conjunto de dados de treino. Pode continuar a fazer mais treinos para melhorar o desempenho do seu modelo personalizado.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5. Gerir os seus modelos personalizados](quickstarts/client-library.md#manage-custom-models)

A qualquer momento, pode ver uma lista de todos os modelos personalizados sob a sua subscrição, recuperar informações sobre um modelo personalizado específico ou eliminar um modelo personalizado da sua conta.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a biblioteca do cliente Do Formulário Recogniser explorando a nossa documentação de referência da API.
> [!div class="nextstepaction"]
> [Referência API do Reconhecimento de Formulários](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
>
