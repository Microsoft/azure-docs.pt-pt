---
title: Erros de nomes de recursos de reservada do Azure | Documentos da Microsoft
description: Descreve como resolver erros ao fornecer um nome de recurso que inclui uma palavra reservada.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 922389b7c6c1bb7ad1d9b8f6ec35ccc1c5656723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683942"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver erros de nomes de recursos reservados

Este artigo descreve o erro que ocorrer durante a implantação de um recurso que inclui uma palavra reservada em seu nome.

## <a name="symptom"></a>Sintoma

Ao implementar um recurso que está disponível por meio de um ponto final público, poderá receber o erro seguinte:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Recursos que tenham um ponto final público não podem utilizar palavras reservadas ou não, o nome.

As seguintes palavras estão reservadas:

* ACESSO
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPER-V
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

As seguintes palavras não podem ser utilizadas como uma palavra inteira ou uma subcadeia no nome:

* INÍCIO DE SESSÃO
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solução

Forneça um nome que não utilize uma das palavras reservadas.