---
title: O que é o Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: O serviço Azure Form Recogniser permite identificar e extrair pares de chaves/valor e dados de tabela dos seus documentos de formulário, bem como extrair informações importantes dos recibos de venda e cartões de visita.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: processamento automatizado de dados, processamento de documentos, entrada automatizada de dados, tratamento de formulários
ms.openlocfilehash: 8d6c2ea760b85e3170b26c63a28d4ac2b3a2ef5a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639459"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecedor de Formato?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recogniser é um serviço cognitivo que permite construir software automatizado de processamento de dados usando tecnologia de machine learning. Identifique e extraia texto, pares chave/valor, marcas de seleção, tabelas e estrutura dos seus &mdash; documentos os dados estruturados de saídas de serviço que incluem as relações no ficheiro original, caixas de delimitação, confiança e muito mais. Obtém-se rapidamente resultados precisos que são adaptados ao seu conteúdo específico sem uma intervenção manual pesada ou uma vasta experiência em ciências de dados. Utilize o Reconhecimento de Formulários para automatizar a entrada de dados nas suas aplicações e enriquecer as capacidades de pesquisa dos seus documentos.

O Form Recogniser é composto por modelos de processamento de documentos personalizados, modelos pré-construídos para faturas, recibos, IDs e cartões de visita, e o modelo de layout. Pode ligar para os modelos Form Recogniser utilizando um SDKs de API ou biblioteca de clientes para reduzir a complexidade e integrá-lo no seu fluxo de trabalho ou aplicação.

Esta documentação contém os seguintes tipos de artigos:  

* [**Os quickstarts**](quickstarts/client-library.md) estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.  
* [**Os guias de como fazer**](build-training-data-set.md) contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.  
* [**Os conceitos**](concept-layout.md) fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.  
* [**Os tutoriais**](tutorial-bulk-processing.md) são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.  

## <a name="form-recognizer-features"></a>Características do Reconhecimento de Formulários

Com o Form Recogniser, pode facilmente extrair e analisar dados de formulário com estas funcionalidades:

