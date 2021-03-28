---
title: Começar com autoescala em Azure
description: Saiba como escalar o seu recurso Web App, Cloud Service, Virtual Machine ou Virtual Machine set in Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: edc58ed4af3475a45804e3833424bec79d50ff89
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641549"
---
# <a name="get-started-with-autoscale-in-azure"></a>Introdução ao dimensionamento automático no Azure
Este artigo descreve como configurar as suas definições de Autoscale para o seu recurso no portal Microsoft Azure.

A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escala de máquina virtual,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [Serviços cloud,](https://azure.microsoft.com/services/cloud-services/) [Serviço de Aplicações - Aplicações Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de Gestão API.](../../api-management/api-management-key-concepts.md)

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
1. Clique em **Guardar**.

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

### <a name="cool-down-period-effects"></a>Efeitos do período de arrefecimento

A autoescala utiliza um período de arrefecimento para evitar "bater palmas", que é o rápido, repetativo para cima e para baixo de casos.  Para obter mais informações, consulte [as etapas de avaliação da Autoscale.](autoscale-understanding-settings.md#autoscale-evaluation)  Outras informações valiosas sobre como monitorizar o motor de autoescala podem ser encontradas em [Autoscale Best Practices](autoscale-best-practices.md#choose-the-thresholds-carefully-for-all-metric-types) e [Troubleshooting autoscale,](autoscale-troubleshoot.md) respectivamente. 

## <a name="route-traffic-to-healthy-instances-app-service"></a>Encaminhar o tráfego para instâncias saudáveis (Serviço de Aplicações)

<a id="health-check-path"></a>

Quando a sua aplicação web Azure é dimensionada para várias instâncias, o Serviço de Aplicações pode realizar verificações de saúde nas suas instâncias para encaminhar o tráfego para as instâncias saudáveis. Para saber mais, consulte [este artigo sobre a verificação do Serviço de Aplicações Health.](../../app-service/monitor-instances-health-check.md)

## <a name="moving-autoscale-to-a-different-region"></a>Mover autoescala para uma região diferente
Esta secção descreve como mover a autoescala do Azure para outra região sob a mesma Subscrição e Grupo de Recursos. Pode utilizar a API REST para mover configurações de autoescala.
### <a name="prerequisite"></a>Pré-requisito
1. Certifique-se de que a subscrição e o Grupo de Recursos estão disponíveis e os detalhes nas regiões de origem e destino são idênticos.
1. Certifique-se de que a autoestama Azure está disponível na [região de Azure para onde pretende mover-se](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Mover
Utilize [a API REST](/rest/api/monitor/autoscalesettings/createorupdate) para criar uma definição de autoescala no novo ambiente. A definição de autoescala criada na região de destino será uma cópia da definição de autoescala na região de origem.

[As definições de diagnóstico](../essentials/diagnostic-settings.md) que foram criadas em associação com a definição de autoescala na região de origem não podem ser movidas. Terá de recriar definições de diagnóstico na região de destino, após a criação de definições de auto-venda. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Saiba mais sobre a movimentação de recursos em todas as regiões de Azure
Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte a [Move resources to a new resource group or subscription](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>Passos seguintes
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações do motor de autoescala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações falhadas de escala/escala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)


<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
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
