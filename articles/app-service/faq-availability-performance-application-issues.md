---
title: Desempenho do aplicativo FAQs - serviço de aplicações do Azure | Documentos da Microsoft
description: Obtenha respostas às perguntas mais frequentes sobre a disponibilidade, desempenho e problemas de aplicativos na funcionalidade de aplicações Web do serviço de aplicações do Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: ae59f54de9c02bfc4e538dd10c809ba398d599ee
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565970"
---
> [!NOTE]
> Alguns das diretrizes abaixo só pode funcionar no Windows ou dos serviços de aplicações do Linux. Por exemplo, os serviços de aplicações do Linux executado no modo de 64 bits por predefinição.
>

# <a name="application-performance-faqs-for-web-apps-in-azure"></a>FAQs de desempenho da aplicação para aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas de desempenho de aplicativos para o [funcionalidade de aplicações Web do serviço de aplicações do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>É por que motivo a minha aplicação lenta?

Vários fatores pode contribuir para diminuir o desempenho da aplicação. Para mais passos de resolução de problemas, consulte [o desempenho de aplicação web lenta de resolução de problemas](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Como posso resolver um cenário de consumo de CPU elevado?

Em alguns cenários de consumo de CPU elevados, a aplicação verdadeiramente pode exigir mais recursos de computação. Nesse caso, considere Dimensionar para um escalão de serviço superior, para que o aplicativo obtém todos os recursos de que necessita. Outras vezes, alto consumo de CPU poderá ser causado por um loop incorreto ou por uma prática de codificação. Obter informações sobre o que é o acionamento do maior consumo de CPU é um processo de duas partes. Em primeiro lugar, crie um despejo de processo e, em seguida, analisar o despejo de processo. Para obter mais informações, consulte [capturar e analisar um arquivo de despejo para consumo de CPU elevado para aplicações Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Como posso resolver um cenário de consumo de memória elevada?

Em alguns cenários de consumo de memória elevada, a aplicação verdadeiramente pode exigir mais recursos de computação. Nesse caso, considere Dimensionar para um escalão de serviço superior, para que o aplicativo obtém todos os recursos de que necessita. Outras vezes, um bug no código pode causar um vazamento de memória. Uma prática de codificação também pode aumentar o consumo de memória. A obter informações sobre o que está disparando o consumo é um processo de duas partes de memória elevada. Em primeiro lugar, crie um despejo de processo e, em seguida, analisar o despejo de processo. Diagnosticador de falhas da Galeria de extensão de Site do Azure com eficiência pode executar ambos estes passos. Para obter mais informações, consulte [capturar e analisar um arquivo de despejo de memória elevada intermitente, para aplicações Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Como posso automatizar aplicações web do serviço de aplicações com o PowerShell?

Pode utilizar cmdlets do PowerShell para gerir e manter aplicações web do serviço de aplicações. Nossa mensagem de blogue [automatizar aplicações web alojadas no serviço de aplicações do Azure com o PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), descrevemos como utilizar cmdlets do PowerShell com base no Azure Resource Manager para automatizar tarefas comuns. A mensagem de blogue também tem o código de exemplo para várias tarefas de gestão de aplicações web. Para obter descrições e sintaxe para todos os cmdlets de aplicações de serviço de aplicações web, consulte [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Como faço para visualizar registos de eventos do meu aplicativo web?

Para ver registos de eventos da aplicação web:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. No menu, selecione **consola de depuração** > **CMD**.
3. Selecione o **LogFiles** pasta.
4. Para ver registos de eventos, selecione o ícone de lápis junto a **eventlog.xml**.
5. Para transferir os registos, execute o cmdlet do PowerShell `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Como posso capturar um despejo de memória do modo de usuário do meu aplicativo web?

Para capturar um despejo de memória do modo de utilizador da sua aplicação web:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o **Process Explorer** menu.
3. Com o botão direito a **w3wp.exe** processo ou seu processo de trabalho Web.
4. Selecione **transferir o despejo de memória** > **completa de despejo**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Como faço para visualizar informações de nível de processo para a minha aplicação web?

Tem duas opções para a visualização de informações de nível de processo para a sua aplicação web:

*   No portal do Azure:
    1. Abra o **Process Explorer** para a aplicação web.
    2. Para ver os detalhes, selecione o **w3wp.exe** processo.
*   Na consola Kudu:
    1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Selecione o **Process Explorer** menu.
    3. Para o **w3wp.exe** processo, selecione **propriedades**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Quando faço para navegar para a minha aplicação, vejo "Erro 403 - esta aplicação web está parado." Como posso resolver isto?

Três condições podem causar este erro:

* A aplicação web atingiu um limite de faturação e o site foi desabilitado.
* A aplicação web foi parada no portal.
* A aplicação web atingiu um limite de quota de recursos que pode ser aplicadas a um gratuito ou partilhado plano do serviço de dimensionamento.

Para ver o que está causando o erro e resolver o problema, siga os passos em [aplicações Web: "Erro 403 – esta aplicação web está parado"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Onde posso saber mais sobre quotas e limites para vários planos de serviço de aplicações?

Para obter informações sobre quotas e limites, consulte [limites do serviço de aplicações](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Como posso reduzir o tempo de resposta para a primeira solicitação após o tempo de inatividade?

Por predefinição, aplicações web são descarregadas que estejam Inativos durante um determinado período de tempo. Dessa forma, o sistema pode poupar recursos. A desvantagem é que a resposta ao primeiro pedido depois da aplicação web é descarregada é mais tempo, para permitir que a aplicação web para carregar e começa a servir as respostas. Nos planos de serviço básico e Standard, pode ativar a **Always On** definição para que o aplicativo sempre carregado. Isso elimina a tempos de carregamento mais tempo depois da aplicação está ociosa. Para alterar o **Always On** definição:

1. No portal do Azure, aceda à sua aplicação web.
2. Selecione **as definições da aplicação**.
3. Para **Always On**, selecione **no**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Como ativar o rastreio de pedidos falhados

Para ativar o rastreio de pedido falhado:

1. No portal do Azure, aceda à sua aplicação web.
3. Selecione **todas as definições** > **os registos de diagnóstico**.
4. Para **Failed Request Tracing**, selecione **no**.
5. Selecione **Guardar**.
6. No painel da aplicação web, selecione **ferramentas**.
7. Selecione **Visual Studio Online**.
8. Se a definição não estiver **nos**, selecione **no**.
9. Selecione **ir**.
10. Selecione **Web. config**.
11. No System. webServer, adicione esta configuração (para capturar um URL específico):

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
12. Para resolver problemas de desempenho lento, adicione esta configuração (se o pedido de captura está a demorar mais de 30 segundos):
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
13. Para transferir os rastreios de pedido falhado, no [portal](https://portal.azure.com), aceda ao seu Web site.
15. Selecione **ferramentas** > **Kudu** > **ir**.
18. No menu, selecione **consola de depuração** > **CMD**.
19. Selecione o **LogFiles** e, em seguida, selecione a pasta com um nome que começa com **W3SVC**.
20. Para ver o ficheiro XML, selecione o ícone de lápis.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Posso ver a mensagem "O processo de trabalho solicitado Reciclagem devido ao limite de"Percentagem de memória"." Como posso resolver esse problema?

A quantidade disponível máxima de memória para um processo de 32 bits (mesmo num sistema operativo de 64 bits) é de 2 GB. Por predefinição, o processo de trabalho está definido para 32-bit no serviço de aplicações (para compatibilidade com aplicativos web herdados).

Considere mudar para processos de 64 bits, para que possa tirar partido da memória adicional disponível na sua função de trabalhador Web. Isto aciona um reinício de aplicação web, por isso, agende adequadamente.

Observe também que um ambiente de 64 bits requer um plano de serviço básico ou Standard. Gratuito e partilhado planos sempre executados num ambiente de 32 bits.

Para obter mais informações, consulte [configurar aplicações web no serviço de aplicações](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Por que motivo o meu tempo limite do pedido após segundos 230?

O Balanceador de carga do Azure tem uma definição de tempo limite de inatividade do padrão de quatro minutos. Normalmente, é um limite de tempo de resposta razoáveis para uma solicitação da web. Se a sua aplicação web requer processamento em segundo plano, recomendamos que utilize o WebJobs do Azure. A aplicação web do Azure pode chamar o WebJobs e ser notificada quando o processamento em segundo plano estiver concluído. Pode escolher entre vários métodos para utilizar o WebJobs, incluindo filas e acionadores.

WebJobs foi concebido para processamento em segundo plano. Pode fazer muito mais processamento em segundo plano que quiser um WebJob. Para obter mais informações sobre o WebJobs, consulte [executar tarefas em segundo plano com o WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Aplicações de ASP.NET Core que estão alojadas no serviço de aplicações, às vezes, deixam de responder. Como corrigir este problema?

Um problema conhecido com anterior [versão Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) pode fazer com que uma aplicação ASP.NET Core 1.0, que está alojada no serviço de aplicações intermitentemente deixe de responder. Também poderá ver esta mensagem: "A aplicação CGI especificada encontrou um erro e o servidor terminou o processo".

Este problema é resolvido de Kestrel versão 1.0.2. Esta versão está incluída na atualização do ASP.NET Core 1.0.3. Para resolver este problema, certifique-se de que atualizar suas dependências de aplicação para utilizar o Kestrel 1.0.2. Em alternativa, pode utilizar uma das duas soluções que são descritas na mensagem de blogue [problemas de desempenho lento do ASP.NET Core 1.0 nas aplicações do serviço de aplicações web](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Não consigo encontrar meus arquivos de log na estrutura de arquivo do meu aplicativo web. Como posso encontrá-los?

Se usar o recurso de Local Cache do serviço de aplicações, a estrutura de pastas das pastas LogFiles e os dados para a sua instância do serviço de aplicações são afetados. Quando é utilizado o Local Cache, são criadas subpastas em pastas de dados e o armazenamento LogFiles. As subpastas utilizam a nomenclatura padrão "Identificador exclusivo" + o carimbo de data / hora. Cada subpasta corresponde a uma instância de VM em que a aplicação web está em execução ou foi executada.

Para determinar se estiver a utilizar a Local Cache, verifique o serviço de aplicações **as definições da aplicação** separador. Se a Local Cache está a ser utilizado, a definição de aplicação `WEBSITE_LOCAL_CACHE_OPTION` está definido como `Always`.

Se não estiver a utilizar o Local Cache e está a ter este problema, submeta um pedido de suporte.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Posso ver a mensagem "foi efetuada uma tentativa para aceder a um soquete de uma forma proibido por suas permissões de acesso." Como posso resolver isto?

Este erro ocorre normalmente se as ligações de TCP de saída na instância de VM ficam esgotadas. No serviço de aplicações, os limites são impostos para o número máximo de ligações de saída que podem ser feitas para cada instância VM. Para obter mais informações, consulte [limites numéricas da VM entre](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Este erro também poderá ocorrer se tentar acessar um endereço local a partir da sua aplicação. Para obter mais informações, consulte [pedidos de endereço Local](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Para obter mais informações sobre ligações de saída na sua aplicação web, consulte o mensagem de blogue sobre [ligações para Web sites do Azure de saída](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Como posso utilizar o Visual Studio remoto depurar meu aplicativo web do serviço de aplicações?

Para obter instruções detalhadas que lhe mostra como depurar a sua aplicação web com o Visual Studio, consulte [remoto depurar a sua aplicação web do serviço de aplicações](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
