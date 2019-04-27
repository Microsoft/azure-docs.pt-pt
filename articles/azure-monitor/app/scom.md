---
title: Integração do SCOM – com o Application Insights | Documentos da Microsoft
description: Se for um utilizador do SCOM, monitorizar o desempenho e diagnosticar problemas com o Application Insights. Dashboards abrangentes, alertas inteligentes, poderosas ferramentas de diagnóstico e consultas de análise.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: a08a67a78362325e29b1002b44f390a94e78888a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784882"
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Monitorização de Desempenho de Aplicações com o Application Insights para SCOM
Se utilizar o System Center Operations Manager (SCOM) para gerenciar seus servidores, pode monitorizar o desempenho e diagnosticar problemas de desempenho com a ajuda do [do Azure Application Insights](../../azure-monitor/app/asp-net.md). Application Insights monitoriza os pedidos recebidos da sua aplicação web, REST e chamadas SQL, exceções e rastreios de registos de saída. Ele fornece a dashboards com gráficos de métricas e alertas inteligentes, bem como poderosa pesquisa de diagnóstico e consultas de análise sobre esta telemetria. 

Pode alternar na monitorização do Application Insights através de um pacote de gestão do SCOM.

> [!IMPORTANT]
> Este pacote de gestão do System Center Operations Manager está agora **preterido**. Ele não suporta a mais recente SDKs do Application Insights e já não é recomendado.

## <a name="before-you-start"></a>Antes de começar
Partimos do princípio:

* Servidores web estiver familiarizado com o SCOM e que utilize o SCOM 2012 R2 ou 2016 para gerir o IIS.
* Já ter instalado o nos seus servidores de uma aplicação web que pretende monitorizar com o Application Insights.
* Versão da estrutura de aplicação é o .NET 4.5 ou posterior.
* Tem acesso a uma subscrição no [Microsoft Azure](https://azure.com) e pode iniciar sessão para o [portal do Azure](https://portal.azure.com). Sua organização pode ter uma subscrição e pode adicionar a sua conta Microsoft à mesma.

(A equipe de desenvolvimento pode criar os [SDK do Application Insights](../../azure-monitor/app/asp-net.md) na aplicação web. Essa instrumentação do tempo de compilação dá a eles uma flexibilidade maior escrever telemetria personalizada. No entanto, não importa: pode seguir os passos descritos aqui, com ou sem o SDK incorporada.)

## <a name="one-time-install-application-insights-management-pack"></a>(Uma vez) Instalar o pacote de gestão do Application Insights
Na máquina em que executar o Operations Manager:

1. Desinstale qualquer versão anterior do pacote de gestão:
   1. No Operations Manager, abra administração, pacotes de gestão. 
   2. Elimine a versão antiga.
2. Baixe e instale o pacote de gestão do catálogo.
3. Reinicie o Operations Manager.

## <a name="create-a-management-pack"></a>Criar um pacote de gestão
1. No Operations Manager, abra **Authoring**, **.NET... com o Application Insights**, **Adicionar Assistente de monitorização**e escolha novamente **.NET... com a aplicação Insights**.
   
    ![](./media/scom/020.png)
2. Nome da configuração após a sua aplicação. (Precisa instrumentar uma aplicação ao mesmo tempo).
   
    ![](./media/scom/030.png)
3. Na mesma página do assistente, crie um novo pacote de gestão ou selecione um pacote do Application Insights que criou anteriormente.
   
     (O Application Insights [pacote de gestão](https://technet.microsoft.com/library/cc974491.aspx) é um modelo, a partir do qual cria uma instância. Pode reutilizar a mesma instância mais tarde.)

    ![No separador de propriedades gerais, escreva o nome da aplicação. Clique em novo e escreva um nome para um pacote de gestão. Clique em OK e, em seguida, clique em seguinte.](./media/scom/040.png)

1. Escolha uma aplicação que pretende monitorizar. A funcionalidade de pesquisa procura entre aplicações instaladas nos seus servidores.
   
    ![Sobre o que a guia Monitor, clique em Adicionar, escreva parte do nome da aplicação, clique em Procurar, escolha a aplicação e, em seguida, adicionar, OK.](./media/scom/050.png)
   
    O campo de âmbito de monitorização opcional pode ser utilizado para especificar um subconjunto dos seus servidores, se não pretender monitorizar a aplicação em todos os servidores.
2. Na página seguinte do assistente, primeiro tem de fornecer as suas credenciais para iniciar sessão no Microsoft Azure.
   
    Nesta página, escolha o recurso do Application Insights onde pretende que os dados de telemetria a ser analisados e apresentados. 
   
   * Se a aplicação foi configurada para o Application Insights durante o desenvolvimento, selecione o recurso existente.
   * Caso contrário, crie um novo recurso com o nome da aplicação. Se existirem outras aplicações que são componentes do mesmo sistema, colocá-los no mesmo grupo de recursos, para facilitar o acesso à telemetria gerir.
     
     Pode alterar estas definições mais tarde.
     
     ![No separador de definições do Application Insights, clique em 'entrar' e forneça as credenciais da conta Microsoft para o Azure. Em seguida, escolha uma subscrição, grupo de recursos e recursos.](./media/scom/060.png)
3. Conclua o assistente.
   
    ![Clique em Criar](./media/scom/070.png)

Repita este procedimento para cada aplicação que pretende monitorizar.

Se precisar de alterar as definições mais tarde, volte a abrir as propriedades do monitor da janela de criação de conteúdos.

![Na criação de conteúdos, selecione a monitorização de desempenho de aplicações do .NET com o Application Insights, em seguida, selecione o seu monitor e clique em propriedades.](./media/scom/080.png)

## <a name="verify-monitoring"></a>Certifique-se de monitorização
O monitor que tem instalado pesquisas para a sua aplicação em cada servidor. Quando encontrar a aplicação, este configura o Monitor de estado do Application Insights para monitorizar a aplicação. Se necessário, primeiro instala Monitor de estado no servidor.

Pode verificar quais as instâncias da aplicação ser detetado:

![Em monitorização, abrir o Application Insights](./media/scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Ver a telemetria no Application Insights
Na [portal do Azure](https://portal.azure.com), navegue para o recurso para a sua aplicação. [Ver gráficos que mostram a telemetria](../../azure-monitor/app/app-insights-dashboards.md) da sua aplicação. (Se ele ainda não mostra a cópia de segurança na página principal ainda, clique em Live Stream de métricas.)

## <a name="next-steps"></a>Passos Seguintes
* [Configurar um dashboard](../../azure-monitor/app/app-insights-dashboards.md) para reunir os gráficos mais importantes monitorização esta e outras aplicações.
* [Saiba mais sobre métricas](../../azure-monitor/app/metrics-explorer.md)
* [Configurar alertas](../../azure-monitor/app/alerts.md)
* [Diagnosticar problemas de desempenho](../../azure-monitor/app/detect-triage-diagnose.md)
* [Consultas de análise avançadas](../../azure-monitor/app/analytics.md)
* [Testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)

