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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564746"
---
## <a name="register-extensions"></a>Registar extensões

Com exceção dos gatilhos HTTP e temporizador, as ligações de funções na versão 2.x e superior são implementadas como pacotes de extensão. Na versão 2.x e além do tempo de funcionamento das Funções Azure, tem de registar explicitamente as extensões para os tipos de encadernação utilizados nas suas funções. As exceções a isto são ligações HTTP e gatilhos temporizadores, que não requerem extensões.

Pode optar por instalar extensões de ligação individualmente, ou pode adicionar uma referência de extensão ao ficheiro do projeto host.json. Os feixes de extensão eliminam a possibilidade de ter problemas de compatibilidade do pacote ao utilizar vários tipos de encadernação. É a abordagem recomendada para o registo de extensões vinculativas. Os feixes de extensão também removem a exigência de instalação do .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Pacotes de extensão

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Para saber mais, consulte as [extensões de ligação das Funções Do Registo Azure](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Deve adicionar pacotes de extensão ao host.json antes de adicionar ligações ao ficheiro função.json.

### <a name="register-individual-extensions"></a>Registar extensões individuais

Se precisar de instalar extensões que não estejam num pacote, pode registar manualmente pacotes de extensão individuais para encadernações específicas. 

> [!NOTE]
> Para registar manualmente as `func extensions install`extensões utilizando, deve ter instalado o .NET Core 2.x SDK.

Depois de ter atualizado o ficheiro *function.json* para incluir todas as encadernações de que a sua função necessita, execute o seguinte comando na pasta do projeto.

```bash
func extensions install
```

O comando lê o ficheiro *função.json* para ver quais os pacotes que precisa, instala-os e reconstrói o projeto de extensões. Adiciona novas ligações na versão atual, mas não atualiza as encadernações existentes. Utilize `--force` a opção de atualizar as ligações existentes para a versão mais recente ao instalar novas.
