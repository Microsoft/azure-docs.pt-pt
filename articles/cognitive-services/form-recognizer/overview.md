---
title: O que é o Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: O Azure Cognitive Services Form Recogniser permite identificar e extrair pares de chaves/valor e dados de tabela a partir de documentos de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 070796cd260e56bb51115a7ef33ced8455bfb6a9
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394402"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecedor de Formato?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recogniser é um serviço cognitivo que utiliza tecnologia de machine learning para identificar e extrair texto, pares de chaves/valor e dados de tabela a partir de documentos de formulário. Ingere texto a partir de formulários e saídas de dados estruturados que incluem as relações no ficheiro original. Obtém-se rapidamente resultados precisos que são adaptados ao seu conteúdo específico sem uma intervenção manual pesada ou uma vasta experiência em ciências de dados. O Form Recogniser é composto por modelos personalizados, o modelo de receção pré-construído e a API de layout. Pode chamar modelos De Reconhecimento de Formulários utilizando uma API REST para reduzir a complexidade e integrá-la no seu fluxo de trabalho ou aplicação.

O Form Recogniser é composto pelos seguintes serviços:
* **Modelos personalizados** - Extrair pares de chaves/valor e dados de tabela a partir de formulários. Estes modelos são treinados com os seus próprios dados, por isso são adaptados aos seus formulários.
* **Modelos pré-construídos** - Extrair dados de tipos de formulários únicos utilizando modelos pré-construídos. Atualmente disponíveis estão modelos pré-construídos para recibos de venda e cartões de visita em inglês.
* **Layout API** - Extrair texto e estruturas de mesa, juntamente com as suas coordenadas de caixa de delimitação, a partir de documentos.

<!-- add diagram -->

## <a name="custom-models"></a>Modelos personalizados

Os modelos personalizados Do Reconhecimento de Formulários treinam para os seus próprios dados, e só precisa de cinco formas de entrada de amostra para começar. Um modelo treinado pode dar dados estruturados que incluem as relações no documento original. Depois de treinar o modelo, pode testá-lo e reforçá-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

Tem as seguintes opções quando treina modelos personalizados: treinar com dados rotulados e sem dados rotulados.

### <a name="train-without-labels"></a>Comboio sem rótulos

Por predefinição, o Form Recogniser utiliza aprendizagem não supervisionada para entender o layout e as relações entre campos e entradas nos seus formulários. Quando submete os formulários de entrada, o algoritmo agrupa os formulários por tipo, descobre quais as teclas e tabelas presentes e associa valores às teclas e entradas nas tabelas. Isto não requer rotulagem manual de dados ou codificação e manutenção intensivas, e recomendamos que experimente este método primeiro.

### <a name="train-with-labels"></a>Treine com etiquetas

Quando treina com dados rotulados, o modelo supervisiona a aprendizagem para extrair valores de interesse, utilizando os formulários etiquetados que fornece. Isto resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formas ou formas complexas que contêm valores sem chaves.

