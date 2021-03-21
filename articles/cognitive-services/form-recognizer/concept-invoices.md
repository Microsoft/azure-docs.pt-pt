---
title: Faturas - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a análise de fatura com a API do Reconhecimento de Formulários - utilização e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 46cf34bd40832488985008a645f1da25eb87b9d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467396"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Modelo de fatura pré-construído do Reconhecimento de Formulário

O Azure Form Recogniser pode analisar e extrair informações das faturas de venda utilizando os seus modelos de fatura pré-construídos. A Fatura API permite que os clientes levem faturas em diversos formatos e devolvam dados estruturados para automatizar o processamento da fatura. Combina as nossas poderosas capacidades [de Reconhecimento de Caracteres Óticos (OCR)](../computer-vision/concept-recognizing-text.md) com a compreensão da fatura de modelos de aprendizagem profunda para extrair informações-chave das faturas em inglês. Extrai o texto, tabelas e informações como cliente, fornecedor, identificação de fatura, data de vencimento da fatura, total, valor da fatura devido, valor do imposto, envio para, conta para, itens de linha e muito mais. A API de fatura pré-construída está disponível publicamente na pré-visualização do Reconhecimento de Formulários v2.1.

## <a name="what-does-the-invoice-service-do"></a>O que faz o serviço de Fatura?

A API de fatura extrai campos-chave e itens de linha a partir de faturas e devolve-os numa resposta JSON estruturada organizada. As faturas podem ser de uma variedade de formatos e qualidade, incluindo imagens capturadas por telefone, documentos digitalizados e PDFs digitais. A fatura API extrairá a produção estruturada de todas estas faturas. 

