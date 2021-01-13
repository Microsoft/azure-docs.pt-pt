---
title: Recibos - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a análise de recibo com a API do Reconhecimento de Formulários - utilização e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 43eae43d11a48ee6c395e4a86b8e8c1353843991
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131458"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Modelo de recibo pré-construído do Reconhecimento de Formulário

O Azure Form Recogniser pode analisar e extrair informações dos recibos de venda utilizando o seu modelo de recibo pré-construído. Combina as nossas poderosas capacidades [de Reconhecimento de Caracteres Óticos (OCR)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) com modelos de aprendizagem profunda de receção para extrair informações-chave dos recibos em inglês. A API recibo extrai informações-chave dos recibos de venda em inglês, tais como nome de comerciante, data de transação, total de transações, rubricas e muito mais. 

## <a name="understanding-receipts"></a>Compreensão de recibos 

Muitas empresas e particulares ainda dependem da extração manual de dados das suas receitas de venda, seja para relatórios de despesas de negócios, reembolsos, auditorias, fins fiscais, orçamentação, marketing ou outros fins. Muitas vezes nestes cenários, são necessárias imagens do recibo físico para efeitos de validação.  

Extrair automaticamente dados destes Recibos pode ser complicado. Os recibos podem ser amassados e difíceis de ler, imprimir ou manuscritos peças e imagens de smartphones de recibos podem ser de baixa qualidade. Além disso, os modelos de recibo e os campos podem variar muito por mercado, região e comerciante. Estes desafios tanto na extração de dados como na deteção de campo tornam o processamento de recibos um problema único.  

Utilizando o Reconhecimento de Caracteres Óticos (OCR) e o nosso modelo de receção pré-construído, a API recibo permite estes cenários de processamento de recibos e extrai dados dos recibos, por exemplo, nome do mercador, ponta, total, itens de linha e muito mais. Com esta API não há necessidade de formar um modelo, basta enviar a imagem de recibo para a API de Receção de Análise e os dados são extraídos.

