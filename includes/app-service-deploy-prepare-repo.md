---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 7ec4028c319749b6a3da019e1d320d3937e9c4b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133159"
---
## <a name="prepare-your-repository"></a>Preparar o seu repositório

Para obter as compilações automáticas do servidor de compilação Kudu de serviço de aplicações do Azure, certifique-se de que a raiz do repositório tem os arquivos corretos no seu projeto.

| Runtime | Ficheiros do diretório de raiz |
|-|-|
| ASP.NET (apenas Windows) | _*.sln_, _*.csproj_, or _default.aspx_ |
| Núcleo de ASP.NET | _*.sln_ or _*.csproj_ |
| PHP | _index.php_ |
| Ruby (apenas Linux) | _Gemfile_ |
| Node.js | _Server. js_, _App. js_, ou _Package. JSON_ com um script de início |
| Python | _\*. PY_, _Requirements. txt_, ou _Runtime. txt_ |
| HTML | _default. htm_, _default. HTML_, _default_, _htm_, _Index_, ou  _iisstart. htm_ |
| Trabalhos Web | _\<job_name > / run. \<extensão >_ sob _App\_dados/tarefas/contínua_ (para o WebJobs contínuos) ou _aplicação\_dados/tarefas/acionada_ (para acionada WebJobs). Para obter mais informações, consulte [documentação de WebJobs do Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Ver [implementação contínua para funções do Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar sua implantação, inclua uma _. Deployment_ ficheiro na raiz do repositório. Para obter mais informações, consulte [personalizar implementações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se desenvolver no Visual Studio, permitem [Visual Studio criar um repositório para si](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). O projeto é imediatamente pronto para ser implementado utilizando o Git.
>
>

