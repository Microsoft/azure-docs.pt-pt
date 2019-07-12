---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836802"
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
| Trabalhos Web | _\<job_name > / run. \<extensão >_ sob _App\_dados/tarefas/contínua_ para o WebJobs contínuos, ou _aplicação\_dados/tarefas/acionada_ para acionada WebJobs. Para obter mais informações, consulte [documentação de WebJobs do Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Ver [implementação contínua para funções do Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar sua implantação, inclua uma *. Deployment* ficheiro na raiz do repositório. Para obter mais informações, consulte [personalizar implementações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se desenvolver no Visual Studio, permitem [Visual Studio criar um repositório para si](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). O projeto é imediatamente pronto para ser implementado utilizando o Git.
>

