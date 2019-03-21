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
ms.openlocfilehash: df987d1e13cb5330842fbab41dae96b24b581ddb
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227274"
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
| Python (apenas Windows) | _\*. PY_, _Requirements. txt_, ou _Runtime. txt_ |
| HTML | _default. htm_, _default. HTML_, _default_, _htm_, _Index_, ou  _iisstart. htm_ |
| Trabalhos Web | _\<job_name > / run. \<extensão >_ sob _App\_dados/tarefas/contínua_ (para o WebJobs contínuos) ou _aplicação\_dados/tarefas/acionada_ (para acionada WebJobs). Para obter mais informações, consulte [documentação de WebJobs do Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Ver [implementação contínua para funções do Azure](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Para personalizar sua implantação, inclua uma _. Deployment_ ficheiro na raiz do repositório. Para obter mais informações, consulte [personalizar implementações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se desenvolver no Visual Studio, permitem [Visual Studio criar um repositório para si](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). O projeto é imediatamente pronto para ser implementado utilizando o Git.
>
>

