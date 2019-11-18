---
title: Erros de nome de recurso reservado
description: Descreve como resolver erros ao fornecer um nome de recurso que inclui uma palavra reservada.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150462"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver erros de nome de recurso reservado

Este artigo descreve o erro que você encontra ao implantar um recurso que inclui uma palavra reservada em seu nome.

## <a name="symptom"></a>Sintoma

Ao implantar um recurso que está disponível por meio de um ponto de extremidade público, você pode receber o seguinte erro:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Os recursos que têm um ponto de extremidade público não podem usar palavras reservadas ou marcas no nome.

As palavras a seguir são reservadas:

* ÀS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* Tana
* DIRECTX
* DOTNET
* PINCEL
* EXCEL
* TRANSFERÊNCIA
* VANGUARDA
* GROOVE
* HOLOLENS
* V
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* FOR
* ONENOTE
* OUTLOOK
* Point
* SHAREPOINT
* DO
* VISIO
* VISUALSTUDIO

As palavras a seguir não podem ser usadas como uma palavra inteira ou uma subcadeia de caracteres no nome:

* ENTRAR
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solução

Forneça um nome que não use uma das palavras reservadas.