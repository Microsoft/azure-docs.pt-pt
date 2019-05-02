---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733276"
---
Os pacotes de extensão tornam todos os enlaces publicados pela equipe de funções do Azure disponível através de uma definição no *Host. JSON* ficheiro. Para o desenvolvimento local, certifique-se de que tem a versão mais recente de [ferramentas de núcleo de funções do Azure](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Para utilizar os pacotes de extensão, atualize o *Host. JSON* arquivo para incluir a seguinte entrada para `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- O `id` propriedade referencia o espaço de nomes para os pacotes de extensão de funções do Microsoft Azure.
- O `version` referencia a versão do pacote.

Incremento de versões de pacote como pacotes nas alterações do pacote. Alterações de versão principal acontecem apenas quando pacotes no pacote de mover uma versão principal. O `version` propriedade utiliza o [notação de intervalo para a especificação de intervalos de versão](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). O runtime das funções sempre escolhe a versão de permissível máxima definida pelo intervalo ou intervalo de versão.

Depois de fazer referência os pacotes de extensão no seu projeto, em seguida, todos os enlaces de predefinição estão disponíveis para as suas funções. As ligações disponíveis no [pacote de extensão](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) são:

|Pacote  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|