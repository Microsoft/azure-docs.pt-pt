---
title: Criar um novo recurso do Application Insights do Azure | Documentos da Microsoft
description: Defina manualmente a monitorização do Application Insights para uma nova aplicação em direto.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073319"
---
# <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

O Azure Application Insights apresenta dados sobre a sua aplicação num Microsoft Azure *recursos*. Criar um novo recurso, por conseguinte, é a parte [configurar o Application Insights para monitorizar uma nova aplicação][start]. Depois de criar o novo recurso, pode obter a chave de instrumentação e usá-lo para configurar o Application Insights SDK. A chave de instrumentação liga a sua telemetria para o recurso.

## <a name="sign-in-to-microsoft-azure"></a>Inicie sessão no Microsoft Azure

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Inicie sessão para o [portal do Azure](https://portal.azure.com)e crie um recurso do Application Insights:

![Clique no sinal '+', no canto superior esquerdo. Selecione as ferramentas de programação, seguido pelo Application Insights](./media/create-new-resource/new-app-insights.png)

   | Definições        |  Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar. |
   | **Grupo de Recursos**     | myResourceGroup      | Nome para o grupo de recursos novo ou existente para alojar dados do App Insights. |
   | **Location** | EUA Leste | Escolha uma localização perto de si ou perto do local onde está alojada a sua aplicação. |

Introduza os valores adequados para os campos obrigatórios e, em seguida, selecione **rever + criar**.

![Introduza valores em campos obrigatórios e, em seguida, selecione "Rever + criar".](./media/create-new-resource/review-create.png)

Quando tiver sido criada a sua aplicação, é aberto um novo painel. Este painel é onde ver dados de utilização e desempenho sobre a sua aplicação monitorizada. 

## <a name="copy-the-instrumentation-key"></a>Copie a chave de instrumentação

A chave de instrumentação identifica o recurso que pretende associar os dados de telemetria com. Terá de copiar para adicionar a chave de instrumentação ao código de seu aplicativo.

![Clique e copie a chave de instrumentação](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalar o SDK na sua aplicação

Instale o SDK do Application Insights na sua aplicação. Este passo depende muito do tipo de seu aplicativo.

Utilize a chave de instrumentação para configurar [o SDK que instala no seu aplicativo][start].

O SDK inclui módulos padrão que enviam telemetria sem a necessidade de escrever qualquer código adicional. Para controlar ações do usuário ou diagnosticar problemas em mais detalhes, [utilizar a API] [ api] para enviar sua própria telemetria.

## <a name="creating-a-resource-automatically"></a>Criação de um recurso automaticamente
Pode escrever um [script do PowerShell](../../azure-monitor/app/powershell.md) para criar um recurso automaticamente.

## <a name="next-steps"></a>Passos Seguintes
* [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Explorar métricas](../../azure-monitor/app/metrics-explorer.md)
* [Escrever consultas da Análise](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md