---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/10/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 47f66fc61fdd397350efb934bf21f488960a27a2
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54233064"
---
> [!NOTE]
> Pedidos de várias partes, normalmente, necessitam de três partes:
> * R **Content-Type** cabeçalho:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * R **Content-Disposition**:
>   * `form-data; name="metadata"`
> * O conteúdo do ficheiro a carregar
>
> **Tipo de conteúdo** e **Content-Disposition** irá variar dependendo do cenário de utilização.

Pedidos de várias partes podem ser feitos por meio de programação (através do C#), através de um cliente REST ou a ferramenta, como [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). Ferramentas de cliente REST podem ter diferentes níveis de suporte para solicitações de várias partes complexas. Certifique-se de que ferramenta é mais adequada para suas necessidades.

> [!IMPORTANT]
> Com várias partes pedidos efetuados, normalmente, as APIs de gestão de duplos Digital do Azure tem duas partes:
> * Metadados do blob (por exemplo, um tipo de MIME associado) que foi declarado pelo **Content-Type** e **Content-Disposition**
> * Conteúdo do blob que incluem os conteúdos não estruturados de um ficheiro a carregar
>
> Nenhuma das duas partes é necessária para **aplicar o PATCH** pedidos. Ambas são necessárias para **POST** ou operações de criação.

O [código de origem de início rápido de ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contém concluído C# exemplos que demonstram como fazer pedidos de várias partes contra as APIs de gestão de duplos Digital do Azure.