---
title: Perguntas-gerais de desempenho da aplicação
description: Obtenha respostas a perguntas frequentes sobre disponibilidade, desempenho e problemas de aplicação no Serviço de Aplicações Azure.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 433f5885c7f057226e78c4ae57e03d7619004d21
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259867"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Perguntas de desempenho de aplicações para aplicações web em Azure

> [!NOTE]
> Algumas das diretrizes abaixo só podem funcionar em Serviços de Aplicações Windows ou Linux. Por exemplo, os Serviços de Aplicações Linux funcionam em modo de 64 bits por padrão.
>

Este artigo tem respostas a perguntas frequentes (PERGUNTAS) sobre problemas de desempenho de aplicações para a [funcionalidade de Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Porque é que a minha aplicação é lenta?

Vários fatores podem contribuir para um desempenho lento da aplicação. Para obter passos detalhados de resolução de problemas, consulte [o desempenho lento da aplicação web troubleshoot](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Como posso resolver um cenário de consumo de CPU?

Em alguns cenários elevados de consumo de CPU, a sua aplicação pode realmente necessitar de mais recursos informáticos. Nesse caso, considere a escala para um nível de serviço mais elevado para que a aplicação obtenha todos os recursos de que necessita. Outras vezes, o elevado consumo de CPU pode ser causado por um mau ciclo ou por uma prática de codificação. Obter informações sobre o que está a desencadear o aumento do consumo de CPU é um processo em duas partes. Primeiro, crie um depósito de processo, e depois analise o despejo do processo. Para mais informações, consulte [Capturar e analisar um ficheiro de despejo para um alto consumo de CPU para Aplicações Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Como posso resolver um cenário de alto consumo de memória?

Em alguns cenários de alto consumo de memória, a sua aplicação pode realmente necessitar de mais recursos informáticos. Nesse caso, considere a escala para um nível de serviço mais elevado para que a aplicação obtenha todos os recursos de que necessita. Outras vezes, um inseto no código pode causar uma fuga de memória. Uma prática de codificação também pode aumentar o consumo de memória. Obter informações sobre o que está a desencadear um elevado consumo de memória é um processo em duas partes. Primeiro, crie um depósito de processo, e depois analise o despejo do processo. O Diagnosticizador de Acidentes da Galeria de Extensão do Sítio Azure pode executar eficazmente estes dois passos. Para mais informações, consulte [Capturar e analisar um ficheiro de despejo para alta memória intermitente para Aplicações Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Como automatizar aplicações web do App Service utilizando o PowerShell?

Pode utilizar cmdlets PowerShell para gerir e manter aplicações web do App Service. No nosso blog post [Aplicações web Automate hospedadas no Azure App Service utilizando o PowerShell,](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)descrevemos como usar cmdlets PowerShell baseados em Recursos Azure para automatizar tarefas comuns. O post do blog também tem código de amostra para várias tarefas de gestão de aplicações web. Para descrições e sintaxe para todas as aplicações web do App Service, consulte [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Como vejo os registos de eventos da minha aplicação web?

Para ver os registos de eventos da sua aplicação web:

1. Inscreva-se no seu [site da Kudu.](https://*yourwebsitename*.scm.azurewebsites.net)
2. No menu, selecione **Debug Console** > **CMD**.
3. Selecione a pasta **LogFiles.**
4. Para ver os registos de eventos, selecione o ícone do lápis ao lado do **eventlog.xml**.
5. Para descarregar os registos, execute o cmdlet PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Como posso capturar um despejo de memória em modo utilizador da minha aplicação web?

Para capturar um despejo de memória em modo utilizador da sua aplicação web:

1. Inscreva-se no seu [site da Kudu.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Selecione o menu **Process Explorer.**
3. Clique no processo **w3wp.exe** ou no seu processo WebJob.
4. Selecione **Descarregar despejo de memória** > despejo **completo**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Como vejo informações de nível de processo para a minha aplicação web?

Tem duas opções para visualizar informações de nível de processo para a sua aplicação web:

*   No portal do Azure:
    1. Abra o **Process Explorer** para a aplicação web.
    2. Para ver os detalhes, selecione o processo **w3wp.exe.**
*   Na consola kudu:
    1. Inscreva-se no seu [site da Kudu.](https://*yourwebsitename*.scm.azurewebsites.net)
    2. Selecione o menu **Process Explorer.**
    3. Para o processo **w3wp.exe,** selecione **Propriedades**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Quando navego na minha aplicação, vejo "Error 403 - Esta aplicação web é interrompida." Como posso resolver isto?

Três condições podem causar este erro:

* A aplicação web atingiu um limite de faturação e o seu site foi desativado.
* A aplicação web foi interrompida no portal.
* A aplicação web atingiu um limite de quota de recursos que pode aplicar-se a um plano de serviço à escala livre ou partilhada.

Para ver o que está a causar o erro e resolver o problema, siga os passos nas [Aplicações Web: "Error 403 – Esta aplicação web está parada"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Onde posso aprender mais sobre quotas e limites para vários planos de Serviço de Aplicações?

Para obter informações sobre quotas e limites, consulte os limites do Serviço de [Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Como reduzi o tempo de resposta para o primeiro pedido após o tempo de inatividade?

Por padrão, as aplicações web são descarregadas se ficarem inativas por um determinado período de tempo. Desta forma, o sistema pode conservar recursos. A desvantagem é que a resposta ao primeiro pedido após o descarregamento da aplicação web é mais longa, para permitir que a aplicação web carregue e comece a servir respostas. Nos planos de serviço Basic e Standard, pode ligar a definição **Always On** para manter a aplicação sempre carregada. Isto elimina tempos de carga mais longos após a aplicação estar inativa. Para alterar a definição **sempre em cima:**

1. No portal Azure, vá à sua aplicação web.
2. **Configuração** selecione
3. Selecione **as definições gerais**.
4. Para **sempre ligado,** selecione **On**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Como ligo o pedido falhado?

Para ativar o rastreio de pedido falhado:

1. No portal Azure, vá à sua aplicação web.
3. **Selecione todas as definições** > **registos**de diagnóstico .
4. Para rastreio de **pedido falhado,** selecione **On**.
5. Selecione **Guardar**.
6. Na lâmina da aplicação web, selecione **Tools**.
7. Selecione **Estúdio Visual Online**.
8. Se a definição não estiver **ligado,** selecione **On**.
9. Selecione **Go**.
10. Selecione **Web.config**.
11. No system.webServer, adicione esta configuração (para capturar um URL específico):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Para resolver problemas de desempenho lento, adicione esta configuração (se o pedido de captura estiver a demorar mais de 30 segundos):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Para descarregar os vestígios de pedido falhados, no [portal,](https://portal.azure.com)vá ao seu site.
15. Selecione **Ferramentas** > **Kudu** > **Go**.
18. No menu, selecione **Debug Console** > **CMD**.
19. Selecione a pasta **LogFiles** e, em seguida, selecione a pasta com um nome que começa com **W3SVC**.
20. Para ver o ficheiro XML, selecione o ícone do lápis.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Vejo a mensagem "Processo de Trabalhador pediu reciclagem devido ao limite de 'Memória Por Cento'." Como posso abordar esta questão?

A quantidade máxima de memória disponível para um processo de 32 bits (mesmo num sistema operativo de 64 bits) é de 2 GB. Por padrão, o processo do trabalhador está definido para 32 bits no App Service (para compatibilidade com aplicações web legados).

Considere mudar para processos de 64 bits para que possa tirar partido da memória adicional disponível no seu papel de Web Worker. Isto despoleta o reinício de uma aplicação web, por isso, agende em conformidade.

Note também que um ambiente de 64 bits requer um plano de serviço Básico ou Standard. Os planos gratuitos e partilhados funcionam sempre num ambiente de 32 bits.

Para mais informações, consulte [as aplicações web da Configure no App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Porque é que o meu pedido acaba depois de 230 segundos?

O Equilíbrio de Carga Azure tem um tempo limite de quatro minutos. Este é geralmente um prazo de resposta razoável para um pedido web. Se a sua aplicação web necessitar de processamento de fundo, recomendamos a utilização do Azure WebJobs. A aplicação web Azure pode ligar para webJobs e ser notificada quando o processamento de fundo estiver terminado. Pode escolher entre vários métodos para usar WebJobs, incluindo filas e gatilhos.

WebJobs é projetado para processamento de fundo. Pode fazer o processamento de antecedentes que quiser num WebJob. Para obter mais informações sobre webJobs, consulte executar tarefas de [fundo com WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET aplicações Core que são hospedadas no App Service às vezes deixam de responder. Como posso resolver este problema?

Um problema conhecido com uma [versão kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) anterior pode fazer com que uma ASP.NET aplicação Core 1.0 que está hospedada no App Service para parar intermitentemente de responder. Pode também ver esta mensagem: "A aplicação CGI especificada encontrou um erro e o servidor terminou o processo."

Esta edição é corrigida na versão 1.0.2 da Kestrel. Esta versão está incluída na atualização ASP.NET Core 1.0.3. Para resolver este problema, certifique-se de atualizar as dependências da sua aplicação para utilizar o Kestrel 1.0.2. Em alternativa, pode utilizar uma das duas sobras descritas na publicação de blog ASP.NET problemas de [perf lentos Core 1.0 em aplicações web do App Service.](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Não encontro os meus ficheiros de registo na estrutura de ficheiros da minha aplicação web. Como posso encontrá-los?

Se utilizar a funcionalidade Cache Local do Serviço de Aplicações, a estrutura de pastas das pastas LogFiles e Data para a sua instância de Serviço de Aplicações é afetada. Quando a Cache Local é utilizada, as subpastas são criadas nas pastas de registo de armazenamento Files e Data. As subpastas utilizam o padrão de nomeação "identificador único" + carimbo de tempo. Cada subpasta corresponde a uma instância VM em que a aplicação web está a funcionar ou executa.

Para determinar se está a utilizar cache local, verifique o separador de definições de aplicações do serviço de **aplicações.** Se o Cache Local estiver a ser utilizado, a definição de `WEBSITE_LOCAL_CACHE_OPTION` da aplicação está definida para `Always`.

Se não estiver a usar cache local e estiver a passar por este problema, apresente um pedido de apoio.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Vejo a mensagem "Foi feita uma tentativa de aceder a uma tomada de uma forma proibida pelas suas permissões de acesso." Como posso resolver isto?

Este erro ocorre normalmente se as ligações TCP de saída na instância VM estiverem esgotadas. No Serviço de Aplicações, são aplicados limites para o número máximo de ligações de saída que podem ser feitas para cada instância VM. Para mais informações, consulte [os limites numéricos cross-VM](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Este erro também pode ocorrer se tentar aceder a um endereço local a partir da sua aplicação. Para mais informações, consulte [os pedidos de morada local](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Para obter mais informações sobre ligações de saída na sua aplicação web, consulte a publicação de blog sobre [ligações de saída a websites Azure](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Como uso o Visual Studio para depurar remotamente a minha aplicação web do App Service?

Para uma passagem detalhada que lhe mostre como depurar a sua aplicação web usando o Visual Studio, consulte [Remote debug your App Service web app](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
