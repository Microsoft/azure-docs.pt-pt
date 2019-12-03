---
title: Cadência da aplicação de patch do sistema operacional e tempo de execução
description: Saiba como Azure App serviço atualiza o sistema operacional e os tempos de execução, os tempos de execução e o nível de patch que seus aplicativos têm e como você pode obter comunicados de atualização.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18
ms.openlocfilehash: 1a56fed04399325be315d8d977e5a72223bddac5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688577"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Aplicação de patch de sistema operacional e tempo de execução no serviço Azure App

Este artigo mostra como obter determinadas informações de versão sobre o sistema operacional ou o software no [serviço de aplicativo](overview.md). 

O serviço de aplicativo é uma plataforma como um serviço, o que significa que o sistema operacional e a pilha de aplicativos são gerenciados para você pelo Azure; Você só gerencia seu aplicativo e seus dados. Mais controle sobre o sistema operacional e a pilha de aplicativos estão disponíveis em [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/). Com isso em mente, é muito útil para você como um usuário do serviço de aplicativo saber mais informações, como:

-   Como e quando as atualizações do sistema operacional são aplicadas?
-   Como o serviço de aplicativo é corrigido em relação a vulnerabilidades significativas (como um dia zero)?
-   Quais versões do sistema operacional e do tempo de execução estão executando seus aplicativos?

Por motivos de segurança, determinadas especificações de informações de segurança não são publicadas. No entanto, o artigo visa aliviar as preocupações maximizando a transparência do processo e como você pode se manter atualizado sobre comunicados relacionados à segurança ou atualizações de tempo de execução.

## <a name="how-and-when-are-os-updates-applied"></a>Como e quando as atualizações do sistema operacional são aplicadas?

O Azure gerencia a aplicação de patch de so em dois níveis, os servidores físicos e as VMs (máquinas virtuais) convidadas que executam os recursos do serviço de aplicativo. Ambos são atualizados mensalmente, que se alinham ao agendamento mensal de [terça-feira](https://technet.microsoft.com/security/bulletins.aspx) . Essas atualizações são aplicadas automaticamente, de forma a garantir o SLA de alta disponibilidade dos serviços do Azure. 

Para obter informações detalhadas sobre como as atualizações são aplicadas, consulte [Desmistificando a mágica por trás das atualizações do sistema operacional do serviço de aplicativo](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Como o Azure lida com vulnerabilidades significativas?

Quando vulnerabilidades severas exigem aplicação de patch imediata, como [vulnerabilidades de dia zero](https://wikipedia.org/wiki/Zero-day_(computing)), as atualizações de alta prioridade são tratadas caso a caso.

Mantenha-se atualizado com comunicados de segurança críticos no Azure visitando o [blog de segurança do Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Quando os tempos de execução de idioma com suporte são atualizados, adicionados ou substituídos?

Novas versões estáveis de tempos de execução de idioma com suporte (Major, Minor ou patch) são adicionadas periodicamente às instâncias do serviço de aplicativo. Algumas atualizações substituem a instalação existente, enquanto outras são instaladas lado a lado com versões existentes. Uma instalação de substituição significa que seu aplicativo é executado automaticamente no tempo de execução atualizado. Uma instalação lado a lado significa que você deve migrar manualmente seu aplicativo para tirar proveito de uma nova versão de tempo de execução. Para obter mais informações, consulte uma das subseções.

As atualizações de tempo de execução e as preterições são anunciadas aqui:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> As informações aqui se aplicam aos tempos de execução de linguagem que são criados em um aplicativo do serviço de aplicativo. Um tempo de execução personalizado que você carrega no serviço de aplicativo, por exemplo, permanece inalterado, a menos que você o atualize manualmente.
>
>

### <a name="new-patch-updates"></a>Novas atualizações de patch

As atualizações de patch para .NET, PHP, SDK Java ou a versão Tomcat/Jetty são aplicadas automaticamente pela substituição da instalação existente pela nova versão. As atualizações de patch do node. js são instaladas lado a lado com as versões existentes (semelhante às versões principais e secundárias na próxima seção). As novas versões de patch do Python podem ser instaladas manualmente por meio de [extensões de site](https://www.siteextensions.net/packages?q=Tags%3A%22python%22), lado a lado com as instalações internas do Python.

### <a name="new-major-and-minor-versions"></a>Novas versões principais e secundárias

Quando uma nova versão principal ou secundária é adicionada, ela é instalada lado a lado com as versões existentes. Você pode atualizar manualmente seu aplicativo para a nova versão. Se você configurou a versão de tempo de execução em um arquivo de configuração (como `web.config` e `package.json`), precisará atualizar com o mesmo método. Se você usou uma configuração do serviço de aplicativo para configurar sua versão de tempo de execução, poderá alterá-la na [portal do Azure](https://portal.azure.com) ou executando um comando [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) no [Cloud Shell](../cloud-shell/overview.md), conforme mostrado nos exemplos a seguir:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Versões preteridas  

Quando uma versão mais antiga é preterida, a data de remoção é anunciada para que você possa planejar a atualização da versão de tempo de execução de acordo. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Como fazer para consultar o status de atualização do sistema operacional e tempo de execução em minhas instâncias?  

Embora as informações críticas do sistema operacional sejam bloqueadas do acesso (consulte a [funcionalidade do sistema operacional no serviço Azure app](operating-system-functionality.md)), o [console do kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) permite consultar sua instância do serviço de aplicativo em relação à versão do sistema operacional e às versões de tempo de execução. 

A tabela a seguir mostra como as versões do Windows e do tempo de execução de linguagem que estão executando seus aplicativos:

| Proteção das | Onde encontrá-lo | 
|-|-|
| Versão do Windows | Consulte `https://<appname>.scm.azurewebsites.net/Env.cshtml` (em informações do sistema) |
| Versão do .NET | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Versão do .NET Core | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `dotnet --version` |
| Versão de PHP | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `php --version` |
| Versão padrão do node. js | No [Cloud Shell](../cloud-shell/overview.md), execute o seguinte comando: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Versão de Python | Em `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no prompt de comando: <br> `python --version` |  

> [!NOTE]  
> Acesso ao local do registro `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, em que as informações sobre [patches de "KB"](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) são armazenadas, são bloqueadas.
>
>

## <a name="more-resources"></a>Mais recursos

[Central de confiabilidade: segurança](https://www.microsoft.com/en-us/trustcenter/security)  
[ASP.NET Core de 64 bits no serviço Azure App](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
