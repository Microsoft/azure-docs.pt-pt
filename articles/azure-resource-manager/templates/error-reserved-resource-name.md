---
title: Erros de nome de recursos reservados
description: Descreve como resolver erros ao fornecer um nome de recurso que inclui uma palavra reservada.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75477632"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver erros de nome de recursos reservados

Este artigo descreve o erro que encontra ao implementar um recurso que inclui uma palavra reservada em seu nome.

## <a name="symptom"></a>Sintoma

Ao implementar um recurso disponível através de um ponto final público, poderá receber o seguinte erro:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Os recursos que têm um ponto final público não podem usar palavras ou marcas reservadas no nome.

As seguintes palavras são reservadas:

* ACESSO
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRETO
* DOTNET
* DINÂMICA
* EXCEL
* TROCA
* VANGUARDA
* RANHURA
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* ESCRITÓRIO
* ESCRITÓRIO365
* ONEDRIVE
* NOTA ÚNICA
* PERSPETIVA
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* ESTÚDIO VISUAL

As seguintes palavras não podem ser utilizadas como palavra inteira ou substring no nome:

* LOGIN
* MICROSOFT
* JANELAS
* XBOX

## <a name="solution"></a>Solução

Forneça um nome que não utilize uma das palavras reservadas.