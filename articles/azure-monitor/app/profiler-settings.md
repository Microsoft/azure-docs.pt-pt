---
title: Utilize o painel de definições do perfil de insights de aplicação Azure / Microsoft Docs
description: Consulte o estado do Profiler e inicie sessões de perfis
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671635"
---
# <a name="configure-application-insights-profiler"></a>Configure Perfil de Insights de Aplicação

## <a name="updated-profiler-agent"></a>Agente de perfil atualizado
O gatilho funciona apenas com a versão 2.6 ou mais recente do agente de perfis. Se estiver a executar um Serviço de Aplicações Azure, o seu agente será atualizado automaticamente. Pode ver que versão do agente está a executar se for ao URL kudu para o seu website e anexar \DiagnosticServices até ao fim, como esta: https://yourwebsite.scm.azurewebsites.net/diagnosticservices. O Webjob do Perfil de Insights de Aplicação deve ver a versão 2.6 ou mais recente. Pode forçar uma atualização reiniciando a sua aplicação web. 

Se estiver a executar o perfil num VM ou cloud Service, precisa de ter a versão 16.0.4 ou mais recente do Windows Azure Diagnostics (WAD) ou mais recente. Pode verificar a versão do WAD iniciando login no seu VM e olhando para este diretório: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. O nome do diretório é a versão do WAD que está instalada. O agente Azure VM atualizará o WAD automaticamente quando estiverem disponíveis novas versões.

## <a name="profiler-settings-page"></a>Página de definições de perfis

Para abrir o painel de definições do perfil de insights de aplicação Azure, vá ao painel de desempenho de Insights de Aplicação e, em seguida, selecione o botão **'Perfil de Configuração'.**

![Link para abrir página de definições de Perfil][configure-profiler-entry]

Abre uma página que se parece com esta:

![Página de definições de perfis][configure-profiler-page]

A página de Perfilde Insights de **Aplicação Configure** tem estas funcionalidades:

| | |
|-|-|
Perfil Agora | Inicia sessões de perfis para todas as aplicações que estão ligadas a este exemplo de Insights de Aplicação.
Acionadores | Permite configurar gatilhos que fazem com que o perfil seja executado. 
Sessões recentes de perfis | Exibe informações sobre sessões de perfis passados.

## <a name="profile-now"></a>Perfil Agora
Esta opção permite-lhe iniciar uma sessão de perfis a pedido. Quando clicar neste link, todos os agentes de perfil que estão a enviar dados para esta instância de Insights de Aplicação começarão a capturar um perfil. Após 5 a 10 minutos, a sessão de perfil aparecerá na lista abaixo.

Para que um utilizador desencadeie manualmente uma sessão de perfis, exige miná-lo no acesso mínimo de "escrever" sobre a sua função para o componente Application Insights. Na maioria dos casos, obtém-se este acesso automaticamente e não é necessário trabalho adicional. Se tiver problemas, a função de âmbito de subscrição a adicionar seria a função "Application Insights Component Contributor". Veja mais sobre o controlo de acesso de [funções com a Monitorização Azure](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Definições do gatilho
![Definições de gatilho Flyout][trigger-settings-flyout]

Clicar no botão Triggers na barra de menu abre a caixa de definições do gatilho. Pode configurar o gatilho para iniciar perfis quando a percentagem de UTILIZAÇÃO de CPU ou Memória atingir o nível que definiu.

| | |
|-|-|
On / Off Botão | Em: o perfil pode ser iniciado por este gatilho; Desligado: o perfil não será iniciado por este gatilho.
Limiar de memória | Quando esta percentagem de memória estiver em uso, o perfil será iniciado.
Duração | Define o tempo que o perfil executará quando acionado.
Arrefecimento | Define o tempo que o profiler irá esperar antes de verificar a memória ou o uso do CPU novamente depois de acionado.

## <a name="recent-profiling-sessions"></a>Sessões recentes de perfis
Esta secção da página mostra informações sobre sessões de perfis recentes. Uma sessão de perfis representa o período de tempo em que o agente de perfis estava a tirar um perfil numa das máquinas que hospedam a sua aplicação. Pode abrir os perfis a partir de uma sessão clicando numa das linhas. Para cada sessão, mostramos:

| | |
|-|-|
Desencadeado por | Como a sessão foi iniciada, seja por um gatilho, Profile Now, ou por amostragem padrão. 
Nome da Aplicação | Nome da aplicação que foi perfilada.
Instância da Máquina | Nome da máquina que o agente de perfis correu.
Carimbo de data/hora | Está na altura em que o perfil foi capturado.
Tracee | Número de vestígios ligados a pedidos individuais.
CPU % | Percentagem de CPU que estava a ser usada enquanto o perfil estava a funcionar.
Memória % | Percentagem de memória que estava a ser usada enquanto o profiler estava a funcionar.

## <a id="profileondemand"></a>Utilize testes de desempenho web para gerar tráfego na sua aplicação

Pode ativar o Profiler manualmente com um único clique. Suponha que esteja a fazer um teste de desempenho na web. Você precisará de vestígios para ajudá-lo a entender como a sua aplicação web está a funcionar sob carga. Ter controlo sobre quando os vestígios são capturados é crucial, porque sabe quando o teste de carga estará em execução. Mas o intervalo de amostragem aleatório pode perdê-lo.

As próximas secções ilustram como este cenário funciona:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Passo 1: Gere tráfego para a sua aplicação web iniciando um teste de desempenho web

Se a sua aplicação já tiver tráfego de entrada ou se apenas quiser gerar tráfego manualmente, ignore esta secção e continue até ao Passo 2.

1. No portal Insights da Aplicação, **selecione Configure** > Teste de **Desempenho**. 

1. Para iniciar um novo teste de desempenho, selecione o botão **Novo.**

   ![criar novo teste de desempenho][create-performance-test]

1. No novo painel de teste de **desempenho,** configure o URL do alvo de teste. Aceite todas as definições predefinidas e, em seguida, selecione **teste executar** para começar a executar o teste de carga.

    ![Configure teste de carga][configure-performance-test]

    O novo teste está em fila primeiro, seguido de um estado em *curso.*

    ![O teste de carga é submetido e feito em fila][load-test-queued]

    ![O teste de carga está em andamento][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Passo 2: Iniciar uma sessão de Profiler a pedido

1. Quando o teste de carga estiver em execução, inicie o Profiler para capturar vestígios na aplicação web enquanto está a receber carga.

1. Vá ao painel de perfis **de configuração.**


### <a name="step-3-view-traces"></a>Passo 3: Ver vestígios

Depois de o Profiler terminar a execução, siga as instruções na notificação para ir ao painel performance e ver os vestígios.

## <a name="next-steps"></a>Passos seguintes
[Ativar perfis e ver vestígios](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

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
