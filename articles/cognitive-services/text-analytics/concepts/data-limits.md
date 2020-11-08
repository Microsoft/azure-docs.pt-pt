---
title: Limites de dados para a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Limitações de dados para a API de Análise de Texto da Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 905dde6932afb440c34bcccb563bfda98f23eb7c
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363838"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limites de dados e taxas para a API de Análise de Texto
<a name="data-limits"></a>

Use este artigo para encontrar os limites para o tamanho e as tarifas que pode enviar para a API de Text Analytics. 

## <a name="data-limits"></a>Limites de dados

> [!NOTE]
> * Se precisar de analisar documentos maiores do que o limite permite, pode dividir o texto em pedaços menores de texto antes de enviá-los para a API. 
> * Um documento é uma única cadeia de caracteres de texto.  

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um documento único | 5.120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Aplica-se também ao Text Analytics para recipiente de saúde. |
| Tamanho máximo do pedido completo | 1 MB. Aplica-se também ao Text Analytics para recipiente de saúde. |

O número máximo de documentos que pode enviar num único pedido dependerá da versão API e da funcionalidade que estiver a utilizar.

#### <a name="version-3"></a>[Versão 3](#tab/version-3)

Os seguintes limites mudaram na V3 da API. Exceder os limites abaixo gerará um código de erro HTTP 400.


| Funcionalidade | Documentos Max Por Pedido | 
|----------|-----------|
| Deteção de Idioma | 1000 |
| Análise de Sentimentos | 10 |
| Extração de Expressões-Chave | 10 |
| Reconhecimento de Entidades Nomeadas | 5 |
| Ligar à Entidade | 5 |
| Análise de texto para recipiente de saúde | 1000 |
#### <a name="version-2"></a>[Versão 2](#tab/version-2)

| Funcionalidade | Documentos Max Por Pedido | 
|----------|-----------|
| Deteção de Idioma | 1000 |
| Análise de Sentimentos | 1000 |
| Extração de Expressões-Chave | 1000 |
| Reconhecimento de Entidades Nomeadas | 1000 |
| Ligar à Entidade | 1000 |

---

## <a name="rate-limits"></a>Limites de taxa

O seu limite de tarifa variará em função do seu [nível de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Estes limites são os mesmos para ambas as versões da API. Estes limites de taxa não se aplicam ao Text Analytics para recipiente de saúde, que não tem um limite de taxa definido.

| Escalão de serviço          | Pedidos por segundo | Pedidos por minuto |
|---------------|---------------------|---------------------|
| S / Multi-serviço | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Os pedidos são medidos para cada função de Análise de Texto separadamente. Por exemplo, pode enviar o número máximo de pedidos para o seu nível de preços para cada recurso, ao mesmo tempo.  


## <a name="see-also"></a>Ver também

* [O que é a API de Análise de Texto](../overview.md)
* [Detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)