O Form Recogniser utiliza a [API do Layout](#layout-api) para aprender os tamanhos e posições esperados de elementos de texto impressos e manuscritos. Em seguida, utiliza etiquetas especificadas pelo utilizador para aprender as associações chave/valor nos documentos. Recomendamos que utilize cinco formas de marca manual do mesmo tipo para começar a treinar um novo modelo e adicionar mais dados rotulados conforme necessário para melhorar a precisão do modelo.

## <a name="prebuilt-models"></a>Modelos pré-construídos

O Form Recogniser também inclui modelos pré-construídos para tipos de formas únicos.

### <a name="prebuilt-receipt-model"></a>Modelo de recibo pré-construído
O modelo de recibo pré-construído é utilizado para a leitura de recibos de venda ingleses da Austrália, Canadá, Grã-Bretanha, Índia e Estados Unidos &mdash; do tipo usado por restaurantes, postos de gasolina, varejo, e assim por diante. Este modelo extrai informações-chave como a hora e a data da transação, informação do comerciante, montantes de impostos, rubricas, totais e muito mais. Além disso, o modelo de recibo pré-construído é treinado para reconhecer e devolver todo o texto num recibo. 

![recibo de amostra](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Modelo de cartões de visita pré-construídos
O modelo cartões de visita permite-lhe extrair informações como o nome da pessoa, título de emprego, endereço, e-mail, empresa e números de telefone de cartões de visita em inglês. 

![cartão de visita amostra](./media/business-card-english.jpg)

## <a name="layout-api"></a>Layout API

O Reconhecimento de Formulários também pode extrair texto e estrutura de tabela (os números de linha e colunas associados ao texto) utilizando o reconhecimento de caracteres óticos de alta definição (OCR).

## <a name="get-started"></a>Introdução

Siga um arranque rápido para começar a extrair dados dos seus formulários. Recomendamos que utilize o serviço gratuito quando estiver a aprender a tecnologia. Lembre-se que o número de páginas gratuitas está limitado a 500 por mês.

* [Início rápido da biblioteca do cliente](./quickstarts/client-library.md) (todos os idiomas, múltiplos cenários)
* Inícios rápidos da Web UI
  * [Treine com rótulos - ferramenta de rotulagem de amostra](quickstarts/label-tool.md)
* REST quickstarts
  * Treine modelos personalizados e extrair dados de formulário
    * [Comboio sem etiquetas - cURL](quickstarts/curl-train-extract.md)
    * [Comboio sem etiquetas - Python](quickstarts/python-train-extract.md)
    * [Comboio com etiquetas - Python](quickstarts/python-labeled-data.md)
  * Extrair dados dos recibos de venda
    * [Dados de recibos de extração - cURL](quickstarts/curl-receipts.md)
    * [Dados de recibos de extração - Python](quickstarts/python-receipts.md)
  * Extrair dados de cartões de visita
    * [Extrair dados do cartão de visita - Python](quickstarts/python-business-cards.md)
  * Extrair texto e estrutura de mesa a partir de formas
    * [Dados de layout de extrato - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Rever as APIs rest

Você usará as seguintes APIs para treinar modelos e extrair dados estruturados de formulários.

|Nome |Descrição |
|---|---|
| **Modelo personalizado de comboio**| Treine um novo modelo para analisar os seus formulários utilizando cinco formas do mesmo tipo. Desa ajuste o parâmetro _utilizaçãoLabelFile_ `true` para treinar com dados etiquetados manualmente. |
| **Analisar Formulário** |Analise um único documento passado como um fluxo para extrair texto, pares de chaves/valor e tabelas do formulário com o seu modelo personalizado.  |
| **Analisar Recibo** |Analise um único documento de receção para extrair informações chave e outro texto de receção.|
| **Analisar Cartão de Visita** |Analise um cartão de visita para extrair informações e texto chave.|
| **Ver layout** |Analise o layout de um formulário para extrair texto e estrutura de mesa.|

Explore a [documentação de referência da API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) REST para saber mais. Se você está familiarizado com uma versão anterior da API, veja o novo artigo [para](./whats-new.md) saber sobre as mudanças recentes.

## <a name="input-requirements"></a>Requisitos de entrada
### <a name="custom-model"></a>Modelo personalizado

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>Pré-construído

Os requisitos de entrada para o modelo de receção são ligeiramente diferentes.

* O formato deve ser JPEG, PNG, PDF (texto ou digitalizado) ou TIFF.
* O tamanho do ficheiro deve ser inferior a 20 MB.
* As dimensões da imagem devem estar entre 50 x 50 pixels e 10000 x 10000 pixels.
* As dimensões PDF devem ser no máximo 17 x 17 polegadas, correspondentes aos tamanhos legais ou a3 e menores.
* No caso de PDF e TIFF, apenas as primeiras 200 páginas são processadas (com uma subscrição de nível livre, apenas as duas primeiras páginas são processadas).

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os serviços cognitivos, os desenvolvedores que usam o serviço Form Recogniser devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Próximos passos

Complete um [quickstart](quickstarts/curl-train-extract.md) para começar com as [APIs do Reconhecimento de Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).