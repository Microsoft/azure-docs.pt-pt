---
title: Introdução ao dimensionamento automático no Azure
description: Saiba como dimensionar seu aplicativo Web de recursos, serviço de nuvem, máquina virtual ou conjunto de dimensionamento de máquinas virtuais no Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.subservice: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60788593"
---
# <a name="get-started-with-autoscale-in-azure"></a>Introdução ao dimensionamento automático no Azure
Este artigo descreve como definir as configurações de dimensionamento automático para seu recurso no portal do Microsoft Azure.

Azure Monitor dimensionamento automático se aplica somente aos conjuntos de dimensionamento de [máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicativo-aplicativos Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Descobrir as configurações de autoescala em sua assinatura
Você pode descobrir todos os recursos para os quais o dimensionamento automático é aplicável no Azure Monitor. Use as etapas a seguir para obter uma explicação passo a passo:

1. Abra o [portal do Azure.][1]
1. Clique no ícone de Azure Monitor no painel esquerdo.
  ![Abrir Azure Monitor][2]
1. Clique em **dimensionamento automático** para exibir todos os recursos para os quais o dimensionamento automático é aplicável, juntamente com seu status de dimensionamento automático atual.
  ![Descobrir dimensionamento automático em Azure Monitor][3]

Você pode usar o painel de filtro na parte superior para deslimitar a lista para selecionar recursos em um grupo de recursos específico, tipos de recursos específicos ou um recurso específico.

Para cada recurso, você encontrará a contagem de instâncias atual e o status de dimensionamento automático. O status de dimensionamento automático pode ser:

- **Não configurado**: Você ainda não habilitou o dimensionamento automático para este recurso.
- **Habilitado**: Você habilitou o dimensionamento automático para este recurso.
- **Desabilitado**: Você desabilitou o dimensionamento automático para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Criar sua primeira configuração de dimensionamento automático

Agora vamos percorrer um passo a passo simples para criar sua primeira configuração de dimensionamento automático.

1. Abra a folha **dimensionamento automático** em Azure monitor e selecione um recurso que você deseja dimensionar. (As etapas a seguir usam um plano do serviço de aplicativo associado a um aplicativo Web. Você pode [criar seu primeiro aplicativo web ASP.net no Azure em 5 minutos.][4])
1. Observe que a contagem de instâncias atual é 1. Clique em **Habilitar dimensionamento automático**.
  ![Configuração de escala para o novo aplicativo Web][5]
1. Forneça um nome para a configuração de escala e clique em **Adicionar uma regra**. Observe as opções de regra de escala que são abertas como um painel de contexto no lado direito. Por padrão, isso define a opção para dimensionar sua contagem de instâncias em 1 se o percentual de CPU do recurso exceder 70%. Deixe-o com seus valores padrão e clique em **Adicionar**.
  ![Criar configuração de escala para um aplicativo Web][6]
1. Agora você criou sua primeira regra de dimensionamento. Observe que o UX recomenda as práticas recomendadas e afirma que "é recomendável ter pelo menos uma escala na regra". Para tal:

    a. Clique em **Adicionar uma regra**.

    b. Defina **operador** como **menor que**.

    c. Defina o **limite** como **20**.

    d. Defina a **operação** para **diminuir a contagem por**.

   Agora você deve ter uma configuração de escala que expanda/dimensione horizontalmente com base no uso da CPU.
   ![Escala com base na CPU][8]
1. Clique em **Guardar**.

Parabéns! Agora você criou com êxito sua primeira configuração de dimensionamento para fazer o dimensionamento automático de seu aplicativo Web com base no uso da CPU.

> [!NOTE]
> As mesmas etapas são aplicáveis para começar a usar um conjunto de dimensionamento de máquinas virtuais ou uma função de serviço de nuvem.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Dimensionar com base em uma agenda
Além de escala com base na CPU, você pode definir sua escala de forma diferente para dias específicos da semana.

1. Clique em **Adicionar uma condição de escala**.
1. Definir o modo de escala e as regras é o mesmo que a condição padrão.
1. Selecione **repetir dias específicos** para o agendamento.
1. Selecione os dias e a hora de início/término para quando a condição de dimensionamento deve ser aplicada.

![Condição de escala com base na agenda][9]
### <a name="scale-differently-on-specific-dates"></a>Dimensionar de forma diferente em datas específicas
Além de escala com base na CPU, você pode definir sua escala de forma diferente para datas específicas.

1. Clique em **Adicionar uma condição de escala**.
1. Definir o modo de escala e as regras é o mesmo que a condição padrão.
1. Selecione **especificar datas de início/término** para a agenda.
1. Selecione as datas de início/término e a hora de início/término para quando a condição de dimensionamento deve ser aplicada.

![Condição de escala com base nas datas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Exibir o histórico de escala de seu recurso
Sempre que o recurso é aumentado ou reduzido verticalmente, um evento é registrado no log de atividades. Você pode exibir o histórico de escala de seu recurso nas últimas 24 horas alternando para a guia **histórico de execução** .

![Execuções][11]

Se você quiser exibir o histórico de escala completo (por até 90 dias), selecione **clique aqui para ver mais detalhes**. O log de atividades é aberto com a autoescala selecionada previamente para seu recurso e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Exibir a definição de escala do recurso
O dimensionamento automático é um recurso Azure Resource Manager. Você pode exibir a definição de escala em JSON alternando para a guia **JSON** .

![Definição de escala][12]

Você pode fazer alterações diretamente no JSON, se necessário. Essas alterações serão refletidas depois que você salvá-las.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desabilitar o dimensionamento automático e dimensionar manualmente suas instâncias
Pode haver ocasiões em que você deseja desabilitar a configuração de dimensionamento atual e dimensionar manualmente o recurso.

Clique no botão **desabilitar dimensionamento automático** na parte superior.
![Desabilitar dimensionamento automático][13]

> [!NOTE]
> Esta opção desabilita sua configuração. No entanto, você pode voltar a ele depois de habilitar o dimensionamento automático novamente.

Agora você pode definir o número de instâncias que deseja dimensionar manualmente.

![Definir escala manual][14]

Você sempre pode retornar para dimensionamento automático clicando em **Habilitar dimensionamento automático** e em **salvar**.

## <a name="next-steps"></a>Passos seguintes
- [Criar um alerta do log de atividades para monitorar todas as operações do mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Criar um alerta do log de atividades para monitorar todas as operações de expansão/expansão do dimensionamento automático com falha em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

