---
title: Utilize o painel de perfis de insights de aplicação Azure | Microsoft Docs
description: Consulte o estado do Profiler e inicie as sessões de perfis
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499414"
---
# <a name="configure-application-insights-profiler"></a>Configurar perfil de insights de aplicação

## <a name="updated-profiler-agent"></a>Agente de perfis atualizado
As características do gatilho funcionam apenas com a versão 2.6 ou mais recente do agente de perfis. Se estiver a executar um Serviço de Aplicações Azure, o seu agente será atualizado automaticamente. Pode ver que versão do agente está a executar se for ao URL kudu para o seu website e apendam \DiagnosticServices até ao final do mesmo, assim:  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` . O Perfil de Aplicações Webjob deve ser a versão 2.6 ou mais recente. Pode forçar uma atualização reiniciando a sua aplicação web. 

Se estiver a executar o perfil num VM ou Serviço cloud, precisa de ter a versão de extensão 16.0.4 do Windows Azure Diagnostics (WAD) ou mais recente. Pode verificar a versão do WAD iniciando sessão no seu VM e olhando para este diretório: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. O nome do diretório é a versão do WAD que está instalada. O agente Azure VM atualizará o WAD automaticamente quando estiverem disponíveis novas versões.

## <a name="profiler-settings-page"></a>Página de definições de perfis

Para abrir o painel de definições do Perfil de Insights de Aplicação Azure, vá ao painel de desempenho de Insights de Aplicação e, em seguida, selecione o botão **Configure Profiler.**

![Link para abrir a página de definições do Profiler][configure-profiler-entry]

Que abre uma página que se parece com isto:

![Página de definições de perfis][configure-profiler-page]

A página **Configure Application Insights Profiler** tem estas funcionalidades:

| Funcionalidade | Descrição |
|-|-|
Perfil Agora | Inicia sessões de perfis para todas as aplicações que estão ligadas a este caso de Application Insights.
Acionadores | Permite-lhe configurar gatilhos que fazem com que o perfil corra. 
Sessões de perfis recentes | Exibe informações sobre sessões de perfis anteriores.

## <a name="profile-now"></a>Perfil Agora
Esta opção permite iniciar uma sessão de perfis a pedido. Quando clicar neste link, todos os agentes de perfis que estão a enviar dados para esta instância De Insights de Aplicação começarão a capturar um perfil. Após 5 a 10 minutos, a sessão de perfil será apresentada na lista abaixo.

Para que um utilizador desencadeie manualmente uma sessão de perfis, exigem no mínimo o acesso "write" na sua função para o componente Application Insights. Na maioria dos casos, obtém-se este acesso automaticamente e não é necessário trabalho adicional. Se tiver problemas, a função de âmbito de subscrição a adicionar seria a função "Application Insights Component Contributor". [Veja mais sobre o controlo de acesso a funções com a Azure Monitoring](./resources-roles-access-control.md).

## <a name="trigger-settings"></a>Definições de gatilho
![Definições de gatilho Flyout][trigger-settings-flyout]

Ao clicar no botão Gatilhos na barra de menu abre a caixa de definições do gatilho. Pode configurar o gatilho para começar a fazer perfis quando a percentagem de CPU ou utilização de Memória atingir o nível definido.

| Definições | Descrição |
|-|-|
Botão ligado / desligado | Ligado: o perfil pode ser iniciado por este gatilho; Desligado: o profiler não será iniciado por este gatilho.
Limiar de memória | Quando esta percentagem de memória estiver a ser utilizada, o perfil será iniciado.
Duração | Define o tempo de funcionação do perfil quando acionado.
Arrefecimento | Define o tempo que o perfis aguarda antes de verificar a memória ou a utilização do CPU novamente depois de ativado.

## <a name="recent-profiling-sessions"></a>Sessões de Perfis Recentes
Esta secção da página mostra informações sobre sessões de perfis recentes. Uma sessão de perfis representa o período de tempo em que o agente de perfis estava a tirar um perfil numa das máquinas que hospedam a sua aplicação. Pode abrir os perfis a partir de uma sessão clicando numa das linhas. Para cada sessão, mostramos:

| Definições | Descrição |
|-|-|
Desencadeado por | Como a sessão foi iniciada, seja por um gatilho, perfil agora ou amostragem padrão. 
Nome da Aplicação | Nome da aplicação que foi perfilada.
Caso da Máquina | Nome da máquina onde o agente do perfil correu.
CarimboDeDataEHora | Hora em que o perfil foi capturado.
Tracee | Número de vestígios ligados a pedidos individuais.
% de CPU | Percentagem de CPU que estava a ser usada enquanto o perfil estava em funcionamento.
Memória % | Percentagem de memória que estava a ser usada enquanto o perfil estava a funcionar.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Utilize testes de desempenho web para gerar tráfego para a sua aplicação

Pode ativar o Profiler manualmente com um único clique. Suponha que esteja a fazer um teste de desempenho na web. Você precisará de vestígios para ajudá-lo a entender como a sua aplicação web está funcionando sob carga. Ter controlo sobre quando os vestígios são capturados é crucial, porque você sabe quando o teste de carga vai estar em funcionamento. Mas o intervalo de amostragem aleatório pode perdê-lo.

As próximas secções ilustram como este cenário funciona:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Passo 1: Gerar tráfego para a sua aplicação web iniciando um teste de desempenho web

Se a sua aplicação web já tiver tráfego de entrada ou se apenas quiser gerar tráfego manualmente, salte esta secção e continue até ao Passo 2.

1. No portal Application Insights, selecione **Configure**  >  **Performance Testing**. 

1. Para iniciar um novo teste de desempenho, selecione o botão **Novo.**

   ![criar novo teste de desempenho][create-performance-test]

1. No painel de teste de **desempenho novo,** configuure o URL-alvo de teste. Aceite todas as definições predefinidos e, em seguida, selecione **Teste de execução** para começar a executar o teste de carga.

    ![Teste de configuração da carga][configure-performance-test]

    O novo teste é primeiro feito em fila, seguido de um estado de *progresso.*

    ![O teste de carga é submetido e em fila][load-test-queued]

    ![O teste de carga está a decorrer][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Passo 2: Iniciar uma sessão de profiler on demand

1. Quando o teste de carga estiver em execução, inicie o Profiler para capturar vestígios na aplicação web enquanto recebe carga.

1. Vá ao painel de **perfis de configuração.**


### <a name="step-3-view-traces"></a>Passo 3: Ver vestígios

Depois de o Profiler terminar de funcionar, siga as instruções de notificação para ir ao painel de desempenho e ver vestígios.

## <a name="next-steps"></a>Passos seguintes
[Ativar o profiler e visualizar vestígios](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
