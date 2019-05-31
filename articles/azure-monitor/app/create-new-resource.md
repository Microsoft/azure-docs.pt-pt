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
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 712004a1ae8a2a72854b7b2332449a019c0820c3
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256250"
---
# <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
O Azure Application Insights apresenta dados sobre a sua aplicação num Microsoft Azure *recursos*. Criar um novo recurso, por conseguinte, é a parte [configurar o Application Insights para monitorizar uma nova aplicação][start]. Em muitos casos, criação de um recurso pode ser feito automaticamente pelo IDE. Mas em alguns casos, criar um recurso manualmente – por exemplo, ter recursos separados para desenvolvimento e produção baseia-se da sua aplicação.

Depois de criar o recurso, obter a chave de instrumentação e usá-lo para configurar o SDK da aplicação. A chave de recurso liga a telemetria para o recurso.

## <a name="sign-up-to-microsoft-azure"></a>Inscreva-se ao Microsoft Azure
Se ainda não tiver uma [Microsoft conta, crie uma agora](https://live.com). (Se utilizar serviços como o Outlook.com, OneDrive, Windows Phone ou XBox Live, já tem uma conta Microsoft.)

Também precisa de uma subscrição [Microsoft Azure](https://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma, utilizando o seu Windows Live ID. É-lhe cobrado apenas o que utilizar. O plano básico padrão permite que uma determinada quantidade de utilização experimental sem encargos.

Quando tem acesso a uma subscrição, inicie sessão no Application Insights no [ https://portal.azure.com ](https://portal.azure.com)e utilizar o seu Live ID para iniciar sessão.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
Na [portal.azure.com](https://portal.azure.com), adicionar um recurso do Application Insights:

![Clicar em Novo, Application Insights](./media/create-new-resource/01-new.png)

* **Tipo de aplicação** afeta o que vê no painel de descrição geral e as propriedades disponíveis no [Explorador de métricas][metrics]. Se não vir o seu tipo de aplicação, escolha geral.
* **Subscrição** é a sua conta de pagamento no Azure.
* **Grupo de recursos** é uma conveniência para gerir as propriedades, como o controlo de acesso. Se já tiver criado outros recursos do Azure, pode optar por colocar esse novo recurso no mesmo grupo.
* **Localização** é onde manter os seus dados.
* **Afixar ao dashboard** coloca um mosaico de acesso rápido para o seu recurso na sua página inicial do Azure. Recomendado.

Quando tiver sido criada a sua aplicação, é aberto um painel novo. Este painel é onde ver dados de utilização e desempenho sobre a sua aplicação. 

Para voltar à mesma próxima vez que iniciar sessão Azure, procure o mosaico de início rápido da sua aplicação no quadro de início (ecrã principal). Ou clique em Procurar para encontrá-lo.

## <a name="copy-the-instrumentation-key"></a>Copie a chave de instrumentação
A chave de instrumentação identifica o recurso que criou. Que é necessária para permitir ao SDK.

![Clique em Essentials, clique na chave de instrumentação, CTRL + C](./media/create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Instalar o SDK na sua aplicação
Instale o SDK do Application Insights na sua aplicação. Este passo depende muito do tipo de seu aplicativo. 

Utilize a chave de instrumentação para configurar [o SDK que instala no seu aplicativo][start].

O SDK inclui módulos padrão que enviam telemetria sem a necessidade de escrever qualquer código. Para controlar ações do usuário ou diagnosticar problemas em mais detalhes, [utilizar a API] [ api] para enviar sua própria telemetria.

## <a name="monitor"></a>Ver dados de telemetria
Feche o painel de início rápido para voltar ao painel do aplicativo no portal do Azure.

Clique no mosaico de pesquisa para ver [pesquisa de diagnóstico][diagnostic], onde são apresentados os primeiros eventos. 

Se estiver à espera de mais dados, clique em **atualizar** após alguns segundos.

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

