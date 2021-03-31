---
title: FaQs de desempenho da aplicação
description: Obtenha respostas para perguntas frequentes sobre disponibilidade, desempenho e problemas de aplicação no Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: dfaeee6a6e2a9728d7e63fb5681c487fbbd6139e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88959005"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>FaQs de desempenho da aplicação para aplicações web em Azure

> [!NOTE]
> Algumas das diretrizes abaixo podem funcionar apenas em Windows ou Linux App Services. Por exemplo, os Serviços de Aplicações Linux funcionam em modo 64 bits por padrão.
>

Este artigo tem respostas para perguntas frequentes (FAQs) sobre problemas de desempenho de aplicações para a [funcionalidade de Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Porque é que a minha aplicação é lenta?

Vários fatores podem contribuir para retardar o desempenho da aplicação. Para obter etapas detalhadas de resolução de problemas, consulte [o desempenho lento da aplicação web troubleshoot](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Como é que resolvo um cenário de consumo elevado de CPU?

Em alguns cenários elevados de consumo de CPU, a sua app pode realmente necessitar de mais recursos informáticos. Nesse caso, considere a escala para um nível de serviço mais elevado para que a aplicação obtenha todos os recursos de que necessita. Outras vezes, o consumo elevado de CPU pode ser causado por um mau ciclo ou por uma prática de codificação. Obter informações sobre o que está a desencadear um aumento do consumo de CPU é um processo em duas partes. Primeiro, criar uma lixeira de processo, e depois analisar o despejo de processo. Para obter mais informações, consulte [Capture e analise um ficheiro de despejo para um consumo elevado de CPU para aplicações web.](/archive/blogs/asiatech/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app)

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Como é que resolvo um cenário de consumo de memória elevado?

Em alguns cenários de alto consumo de memória, a sua app pode realmente necessitar de mais recursos informáticos. Nesse caso, considere a escala para um nível de serviço mais elevado para que a aplicação obtenha todos os recursos de que necessita. Outras vezes, um erro no código pode causar uma fuga de memória. Uma prática de codificação também pode aumentar o consumo de memória. Ter uma visão do que está a desencadear um alto consumo de memória é um processo em duas partes. Primeiro, criar uma lixeira de processo, e depois analisar o despejo de processo. O Diagnóstico de Colisão da Galeria de Extensão do Local de Azure pode executar eficientemente estes dois passos. Para obter mais informações, consulte [Capture e analise um ficheiro de despejo para uma memória alta intermitente para aplicações web.](/archive/blogs/asiatech/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app)

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Como automatizar aplicações web do Serviço de Aplicações utilizando o PowerShell?

Pode utilizar cmdlets PowerShell para gerir e manter aplicações web do Serviço de Aplicações. Na nossa publicação de [blogs, automate web apps hospedados no Azure App Service usando PowerShell,](/archive/blogs/puneetgupta/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way)descrevemos como usar cmdlets PowerShell baseados em recursos do Azure para automatizar tarefas comuns. O blog post também tem código de amostra para várias tarefas de gestão de aplicações web. Para descrições e sintaxe para todas as aplicações web do Serviço de Aplicações, consulte [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Como vejo os registos de eventos da minha aplicação web?

Para ver os registos de eventos da sua aplicação web:

1. Inscreva-se no seu **website da Kudu** `https://*yourwebsitename*.scm.azurewebsites.net` ().
2. No menu, selecione **Debug Console**  >  **CMD**.
3. Selecione a pasta **'Ficheiros de Registo'.**
4. Para visualizar os registos do evento, selecione o ícone do lápis ao lado **eventlog.xml**.
5. Para descarregar os registos, execute o cmdlet PowerShell `Save-AzureWebSiteLog -Name webappname` .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Como posso capturar um despejo de memória em modo de utilizador da minha aplicação web?

Para capturar um despejo de memória em modo de utilizador da sua aplicação web:

1. Inscreva-se no seu **website da Kudu** `https://*yourwebsitename*.scm.azurewebsites.net` ().
2. Selecione o menu **Process Explorer.**
3. Clique com o botão direito no processo **dew3wp.exe** ou no seu processo WebJob.
4. Selecione **Descarregar memória de despejo**  >  **completo**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Como vejo informações de nível de processo para a minha aplicação web?

Tem duas opções para visualizar informações de nível de processo para a sua aplicação web:

*   No portal do Azure:
    1. Abra o **Process Explorer** para a aplicação web.
    2. Para ver os detalhes, selecione o processo **w3wp.exe.**
*   Na consola Kudu:
    1. Inscreva-se no seu **website da Kudu** `https://*yourwebsitename*.scm.azurewebsites.net` ().
    2. Selecione o menu **Process Explorer.**
    3. Para o processo **w3wp.exe,** selecione **Propriedades.**

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Quando navegue na minha aplicação, vejo "Error 403 - This web app is stop". Como posso resolver isto?

Três condições podem causar este erro:

* A aplicação web atingiu um limite de faturação e o seu site foi desativado.
* A aplicação web foi parada no portal.
* A aplicação web atingiu um limite de quota de recursos que pode aplicar-se a um plano de serviço de escala Livre ou Partilhado.

Para ver o que está a causar o erro e resolver o problema, siga os passos nas [Aplicações Web: "Error 403 – This web app is stop"](/archive/blogs/waws/azure-web-apps-error-403-this-web-app-is-stopped).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Onde posso saber mais sobre quotas e limites para vários planos do Serviço de Aplicações?

Para obter informações sobre quotas e limites, consulte [os limites do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Como posso diminuir o tempo de resposta para o primeiro pedido após o tempo de marcha lenta?

Por padrão, as aplicações web são descarregadas se estiverem inativas por um período de tempo definido. Desta forma, o sistema pode conservar recursos. A desvantagem é que a resposta ao primeiro pedido após o descarregamento da aplicação web é mais longa, para permitir que a aplicação web carregue e comece a servir respostas. Nos planos de serviço Basic e Standard, pode ligar a definição **Always On** para manter a aplicação sempre carregada. Isto elimina tempos de carga mais longos após a aplicação ficar inativa. Para alterar a definição **Always On:**

1. No portal Azure, vá à sua aplicação web.
2. Selecione **configuração**
3. Selecione **definições gerais**.
4. Para **Sempre Ligado**, selecione **On**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Como é que ingi o rastreio de pedidos falhados?

Para ligar o rastreio de pedido falhado:

1. No portal Azure, vá à sua aplicação web.
3. Selecione **todos os**  >  **registos de diagnóstico de definições**.
4. Para **rastreio de pedido falhado**, selecione **On**.
5. Selecione **Guardar**.
6. Na lâmina da aplicação web, selecione **Ferramentas.**
7. Selecione **Visual Studio Online**.
8. Se a definição não estiver **acesa**, selecione **On**.
9. Selecione **Go**.
10. Selecione **Web.config**.
11. Em system.webServer, adicione esta configuração (para capturar um URL específico):

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
13. Para descarregar os vestígios de pedidos falhados, no [portal,](https://portal.azure.com)aceda ao seu website.
15. Selecione **Ferramentas**  >  **Kudu**  >  **Go**.
18. No menu, selecione **Debug Console**  >  **CMD**.
19. Selecione a pasta **'Ficheiros' e,** em seguida, selecione a pasta com um nome que comece por **W3SVC**.
20. Para ver o ficheiro XML, selecione o ícone do lápis.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Vejo a mensagem "Processo do Trabalhador solicitado reciclar devido ao limite de 'Memória por cento'." Como é que abordo esta questão?

A quantidade máxima disponível de memória para um processo de 32 bits (mesmo num sistema operativo de 64 bits) é de 2 GB. Por padrão, o processo de trabalhador está definido para 32 bits no Serviço de Aplicações (para compatibilidade com aplicações web antigas).

Considere mudar para processos de 64 bits para que possa tirar partido da memória adicional disponível na sua função de Web Worker. Isto desencadeia um reinício de uma aplicação web, por isso, agende em conformidade.

Note também que um ambiente de 64 bits requer um plano de serviço Básico ou Standard. Os planos gratuitos e partilhados funcionam sempre num ambiente de 32 bits.

Para obter mais informações, consulte [aplicações web Configure no Serviço de Aplicações.](configure-common.md)

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Porque é que o meu pedido acaba depois de 230 segundos?

O Azure Load Balancer tem uma definição de tempo limite de marcha lenta por defeito de quatro minutos. Trata-se geralmente de um prazo de resposta razoável para um pedido web. Se a sua aplicação web necessitar de processamento de fundo, recomendamos a utilização de Azure WebJobs. A aplicação web Azure pode ligar para WebJobs e ser notificada quando o processamento de fundo estiver terminado. Pode escolher entre vários métodos para usar WebJobs, incluindo filas e gatilhos.

WebJobs é projetado para processamento de fundo. Você pode fazer o processamento de fundo que quiser num WebJob. Para obter mais informações sobre webJobs, consulte [executar tarefas de fundo com WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET aplicações Core que estão hospedadas no Serviço de Aplicações por vezes deixam de responder. Como é que resolvo este problema?

Um problema conhecido com uma [versão kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) anterior pode fazer com que uma aplicação Core 1.0 ASP.NET que está hospedada no Serviço de Aplicações pare de responder intermitentemente. Também pode ver esta mensagem: "A aplicação CGI especificada encontrou um erro e o servidor terminou o processo."

Esta emissão é corrigida na versão Kestrel 1.0.2. Esta versão está incluída na atualização core 1.0.3 ASP.NET. Para resolver este problema, certifique-se de atualizar as dependências da sua aplicação para utilizar o Kestrel 1.0.2. Em alternativa, pode utilizar uma das duas soluções alternativas descritas no post do blog [ASP.NET problemas de perf lentos core 1.0 em aplicações web do App Service](/archive/blogs/waws/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Não encontro os meus ficheiros de registo na estrutura de ficheiros da minha aplicação web. Como posso encontrá-los?

Se utilizar a funcionalidade Cache Local do Serviço de Aplicações, a estrutura de pastas dos Ficheiros de Registo e pastas de Dados para a sua instância do Serviço de Aplicações é afetada. Quando a Cache Local é utilizada, as sub-dobradeiras são criadas nas pastas de registo de armazenamento e de dados. As sub-dobradeiras utilizam o padrão de nomeação "identificador único" + carimbo temporal. Cada subpasta corresponde a uma instância VM em que a aplicação web está em execução ou foi executada.

Para determinar se está a utilizar a Cache Local, consulte o separador **de definições de aplicações** do Serviço de Aplicação de Aplicações de Aplicações. Se a Cache Local estiver a ser utilizada, a definição da aplicação `WEBSITE_LOCAL_CACHE_OPTION` está definida para `Always` .

Se não estiver a utilizar a Cache Local e estiver a passar por este problema, envie um pedido de apoio.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Vejo a mensagem "Foi feita uma tentativa de acesso a uma tomada de uma forma proibida pelas suas permissões de acesso." Como posso resolver isto?

Este erro ocorre normalmente se as ligações TCP de saída na instância VM estiverem esgotadas. No Serviço de Aplicações, são aplicados limites para o número máximo de ligações de saída que podem ser feitas para cada instância VM. Para obter mais informações, consulte [os limites numéricos Cross-VM](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Este erro também pode ocorrer se tentar aceder a um endereço local a partir da sua aplicação. Para mais informações, consulte [os pedidos de endereço local.](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)

Para obter mais informações sobre as ligações de saída na sua aplicação web, consulte o post de blog sobre [ligações de saída para websites Azure](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Como uso o Visual Studio para depurar remotamente a minha aplicação web do App Service?

Para uma passagem detalhada que lhe mostre como desorgaçar a sua aplicação web utilizando o Visual Studio, consulte [Remote depurar a sua aplicação web do Serviço de Aplicações](/archive/blogs/benjaminperkins/remote-debug-your-azure-app-service-web-app).