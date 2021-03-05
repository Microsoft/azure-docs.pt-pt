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
ms.openlocfilehash: f783245b17a880a67b62aa24a3feabb9b113053b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122260"
---
## <a name="prepare-your-repository"></a>Prepare o seu repositório

Para obter construções automatizadas a partir do servidor de construção do Azure App Service, certifique-se de que a raiz do repositório tem os ficheiros corretos no seu projeto.

| Runtime | Ficheiros de diretório de raiz |
|-|-|
| ASP.NET (apenas para Windows) | _*.sln_, _*.csproj,_ ou _padrão.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _índice.php_ |
| Ruby (apenas Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js,_ oupackage.js _com_ um roteiro inicial |
| Python | _\* .py,_ _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm,_ default.htm _l,_ _por defeito.asp,_ _index.htm,_ _index.html,_ ou _iisstart.htm_ |
| WebJobs | _\<job_name>/correr.\<extension>_ em _\_ dados de aplicações/empregos/contínuos_ para WebJobs contínuos, ou _\_ Dados de Aplicações/empregos/desencadeados_ para WebJobs desencadeados. Para mais informações, consulte [a documentação da Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Ver [implementação contínua para funções Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar a sua implementação, inclua um ficheiro *de .implementação* na raiz do repositório. Para obter mais informações, consulte [Personalizar as implementações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [o script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se desenvolver no Visual Studio, deixe o [Visual Studio criar um repositório para si](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio&preserve-view=true). O projeto está imediatamente pronto para ser implementado usando Git.
>

