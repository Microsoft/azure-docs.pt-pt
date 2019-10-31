---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 10/30/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c141e67157f3ec17d475062ec76406ec765c4f50
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199171"
---
Você aplica marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome e valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Depois de aplicar etiquetas, pode obter todos os recursos na sua subscrição com este nome e este valor da etiqueta. As marcas permitem que você recupere recursos relacionados de diferentes grupos de recursos. Esta abordagem é útil quando precisa de organizar os recursos de gestão ou de faturação.

Sua taxonomia deve considerar uma estratégia de marcação de metadados de autoatendimento, além de uma estratégia de marcação automática para reduzir a carga sobre os usuários e aumentar a precisão.

As seguintes limitações aplicam-se às etiquetas:

* Nem todos os tipos de recurso dão suporte a marcas. Para determinar se você pode aplicar uma marca a um tipo de recurso, consulte [suporte a marcas para recursos do Azure](../articles/azure-resource-manager/tag-support.md).
* Cada recurso ou grupo de recursos pode ter um máximo de 50 pares de nome/valor de marca. Se você precisar aplicar mais marcas do que o número máximo permitido, use uma cadeia de caracteres JSON para o valor da marca. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Um grupo de recursos pode conter muitos recursos que têm um número de 50 pares de nome/valor de marca.
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As VMs generalizadas não dão suporte a marcas.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.
* As marcas não podem ser aplicadas a recursos clássicos, como serviços de nuvem.
* Os nomes de marca não podem conter estes caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Atualmente, as zonas DNS do Azure e os serviços do Gerenciador de tráfego também não permitem o uso de espaços na marca. 
