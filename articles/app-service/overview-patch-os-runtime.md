---
title: Cadência de remendos de so e tempo de execução
description: Saiba como o Azure App Service atualiza o SISTEMA e os tempos de execução, quais os tempos de execução e nível de patch que as suas apps têm e como pode obter anúncios de atualização.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 31b2d39f5d0d125d2bf23ac72718d9b386b48923
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833491"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Correção de SO e tempo de execução no Serviço de Aplicações Azure

Este artigo mostra-lhe como obter determinadas informações de versão relativas ao SISTEMA ou software no [Serviço de Aplicações.](overview.md) 

O App Service é uma Plataforma-as-a-Service, o que significa que o SISTEMA e a pilha de aplicações são geridos por Azure; gere apenas a sua aplicação e os seus dados. Mais controlo sobre o sistema operativo e a pilha de aplicações está disponível em [Máquinas Virtuais Azure.](../virtual-machines/index.yml) Com isso em mente, é, no entanto, útil para si, como utilizador do Serviço de Aplicações, saber mais informações, tais como:

-   Como e quando são aplicadas atualizações de SO?
-   Como é que o Serviço de Aplicações é corrigido contra vulnerabilidades significativas (como o dia zero)?
-   Que versões de SO e runtime estão a executar as suas apps?

Por razões de segurança, certas especificidades das informações de segurança não são publicadas. No entanto, o artigo visa aliviar as preocupações, maximizando a transparência no processo e como pode manter-se atualizado sobre anúncios relacionados com segurança ou atualizações de tempo de execução.

## <a name="how-and-when-are-os-updates-applied"></a>Como e quando são aplicadas atualizações de SO?

O Azure gere o patching de SO em dois níveis, os servidores físicos e as máquinas virtuais (VMs) que executam os recursos do Serviço de Aplicações. Ambos são atualizados mensalmente, o que alinha com o horário mensal [de terça-feira do Patch.](/security-updates/) Estas atualizações são aplicadas automaticamente, de forma a garantir a elevada disponibilidade dos serviços SLA da Azure. 

Para obter informações detalhadas sobre a forma como as atualizações são [aplicadas, consulte desmistificar a magia por trás das atualizações do Serviço de Aplicação OS](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Como é que o Azure lida com vulnerabilidades significativas?

Quando as vulnerabilidades graves requerem correção imediata, como [vulnerabilidades de zero dias,](https://wikipedia.org/wiki/Zero-day_(computing))as atualizações de alta prioridade são tratadas caso a caso.

Mantenha-se atual com anúncios críticos de segurança em Azure visitando [o Azure Security Blog](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Quando os tempos de execução da linguagem suportados são atualizados, adicionados ou depreciados?

Novas versões estáveis de tempos de execução de linguagem suportados (major, menor ou patch) são periodicamente adicionadas às instâncias do Serviço de Aplicações. Algumas atualizações substituem a instalação existente, enquanto outras são instaladas lado a lado com as versões existentes. Uma instalação de substituição significa que a sua aplicação funciona automaticamente no tempo de execução atualizado. Uma instalação lado a lado significa que deve migrar manualmente a sua aplicação para tirar partido de uma nova versão de tempo de execução. Para mais informações, consulte uma das subsecções.

Atualizações e depreciações de tempo de execução são anunciadas aqui:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> As informações aqui aplicam-se aos tempos de execução de idiomas que são incorporados numa aplicação do Serviço de Aplicações. Um tempo de execução personalizado que faz o upload para o Serviço de Aplicações, por exemplo, permanece inalterado a menos que o atualize manualmente.
>
>

### <a name="new-patch-updates"></a>Novas atualizações de patch

As atualizações de patch para a versão .NET, PHP, Java SDK ou Tomcat/Jetty são aplicadas automaticamente, sobrepondo a instalação existente com a nova versão. Node.js atualizações de patch são instaladas lado a lado com as versões existentes (semelhantes às versões principais e menores na secção seguinte). As novas versões de patch Python podem ser instaladas manualmente através de [extensões do local,](https://azure.microsoft.com/blog/azure-web-sites-extensions/)lado a lado com as instalações python incorporadas.

### <a name="new-major-and-minor-versions"></a>Novas versões principais e menores

Quando uma nova versão maior ou menor é adicionada, é instalada lado a lado com as versões existentes. Pode atualizar manualmente a sua aplicação para a nova versão. Se configurar a versão de tempo de execução num ficheiro de configuração (como `web.config` `package.json` e), terá de atualizar com o mesmo método. Se usou uma definição de Serviço de Aplicações para configurar a sua versão de tempo de execução, pode alterá-la no [portal Azure](https://portal.azure.com) ou executando um comando [Azure CLI](/cli/azure/get-started-with-azure-cli) no [Cloud Shell](../cloud-shell/overview.md), como mostram os seguintes exemplos:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Versões preprecadas  

Quando uma versão mais antiga é depreciada, a data de remoção é anunciada para que possa planear a sua versão de tempo de execução em conformidade. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Como posso consultar o sistema operativo e o estado de atualização do tempo de funcionação dos meus casos?  

Embora as informações críticas do SISTEMA tenham sido bloqueadas a partir do acesso (ver [funcionalidade do sistema operativo no Azure App Service),](operating-system-functionality.md)a consola [Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) permite-lhe consultar a sua instância do Serviço de Aplicações no que diz respeito à versão OS e versões de tempo de execução. 

A tabela a seguir mostra como as versões do Windows e do tempo de execução do idioma que estão a executar as suas aplicações:

| Informações | Onde encontrá-lo | 
|-|-|
| Versão do Windows | Ver `https://<appname>.scm.azurewebsites.net/Env.cshtml` (informações do Sistema) |
| Versão .NET | Em `https://<appname>.scm.azurewebsites.net/DebugConsole` , executar o seguinte comando no comando pronta: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| Versão .NET Core | Em `https://<appname>.scm.azurewebsites.net/DebugConsole` , executar o seguinte comando no comando pronta: <br> `dotnet --version` |
| Versão PHP | Em `https://<appname>.scm.azurewebsites.net/DebugConsole` , executar o seguinte comando no comando pronta: <br> `php --version` |
| Versão de Node.js predefinido | Na [Cloud Shell](../cloud-shell/overview.md), executar o seguinte comando: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Versão de Python | Em `https://<appname>.scm.azurewebsites.net/DebugConsole` , executar o seguinte comando no comando pronta: <br> `python --version` |  
| Versão java | Em `https://<appname>.scm.azurewebsites.net/DebugConsole` , executar o seguinte comando no comando pronta: <br> `java -version` |  

> [!NOTE]  
> O acesso ao local do registo `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages` , onde as informações sobre patches ["KB"](/security-updates/SecurityBulletins/securitybulletins) são armazenadas, é bloqueado.
>
>

## <a name="more-resources"></a>Mais recursos

[Centro de Confiança: Segurança](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bits ASP.NET Core no Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
