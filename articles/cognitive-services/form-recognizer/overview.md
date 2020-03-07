---
title: O que é o Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: O Reconhecimento de Formulários de Serviços Cognitivos Azure permite identificar e extrair pares chave/valor e dados de tabela a partir de documentos de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 0d78f3cc4f2b12b2d9f45878a0c1b91263112689
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385629"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecedor de Formato?

O Azure Form Recogniser é um serviço cognitivo que utiliza tecnologia de machine learning para identificar e extrair texto, pares chave/valor e dados de tabela saem de documentos de formulário. Ingere texto a partir de formulários e saídas de dados estruturados que incluem as relações no ficheiro original. Obtém rapidamente resultados precisos que são adaptados ao seu conteúdo específico sem uma intervenção manual pesada ou uma vasta experiência em ciência de dados. O Reconhecimento de Formulários é composto por modelos personalizados, o modelo de recibo pré-construído e a API de layout. Pode chamar os modelos 'Reconhecimento de Formulários', utilizando uma API REST para reduzir a complexidade e integrá-la no seu fluxo de trabalho ou aplicação.

O Reconhecimento de Formulários é composto dos seguintes serviços:
* **Modelos personalizados** - Extrair pares de chaves/valor e dados de tabela saem de formulários. Estes modelos são treinados com os seus próprios dados, por isso são adaptados aos seus formulários.
* Modelo de **recibo pré-construído** - Extrair dados dos recibos de vendas dos EUA utilizando um modelo pré-construído.
* **Layout API** - Extrair texto e estruturas de mesa, juntamente com as suas coordenadas de caixa de limitador, a partir de documentos.

<!-- add diagram -->

## <a name="custom-models"></a>Modelos personalizados

Os modelos personalizados Do Reconhecimento de Formulário treinam para os seus próprios dados, e só precisa de cinco formulários de entrada de amostra para iniciar. Um modelo treinado pode formar dados estruturados que incluem as relações no documento de formulário original. Depois de treinar o modelo, pode testar e retreiná-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

Tem as seguintes opções quando treina modelos personalizados: treino com dados rotulados e sem dados rotulados.

### <a name="train-without-labels"></a>Treme sem rótulos

Por padrão, o Reconhecimento de Formulários utiliza a aprendizagem não supervisionada para compreender o layout e as relações entre campos e entradas nas suas formas. Quando submete os formulários de entrada, o algoritmo agrupa os formulários por tipo, descobre quais as teclas e tabelas presentes e associa valores a teclas e entradas nas tabelas. Isto não requer rotulagem manual de dados ou codificação e manutenção intensivas, e recomendamos que tente este método primeiro.

### <a name="train-with-labels"></a>Trem com rótulos

Quando treina com dados rotulados, o modelo supervisiona a aprendizagem para extrair valores de interesse, utilizando os formulários rotulados que fornece. Isto resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formas ou formas complexas que contenham valores sem teclas.

