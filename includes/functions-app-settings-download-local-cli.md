---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "72329541"
---
Já criou uma aplicação de função em Azure, juntamente com a conta de Armazenamento necessária. O fio de ligação desta conta é armazenado de forma segura nas definições de aplicações em Azure. Neste artigo, você escreve mensagens para uma fila de armazenamento na mesma conta. Para se ligar à sua conta de Armazenamento ao executar a função localmente, tem de transferir as definições da aplicação para o local.settings.jsno ficheiro. 

A partir da raiz do projeto, executar o seguinte comando Azure Functions Core Tools para descarregar definições para local.settings.jsligado, substituindo `<APP_NAME>` pelo nome da sua aplicação de função do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez precise de se inscrever na sua conta Azure.

> [!IMPORTANT]  
> Este comando substitui quaisquer definições existentes com valores da sua aplicação de função em Azure.  
>
> Como contém segredos, o local.settings.jsem ficheiro nunca é publicado, e deve ser excluído do controlo de fontes.  
> 

Precisa do valor `AzureWebJobsStorage` , que é a cadeia de ligação da conta de armazenamento. Utilize esta ligação para verificar se a ligação de saída funciona como esperado.
