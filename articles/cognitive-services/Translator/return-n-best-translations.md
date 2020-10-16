---
title: Retorno N-Melhores Traduções - Tradutor
titleSuffix: Azure Cognitive Services
description: Devolução de traduções N-Best utilizando o Tradutor.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: 1fe370070aa97befb418d27def32725bf0a148b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83592683"
---
# <a name="how-to-return-n-best-translations"></a>Como devolver as melhores traduções de N

> [!NOTE]
> Este método é precotado. Não está disponível na V3.0 do Tradutor.

Os métodos GetTranslations() e GetTranslationsArray() do Tradutor incluem uma bandeira booleana opcional "IncludeMultipleMTAlternatives".
O método regressará às alternativas maxTranslations onde o delta é fornecido a partir da lista N-Best do motor tradutor.

A assinatura é:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Tradutor.GetTranslations (appId, texto, de, para, maxTranslations, opções); |

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Requerido** Se o cabeçalho de autorização for utilizado, deixe o campo appid vazio, especifique uma corda que contenha "Portador" + " + símbolo de acesso.|
| texto | **Requerido** Uma corda que representa o texto para traduzir. O tamanho do texto não deve exceder 10000 caracteres.|
| De | **Requerido** Uma cadeia que representa o código linguístico do texto para traduzir. |
| para | **Requerido** Uma cadeia que representa o código linguístico para traduzir o texto em. |
| maxTranslations | **Requerido** Um int que representa o número máximo de traduções a devolver. |
| opções | **Opcional** Um objeto TranslateOptions que contém os valores listados abaixo. São todas opcionais e padrão às definições mais comuns.

* Categoria: A única opção suportada e padrão é "geral".
* ContentType: A única opção suportada e padrão é "texto/planície".
* Estado: Estado do utilizador para ajudar a correlacionar pedido e resposta. O mesmo conteúdo será devolvido na resposta.
* IncluirMultipleMTAlternatives: bandeira para determinar se deve devolver mais do que uma alternativa do motor MT. O padrão é falso e inclui apenas 1 alternativa.

## <a name="ratings"></a>Classificações
As classificações são aplicadas da seguinte forma: A melhor tradução automática tem uma classificação de 5.
As alternativas de tradução geradas automaticamente (N-Best) têm uma classificação de 0, e têm um grau de correspondência de 100.

## <a name="number-of-alternatives"></a>Número de alternativas
O número de alternativas devolvidas é até ao máximo As transferências, mas podem ser menores.

## <a name="language-pairs"></a>Pares de linguagem
Esta funcionalidade não se encontra disponível para traduções entre o chinês simplificado e o chinês tradicional, ambas as direções. Está disponível para todos os outros pares de linguagem suportados pelo Microsoft Tradutor.
