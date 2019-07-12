---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841470"
---
Aplicar etiquetas aos recursos do Azure, fornecendo os metadados para as organizar numa taxonomia. Cada etiqueta é constituída por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Depois de aplicar etiquetas, pode obter todos os recursos na sua subscrição com este nome e este valor da etiqueta. As etiquetas permitem-lhe obter recursos relacionados de diferentes grupos de recursos. Esta abordagem é útil quando precisa de organizar os recursos de gestão ou de faturação.

A taxonomia deve considerar uma estratégia para além de uma estratégia de identificação de automática para reduzir a carga sobre os utilizadores e aumentar a precisão de marcação de metadados de self-service.

As seguintes limitações aplicam-se às etiquetas:

* Nem todos os tipos de recursos suportam etiquetas. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, veja [marca o suporte para recursos do Azure](../articles/azure-resource-manager/tag-support.md).
* Cada recurso ou grupo de recursos pode ter um máximo de 50 pares de nome/valor de etiqueta. Atualmente, apenas suporte de contas de armazenamento, de 15 etiquetas, mas esse limite será gerado para 50 numa versão futura. Se precisar de aplicar etiquetas mais do que o número máximo permitido, utilize uma cadeia de caracteres do JSON para o valor da etiqueta. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Um grupo de recursos pode conter muitos recursos que tenham, cada 50 pares de nome/valor de etiqueta.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* VMs generalizadas não oferecem suporte a marcas.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.
* Não não possível aplicar etiquetas a recursos clássicos, como os serviços Cloud.
* Nomes de etiquetas não podem conter estes carateres: `<`, `>`, `%`, `&`, `\`, `?`, `/`
