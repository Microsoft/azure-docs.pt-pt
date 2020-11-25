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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004361"
---
## <a name="prepare-your-repository"></a>Prepare o seu repositório

Para obter construções automáticas a partir do Azure App Service Kudu construa o servidor, certifique-se de que a raiz do repositório tem os ficheiros corretos no seu projeto.

| Runtime | Ficheiros de diretório de raiz |
|-|-|
| ASP.NET (apenas para Windows) | _*.sln_, _*.csproj,_ ou _padrão.aspx_ |
| Núcleo de ASP.NET | _*.sln_ ou _*.csproj_ |
| PHP | _índice.php_ |
| Ruby (apenas Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js,_ oupackage.js _com_ um roteiro inicial |
| Python | _\* .py,_ _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm,_ default.htm _l,_ _por defeito.asp,_ _index.htm,_ _index.html,_ ou _iisstart.htm_ |
| WebJobs | _\<job_name>/correr.\<extension>_ em _\_ dados de aplicações/empregos/contínuos_ para WebJobs contínuos, ou _\_ Dados de Aplicações/empregos/desencadeados_ para WebJobs desencadeados. Para mais informações, consulte [a documentação da Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Ver [implementação contínua para funções Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar a sua implementação, inclua um ficheiro *de .implementação* na raiz do repositório. Para obter mais informações, consulte [Personalizar as implementações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [o script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se desenvolver no Visual Studio, deixe o [Visual Studio criar um repositório para si](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). O projeto está imediatamente pronto para ser implementado usando Git.
>

