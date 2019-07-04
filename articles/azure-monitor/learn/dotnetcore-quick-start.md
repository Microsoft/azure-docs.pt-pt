---
title: Início rápido com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente uma aplicação Web do ASP.NET Core para monitorização com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 931de532aa6e09b2cd00955df6ba1f05d7e4f42c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428502"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Iniciar a Monitorização de uma Aplicação Web ASP.NET Core

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. 

Este início rápido orienta-o ao processo de adicionar o Application Insights SDK para um aplicativo de web do ASP.NET Core existente. Para saber mais sobre como configurar o Application Insights sem Check-Out do Visual Studio [artigo](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- [Instalar o Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
  - Desenvolvimento ASP.NET e Web
  - Desenvolvimento do Azure
- [Instale o SDK .NET Core 2.0](https://www.microsoft.com/net/core)
- Irá precisar de uma subscrição do Azure e de uma aplicação Web .NET Core existente.

Se não tiver uma aplicação web ASP.NET Core, pode usar o nosso guia passo a passo para [criar uma aplicação ASP.NET Core e adicionar o Application Insights.](../../azure-monitor/app/asp-net-core.md)

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados telemétricos de qualquer aplicação ligada à Internet, independentemente de estar a ser executado no local ou na cloud. Utilize os passos seguintes para começar a ver estes dados.

1. Selecione **Criar um recurso** > **Ferramentas de programador** > **Application Insights**.

   > [!NOTE]
   >Se esta for a primeira vez que a criação de um recurso do Application Insights pode saber mais, visite o [criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc.

    É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

   | Definições        |  Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Name**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Location** | East US | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="configure-app-insights-sdk"></a>Configurar o SDK do Application Insights

1. Abra o seu **projeto** da Aplicação Web ASP.NET Core no Visual Studio > Clique com o botão direito do rato em AppName no **Explorador de Soluções** > Selecione **Adicionar** > **Telemetria do Application Insights**.

    ![Adicionar Telemetria do Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Clique nas **começar** botão

3. Selecione a sua conta e subscrição > selecione o **recurso existente** que criou no portal do Azure > clique em **registar**.

4. Selecione **Depurar** > **Iniciar sem Depuração** (Ctrl + F5) para Iniciar a sua aplicação

    ![Menu de Descrição Geral do Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Volte a abrir o Application Insights **descrição geral** página no portal do Azure ao selecionar **home page** e em recursos recentes, selecione o recurso que criou anteriormente, para ver os detalhes sobre a sua execução atualmente aplicação.

   ![Menu de Descrição Geral do Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Clique em **Mapa da aplicação** para obter um esquema visual das relações de dependência entre os componentes da aplicação. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapeamento de Aplicações](./media/dotnetcore-quick-start/5appmap.png)

3. Clique nas **análise da aplicação** ícone ![ícone do mapa da aplicação](./media/dotnetcore-quick-start/006.png) **ver na análise**. Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráfico de análise de pedidos de utilizador durante um período de tempo](./media/dotnetcore-quick-start/6analytics.png)

4. Retorno para o **descrição geral** página e examinar os Dashboards de KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram. 

   ![Gráficos de linha cronológica de Descrição Geral do Estado de Funcionamento](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Sobre o lado esquerdo clique em **métricas**. Utilize o Explorador de métricas para investigar o estado de funcionamento e a utilização do seu recurso. Pode clicar em **Adicionar novo gráfico** criar vistas personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráficos existentes, a altura, a paleta de cores, os agrupamentos e as métricas. Por exemplo, pode fazer um gráfico que apresenta o tempo de carregamento de página do browser média ao selecionar um "Tempo de carregamento de páginas do Browser" na lista pendente da métricas e "Média" de agregação. Para saber mais sobre o Explorador de métricas do Azure visite [introdução ao Explorador de métricas do Azure](../../azure-monitor/platform/metrics-getting-started.md).

     ![Separador de métricas: Gráfico de tempo de carregamento de página browser médio](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Vídeo

- Externo vídeo passo a passo sobre [configuração do Application Insights com o Visual Studio e .NET Core](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Externo vídeo passo a passo sobre [configuração do Application Insights com o .NET Core e o Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver terminado, teste, pode eliminar o grupo de recursos e todos os recursos relacionados. Para tal, siga os passos abaixo.

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar exceções de runtime](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
