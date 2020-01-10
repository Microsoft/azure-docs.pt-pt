---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564746"
---
## <a name="register-extensions"></a>Registrar extensões

Com exceção dos gatilhos HTTP e Timer, as associações de funções no tempo de execução versão 2. x e superiores são implementadas como pacotes de extensão. Na versão 2. x e além do tempo de execução de Azure Functions, você precisa registrar explicitamente as extensões para os tipos de associação usados em suas funções. As exceções a isso são as associações HTTP e os gatilhos de temporizador que não exigem extensões.

Você pode optar por instalar as extensões de associação individualmente ou pode adicionar uma referência de pacote de extensão ao arquivo de projeto host. JSON. Os pacotes de extensão eliminam a possibilidade de haver problemas de compatibilidade de pacote ao usar vários tipos de associação. É a abordagem recomendada para registrar extensões de associação. Os pacotes de extensão também eliminam a necessidade de instalar o SDK do .NET Core 2. x. 

### <a name="extension-bundles"></a>Pacotes de extensão

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Para saber mais, consulte [registrar Azure Functions extensões de associação](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Você deve adicionar pacotes de extensão ao host. JSON antes de adicionar associações ao arquivo function. JSON.

### <a name="register-individual-extensions"></a>Registrar extensões individuais

Se precisar instalar extensões que não estejam em um pacote, você poderá registrar manualmente pacotes de extensão individuais para associações específicas. 

> [!NOTE]
> Para registrar manualmente as extensões usando `func extensions install`, você deve ter o SDK do .NET Core 2. x instalado.

Depois de atualizar seu arquivo *Function. JSON* para incluir todas as associações de que sua função precisa, execute o seguinte comando na pasta do projeto.

```bash
func extensions install
```

O comando lê o arquivo *Function. JSON* para ver quais pacotes são necessários, instala-os e recria o projeto de extensões. Ele adiciona novas associações na versão atual, mas não atualiza as associações existentes. Use a opção `--force` para atualizar as ligações existentes para a versão mais recente ao instalar novas.
