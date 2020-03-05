---
title: Cadência de remendo de sos e tempo de execução
description: Saiba como o Azure App Service atualiza o OS e os tempos de execução, quais os tempos de execução e nível de patch que as suas aplicações têm e como pode obter anúncios de atualização.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18
ms.openlocfilehash: 597964914f4022899ab027b735ec6932105497b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273627"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>SO e patching de tempo de execução no Serviço de Aplicações Azure

Este artigo mostra-lhe como obter certas informações de versão relativas ao SISTEMA ou software no [App Service](overview.md). 

O Serviço de Aplicações é um Serviço de Plataforma-as-a-Service, o que significa que o SISTEMA e a pilha de aplicações são geridos para si pelo Azure; só gere a sua aplicação e os seus dados. Mais controlo sobre o SISTEMA e a pilha de aplicações está disponível em [Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/). Com isso em mente, é, no entanto, útil para si, enquanto utilizador do App Service, saber mais informações, tais como:

-   Como e quando são aplicadas as atualizações do OS?
-   Como é que o Serviço de Aplicações é corrigido contra vulnerabilidades significativas (como o dia zero)?
-   Que versões DE E E o SISTEMA e o tempo de execução estão a executar as suas aplicações?

Por razões de segurança, certas especificidades das informações de segurança não são publicadas. No entanto, o artigo visa aliviar as preocupações maximizando a transparência no processo e como pode manter-se atualizado sobre anúncios relacionados com a segurança ou atualizações de tempo de funcionamento.

## <a name="how-and-when-are-os-updates-applied"></a>Como e quando são aplicadas as atualizações do OS?

O Azure gere a correção de OS em dois níveis, os servidores físicos e as máquinas virtuais dos hóspedes (VMs) que executam os recursos do Serviço de Aplicações. Ambos são atualizados mensalmente, o que se alinha com o horário mensal de [terça-feira.](https://technet.microsoft.com/security/bulletins.aspx) Estas atualizações são aplicadas automaticamente, de forma a garantir a alta disponibilidade de Serviços Azure. 

Para obter informações detalhadas sobre como as atualizações são aplicadas, consulte [desmistificar a magia por trás](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html)das atualizações do App Service OS .

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Como é que o Azure lida com vulnerabilidades significativas?

Quando as vulnerabilidades severas requerem remendos imediatos, tais como [vulnerabilidades de zero dias,](https://wikipedia.org/wiki/Zero-day_(computing))as atualizações de alta prioridade são tratadas caso a caso.

Mantenha-se atual com anúncios de segurança críticos em Azure visitando [o Azure Security Blog](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Quando é que os tempos de execução da linguagem suportados são atualizados, adicionados ou depreciados?

Novas versões estáveis de tempos de execução de idiomas suportados (grandes, menores ou patch) são periodicamente adicionadas às instâncias do App Service. Algumas atualizações sobrepõem a instalação existente, enquanto outras são instaladas lado a lado com as versões existentes. Uma instalação de sobreescrita significa que a sua aplicação funciona automaticamente no tempo de execução atualizado. Uma instalação lado a lado significa que deve migrar manualmente a sua aplicação para tirar partido de uma nova versão de tempo de funcionamento. Para mais informações, consulte uma das subsecções.

Atualizações e depreciações de tempo de execução são anunciados aqui:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> A informação aqui se aplica aos tempos de execução de idiomas que são incorporados numa aplicação do App Service. Um tempo de funcionamento personalizado que você envia para o App Service, por exemplo, permanece inalterado a menos que você atualize manualmente.
>
>

### <a name="new-patch-updates"></a>Novas atualizações de patch

As atualizações de patch para .NET, PHP, Java SDK ou tomcat/Jetty são aplicadas automaticamente, sobrecarregando a instalação existente com a nova versão. As atualizações de patch node.js são instaladas lado a lado com as versões existentes (semelhantes às versões principais e menores na secção seguinte). As novas versões de patch Python podem ser instaladas manualmente através de extensões do [local,](https://azure.microsoft.com/blog/azure-web-sites-extensions/)lado a lado com as instalações python incorporadas.

### <a name="new-major-and-minor-versions"></a>Novas versões principais e menores

Quando uma nova versão principal ou menor é adicionada, é instalada lado a lado com as versões existentes. Pode atualizar manualmente a sua aplicação para a nova versão. Se configurar a versão tempo de execução num ficheiro de configuração (como `web.config` e `package.json`), precisa de atualizar com o mesmo método. Se usou uma definição do Serviço de Aplicações para configurar a sua versão de tempo de execução, pode trocá-la no [portal Azure](https://portal.azure.com) ou executando um comando [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) na [Cloud Shell,](../cloud-shell/overview.md)como mostram os seguintes exemplos:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Versões depreciadas  

Quando uma versão mais antiga é depreciada, a data de remoção é anunciada para que possa planear a atualização da sua versão runtime em conformidade. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Como posso consultar o OS e o estado de atualização do tempo de funcionar nas minhas instâncias?  

Embora as informações críticas do OS estão bloqueadas a partir do acesso (ver [Funcionalidade do sistema operativo no Serviço de Aplicações Azure),](operating-system-functionality.md)a consola [Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) permite-lhe consultar a sua instância de Serviço de Aplicações relativamente à versão OS e versões de tempo de execução. 

A tabela que se segue mostra como é que as versões do Windows e do tempo de execução do idioma que estão a executar as suas aplicações:

| Informações | Onde encontrá-lo | 
|-|-|
| Versão do Windows | Ver `https://<appname>.scm.azurewebsites.net/Env.cshtml` (de acordo com informações do Sistema) |
| versão .NET | No `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no pedido de comando: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| versão .NET Core | No `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no pedido de comando: <br> `dotnet --version` |
| Versão PHP | No `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no pedido de comando: <br> `php --version` |
| Versão Padrão Node.js | Na [Cloud Shell,](../cloud-shell/overview.md)executar o seguinte comando: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Versão de Python | No `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no pedido de comando: <br> `python --version` |  
| Versão do Java | No `https://<appname>.scm.azurewebsites.net/DebugConsole`, execute o seguinte comando no pedido de comando: <br> `java -version` |  

> [!NOTE]  
> O acesso à localização do registo `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, onde as informações sobre [os patches "KB"](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) são armazenadas, é bloqueada.
>
>

## <a name="more-resources"></a>Mais recursos

[Centro de Confiança: Segurança](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bits ASP.NET Core no Serviço de Aplicações Azure](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