* **[Layout API](#layout-api)** - Extrair texto, marcas de seleção e estruturas de mesas, juntamente com as suas coordenadas de caixa de delimitação, a partir de documentos.
* **[Modelos personalizados](#custom-models)** - Extrair texto, pares de chaves/valor, marcas de seleção e dados de tabela a partir de formulários. Estes modelos são treinados com os seus próprios dados, por isso são adaptados aos seus formulários.

* **[Modelos pré-construídos](#prebuilt-models)** - Extrair dados de tipos de documentos únicos utilizando modelos pré-construídos. Atualmente disponíveis são os seguintes modelos pré-construídos

  * [Faturas](./concept-invoices.md)
  * [Recibos de venda](./concept-receipts.md)
  * [Cartões de visita](./concept-business-cards.md)
  * [Cartões de identificação (ID)](./concept-identification-cards.md)


## <a name="get-started"></a>Introdução

Utilize a ferramenta de reconhecimento de formulários de amostra para experimentar layout, modelos pré-construídos e treine um modelo personalizado para os seus documentos. Você precisará de uma subscrição Azure [**(crie uma gratuitamente**](https://azure.microsoft.com/free/cognitive-services)) e um ponto final de [**recurso do Reconhecimento de Formulários**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e chave para experimentar o serviço Deseclarador de Formulários.

### <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Experimente o Reconhecimento de Formulários](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Experimente o Reconhecimento de Formulários](https://fott.azurewebsites.net/)

---
Siga a biblioteca cliente [/ REST API quickstart](./quickstarts/client-library.md) para começar a extrair dados dos seus documentos. Recomendamos que utilize o serviço gratuito quando estiver a aprender a tecnologia. Lembre-se que o número de páginas gratuitas está limitado a 500 por mês.

Também pode usar as amostras REST (GitHub) para começar - 

* Extrair texto, marcas de seleção e estrutura de mesa a partir de documentos
  * [Dados de layout de extrato - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Treine modelos personalizados e extrair dados de formulário
  * [Comboio sem etiquetas - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Comboio com etiquetas - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Extrair dados das faturas
  * [Extrair dados da fatura - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Extrair dados dos recibos de venda
  * [Dados de recibos de extração - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Extrair dados de cartões de visita
  * [Extrair dados do cartão de visita - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Rever as APIs rest

Você usará as seguintes APIs para treinar modelos e extrair dados estruturados de formulários.

|Nome |Descrição |
|---|---|
| **Ver layout** | Analise um documento passado como um fluxo para extrair texto, marcas de seleção, tabelas e estrutura do documento |
| **Modelo personalizado de comboio**| Treine um novo modelo para analisar os seus formulários utilizando cinco formas do mesmo tipo. Desa ajuste o parâmetro _utilizaçãoLabelFile_ `true` para treinar com dados etiquetados manualmente. |
| **Analisar Formulário** |Analise um formulário passado como um fluxo para extrair texto, pares de chaves/valor e tabelas do formulário com o seu modelo personalizado.  |
| **Analisar Fatura** | Analise uma fatura para extrair informações chave, tabelas e outro texto de fatura.|
| **Analisar Recibo** | Analise um documento de receção para extrair informações chave e outro texto de receção.|
| **Analisar ID** | Analise um documento de cartão de identificação para extrair informações chave e outro texto de cartão de identificação.|
| **Analisar Cartão de Visita** | Analise um cartão de visita para extrair informações e texto chave.|

### <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)

Explore a [documentação de referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) REST para saber mais. Se você está familiarizado com uma versão anterior da API, veja o novo artigo [para](./whats-new.md) saber sobre as mudanças recentes.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Explore a [documentação de referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) REST para saber mais. Se você está familiarizado com uma versão anterior da API, veja o novo artigo [para](./whats-new.md) saber sobre as mudanças recentes.

---

## <a name="layout-api"></a>Layout API

O Reconhecimento de Formulários pode extrair texto, marcas de seleção e estrutura de mesa (os números de linha e colunas associados ao texto) utilizando reconhecimento de caracteres óticos de alta definição (OCR) e um modelo de aprendizagem profunda melhorado a partir de documentos. Consulte o guia conceptual [do Layout](./concept-layout.md) para mais informações.

:::image type="content" source="./media/tables-example.jpg" alt-text="tabelas exemplo" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modelos personalizados

Os modelos personalizados Do Reconhecimento de Formulários treinam para os seus próprios dados, e só precisa de cinco formas de entrada de amostra para começar. Um modelo de processamento de documentos treinados pode dar dados estruturados que incluem as relações no documento original. Depois de treinar o modelo, pode testá-lo e reforçá-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

Tem as seguintes opções quando treina modelos personalizados: treinar com dados rotulados e sem dados rotulados.

### <a name="train-without-labels"></a>Comboio sem rótulos

O Form Recogniser utiliza aprendizagem não supervisionada para entender o layout e as relações entre campos e entradas nas suas formas. Quando submete os formulários de entrada, o algoritmo agrupa os formulários por tipo, descobre quais as teclas e tabelas presentes e associa valores às teclas e entradas nas tabelas. A formação sem etiquetas não requer rotulagem manual de dados ou codificação e manutenção intensivas, e recomendamos que experimente este método primeiro.

Consulte [Construir um conjunto de dados](./build-training-data-set.md) de treino para obter dicas sobre como recolher os seus documentos de treino.

### <a name="train-with-labels"></a>Treine com etiquetas

Quando treina com dados rotulados, o modelo utiliza a aprendizagem supervisionada para extrair valores de interesse, utilizando os formulários etiquetados que fornece. Os dados rotulados resultam em modelos de melhor desempenho e podem produzir modelos que funcionam com formas ou formas complexas que contêm valores sem chaves.

O Form Recogniser utiliza a [API do Layout](#layout-api) para aprender os tamanhos e posições esperados de elementos de texto impressos e manuscritos e tabelas de extrato. Em seguida, utiliza etiquetas especificadas pelo utilizador para aprender as associações e tabelas chave/valor nos documentos. Recomendamos que utilize cinco formas de marca manual do mesmo tipo (mesma estrutura) para começar a treinar um novo modelo e adicionar mais dados rotulados conforme necessário para melhorar a precisão do modelo. O Form Recogniser permite a formação de um modelo para extrair pares de valores chave e tabelas utilizando capacidades de aprendizagem supervisionadas. 

[Começar com Train com rótulos](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Modelos pré-construídos

O Form Recogniser também inclui modelos pré-construídos para processamento automatizado de dados de tipos de formulários únicos.

### <a name="prebuilt-invoice-model"></a>Modelo de fatura pré-construído

O modelo de Fatura Pré-construída extrai dados de faturas em vários formatos e devolve dados estruturados. Este modelo extrai informações fundamentais como o ID da fatura, detalhes do cliente, detalhes do fornecedor, envio para, fatura para total, imposto, subtotal, itens de linha e muito mais. Além disso, o modelo de fatura pré-construído é treinado para analisar e devolver todo o texto e tabelas na fatura. Consulte o guia conceptual [das Faturas](./concept-invoices.md) para obter mais informações.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="fatura de amostra" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Modelo de recibo pré-construído

O modelo de recibo pré-construído é utilizado para a leitura de recibos de venda ingleses da Austrália, Canadá, Grã-Bretanha, Índia e Estados Unidos &mdash; do tipo usado por restaurantes, postos de gasolina, varejo, e assim por diante. Este modelo extrai informações-chave como a hora e a data da transação, informação do comerciante, montantes de impostos, rubricas, totais e muito mais. Além disso, o modelo de recibo pré-construído é treinado para analisar e devolver todo o texto num recibo. Consulte o guia conceptual [dos Recibos](./concept-receipts.md) para obter mais informações.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="recibo de amostra" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Modelo de cartões de identificação pré-construído (ID)

O modelo de cartões de identificação (ID) permite-lhe extrair informações-chave de passaportes de todo o mundo e cartas de condução dos EUA. Extrai dados como o documento ID, data de validade do nascimento, data de validade, nome, país, região, zona legível por máquinas e muito mais. Consulte o guia conceptual dos [cartões de identificação (ID)](./concept-identification-cards.md) para obter mais informações.

:::image type="content" source="./media/overview-id.jpg" alt-text="cartão de identificação da amostra" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Modelo de cartões de visita pré-construídos

O modelo cartões de visita permite-lhe extrair informações como o nome da pessoa, título de emprego, endereço, e-mail, empresa e números de telefone de cartões de visita em inglês. Consulte o guia conceptual dos [cartões](./concept-business-cards.md) de visita para obter mais informações.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="cartão de visita amostra" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Implantar nas instalações utilizando contentores Docker

[Utilize recipientes do Reconhecimento de Formulários (pré-visualização)](form-recognizer-container-howto.md) para implantar funcionalidades de API no local. Este recipiente Docker permite-lhe aproximar o serviço dos seus dados por razões de conformidade, segurança ou outras razões operacionais.

## <a name="service-availability-and-redundancy"></a>Disponibilidade de serviços e redundância

### <a name="is-form-recognizer-service-zone-resilient"></a>A zona de serviço do Reconhecimento de Formulários é resiliente?

Sim. O serviço Desemaguiso de Reconhecimento de Formulários é resistente à zona por defeito.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Como posso configurar o serviço de reconhecimento de formulários para ser resistente à zona?

Não é necessária nenhuma configuração do cliente para permitir a resiliência da zona. A resiliência da zona para os recursos do Reconhecimento de Formulários está disponível por padrão e gerida pelo próprio serviço.

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os serviços cognitivos, os desenvolvedores que usam o serviço Form Recogniser devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Experimente a nossa ferramenta online e inicie rapidamente para saber mais sobre o serviço Form Recogniser.

* [**Ferramenta de reconhecimento de formulários**](https://fott-preview.azurewebsites.net/)
* [**Biblioteca de clientes e arranque rápido da API REST**](quickstarts/client-library.md)
