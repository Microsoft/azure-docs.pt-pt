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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836802"
---
## <a name="prepare-your-repository"></a>Prepare o seu repositório

Para obter construções automáticas do servidor de construção de Software de Aplicações Azure Kudu, certifique-se de que a sua raiz de repositório tem os ficheiros corretos no seu projeto.

| Runtime | Ficheiros de diretório de raiz |
|-|-|
| ASP.NET (apenas janelas) | _*.sln,_ _*.csproj,_ ou _padrão.aspx_ |
| Núcleo de ASP.NET | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Rubi (apenas Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_, ou _package.json_ com um script inicial |
| Python | .py, _requirements.txt,_ or _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html,_ _default.asp,_ _index.htm,_ _index.html,_ or _iisstart.htm_ |
| Trabalhos Web | _\<job_name>/correr.>\<_ de extensão em dados de _\_aplicações/empregos/contínuos_ para WebJobs contínuos, ou Dados de _Aplicações/empregos/desencadeados\__ para WebJobs desencadeados. Para mais informações, consulte a [documentação do Kudu WebJobs.](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Funções | Ver [implantação contínua para funções Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar a sua implementação, inclua um ficheiro *.deployment* na raiz do repositório. Para mais informações, consulte [personalizar implementações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se desenvolver no Visual Studio, deixe o [Visual Studio criar um repositório para si.](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) O projeto está imediatamente pronto para ser implementado usando Git.
>

