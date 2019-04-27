---
title: Introdução ao dimensionamento automático no Azure
description: Saiba como dimensionar o recurso da aplicação Web, defina o serviço em nuvem, de Máquina Virtual ou de dimensionamento de máquinas virtuais no Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.component: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60788593"
---
# <a name="get-started-with-autoscale-in-azure"></a>Introdução ao dimensionamento automático no Azure
Este artigo descreve como configurar as definições de dimensionamento automático para o seu recurso no portal do Microsoft Azure.

Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços Cloud](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicações - aplicações Web](https://azure.microsoft.com/services/app-service/web/), e [deserviçosdegestãodeAPI](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Detetar as definições de dimensionamento automático na sua subscrição
Pode descobrir todos os recursos para o qual o dimensionamento automático é aplicável no Azure Monitor. Utilize os seguintes passos para obter instruções passo a passo:

1. Abra o [portal do Azure.][1]
1. Clique no ícone do Azure Monitor no painel esquerdo.
  ![Abra o Monitor do Azure][2]
1. Clique em **dimensionamento automático** para ver todos os recursos para o qual a escala automática é aplicável, juntamente com o respetivo estado atual do dimensionamento automático.
  ![Detetar o dimensionamento automático no Azure Monitor][3]

Pode utilizar o painel de filtro na parte superior para o âmbito para baixo na lista para selecionar os recursos no grupo de recursos específico, tipos de recurso específico ou um recurso específico.

Para cada recurso, encontrará a contagem de instâncias atual e o estado do dimensionamento automático. O estado do dimensionamento automático pode ser:

- **Não configurado**: Não ativou a escala automática ainda para este recurso.
- **Ativado**: Ativou o dimensionamento automático para este recurso.
- **Desativado**: Ativou o dimensionamento automático para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Criar a sua primeira definição de dimensionamento automático

Agora vamos através de um simples passo a passo para criar a sua primeira definição de dimensionamento automático.

1. Abra o **dimensionamento automático** painel no Azure Monitor e selecione um recurso que pretende dimensionar. (Os passos seguintes utilizam um plano do serviço de aplicação associado a uma aplicação web. Pode [criar sua primeira aplicação web ASP.NET no Azure em 5 minutos.] [4])
1. Tenha em atenção que a contagem de instâncias atual é 1. Clique em **ativar o dimensionamento automático**.
  ![Definição de dimensionamento para a nova aplicação web][5]
1. Forneça um nome para a definição de dimensionamento e, em seguida, clique em **adicionar uma regra**. Observe as opções de regra de dimensionamento que abra como um painel de contexto no lado direito. Por predefinição, isto define a opção de dimensionar sua contagem de instâncias por 1 se a percentagem de CPU do recurso superior a 70 por cento. Deixá-lo em seus valores predefinidos e clique em **adicionar**.
  ![Criar definição de dimensionamento para uma aplicação web][6]
1. Acabou de criar a primeira regra de dimensionamento. Tenha em atenção que a experiência do Usuário recomenda as práticas recomendadas e afirma que "é recomendado ter, pelo menos, um dimensionamento na regra." Para tal:

    a. Clique em **adicionar uma regra**.

    b. Definir **operador** ao **inferior a**.

    c. Definir **limiar** ao **20**.

    d. Definir **operação** ao **diminuir contagem em**.

   Agora, deve ter uma definição de dimensionamento que escalas out/escalas com base na utilização da CPU.
   ![Dimensionar com base na CPU][8]
1. Clique em **Guardar**.

Parabéns! Agora criou com êxito a primeira definição de dimensionamento para dimensionar automaticamente a sua aplicação web com base na utilização da CPU.

> [!NOTE]
> Os mesmos passos são aplicáveis a começar com um conjunto de dimensionamento de máquinas virtuais ou função do serviço de nuvem.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Dimensionar com base numa agenda
Além de dimensionar com base na CPU, pode definir seu dimensionamento diferente para dias específicos da semana.

1. Clique em **adicionar uma condição de escala**.
1. Definir o modo de dimensionamento e as regras é o mesmo que a condição de predefinição.
1. Selecione **repetir dias específicos** para a agenda.
1. Selecione os dias e a hora de início/fim para quando a condição de escala deve ser aplicada.

![Condição de dimensionamento com base numa agenda][9]
### <a name="scale-differently-on-specific-dates"></a>Dimensionar de forma diferente em datas específicas
Além de dimensionar com base na CPU, pode definir seu dimensionamento diferentes para datas específicas.

1. Clique em **adicionar uma condição de escala**.
1. Definir o modo de dimensionamento e as regras é o mesmo que a condição de predefinição.
1. Selecione **datas de início/fim especifique** para a agenda.
1. Selecione as datas de início/fim e a hora de início/fim para quando a condição de escala deve ser aplicada.

![Com base nas datas de condição de escala][10]

### <a name="view-the-scale-history-of-your-resource"></a>Ver o histórico de escala do seu recurso
Sempre que o recurso é aumentado vertical ou horizontalmente, um evento é registado no registo de atividades. Pode ver o histórico de escala do seu recurso das últimas 24 horas por mudar para o **histórico de execuções** separador.

![Histórico de execuções][11]

Se quiser ver o histórico de dimensionamento completo (de até 90 dias), selecione **clique aqui para ver mais detalhes**. Abrir o registo de atividade, com o dimensionamento automático pré-selecionado para os seus recursos e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Ver a definição de dimensionamento do seu recurso
Dimensionamento automático é um recurso do Azure Resource Manager. Pode ver a definição de dimensionamento em JSON ao mudar para o **JSON** separador.

![Definição de dimensionamento][12]

Pode efetuar alterações no JSON diretamente, se necessário. Estas alterações serão refletidas depois de guardá-los.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desativar o dimensionamento automático e dimensione manualmente as instâncias
Haverá ocasiões em que deseja desativar a definição de dimensionamento atual e dimensionar manualmente o seu recurso.

Clique nas **desativar o dimensionamento automático** botão na parte superior.
![Desativar o dimensionamento automático][13]

> [!NOTE]
> Esta opção desativa a configuração. No entanto, pode voltar à mesma depois de ativar novamente o dimensionamento automático.

Agora pode definir o número de instâncias que pretende dimensionar para manualmente.

![Conjunto de dimensionamento manual][14]

Pode sempre regressar ao dimensionamento automático ao clicar em **ativar o dimensionamento automático** e, em seguida **guardar**.

## <a name="next-steps"></a>Passos Seguintes
- [Criar um alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Criar um alerta de registo de atividade para monitorizar as operações de no/dimensionamento-Escalamento horizontal de dimensionamento automático com todas as falhas na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

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
