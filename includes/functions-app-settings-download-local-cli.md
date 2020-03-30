---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "72329541"
---
Já criou uma aplicação de função em Azure, juntamente com a conta de Armazenamento necessária. A cadeia de ligação para esta conta é armazenada de forma segura nas definições da aplicação em Azure. Neste artigo, escreve mensagens para uma fila de armazenamento na mesma conta. Para se ligar à sua conta de Armazenamento ao executar a função localmente, tem de descarregar as definições da aplicação para o ficheiro local.settings.json. 

A partir da raiz do projeto, execute o seguinte comando de Ferramentas Core funções `<APP_NAME>` Azure para descarregar definições para local.settings.json, substituindo com o nome da sua app de funções do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez precise sintetizar na sua conta Azure.

> [!IMPORTANT]  
> Este comando substitui quaisquer definições existentes com valores da sua aplicação de funções no Azure.  
>
> Como contém segredos, o ficheiro local.settings.json nunca é publicado, e deve ser excluído do controlo de origem.  
> 

Você precisa `AzureWebJobsStorage`do valor , que é o string de conexão da conta de armazenamento. Utilize esta ligação para verificar se a ligação de saída funciona como esperado.
