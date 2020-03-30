---
title: Começar com escala automática em Azure
description: Saiba como escalar a sua aplicação web de recursos, serviço de nuvem, máquina virtual ou escala de máquina virtual definida em Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 0a40496e4d496d0062c6ee7a6ab935c1ad9b35a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396358"
---
# <a name="get-started-with-autoscale-in-azure"></a>Introdução ao Dimensionamento Automático no Azure
Este artigo descreve como configurar as definições de escala automática para o seu recurso no portal Microsoft Azure.

A escala automática Do Monitor Azure aplica-se apenas a conjuntos de escala de [máquinas virtuais, serviços](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [de cloud,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Aplicações Web](https://azure.microsoft.com/services/app-service/web/)e serviços de [Gestão API.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Descubra as definições de escala automática na sua subscrição
Pode descobrir todos os recursos para os quais a Escala Automática é aplicável no Monitor Azure. Utilize os seguintes passos para uma passagem passo a passo:

1. Abra o [portal Azure.][1]
1. Clique no ícone Do Monitor Azure no painel esquerdo.
  ![Monitor Azure Aberto][2]
1. Clique em **Escala Automática** para ver todos os recursos para os quais a Escala Automática é aplicável, juntamente com o seu estado atual de escala Automática.
  ![Descubra a escala automática no Monitor Azure][3]

Pode utilizar o painel de filtros na parte superior para analisar a lista para selecionar recursos num grupo de recursos específicos, tipos de recursos específicos ou um recurso específico.

Para cada recurso, encontrará a contagem de instâncias atuais e o estado de escala automática. O estado de escala automática pode ser:

- **Não configurado**: Ainda não ativou a Escala Automática para este recurso.
- **Ativado**: Ativou a escala Automática para este recurso.
- **Desativado:** Desativou a escala automática para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Crie a sua primeira definição de escala automática

Vamos agora passar por uma simples passagem passo a passo para criar a sua primeira definição de escala automática.

1. Abra a lâmina **de escala automática** no Monitor Azure e selecione um recurso que pretende escalar. (Os seguintes passos utilizam um plano de Serviço de Aplicações associado a uma aplicação web. Pode [criar a sua primeira ASP.NET aplicação web em Azure em 5 minutos.][4]
1. Note que a contagem de instâncias atuais é 1. Clique em **ativar a escala automática**.
  ![Definição de escala para nova aplicação web][5]
1. Forneça um nome para a definição de escala e, em seguida, clique em **Adicionar uma regra**. Note as opções de regra de escala que se abrem como um painel de contexto no lado direito. Por padrão, isto define a opção de escalar a sua contagem de instâncias em 1 se a percentagem de CPU do recurso exceder 70%. Deixe-o nos seus valores padrão e clique em **Adicionar**.
  ![Criar definição de escala para uma aplicação web][6]
1. Agora criaste a tua regra de primeira escala. Note que o UX recomenda as melhores práticas e afirma que "recomenda-se ter pelo menos uma escala em regra." Para tal:

    a. Clique em **Adicionar uma regra**.

    b. Definir **operador** para **menos de**.

    c. Definir **limiar** para **20**.

    d. Definir **Operação** para **Diminuir a contagem por**.

   Deve agora ter uma regulação de escala que escala/escala sem escalas com base na utilização do CPU.
   ![Escala baseada em CPU][8]
1. Clique em **Guardar**.

Parabéns! Criou com sucesso a sua definição de primeira escala para escalar automaticamente a sua aplicação web com base no uso do CPU.

> [!NOTE]
> Os mesmos passos são aplicáveis para começar com um conjunto de escala de máquina virtual ou função de serviço em nuvem.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Escala com base num horário
Além da escala baseada no CPU, pode definir a sua escala de forma diferente para dias específicos da semana.

1. Clique **Em adicionar uma condição de escala**.
1. A definição do modo de escala e as regras são as mesmas que a condição predefinida.
1. Selecione **Repita os dias específicos** para o horário.
1. Selecione os dias e o tempo de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base no horário][9]
### <a name="scale-differently-on-specific-dates"></a>Escala de forma diferente em datas específicas
Além da escala baseada no CPU, pode definir a sua escala de forma diferente para datas específicas.

1. Clique **Em adicionar uma condição de escala**.
1. A definição do modo de escala e as regras são as mesmas que a condição predefinida.
1. Selecione Especificar as datas de **início/fim** para o horário.
1. Selecione as datas de início/fim e o tempo de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base em datas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Veja a história da escala do seu recurso
Sempre que o seu recurso é dimensionado para cima ou para baixo, um evento é registado no registo de atividade. Pode ver a história da escala do seu recurso nas últimas 24 horas, mudando para o separador **histórico Run.**

![Histórico de execuções][11]

Se quiser ver o histórico de escala completa (por um prazo de até 90 dias), selecione **Clique aqui para ver mais detalhes**. O registo de atividade abre, com escala automática pré-selecionada para o seu recurso e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Veja a definição de escala do seu recurso
A escala automática é um recurso do Gestor de Recursos Azure. Pode visualizar a definição de escala em JSON, mudando para o separador **JSON.**

![Definição de escala][12]

Pode fazer alterações na JSON diretamente, se necessário. Estas alterações serão refletidas depois de as salvares.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desative a escala automática e escala manualmente as suas instâncias
Pode haver momentos em que pretende desativar a sua regulação de escala atual e escalar manualmente o seu recurso.

Clique no botão **de desativação** de escala automática na parte superior.
![Desativar a escala automática][13]

> [!NOTE]
> Esta opção desativa a sua configuração. No entanto, pode voltar a fazê-lo depois de voltar a ativar a Escala Automática.

Agora pode definir o número de instâncias que pretende escalar manualmente.

![Definir a escala manual][14]

Pode sempre voltar à escala automática clicando em **escala automática** **e,** em seguida, guardar .

## <a name="next-steps"></a>Passos seguintes
- [Crie um Alerta de Registo de Atividades para monitorizar todas as operações do motor de escala automática na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Registo de Atividades para monitorizar todas as operações falhadas de escala automática/escala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

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

