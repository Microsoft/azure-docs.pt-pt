---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75564746"
---
## <a name="register-extensions"></a>Extensões de registo

Com exceção dos disparadores HTTP e timer, as ligações de funções na versão 2.x e superior são implementadas como pacotes de extensão. Na versão 2.x e além do tempo de funcionamento das Funções Azure, tem de registar explicitamente as extensões para os tipos de encadernação utilizados nas suas funções. As exceções a isto são ligações HTTP e gatilhos de temporizador, que não requerem extensões.

Pode optar por instalar extensões de ligação individualmente, ou pode adicionar uma referência ao pacote de extensão à host.jsno ficheiro do projeto. Os pacotes de extensão eliminam a possibilidade de ter problemas de compatibilidade do pacote quando se utilizam vários tipos de ligação. É a abordagem recomendada para o registo de extensões vinculativas. Os pacotes de extensão também removem a exigência de instalação do .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Pacotes de extensão

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Para saber mais, consulte [as extensões de ligação do Registo Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Deve adicionar pacotes de extensão ao host.jsantes de adicionar encadernações ao function.jsno ficheiro.

### <a name="register-individual-extensions"></a>Registar extensões individuais

Se precisar de instalar extensões que não estejam num pacote, pode registar manualmente pacotes de extensão individuais para encadernações específicas. 

> [!NOTE]
> Para registar manualmente as extensões `func extensions install` utilizando, tem de ter o .NET Core 2.x SDK instalado.

Depois de ter atualizado o seu *function.jsno* ficheiro para incluir todas as encadernações de que a sua função necessita, execute o seguinte comando na pasta do projeto.

```bash
func extensions install
```

O comando lê o *function.jsem* ficheiro para ver quais os pacotes de que necessita, instala-os e reconstrói o projeto de extensões. Adiciona quaisquer novas ligações na versão atual, mas não atualiza as ligações existentes. Utilize a `--force` opção para atualizar as ligações existentes para a versão mais recente ao instalar as novas.
