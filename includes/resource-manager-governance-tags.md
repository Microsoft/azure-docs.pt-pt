---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c426df2293cfb2d8ba4dc02e8fc5519c3d822168
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66128761"
---
Aplicar etiquetas aos recursos do Azure, fornecendo os metadados para as organizar numa taxonomia. Cada etiqueta é constituída por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Depois de aplicar etiquetas, pode obter todos os recursos na sua subscrição com este nome e este valor da etiqueta. As etiquetas permitem-lhe obter recursos relacionados de diferentes grupos de recursos. Esta abordagem é útil quando precisa de organizar os recursos de gestão ou de faturação.

A taxonomia deve considerar uma estratégia para além de uma estratégia de identificação de automática para reduzir a carga sobre os utilizadores e aumentar a precisão de marcação de metadados de self-service.

As seguintes limitações aplicam-se às etiquetas:

* Nem todos os tipos de recursos suportam etiquetas. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, veja [marca o suporte para recursos do Azure](../articles/azure-resource-manager/tag-support.md).
* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nomes/valores de etiquetas. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos que tenham, cada um, 15 pares de nomes/valores de etiqueta. Se tiver mais de 15 valores que têm de ser associados a um recurso, utilize uma cadeia JSON para o valor da etiqueta. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Este artigo mostra um exemplo de atribuição de uma cadeia JSON à etiqueta.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* Máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais estão limitados a um total de 2048 carateres para todos os nomes de etiquetas e valores.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.
* Não não possível aplicar etiquetas a recursos clássicos, como os serviços Cloud.
* Nomes de etiquetas não podem conter estes carateres: `<`, `>`, `%`, `&`, `\`, `?`, `/`
