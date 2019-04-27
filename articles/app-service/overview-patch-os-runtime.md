---
title: Sistema operacional e do runtime de aplicação de patches cadência - serviço de aplicações do Azure | Documentos da Microsoft
description: Descreve como atualizações de serviço de aplicações do Azure o sistema operacional e tempos de execução e como pode obter anúncios de atualização.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 576627c96b19dd3563ab21a5d478b779e4a3ed64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838999"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>SO e aplicação de patches de tempo de execução no serviço de aplicações do Azure

Este artigo mostra-lhe como obter determinadas informações de versão sobre o sistema operacional ou software no [serviço de aplicações](overview.md). 

Serviço de aplicações é uma plataforma-como-serviço, que significa que o sistema operacional e pilha de aplicação são geridos pelo Azure; apenas gerenciar seu aplicativo e os respetivos dados. Mais controlo sobre o sistema operacional e aplicativo stack está disponível no [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/). Com isso em mente, contudo é útil para como um utilizador do serviço de aplicações para saber mais informações, tais como:

-   Como e quando são as atualizações do SO aplicadas?
-   Como o serviço de aplicações é corrigido contra vulnerabilidades significativas (por exemplo, o dia zero)?
-   Quais versões de SO e o tempo de execução estão executando as suas aplicações?

Por motivos de segurança, determinadas especificidades de informações de segurança não são publicadas. No entanto, o artigo tem como objetivo minimizar as preocupações, maximiza a transparência sobre o processo e como pode se manter atualizado sobre os anúncios relacionados à segurança ou atualizações de tempo de execução.

## <a name="how-and-when-are-os-updates-applied"></a>Como e quando são as atualizações do SO aplicadas?

O Azure gere a aplicação de patches de SO em dois níveis, os servidores físicos e as máquinas de virtuais de convidado (VMs) com os recursos de serviço de aplicações. Ambos são atualizadas mensalmente, que se alinha para mensal [Patch Terça-feira](https://technet.microsoft.com/security/bulletins.aspx) agenda. Estas atualizações são aplicadas automaticamente, de forma que garante os elevada disponibilidade SLA de serviços do Azure. 

Para obter informações detalhadas sobre como as atualizações são aplicadas, consulte [desmistificando da mágica por trás de atualizações de sistema operacional de serviço de aplicações](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Como o Azure lidar com vulnerabilidades significativas?

Quando precisam imediata aplicação de patches, tais como que vulnerabilidades graves [vulnerabilidades de dia zero](https://wikipedia.org/wiki/Zero-day_(computing)), as atualizações de alta prioridade são tratadas numa base caso a caso.

Mantenha-se atualizado com anúncios de segurança críticas no Azure visitando [blogue de segurança do Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Quando são runtimes de linguagens suportadas atualizados, adicionados ou preteridos?

Novas versões estáveis de runtimes de linguagens suportadas (maior, menor ou aplicar patches) são periodicamente adicionadas a instâncias de serviço de aplicações. Algumas atualizações substituem a instalação existente, enquanto outros estão instalados lado a lado com versões existentes. Uma instalação de substituição significa que seu aplicativo executa automaticamente o tempo de execução atualizado. Uma instalação lado a lado significa que precisará migrar manualmente suas aplicações para tirar partido de uma nova versão de runtime. Para obter mais informações, consulte um das subsecções.

Tempo de execução atualizações e substituições são anunciadas aqui:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informações aqui se aplicam aos tempos de execução de linguagem que incorporam uma aplicação de serviço de aplicações. Um tempo de execução personalizado que carrega para o serviço de aplicações, por exemplo, se mantiver inalterado, a menos que atualizá-lo manualmente.
>
>

### <a name="new-patch-updates"></a>Novas atualizações de patch

Atualizações de patch para .NET, PHP, Java SDK ou versão do Tomcat/Jetty são aplicadas automaticamente ao substituir a instalação existente com a nova versão. Atualizações de patch de node. js são instaladas lado a lado com as versões existentes (semelhantes a versão principal e secundária, na secção seguinte). Versões de patch de Python nova, podem ser instaladas manualmente através de [extensões de site](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), lado a lado com as instalações de Python incorporadas.

### <a name="new-major-and-minor-versions"></a>Novas versões principais e secundárias

Quando é adicionada uma nova versão principal ou secundária, é instalada lado a lado com as versões existentes. Pode atualizar manualmente a sua aplicação para a nova versão. Se tiver configurado a versão de tempo de execução num arquivo de configuração (como `web.config` e `package.json`), tem de atualizar com o mesmo método. Se utilizou um serviço de aplicações na definição de configurar a sua versão de tempo de execução, pode alterá-lo na [portal do Azure](https://portal.azure.com) ou ao executar uma [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) comando no [Cloud Shell](../cloud-shell/overview.md), como mostrado nos exemplos a seguir:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Versões preteridas  

Quando uma versão mais antiga foi preterida, a data de remoção é anunciada, de modo a que pode planear a atualização de versão de runtime em conformidade. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Como consultar estado de atualização de SO e o tempo de execução no meu instâncias?  

Embora informações críticas de sistema operacional permanece bloqueadas de acesso (veja [funcionalidade do sistema operativo no App Service do Azure](operating-system-functionality.md)), o [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) permite-lhe consultar a sua instância de serviço de aplicações sobre o sistema operacional versão e versões de tempo de execução. 

A tabela seguinte mostra como as versões do Windows e do runtime de linguagem que estejam a executar as suas aplicações:

| Informações | Onde encontrá-lo | 
|-|-|
| Versão do Windows | Consulte `https://<appname>.scm.azurewebsites.net/Env.cshtml` (em informações do sistema) |
| Versão do .NET | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Versão do .NET core | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `dotnet --version` |
| Versão de PHP | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `php --version` |
| Versão do node. js predefinida | Na [Cloud Shell](../cloud-shell/overview.md), execute o seguinte comando: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Versão de Python | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `python --version` |  

> [!NOTE]  
> Acesso à localização de registo `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, em que informações sobre [patches "KB"](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) é armazenado, é bloqueado.
>
>

## <a name="more-resources"></a>Mais recursos

[Centro de fidedignidade: Segurança](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bit ASP.NET Core no serviço de aplicações do Azure](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
