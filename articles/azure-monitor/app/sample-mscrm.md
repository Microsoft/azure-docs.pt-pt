---
title: Microsoft Dynamics CRM e insights do Aplicativo Azure | Microsoft Docs
description: Obtenha telemetria do Microsoft Dynamics CRM online usando o Application Insights. Explicação da instalação, obtenção de dados, visualização e exportação.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 470f723782ca29409549e0df8e900edf86cd446e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534291"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Descrição Passo a Passo: Habilitando a telemetria para o Microsoft Dynamics CRM online usando o Application Insights
Este artigo mostra como obter dados de telemetria do [Microsoft Dynamics CRM online usando o](https://www.dynamics.com/) [aplicativo Azure](https://azure.microsoft.com/services/application-insights/)insights. Vamos examinar o processo completo de adição de Application Insights script ao seu aplicativo, captura de dados e visualização de dados.

> [!NOTE]
> [Procure a solução de exemplo](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Adicionar Application Insights à instância do CRM Online nova ou existente
Para monitorar seu aplicativo, você adiciona um SDK do Application Insights ao seu aplicativo. O SDK envia telemetria para o [portal de Application insights](https://portal.azure.com), onde você pode usar nossas poderosas ferramentas de diagnóstico e análise, ou exportar os dados para o armazenamento.

### <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso de Application Insights no Azure
1. Obter [uma conta no Microsoft Azure](https://azure.com/pricing). 
2. Entre no [portal do Azure](https://portal.azure.com) e adicione um novo recurso de Application insights. É aí que seus dados serão processados e exibidos.

    ![Clique em +, serviços de desenvolvedor, Application Insights.](./media/sample-mscrm/01.png)

    Escolha ASP.NET como o tipo de aplicação.
3. Siga as instruções para [obter o script SDK do JavaScript para seu aplicativo](../../azure-monitor/app/javascript.md), copie o trecho de código JavaScript e substitua a chave de instrumentação pelo valor correto para seu recurso de Application insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Criar um recurso da Web JavaScript no Microsoft Dynamics CRM
1. Abra sua instância do CRM Online e faça logon com privilégios de administrador.
2. Abra as configurações do Microsoft Dynamics CRM, personalizações, personalizar o sistema

    ![Configurações do Microsoft Dynamics CRM](./media/sample-mscrm/00001.png)

    ![Configurações > personalizações](./media/sample-mscrm/00002.png)

1. Crie um recurso JavaScript.

    ![Caixa de diálogo novo recurso da Web](./media/sample-mscrm/07.png)

    Dê um nome a ele, selecione **script (JScript)** e abra o editor de texto.

    ![Abrir o editor de texto](./media/sample-mscrm/00004.png)
2. Copie o código do SDK Application Insights JavaScript no qual você configurou sua chave de instrumentação antes. Ao copiar, certifique-se de ignorar as marcas de script. Consulte a captura de tela abaixo:

    ![Definir sua chave de instrumentação](./media/sample-mscrm/000005.png)

    O código inclui a chave de instrumentação que identifica o recurso do Application insights.
3. Salvar e publicar.

    ![Guardar e publicar](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Formulários de instrumento
1. No Microsoft CRM Online, abra o formulário da conta

    ![Formulário de conta](./media/sample-mscrm/00007.png)
2. Abrir as propriedades do formulário

    ![Propriedades do formulário](./media/sample-mscrm/00008.png)
3. Adicionar o recurso da Web do JavaScript que você criou

    ![Adicionar menu](./media/sample-mscrm/13.png)

4. Salve e Publique suas personalizações de formulário.

## <a name="metrics-captured"></a>Métricas capturadas
Agora você configurou a captura de telemetria para o formulário. Sempre que for usado, os dados serão enviados para o recurso de Application Insights.

Aqui estão exemplos dos dados que você verá.

#### <a name="application-health"></a>Integridade do aplicativo
![Tempo de carregamento de página de exemplo](./media/sample-mscrm/15.png)

![Gráfico de exibições de página de exemplo](./media/sample-mscrm/16.png)

Exceções do navegador:

![Gráfico de exceções do navegador](./media/sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![Lista de exceções](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Utilização
![Usuários, sessões e exibições de página](./media/sample-mscrm/19.png)

![Gráficos de sessão](./media/sample-mscrm/20.png)

![Versões do navegador](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Browsers
![Divisão do tempo de carregamento da página](./media/sample-mscrm/22.png)

![Contagem de sessões por versão do navegador](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolocalização
![Contagem de sessões por país](./media/sample-mscrm/24.png)

![Sessões e usuários por país](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Solicitação de exibição de página interna
![Resumo da exibição de página](./media/sample-mscrm/26.png)

![Pesquisar em eventos de exibição de página](./media/sample-mscrm/27.png)

![Vistas de página semelhantes](./media/sample-mscrm/28.png)

![Propriedades de visualização de página](./media/sample-mscrm/29.png)

![Páginas por sessão](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Código de exemplo
[Procure o código de exemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Você pode fazer uma análise ainda mais profunda se [exportar os dados para o Microsoft Power bi](../../azure-monitor/app/export-power-bi.md ).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Solução de exemplo do Microsoft Dynamics CRM
[Aqui está a solução de exemplo implementada no Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Saber mais
* [O que é o Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights para páginas da Web](../../azure-monitor/app/javascript.md)
* [Mais exemplos e orientações](../../azure-monitor/app/app-insights-overview.md)
