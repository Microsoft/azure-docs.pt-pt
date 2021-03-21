---
title: Erros de nome de recursos reservados
description: Descreve como resolver erros ao fornecer um nome de recurso que inclua uma palavra reservada.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75477632"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver erros de nome de recursos reservados

Este artigo descreve o erro que encontra ao implementar um recurso que inclui uma palavra reservada em seu nome.

## <a name="symptom"></a>Sintoma

Ao utilizar um recurso disponível através de um ponto final público, poderá receber o seguinte erro:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Os recursos que têm um ponto final público não podem usar palavras ou marcas reservadas no nome.

As seguintes palavras são reservadas:

* ACESSO
* RIO AZURE
* RIO BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DINÂMICA
* EXCEL
* TROCA
* VANGUARDA
* GROOVE
* HOLOLENS
* HIPERV
* KINECT
* RIO LYNC
* MSDN
* O365
* ESCRITÓRIO
* ESCRITÓRIO365
* ONEDRIVE
* UMA NOTA
* PERSPETIVAS
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

As seguintes palavras não podem ser utilizadas como uma palavra inteira ou um sub-cordão no nome:

* LOGIN
* MICROSOFT
* JANELAS
* XBOX

## <a name="solution"></a>Solução

Forneça um nome que não utilize uma das palavras reservadas.