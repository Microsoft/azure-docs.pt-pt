---
title: O que é o Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: O reconhecedor de formulário de serviços cognitivas do Azure permite que você identifique e extraia pares de chave/valor e dados de tabela de documentos de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 91ea2b68828ac54d4128a90550e9c60e065b719d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379448"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecedor de Formato?

O reconhecedor do Azure Form é um serviço cognitiva que usa a tecnologia de aprendizado de máquina para identificar e extrair texto, pares de chave/valor e dados de tabela de documentos de formulário. Ele ingere texto de formulários e gera dados estruturados que incluem as relações no arquivo original. Você obtém rapidamente resultados precisos que são adaptados ao seu conteúdo específico sem muita intervenção manual ou ampla experiência em ciência de dados. O reconhecedor de formulário é composto de modelos personalizados, do modelo de recebimento predefinido e da API de layout. Você pode chamar modelos de reconhecedor de formulário usando uma API REST para reduzir a complexidade e integrá-la ao seu fluxo de trabalho ou aplicativo.

O reconhecedor de formulário é composto pelos seguintes serviços:
* **Modelos personalizados** – Extraia pares de chave/valor e dados de tabela de formulários. Esses modelos são treinados com seus próprios dados e, portanto, são adaptados para seus formulários.
* **Modelo de recebimento predefinido** – extraia dados de recibos de vendas dos EUA usando um modelo predefinido.
* **API de layout** -Extraia estruturas de texto e tabela, juntamente com suas coordenadas de caixa delimitadora, de documentos.

<!-- add diagram -->

## <a name="custom-models"></a>Modelos personalizados

Os modelos personalizados do reconhecedor de formulário treinam seus próprios dados e você só precisa de cinco formulários de entrada de exemplo para iniciar. Um modelo treinado pode gerar dados estruturados que incluem as relações no documento de formulário original. Depois de treinar o modelo, você pode testá-lo e retreiná-lo e, eventualmente, usá-lo para extrair dados de forma confiável de mais formas de acordo com suas necessidades.

Você tem as seguintes opções ao treinar modelos personalizados: treinamento com dados rotulados e sem rótulos de dados.

### <a name="train-without-labels"></a>Treinar sem rótulos

Por padrão, o reconhecedor de formulário usa o aprendizado não supervisionado para entender o layout e as relações entre campos e entradas em seus formulários. Quando você envia seus formulários de entrada, o algoritmo agrupa os formulários por tipo, descobre quais chaves e tabelas estão presentes e associa valores a chaves e entradas a tabelas. Isso não exige a rotulagem manual de dados nem a codificação e a manutenção intensivas, e recomendamos que você experimente esse método primeiro.

### <a name="train-with-labels"></a>Treinar com rótulos

Quando você treina os dados rotulados, o modelo supervisiona o aprendizado para extrair valores de interesse, usando os formulários rotulados que você fornece. Isso resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.

O reconhecedor de formulário usa a [API de layout](#layout-api) para aprender os tamanhos e posições esperados de elementos de texto impressos e manuscritos. Em seguida, ele usa rótulos especificados pelo usuário para aprender as associações de chave/valor nos documentos. Recomendamos que você use cinco formas rotuladas manualmente do mesmo tipo para começar ao treinar um novo modelo e adicionar mais dados rotulados conforme necessário para melhorar a precisão do modelo.

## <a name="prebuilt-receipt-model"></a>Modelo de recebimento predefinido

O reconhecedor de formulário também inclui um modelo para ler os recibos de vendas em inglês do Estados Unidos&mdash;o tipo usado por restaurantes, estações de gás, varejo e assim por diante ([recebimento de exemplo](./media/contoso-receipt-small.png)). Esse modelo extrai informações importantes, como a hora e a data da transação, informações de comerciante, quantidades de impostos e totais e muito mais. Além disso, o modelo de recebimento predefinido é treinado para reconhecer e retornar todo o texto em um recibo.

## <a name="layout-api"></a>API de layout

O reconhecedor de formulário também pode extrair a estrutura de texto e tabela (os números de linha e coluna associados ao texto) usando o OCR (reconhecimento óptico de caracteres) de alta definição. 

## <a name="where-do-i-start"></a>Por onde devo começar?

**Etapa 1:** Solicitar acesso:

O reconhecedor de formulário está disponível em uma versão prévia de acesso limitado. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) . O formulário solicita informações sobre você, sua empresa e o cenário no qual você usará o reconhecedor de formulário.