![Exemplo de fatura de Contoso](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>Experimente

Para experimentar o Serviço de Faturas Do Reconhecimento de Formulários, aceda à ferramenta online Sample UI:

> [!div class="nextstepaction"]
> [Experimente modelos pré-construídos](https://fott-preview.azurewebsites.net/)

Você precisará de uma subscrição Azure[(crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e um ponto final de [recurso do Reconhecimento de Formulário](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e chave para experimentar o serviço de Fatura Do Reconhecimento de Formulários. 

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Exemplo de fatura analisada" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>A operação de Análise de Fatura

A operação [Analisar fatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291) retira uma imagem ou PDF de uma fatura à medida que a entrada e extrai os valores de interesse. A chamada devolve um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o Resultado ID para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>A operação Get Analyze Fatura Resultado

O segundo passo é chamar a operação [Get Analyze Resultados da Fatura.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83) Esta operação toma como entrada o Resultado ID que foi criado pela operação Análise Fatura. Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. Você chama a esta operação iterativamente até que ela retorne com o valor **bem sucedido.** Utilize um intervalo de 3 a 5 segundos para evitar exceder os pedidos por segundo (RPS).

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: A operação de análise ainda não começou.<br /><br />funcionamento: A operação de análise está em curso.<br /><br />falhou: A operação de análise falhou.<br /><br />conseguiu: A operação de análise foi bem sucedida.|

Quando o campo **de status** tiver o valor **bem sucedido,** a resposta JSON incluirá os resultados de compreensão da fatura, tabelas extraídas e resultados de reconhecimento de texto opcionais, se solicitado. O resultado da compreensão da fatura é organizado como um dicionário de valores de campo nomeados, onde cada valor contém o texto extraído, valor normalizado, caixa de delimitação, confiança e elementos de palavra correspondentes. Inclui também os itens de linha extraídos onde cada item de linha contém a quantidade, descrição, unitPrice, quantidade, etc. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com texto, caixa de delimitação e informação de confiança.

### <a name="sample-json-output"></a>Amostra de saída JSON

A resposta à operação Resultado da Fatura Get Analyze será a representação estruturada da fatura com toda a informação extraída. Consulte aqui um [ficheiro de fatura de amostra](media/sample-invoice.jpg) e a sua saída de fatura de [amostra](media/invoice-example-new.jpg)de saída estruturada .

A saída JSON tem 3 partes: 
* `"readResults"` O nó contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha, depois por palavras individuais. 
* `"pageResults"` O nó contém as tabelas e células extraídas com as suas caixas de delimitação, confiança e uma referência às linhas e palavras em "readResults".
* `"documentResults"` O nó contém os valores específicos da fatura e os itens de linha que o modelo descobriu. É aqui que você encontrará todos os campos a partir da fatura, como iD de fatura, envio para, fatura para, cliente, total, itens de linha e muito mais.

## <a name="example-output"></a>Saída de exemplo

O serviço de Fatura extrairá o texto, as tabelas e 26 campos de fatura. Seguem-se os campos extraídos de uma fatura na resposta de saída JSON (a saída abaixo utiliza esta [fatura de amostra).](media/sample-invoice.jpg)

|Nome| Tipo | Description | Texto | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| CustomerName | string | Cliente a ser faturado | Microsoft Corp |  |
| CustomerId | string | ID de referência para o cliente | CID-12345 |  |
| Pedido de Compra | string | Um número de referência de pedido de compra | PO-3333 | | 
| InvoiceId | string | ID para esta fatura específica (muitas vezes "Número de fatura") | INV-100 | | 
| DataDaFatura | data | Data em que a fatura foi emitida | 11/15/2019 | 2019-11-15 | 
| DueDate | data | O pagamento da data desta fatura é devido | 12/15/2019 | 2019-12-15 |
| Nome do fornecedor | string | Fornecedor que criou esta fatura | CONTOSO LTD. | |
| Pagamento de Fornecedores | string | Endereço de correio para o Fornecedor | 123 456th St New York, NY, 10001 | |
| FornecedorAddressRecipient | string | Nome associado ao VendorAddress | Sede do Contoso | |
| CustomerAddress | string | Endereço de correio para o Cliente | 123 Outras Ruas, Redmond WA, 98052 | |
| CustomerAddressRecipient | string | Nome associado ao CustomerAddress | Microsoft Corp | |
| BillingAddress | string | Endereço de faturação explícito para o cliente | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | string | Nome associado ao BillingAddress | Serviços Microsoft | |
| Envio De Endereço | string | Endereço de envio explícito para o cliente | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | string | Nome associado ao ShippingAddress | Entrega da Microsoft | |
| Subtotal | número | Campo subtotal identificado nesta fatura | $100,00 | 100 | 
| TotalTax | número | Total de impostos identificados nesta fatura | $10,00 | 10 |
| FaturaTotal | número | Total de novos encargos associados a esta fatura | $110,00 | 110 |
| Montantes Vencidos |  número | Montante total devido ao fornecedor | $610,00 | 610 |
| ServiçoAddress | string | Endereço de serviço explícito ou endereço de propriedade para o cliente | 123 Service St, Redmond WA, 98052 | |
| ServiçoAddressRecipient | string | Nome associado ao ServiceAddress | Serviços Microsoft | |
| RemessaAddress | string | Remessa explícita ou endereço de pagamento para o cliente | 123 Remit St New York, NY, 10001 |  |
| RemessaAddressRecipient | string | Nome associado ao RemittanceAddress | Contoso Billing |  |
| ServiceStartDate | data | Primeira data para o período de serviço (por exemplo, um período de serviço de conta de utilidade) | 14/10/2019 | 2019-10-14 |
| ServiceEndDate | data | Data de fim do período de serviço (por exemplo, um período de serviço de conta de utilidade) | 11/14/2019 | 2019-11-14 |
| Anterior Desequilíbrio | número | Saldo explícito anteriormente não pago | $500,00 | 500 |

Seguem-se os itens de linha extraídos de uma fatura na resposta de saída JSON (a saída abaixo utiliza esta [fatura de amostra)](./media/sample-invoice.jpg)  

|Nome| Tipo | Description | Texto (#1 de rubrica) | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| Itens | string | Linha de texto de corda completa do item da linha | 3/4/2021 Serviços de Consultoria A123 2 horas $30,00 10% $60,00 | |
| Montante | número | A quantidade do item da linha | $60,00 | 100 |
| Description | cadeia (de carateres) | A descrição do texto para o item da linha de fatura | Serviço de consultoria | Serviço de consultoria |
| Quantidade | número | A quantidade para este item da linha de fatura | 2 | 2 |
| UnitPrice | número | O preço líquido ou bruto (dependendo da fixação da fatura bruta da fatura) de uma unidade deste item | $30,00 | 30 |
| Código de Produto | string| Código do produto, número de produto ou SKU associado ao item de linha específico | A123 | |
| Unidade | string| A unidade do item de linha, por exemplo, kg, lb etc. | horas | |
| Data | data| Data correspondente a cada item de linha. Muitas vezes esta é uma data em que o item da linha foi enviado | 3/4/2021| 2021-03-04 |
| Impostos | número | Imposto associado a cada item de linha. Os valores possíveis incluem o montante do imposto, o imposto %, e o imposto Y/N | 10% | |


## <a name="next-steps"></a>Passos seguintes

- Experimente as suas próprias faturas e amostras na [UI da amostra do reconhecimento de formulários.](https://fott-preview.azurewebsites.net/)
- Preencha um [quickstart do Form Recogniser](quickstarts/client-library.md) para começar a escrever uma aplicação de processamento de fatura com o Form Recogniser no idioma de desenvolvimento à sua escolha.

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](./overview.md)
* [REST API referenciar docs](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