![recibo de amostra](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>O que faz o serviço recibo? 

O serviço de Receção pré-construído extrai o conteúdo dos recibos de venda &mdash; do tipo de recibo que normalmente se obtém num restaurante, retalhista ou mercearia.

### <a name="fields-extracted"></a>Campos extraídos

|Nome| Tipo | Descrição | Texto | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| Tipo de Recibo | string | Tipo de recibo de venda | Itemizado |  |
| Nome mercante | string | Nome do comerciante que emite o recibo | Contoso |  |
| MerchantPhoneNumber | número de telefone | Número de telefone listado de comerciante | 987-654-3210 | +19876543210 |
| MerchantAddress | string | Endereço listado do comerciante | 123 Main St Redmond WA 98052 |  |
| TransaçãoDate | date | Data em que o recibo foi emitido | 06 de junho de 2019 | 2019-06-26  |
| Tempo de Transação | hora | Hora do recibo ser emitido | 16h49 | 16:49:00  |
| Total | número | Total de transações totais de receção | $14.34 | 14.34 |
| Subtotal | número | Subtotal de recibo, muitas vezes antes de serem aplicados impostos | $12.34 | 12,34 |
| Impostos | número | Imposto sobre a receita, muitas vezes imposto sobre as vendas ou equivalente | 2,00 $ | 2.00 |
| Dica | número | Dica incluída pelo comprador | $1,00 | 1,00 |
| Itens | matriz de objetos | Artigos de linha extraídos, com nome, quantidade, preço unitário e preço total extraído | |
| Name | string | Nome do item | Surface Pro 6 | |
| Quantidade | número | Quantidade de cada item | 1 | |
| Preço | número | Preço individual de cada unidade de artigo | $999,00 | 999.00 |
| Preço Total | número | Preço total do item da linha | $999,00 | 999.00 |

### <a name="additional-features"></a>Características adicionais

A API recibo também devolve as seguintes informações:

* Nível de confiança no campo (cada campo devolve um valor de confiança associado)
* Texto em bruto OCR (saída de texto extraída por OCR para a totalidade do recibo)
* Caixa de limites para cada valor, linha e palavra

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço de recibos Do Reconhecimento de Formulários, aceda à ferramenta online Sample UI:

> [!div class="nextstepaction"]
> [Experimente modelos pré-construídos](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Locais apoiados 

* **Recibo pré-construído v2.0** (GA) suporta recibos de venda na localidade EN-US
* **O recibo pré-construído v2.1-pré-visualização.2** (Visualização pública) adiciona suporte adicional para os seguintes locais de receção EN: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Entrada linguística 
  >
  > O Recibo Pré-construído v2.1-preview.2 tem um parâmetro de pedido opcional para especificar um local de recibo de mercados ingleses adicionais. Para receitas de vendas em inglês da Austrália (EN-AU), Canadá (EN-CA), Grã-Bretanha (EN-GB) e Índia (EN-IN), pode especificar o local para obter melhores resultados. Se não for especificado qualquer local em v2.1-pré-visualização.2, o modelo irá desagravar-se com o modelo EN-US.


## <a name="the-analyze-receipt-operation"></a>A operação De Receção de Análise

O [Recibo de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync) retira uma imagem ou PDF de um recibo como entrada e extrai os valores de interesse e texto. A chamada devolve um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o Resultado ID para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>A operação Obter Resultados de Recibos de Análise

O segundo passo é ligar para a operação [Obter Resultados de Receção.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult) Esta operação toma como entrada o Resultado ID que foi criado pela operação 'Receção de Análise'. Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. Você chama a esta operação iterativamente até que ela retorne com o valor **bem sucedido.** Utilize um intervalo de 3 a 5 segundos para evitar exceder os pedidos por segundo (RPS).

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: A operação de análise ainda não começou. |
| |  | funcionamento: A operação de análise está em curso. |
| |  | falhou: A operação de análise falhou. |
| |  | conseguiu: A operação de análise foi bem sucedida. |

Quando o campo **de status** tiver o valor **bem sucedido,** a resposta JSON incluirá os resultados de compreensão de recibo e reconhecimento de texto. O resultado da compreensão do recibo é organizado como um dicionário de valores de campo nomeados, onde cada valor contém o texto extraído, valor normalizado, caixa de delimitação, confiança e elementos de palavra correspondentes. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com texto, caixa de delimitação e informação de confiança.

![resultados da receção da amostra](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Amostra de saída JSON

Veja o seguinte exemplo de uma resposta JSON bem sucedida: O nó "readResults" contém todo o texto reconhecido. O texto é organizado por página, depois por linha, depois por palavras individuais. O nó "documentResults" contém os valores específicos do cartão de visita que o modelo descobriu. É aqui que encontrará pares de chaves/valor úteis como o primeiro nome, apelido, nome da empresa e muito mais.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```


## <a name="customer-scenarios"></a>Cenários de clientes  

Os dados extraídos com a API de Receção podem ser utilizados para executar uma variedade de tarefas. Seguem-se alguns exemplos do que os nossos clientes conseguiram com a API recibo. 

### <a name="business-expense-reporting"></a>Relatório de despesas de negócio  

Muitas vezes, arquivar despesas de negócio envolve passar o tempo a introduzir manualmente dados a partir de imagens de recibos. Com a API recibo, pode utilizar os campos extraídos para automatizar parcialmente este processo e analisar rapidamente os seus recibos.  

Como a API recibo tem uma saída JSON simples, pode utilizar os valores de campo extraídos de várias maneiras. Integre com pedidos de despesas internas para pré-povoar relatórios de despesas. Para mais informações sobre este cenário, leia sobre como a Acumatica está a utilizar a API de receção para tornar a [despesa reportando um processo menos doloroso.](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure)  

### <a name="auditing-and-accounting"></a>Auditoria e contabilidade 

A produção da API de receção também pode ser utilizada para realizar análises sobre um grande número de despesas em vários pontos do processo de relatório de despesas e reembolso. Pode processar recibos para triagem para auditoria manual ou aprovações rápidas.  

A saída Recibo também é útil para contabilidade geral para uso comercial ou pessoal. Utilize a API de Receção para transformar qualquer imagem de recibo bruto/dados PDF numa saída digital que seja accível.

### <a name="consumer-behavior"></a>Comportamento do consumidor 

As receitas contêm dados úteis que pode utilizar para analisar o comportamento do consumidor e as tendências de compras.

A API de Receção também alimenta a [funcionalidade de Processamento de Recibos de Construtora de IA.](/ai-builder/prebuilt-receipt-processing)

## <a name="next-steps"></a>Passos seguintes

- Preencha um [quickstart do Reconhecimento de Formulário](quickstarts/client-library.md) para começar a escrever uma aplicação de processamento de recibos com o Form Recogniser no idioma à sua escolha.

## <a name="see-also"></a>Veja também

* [O que é o Reconhecedor de Formato?](./overview.md)
* [REST API referenciar docs](./index.yml)