O Reconhecimento de Formulários utiliza a [API de layout](#layout-api) para aprender os tamanhos e posições esperadas dos elementos de texto impressos e manuscritos. Em seguida, utiliza etiquetas especificadas pelo utilizador para aprender as associações chave/valor nos documentos. Recomendamos que utilize cinco formas manualmente rotuladas do mesmo tipo para começar quando treina um novo modelo e adicione mais dados rotulados conforme necessário para melhorar a precisão do modelo.

## <a name="prebuilt-receipt-model"></a>Modelo de recibo pré-construído

O Form Recogniser também inclui um modelo de leitura dos recibos de vendas ingleses dos Estados Unidos&mdash;o tipo utilizado por restaurantes, postos de gasolina, retalho, e assim por diante (recibo de[amostra).](./media/contoso-receipt-small.png) Este modelo extrai informações-chave como a hora e a data da transação, informação dos comerciantes, montantes de impostos e totais e muito mais. Além disso, o modelo de recibo pré-construído é treinado para reconhecer e devolver todo o texto num recibo.

## <a name="layout-api"></a>Layout API

O Reconhecimento de Formulários também pode extrair texto e estrutura de tabela (os números de linha e coluna associados ao texto) utilizando o reconhecimento ótico de caracteres de alta definição (OCR). 

## <a name="get-started"></a>Introdução

Siga um início rápido para começar a extrair dados dos seus formulários. Recomendamos que utilize o serviço gratuito quando estiver a aprender a tecnologia. Lembre-se que o número de páginas gratuitas é limitado a 500 por mês.

* Custom - treine um modelo para as suas formas
  * Treme sem rótulos
    * [Quickstart: Treine um modelo de reconhecimento de formulário e extrai dados de formulário utilizando a API REST com cURL](quickstarts/curl-train-extract.md)
    * [Quickstart: Treine um modelo de reconhecimento de formulário e extrai dados de formulário utilizando a API REST com Python](quickstarts/python-train-extract.md)
  * Trem com rótulos 
    * [Treine um modelo de reconhecimento de formulário com etiquetas utilizando a ferramenta de rotulagem da amostra](quickstarts/label-tool.md)
    * [Treine um modelo de reconhecimento de formulário com etiquetas usando REST API e Python](quickstarts/python-labeled-data.md) 
* Recibos pré-construídos - extrair dados dos recibos de vendas dos EUA
  * [Quickstart: Extrair dados de recibo utilizando cURL](quickstarts/curl-receipts.md)
  * [Quickstart: Extrair dados de recibo usando Python](quickstarts/python-receipts.md)
* Layout - extrair texto e estrutura de mesa a partir de formulários
  * [Quickstart: Extrair dados de layout usando Python](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>Reveja as APIs rest

Utilizará as seguintes APIs para treinar modelos e extrair dados estruturados a partir de formulários.

|Nome |Descrição |
|---|---|
| **Modelo personalizado de comboio**| Treine um novo modelo para analisar os seus formulários utilizando cinco formas do mesmo tipo. Defina o parâmetro _useLabelFile_ para `true` para treinar com dados manualmente rotulados. |
| **Analisar formulário** |Analise um único documento transmitido como um fluxo para extrair texto, pares chave/valor e tabelas do formulário com o seu modelo personalizado.  |
| **Analisar recibo** |Analise um único documento de recibo para extrair informações-chave e outro texto de recibo.|
| **Analisar layout** |Analise o layout de um formulário para extrair texto e estrutura de mesa.|

Explore a documentação de referência rest [API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) para saber mais. Se está familiarizado com uma versão anterior da API, consulte o novo artigo do [What's](./whats-new.md) Para saber sobre as recentes mudanças.

## <a name="input-requirements"></a>Requisitos de entrada
### <a name="custom-model"></a>Modelo personalizado

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Modelo de recibo pré-construído

Os requisitos de entrada para o modelo de recibo são ligeiramente diferentes.

* O formato deve ser JPEG, PNG, BMP, PDF (texto ou digitalizado) ou TIFF.
* O tamanho do ficheiro deve ser inferior a 20 MB.
* As dimensões de imagem devem estar entre 50 x 50 pixels e 10000 x 10000 pixels. 
* As dimensões pdf devem ser no máximo 17 x 17 polegadas, correspondentes aos tamanhos legais ou a3 e menores.
* Para PDF e TIFF, apenas as primeiras 200 páginas são processadas (com uma subscrição de nível livre, apenas as duas primeiras páginas são processadas).

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Este serviço é oferecido como [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço Azure nos Termos de [Serviço Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Tal como acontece com todos os serviços cognitivos, os desenvolvedores que utilizam o serviço 'Reconhecimento de Formulários' devem estar cientes das políticas da Microsoft nos dados dos clientes. Consulte a página de [Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Complete um [início rápido](quickstarts/curl-train-extract.md) para começar com as APIs do Reconhecimento de [Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
