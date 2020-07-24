---
title: Introdução ao dimensionamento automático no Azure
description: Saiba como escalar o seu recurso Web App, Cloud Service, Virtual Machine ou Virtual Machine Scale definido em Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a17d2de24aadfbab218d2b28a157f19e2e845fa9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073546"
---
# <a name="get-started-with-autoscale-in-azure"></a>Começa com a Autoscale em Azure
Este artigo descreve como configurar as suas definições de Autoscale para o seu recurso no portal Microsoft Azure.

A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escalas de máquinas virtuais,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [serviços de nuvem,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Web Apps](https://azure.microsoft.com/services/app-service/web/)e [serviços de Gestão API.](../../api-management/api-management-key-concepts.md)

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Descubra as definições de Autoscale na sua subscrição

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Pode descobrir todos os recursos para os quais a Autoscale é aplicável no Azure Monitor. Utilize os seguintes passos para uma passagem passo a passo:

1. Abra o [portal Azure.][1]
1. Clique no ícone Azure Monitor no painel esquerdo.
  ![Monitor aberto do Azure][2]
1. Clique **em Autoescala** para ver todos os recursos para os quais a Autoscale é aplicável, juntamente com o seu estado atual de Autoscale.
  ![Descubra autoescala no Monitor Azure][3]

Pode utilizar o painel de filtros na parte superior para analisar a lista para selecionar recursos num grupo de recursos específico, tipos de recursos específicos ou num recurso específico.

Para cada recurso, encontrará a contagem de instâncias atual e o estado de Autoscale. O estado de autoescala pode ser:

- **Não configurado**: Ainda não ativou a Autoscale para este recurso.
- **Ativado**: Ativou a Autoscale para este recurso.
- **Desativado**: Desativou a Autoescalada para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Crie a sua primeira definição de Autoscale

Vamos agora passar por uma simples passagem passo a passo para criar a sua primeira definição de Autoscale.

1. Abra a lâmina **de escala automática** no Azure Monitor e selecione um recurso que pretende escalar. (Os seguintes passos utilizam um plano de Serviço de Aplicações associado a uma aplicação web. Pode [criar a sua primeira aplicação web ASP.NET em Azure em 5 minutos.][4]
1. Note que a contagem de instâncias atuais é 1. Clique **em Ativar a escala automática**.
  ![Definição de escala para nova aplicação web][5]
1. Forneça um nome para a definição de escala e, em seguida, clique em **Adicionar uma regra**. Note as opções de regra de escala que se abrem como um painel de contexto no lado direito. Por predefinição, isto define a opção de escalar a contagem de exemplos em 1 se a percentagem de CPU do recurso exceder 70%. Deixe-o nos seus valores predefinidos e clique em **Adicionar**.
  ![Criar definição de escala para uma aplicação web][6]
1. Agora criaste a tua regra de primeira escala. Note que o UX recomenda as melhores práticas e afirma que "Recomenda-se ter pelo menos uma escala em regra." Para tal:

    a. Clique em **Adicionar uma regra**.

    b. Definir **o operador** para menos **de**.

    c. Fixar **limiar** para **20**.

    d. Definir **operação** para **Diminuir a contagem por**.

   Deve agora ter uma definição de escala que escama/escalas com base na utilização do CPU.
   ![Escala baseada no CPU][8]
1. Clique em **Save** (Guardar).

Parabéns! Criou agora com sucesso a sua primeira definição de escala para autoescalar a sua aplicação web com base na utilização do CPU.

> [!NOTE]
> Os mesmos passos são aplicáveis para começar com um conjunto de escala de máquina virtual ou função de serviço de nuvem.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Escala com base num horário
Além da escala baseada no CPU, pode definir a sua escala de forma diferente para dias específicos da semana.

1. Clique **Em Adicionar uma condição de escala**.
1. Definir o modo de escala e as regras é o mesmo que a condição predefinida.
1. **Selecione Repita os dias específicos** para o horário.
1. Selecione os dias e o tempo de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base no horário][9]
### <a name="scale-differently-on-specific-dates"></a>Escala de forma diferente em datas específicas
Além da escala baseada no CPU, pode definir a sua escala de forma diferente para datas específicas.

1. Clique **Em Adicionar uma condição de escala**.
1. Definir o modo de escala e as regras é o mesmo que a condição predefinida.
1. **Selecione Especificar as datas de início/fim** para o horário.
1. Selecione as datas de início/fim e o tempo de início/fim para quando a condição de escala deve ser aplicada.

![Condição de escala com base em datas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Veja o histórico de escala do seu recurso
Sempre que o seu recurso é dimensionado para cima ou para baixo, um evento é registado no registo de atividade. Pode ver o histórico de escala do seu recurso nas últimas 24 horas, mudando para o separador **história do Run.**

![Histórico de execuções][11]

Se pretender ver o histórico de escala completa (até 90 dias), **selecione Clique aqui para ver mais detalhes.** O registo de atividades abre, com autoescala pré-selecionada para o seu recurso e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Ver a definição de escala do seu recurso
A Autoscale é um recurso Azure Resource Manager. Pode ver a definição de escala no JSON mudando para o separador **JSON.**

![Definição de escala][12]

Pode escoar alterações no JSON diretamente, se necessário. Estas alterações serão refletidas depois de as salvares.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desative a autoescalação e dimensione manualmente as suas instâncias
Pode haver alturas em que pretende desativar a definição da escala atual e escalar manualmente o seu recurso.

Clique no botão **de desativação automática** na parte superior.
![Desativar a autoescala][13]

> [!NOTE]
> Esta opção desativa a sua configuração. No entanto, pode voltar a fazê-lo depois de voltar a ativar a Autoscale.

Pode agora definir o número de casos que pretende escalar manualmente.

![Definir escala manual][14]

Pode sempre voltar à Autoescala clicando em **Ativar a autoescala** e, em seguida, **guardar**.

## <a name="next-steps"></a>Passos seguintes
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações do motor de autoescala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações falhadas de escala/escala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/app-service-web-get-started-dotnet.md
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