**Etapa 2:** Crie um recurso de reconhecimento de formulário no portal do Azure:

Quando você tiver acesso para usar o reconhecedor de formulário, receberá um email de boas-vindas com vários links e recursos. Use o link "portal do Azure" nessa mensagem para abrir o portal do Azure e criar um recurso de reconhecimento de formulário.

**Etapa 3:** Extrair dados de seus formulários:

* Personalizar-treinar um modelo para seus formulários
  * Treinar sem rótulos
    * [Início rápido: treinar um modelo de reconhecimento de formulário e extrair dados de formulário usando a API REST com ondulação](quickstarts/curl-train-extract.md)
    * [Início rápido: treinar um modelo de reconhecimento de formulário e extrair dados de formulário usando a API REST com Python](quickstarts/python-train-extract.md)
  * Treinar com rótulos 
    * [Treinar um modelo de reconhecimento de formulário com rótulos usando a ferramenta de rotulagem de exemplo](quickstarts/label-tool.md)
    * [Treinar um modelo de reconhecimento de formulário com rótulos usando a API REST e o Python](quickstarts/python-labeled-data.md) 
* Confirmações predefinidas – extrair dados de recebimentos de vendas dos EUA
  * [Início rápido: extrair dados de recebimento usando ondulação](quickstarts/curl-receipts.md)
  * [Início rápido: extrair dados de recebimento usando o Python](quickstarts/python-receipts.md)
* Layout – extrair a estrutura de texto e tabela de formulários
  * [Início rápido: extrair dados de layout usando Python](quickstarts/python-layout.md)

Recomendamos que você use o serviço gratuito quando estiver aprendendo a tecnologia. Lembre-se de que o número de páginas livres é limitado a 500 por mês.

**Etapa 4:** Examine as APIs REST:

Você usará as seguintes APIs para treinar modelos e extrair dados estruturados de formulários.

|Nome |Descrição |
|---|---|
| **Treinar modelo personalizado**| Treine um novo modelo para analisar seus formulários usando cinco formas do mesmo tipo. Defina o parâmetro _useLabelFile_ como `true` para treinar com os dados rotulados manualmente. |
| **Analisar formulário** |Analise um único documento passado como um fluxo para extrair texto, pares de chave/valor e tabelas do formulário com seu modelo personalizado.  |
| **Analisar recibo** |Analise um único documento de recebimento para extrair informações de chave e outro texto de recebimento.|
| **Analisar layout** |Analise o layout de um formulário para extrair a estrutura de texto e tabela.|

Explore a [documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) para saber mais. Se você estiver familiarizado com uma versão anterior da API, consulte o artigo [novidades](./whats-new.md) para saber mais sobre as alterações recentes.

## <a name="input-requirements"></a>Requisitos de entrada
### <a name="custom-model"></a>Modelo personalizado

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Modelo de recebimento predefinido

Os requisitos de entrada para o modelo de recebimento são um pouco diferentes.

* O formato deve ser JPEG, PNG, BMP, PDF (texto ou verificado) ou TIFF.
* O tamanho do arquivo deve ser inferior a 20 MB.
* As dimensões de imagem devem estar entre 50 x 50 pixels e 10000 x 10000 pixels. 
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondentes aos tamanhos de papel ofício ou a3 e menores.
* Para PDF e TIFF, somente as primeiras 200 páginas são processadas (com uma assinatura de camada gratuita, somente as duas primeiras páginas são processadas).

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Esse serviço é oferecido como uma [Visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço do Azure sob os [termos do serviço online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de reconhecimento de formulário devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a [página serviços cognitivas](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na central de confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Passos seguintes

Conclua um guia de [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do reconhecedor de formulário](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
