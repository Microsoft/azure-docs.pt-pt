---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111224"
---
> [!NOTE]
> Os pedidos multipart normalmente requerem três peças:
> * Um cabeçalho **tipo conteúdo:**
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Uma **disposição de conteúdo:**
>   * `form-data; name="metadata"`
> * O conteúdo do ficheiro para carregar
>
> **O Tipo de Conteúdo** e **a Disposição** do Conteúdo variarão dependendo do cenário de utilização.

Os pedidos multipart podem ser feitos C#programáticamente (através de um cliente REST, ou ferramenta como [o Carteiro.](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request) As ferramentas de cliente REST podem ter diferentes níveis de suporte para pedidos multipartes complexos. As definições de configuração também podem variar ligeiramente de ferramenta para ferramenta. Verifique qual a ferramenta mais adequada para as suas necessidades.

> [!IMPORTANT]
> Os pedidos multipart feitos às APIs de Gestão de Gémeos Digitais Azure normalmente têm duas partes:
> * Metadados blob (como um tipo MIME associado) que é declarado por **Content-Type** e/ou **Content-Disposition**
> * Conteúdo blob que inclua o conteúdo não estruturado de um ficheiro a ser carregado
>
> Nenhuma das duas partes é necessária **para** pedidos patch. Ambos são necessários para **post** ou criar operações.

O código fonte De Ocupação C# [Quickstart](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contém exemplos completos que demonstram como fazer pedidos multipart contra as APIs de Gestão de Gémeos Digitais Azure.