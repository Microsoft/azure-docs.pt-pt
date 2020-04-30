---
title: Limites de dados para a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Limitações de dados para a API de Análise de Texto dos Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 4af2d060c11b804c5fa09bfdabbcb9753f7d5885
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204415"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limites de dados e tarifas para a API de Análise de Texto
<a name="data-limits"></a>

Utilize este artigo para encontrar os limites para o tamanho e as tarifas que pode enviar dados para a API de Análise de Texto. 

## <a name="data-limits"></a>Limites de dados

> [!NOTE]
> * Se precisar de analisar documentos maiores do que o limite permite, pode quebrar o texto em pedaços menores de texto antes de enviá-los para a API. 
> * Um documento é uma única cadeia de caracteres de texto.  

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um documento único | 5.120 caracteres medidos por [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamanho máximo do pedido completo | 1 MB |

O número máximo de documentos que pode enviar num único pedido dependerá da versão API e da funcionalidade que está a utilizar.

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

> [!NOTE]
> Se o seu pedido v3 API exceder estes limites, mas estiver dentro dos limites v2, um aviso será devolvido na resposta da API. A partir de 7/15/2020, será devolvido um código de erro de 400. 

Os seguintes limites mudaram na v3 da API. Exceder os limites abaixo gerará um aviso na resposta da API.


| Funcionalidade | Documentos Max por Pedido | 
|----------|-----------|
| Deteção de Idioma | 1000 |
| Análise de Sentimentos | 10 |
| Extração de Expressões-Chave | 10 |
| Reconhecimento de Entidades Nomeadas | 5 |
| Ligar à Entidade | 5 |

#### <a name="version-2"></a>[Versão 2](#tab/version-2)

| Funcionalidade | Documentos Max por Pedido | 
|----------|-----------|
| Deteção de Idioma | 1000 |
| Análise de Sentimentos | 1000 |
| Extração de Expressões-Chave | 1000 |
| Reconhecimento de Entidades Nomeadas | 1000 |
| Ligar à Entidade | 1000 |

---

## <a name="rate-limits"></a>Limites de taxas

O seu limite de tarifas variará com o seu nível de [preços.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Estes limites são os mesmos para ambas as versões da API.

| Escalão          | Pedidos por segundo | Pedidos por minuto |
|---------------|---------------------|---------------------|
| S / Multi-serviço | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Os pedidos são medidos para cada função de Análise de Texto separadamente. Por exemplo, pode enviar o número máximo de pedidos para o seu nível de preços para cada funcionalidade, ao mesmo tempo.  


## <a name="see-also"></a>Consulte também

* [Qual é a API de Análise de Texto](../overview.md)
* [Detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
