---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 10/02/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 76a05817ae91bb49240e5d72988c776ce67ec1c0
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845500"
---
> [!NOTE]
> Solicitações com várias partes geralmente exigem três partes:
> * Um cabeçalho **Content-Type** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Uma **disposição de conteúdo**:
>   * `form-data; name="metadata"`
> * O conteúdo do arquivo a ser carregado
>
> **Tipo de conteúdo** e **disposição de conteúdo** variam dependendo do cenário de uso.

Solicitações com várias partes podem ser feitas programaticamente (por meio C#de), por meio de um cliente REST ou ferramenta como o [postmaster](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). As ferramentas de cliente REST podem ter níveis variados de suporte a solicitações complexas de várias partes. As definições de configuração também podem variar um pouco da ferramenta para a ferramenta. Verifique qual ferramenta é mais adequada para suas necessidades.

> [!IMPORTANT]
> As solicitações com várias partes feitas às APIs de gerenciamento de gêmeos digital do Azure normalmente têm duas partes:
> * Metadados de BLOB (como um tipo MIME associado) que é declarado por **tipo de conteúdo** e/ou **disposição de conteúdo**
> * Conteúdo do blob que inclui o conteúdo não estruturado de um arquivo a ser carregado
>
> Nenhuma das duas partes é necessária para solicitações de **patch** . Ambos são necessários para operações **post** ou Create.

O [código-fonte de início rápido de ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contém exemplos completos C# demonstrando como fazer solicitações de várias partes em relação às APIs de gerenciamento de gêmeos digital do Azure.