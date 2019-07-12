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
ms.openlocfilehash: ffd9e54c0f39b4256dbc83a336328797a8b53c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608318"
---
## <a name="register-extensions"></a>Registe-se as extensões

Com exceção dos acionadores HTTP e timer, enlaces de funções na versão de runtime 2.x são implementados como pacotes de extensão. Na versão 2.x do runtime das funções do Azure, precisa registrar explicitamente as extensões para os tipos de ligação utilizadas nas suas funções. As exceções a isso são associações HTTP e os acionadores de temporizadores, que não necessitam de extensões.

Pode optar por instalar extensões de vinculação individualmente ou pode adicionar uma referência de pacote de extensão para o arquivo do projeto Host. JSON. Os pacotes de extensão remove a possibilidade de existirem problemas de compatibilidade do pacote ao usar vários tipos de ligação. É a abordagem recomendada para registar as extensões de vinculação. Os pacotes de extensão também remove a necessidade de instalar o .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>pacotes de extensão

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Para obter mais informações, consulte [extensões de vinculação de registrar as funções de Azure](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Deve adicionar os pacotes de extensão para o Host. JSON antes de adicionar enlaces para o ficheiro Functions.

### <a name="register-individual-extensions"></a>Registre-se de extensões individuais

Se precisar de instalar as extensões que não estão num pacote, é possível registrar manualmente os pacotes de extensão individuais para enlaces específicos. 

> [!NOTE]
> Registar manualmente as extensões com `func extensions install`, tem de ter o .NET Core SDK instalado 2.x.

Depois de atualizar seus *Function* arquivo para incluir todas as ligações que precisa de sua função, execute o seguinte comando na pasta de projeto.

```bash
func extensions install
```

O comando lê a *Function* arquivo para ver os pacotes que precisa, instala-os e recria o projeto de extensões. Ele adiciona todos os enlaces novo na versão atual, mas não atualiza "BIND" existente. Utilize o `--force` opção para atualizar "BIND" existente para a versão mais recente ao instalar novas etiquetas.