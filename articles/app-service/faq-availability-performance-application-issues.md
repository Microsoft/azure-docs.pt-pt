---
title: Perguntas frequentes sobre desempenho de aplicativos – serviço de Azure App | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre problemas de disponibilidade, desempenho e aplicativos no recurso de aplicativos Web do serviço de Azure App.
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
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 2b9689eeecb0549fae9f7131dd424e15c040b042
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073170"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Perguntas frequentes de desempenho do aplicativo para aplicativos Web no Azure

> [!NOTE]
> Algumas das diretrizes a seguir podem funcionar apenas nos serviços de aplicativos do Windows ou do Linux. Por exemplo, os serviços de aplicativos do Linux são executados no modo de 64 bits por padrão.
>

Este artigo tem respostas para perguntas frequentes sobre problemas de desempenho de aplicativos para o [recurso de aplicativos Web do serviço de Azure app](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Por que meu aplicativo está lento?

Vários fatores podem contribuir para o desempenho lento do aplicativo. Para obter etapas de solução de problemas detalhadas, consulte [solucionar problemas de desempenho do aplicativo Web lento](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Como fazer solucionar um cenário de alto consumo de CPU?

Em alguns cenários de alto consumo de CPU, seu aplicativo pode realmente exigir mais recursos de computação. Nesse caso, considere a possibilidade de dimensionar para uma camada de serviço superior para que o aplicativo obtenha todos os recursos necessários. Em outras ocasiões, o alto consumo de CPU pode ser causado por um loop incorreto ou por uma prática de codificação. Obter informações sobre o que está disparando o aumento do consumo de CPU é um processo de duas partes. Primeiro, crie um despejo de processo e, em seguida, analise o despejo do processo. Para obter mais informações, consulte [capturar e analisar um arquivo de despejo para alto consumo de CPU para aplicativos Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Como fazer solucionar um cenário de alto consumo de memória?

Em alguns cenários de alto consumo de memória, seu aplicativo pode realmente exigir mais recursos de computação. Nesse caso, considere a possibilidade de dimensionar para uma camada de serviço superior para que o aplicativo obtenha todos os recursos necessários. Outras vezes, um bug no código pode causar um vazamento de memória. Uma prática de codificação também pode aumentar o consumo de memória. Obter informações sobre o que está disparando o alto consumo de memória é um processo de duas partes. Primeiro, crie um despejo de processo e, em seguida, analise o despejo do processo. O diagnóstico de falhas da Galeria de extensões de site do Azure pode executar com eficiência essas duas etapas. Para obter mais informações, consulte [capturar e analisar um arquivo de despejo para uma memória alta intermitente para aplicativos Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Como fazer automatizar aplicativos Web do serviço de aplicativo usando o PowerShell?

Você pode usar os cmdlets do PowerShell para gerenciar e manter aplicativos Web do serviço de aplicativo. Em nossa postagem de blog [automatizar aplicativos Web hospedados no serviço Azure App usando o PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), descrevemos como usar cmdlets do PowerShell baseados em Azure Resource Manager para automatizar tarefas comuns. A postagem do blog também tem um código de exemplo para várias tarefas de gerenciamento de aplicativos Web. Para obter descrições e sintaxe para todos os cmdlets de aplicativos Web do serviço de aplicativo, consulte [AZ. sites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Como fazer exibir os logs de eventos do meu aplicativo Web?

Para exibir os logs de eventos de seu aplicativo Web:

1. Entre no site do [kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. No menu, selecione **console** > de depuração**cmd**.
3. Selecione a pasta LogFiles.
4. Para exibir os logs de eventos, selecione o ícone de lápis ao lado de **EventLog. xml**.
5. Para baixar os logs, execute o cmdlet `Save-AzureWebSiteLog -Name webappname`do PowerShell.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Como fazer capturar um despejo de memória do modo de usuário do meu aplicativo Web?

Para capturar um despejo de memória de modo de usuário de seu aplicativo Web:

1. Entre no site do [kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o menu **Process Explorer** .
3. Clique com o botão direito do mouse no processo **w3wp. exe** ou no processo WebJob.
4. Selecione **baixar memória** > despejo de despejo**completo**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Como fazer exibir informações de nível de processo para meu aplicativo Web?

Você tem duas opções para exibir informações de nível de processo para seu aplicativo Web:

*   No portal do Azure:
    1. Abra o **Gerenciador de processos** para o aplicativo Web.
    2. Para ver os detalhes, selecione o processo **w3wp. exe** .
*   No console do kudu:
    1. Entre no site do [kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Selecione o menu **Process Explorer** .
    3. Para o processo **w3wp. exe** , selecione **Propriedades**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Quando navego até meu aplicativo, vejo "erro 403-este aplicativo Web foi interrompido". Como fazer resolver isso?

Três condições podem causar esse erro:

* O aplicativo Web atingiu um limite de cobrança e seu site foi desabilitado.
* O aplicativo Web foi interrompido no Portal.
* O aplicativo Web atingiu um limite de cota de recursos que pode ser aplicado a um plano de serviço de escala livre ou compartilhado.

Para ver o que está causando o erro e resolver o problema, siga as etapas em [aplicativos Web: "Erro 403 – este aplicativo Web está parado"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Onde posso saber mais sobre cotas e limites para vários planos do serviço de aplicativo?

Para obter informações sobre cotas e limites, consulte [limites do serviço de aplicativo](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Como fazer diminuir o tempo de resposta para a primeira solicitação após o tempo ocioso?

Por padrão, os aplicativos Web serão descarregados se estiverem ociosos por um período de tempo definido. Dessa forma, o sistema pode conservar recursos. A desvantagem é que a resposta à primeira solicitação depois que o aplicativo Web é descarregado é mais longa, para permitir que o aplicativo Web seja carregado e comece a fornecer respostas. Nos planos de serviço básico e Standard, você pode ativar a configuração de **Always on** para manter o aplicativo sempre carregado. Isso elimina tempos de carregamento mais longos depois que o aplicativo está ocioso. Para alterar a configuração de **Always on** :

1. No portal do Azure, acesse seu aplicativo Web.
2. Selecione **configurações do aplicativo**.
3. Para **Always on**, selecione **ativado**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Como fazer ativar o rastreamento de solicitação com falha?

Para ativar o rastreamento de solicitação com falha:

1. No portal do Azure, acesse seu aplicativo Web.
3. Selecione **todas as configurações** > **logs de diagnóstico**.
4. Para **rastreamento de solicitação com falha**, selecione **ativado**.
5. Selecione **Guardar**.
6. Na folha do aplicativo Web, selecione **ferramentas**.
7. Selecione **Visual Studio online**.
8. Se a configuração não estiver **ativada**,selecione ativada.
9. Selecione **ir**.
10. Selecione **Web. config**.
11. Em System. NetServer, adicione esta configuração (para capturar uma URL específica):

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
12. Para solucionar problemas de desempenho lento, adicione essa configuração (se a solicitação de captura estiver demorando mais de 30 segundos):
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
13. Para baixar os rastreamentos de solicitação com falha, no [portal](https://portal.azure.com), acesse seu site.
15. Selecione **ferramentas** > kuduir > para.
18. No menu, selecione **console** > de depuração**cmd**.
19. Selecione a pasta LogFiles e, em seguida, selecione a pasta com um nome que começa com **W3SVC**.
20. Para ver o arquivo XML, selecione o ícone de lápis.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Vejo a mensagem "o processo de trabalho solicitou reciclagem devido ao limite de ' porcentagem de memória '." Como fazer resolver esse problema?

A quantidade máxima de memória disponível para um processo de 32 bits (mesmo em um sistema operacional de 64 bits) é de 2 GB. Por padrão, o processo de trabalho é definido como 32 bits no serviço de aplicativo (para compatibilidade com aplicativos Web herdados).

Considere a possibilidade de alternar para os processos de 64 bits para que você possa aproveitar a memória adicional disponível em sua função Web Worker. Isso dispara uma reinicialização do aplicativo Web, portanto, agende adequadamente.

Observe também que um ambiente de 64 bits requer um plano de serviço básico ou Standard. Os planos gratuito e compartilhado sempre são executados em um ambiente de 32 bits.

Para obter mais informações, consulte [configurar aplicativos Web no serviço de aplicativo](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Por que minha solicitação atingiu o tempo limite após 230 segundos?

Azure Load Balancer tem uma configuração de tempo limite de ociosidade padrão de quatro minutos. Esse é geralmente um limite de tempo de resposta razoável para uma solicitação da Web. Se seu aplicativo Web exigir processamento em segundo plano, recomendamos o uso de Azure WebJobs. O aplicativo Web do Azure pode chamar webjobs e ser notificado quando o processamento em segundo plano for concluído. Você pode escolher entre vários métodos para usar trabalhos Web, incluindo filas e gatilhos.

O webjobs foi projetado para processamento em segundo plano. Você pode fazer tanto processamento em segundo plano quanto desejar em um WebJob. Para obter mais informações sobre trabalhos Web, consulte [executar tarefas em segundo plano com trabalhos](webjobs-create.md)Web.

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Os aplicativos ASP.NET Core hospedados no serviço de aplicativo às vezes param de responder. Como fazer corrigir esse problema?

Um problema conhecido com uma versão anterior do [Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) pode fazer com que um aplicativo ASP.NET Core 1,0 hospedado no serviço de aplicativo pare de responder intermitentemente. Você também poderá ver esta mensagem: "O aplicativo CGI especificado encontrou um erro e o servidor encerrou o processo."

Esse problema é corrigido na versão Kestrel do 1.0.2. Esta versão está incluída na atualização do ASP.NET Core 1.0.3. Para resolver esse problema, certifique-se de atualizar suas dependências de aplicativo para usar o Kestrel 1.0.2. Como alternativa, você pode usar uma das duas soluções alternativas descritas na postagem do blog [ASP.NET Core 1,0 problemas de desempenho lentos nos aplicativos Web do serviço de aplicativo](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Não consigo encontrar meus arquivos de log na estrutura de arquivos do meu aplicativo Web. Como posso encontrá-los?

Se você usar o recurso de cache local do serviço de aplicativo, a estrutura de pastas das pastas de arquivos de log e de dados para sua instância do serviço de aplicativo será afetada. Quando o cache local é usado, as subpastas são criadas nas pastas de dados e arquivos de log de armazenamento. As subpastas usam o padrão de nomenclatura "identificador exclusivo" + carimbo de data/hora. Cada subpasta corresponde a uma instância de VM na qual o aplicativo Web está em execução ou foi executado.

Para determinar se você está usando o cache local, verifique a guia **configurações do aplicativo** do serviço de aplicativo. Se o cache local estiver sendo usado, a configuração `WEBSITE_LOCAL_CACHE_OPTION` do aplicativo será `Always`definida como.

Se você não estiver usando o cache local e estiver enfrentando esse problema, envie uma solicitação de suporte.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Vejo a mensagem "foi feita uma tentativa de acessar um soquete de uma maneira proibida por suas permissões de acesso". Como fazer resolver isso?

Esse erro normalmente ocorre se as conexões TCP de saída na instância de VM estiverem esgotadas. No serviço de aplicativo, os limites são impostos para o número máximo de conexões de saída que podem ser feitas para cada instância de VM. Para obter mais informações, consulte [limites numéricos de VM cruzada](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Esse erro também pode ocorrer se você tentar acessar um endereço local do seu aplicativo. Para obter mais informações, consulte [solicitações de endereço local](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Para obter mais informações sobre conexões de saída em seu aplicativo Web, consulte a postagem de blog sobre [conexões de saída para sites do Azure](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Como fazer usar o Visual Studio para depurar remotamente meu aplicativo Web do serviço de aplicativo?

Para obter uma explicação detalhada que mostra como depurar seu aplicativo Web usando o Visual Studio, consulte [depuração remota do aplicativo Web do serviço de aplicativo](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
