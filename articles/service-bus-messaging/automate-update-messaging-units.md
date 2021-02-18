---
title: Azure Service Bus - Atualizar automaticamente as unidades de mensagens
description: Este artigo mostra-lhe como pode utilizar automaticamente a atualização automática de unidades de mensagens de um espaço de nomes de Service Bus.
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 932c7bb1235cb54aefe67253e38e1683187f4d2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581645"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Atualizar automaticamente as unidades de mensagens de um espaço de nomes do Azure Service Bus 
A autoescala permite-lhe ter a quantidade certa de recursos a correr para lidar com a carga na sua aplicação. Permite-lhe adicionar recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão parados. Consulte [a visão geral da autoescala no Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md) para saber mais sobre a funcionalidade autoescala do Azure Monitor. 

As Mensagens Premium do Service Bus fornecem isolamento de recursos no nível de CPU e memória para que cada carga de trabalho do cliente seja executada de forma isolada. Este contentor de recursos é designado por **unidade de mensagens**. Para saber mais sobre as unidades de mensagens, consulte [a Service Bus Premium Messaging.](service-bus-premium-messaging.md) 

Ao utilizar a funcionalidade Autoscale para espaços de nome premium do Service Bus, pode especificar um número mínimo e máximo de unidades de [mensagens](service-bus-premium-messaging.md) e adicionar ou remover unidades de mensagens automaticamente com base num conjunto de regras. 

Por exemplo, pode implementar os seguintes cenários de escala para espaços de nomes de Service Bus utilizando a funcionalidade Autoscale. 

- Aumente as unidades de mensagens para um espaço de nomes de Service Bus quando o uso do CPU do espaço de nomes for superior a 75%. 
- Diminua as unidades de mensagens para um espaço de nomes de autocarros de serviço quando o uso do CPU do espaço de nomes for inferior a 25%. 
- Utilize mais unidades de mensagens durante o horário de trabalho e menos durante o horário de folga. 

Este artigo mostra-lhe como pode escalar automaticamente um espaço de nomes do Service Bus (atualizar [unidades de mensagens)](service-bus-premium-messaging.md)no portal Azure. 

> [!IMPORTANT]
> Este artigo aplica-se apenas ao nível **premium** da Azure Service Bus. 

## <a name="autoscale-setting-page"></a>Página de definição de escala automática
Em primeiro lugar, siga estes passos para navegar na página **de definições de Autoscale** para o seu espaço de nomes de Service Bus.

1. Inscreva-se no [portal Azure](https://portal.azure.com). 
2. Na barra de pesquisa, escreva **Service Bus,** selecione **Service Bus** da lista de drop-down e prima **ENTER**. 
1. Selecione o seu **espaço de nome premium** na lista de espaços de nome. 
1. Mude para a página **Escala.** 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Espaço de nome de ônibus de serviço - página de escala":::

## <a name="manual-scale"></a>Escala manual 
Esta definição permite-lhe definir um número fixo de unidades de mensagens para o espaço de nome. 

1. Na página **de definição de definição de autoescala,** selecione **escala manual** se ainda não estiver selecionada. 
1. Para **a definição de unidades de mensagens,** selecione o número de unidades de mensagens da lista de recuos.
1. **Selecione Guarde** na barra de ferramentas para guardar a definição. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Unidades de mensagens de escala manual":::       


## <a name="custom-autoscale---default-condition"></a>Autoescala personalizada - Condição padrão
Pode configurar o dimensionamento automático das unidades de mensagens utilizando condições. Esta condição de escala é executada quando nenhuma das outras condições de escala corresponde. Pode definir a condição predefinida de uma das seguintes formas:

- Escala com base numa métrica (como CPU ou utilização da memória)
- Escala para número específico de unidades de mensagens

Não é possível definir um horário para a escala automática num determinado horário de dias ou datas para uma condição predefinida. Esta condição de escala é executada quando nenhuma das outras condições de escala com horários coincidem. 

### <a name="scale-based-on-a-metric"></a>Escala com base numa métrica
O procedimento a seguir mostra como adicionar uma condição para aumentar automaticamente as unidades de mensagens (escala) quando o uso do CPU é superior a 75% e diminuir as unidades de mensagens (escala em) quando o uso do CPU é inferior a 25%. Os incrementos são feitos de 1 a 2, 2 a 4 e 4 a 8. Da mesma forma, os decrementos são feitos de 8 a 4, 4 a 2, e 2 a 1. 

1. Na página **de definição de definição de escala automática,** selecione **escala automática personalizada** para a escolha de como escalar a sua opção de **recurso.** 
1. Na secção **Predefinição** da página, especifique um **nome** para a condição predefinida. Selecione o ícone do **lápis** para editar o texto. 
1. Selecione **Escala com base numa métrica** para o modo **escala**. 
1. **Selecione + Adicione uma regra**. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Padrão - escala baseada numa métrica":::    
1. Na página **da regra escala,** siga estes passos:
    1. Selecione uma métrica da lista de drop-down de **nome métrico.** Neste exemplo, é **CPU.** 
    1. Selecione um operador e valores de limiar. Neste exemplo, são **maiores do que** **75** para o **limiar métrico desencadear uma ação de escala.** 
    1. Selecione uma **operação** na secção **Ação.** Neste exemplo, está definido para **Aumentar.** 
    1. Em seguida, **selecione Adicionar**
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Padrão - escalone se o uso do CPU for superior a 75%":::       

        > [!NOTE]
        > A funcionalidade de autoescala aumenta as unidades de mensagens para o espaço de nome se o uso global do CPU for superior a 75% neste exemplo. Os incrementos são feitos de 1 a 2, 2 a 4 e 4 a 8. 
1. **Selecione + Adicione novamente uma regra** e siga estes passos na página regra **escala:**
    1. Selecione uma métrica da lista de drop-down de **nome métrico.** Neste exemplo, é **CPU.** 
    1. Selecione um operador e valores de limiar. Neste exemplo, são **menos de** **25** para o **limiar métrico desencadear uma ação de escala.** 
    1. Selecione uma **operação** na secção **Ação.** Neste exemplo, está definido para **Diminuir.** 
    1. Em seguida, **selecione Adicionar** 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Padrão - escala em se o uso do CPU for inferior a 25%":::       

        > [!NOTE]
        > A funcionalidade de autoescala diminui as unidades de mensagens para o espaço de nome se o uso global do CPU for inferior a 25% neste exemplo. Os decrementos são feitos de 8 a 4, 4 a 2, e 2 a 1. 
1. Desafine o número **mínimo** e **máximo** e **predefinido** das unidades de mensagens.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Regra padrão baseada numa métrica":::
1. **Selecione Guarde** na barra de ferramentas para guardar a definição de escala automática. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Escala para número específico de unidades de mensagens
Siga estes passos para configurar a regra para escalar o espaço de nome para utilizar um número específico de unidades de mensagens. Mais uma vez, a condição predefinida é aplicada quando nenhuma das outras condições de escala corresponde. 

1. Na página **de definição de definição de escala automática,** selecione **escala automática personalizada** para a escolha de como escalar a sua opção de **recurso.** 
1. Na secção **Predefinição** da página, especifique um **nome** para a condição predefinida. 
1. Selecione **escala para unidades de mensagens específicas** para **o modo escala**. 
1. Para **as unidades de mensagens,** selecione o número de unidades de mensagens predefinidos. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Padrão - escala para unidades de mensagens específicas":::       

## <a name="custom-autoscale---additional-conditions"></a>Autoescala personalizada - condições adicionais
A secção anterior mostra-lhe como adicionar uma condição predefinida para a definição de autoescala. Esta secção mostra-lhe como adicionar mais condições à definição de autoescala. Para estas condições adicionais de não-incumprimento, pode definir um horário com base em dias específicos de uma semana ou num intervalo de datas. 

### <a name="scale-based-on-a-metric"></a>Escala com base numa métrica
1. Na página **de definição de definição de escala automática,** selecione **escala automática personalizada** para a escolha de como escalar a sua opção de **recurso.** 
1. **Selecione Adicione uma condição de escala** sob o bloco **Predefinido.** 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Personalizado - adicione uma ligação de condição de escala":::    
1. Especifique um **nome** para a condição. 
1. Confirme se a Escala com base numa opção **métrica** é selecionada. 
1. **Selecione + Adicione uma regra** para adicionar uma regra para aumentar as unidades de mensagens quando o uso geral do CPU for superior a 75%. Siga os passos da secção [de condição predefinido.](#custom-autoscale---default-condition) 
5. Desafine o número **mínimo** e **máximo** e **predefinido** das unidades de mensagens.
6. Também pode definir um **horário** com uma condição personalizada (mas não na condição predefinida). Pode especificar datas de início e fim para a condição (ou) selecionar dias específicos (segunda, terça-feira e assim por diante.) de uma semana. 
    1. Se selecionar **Especifique as datas de início/fim**, selecione o **Timezone**, **Data de início e hora** e **fim** (como mostrado na imagem seguinte) para que a condição esteja em vigor. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Valores mínimos, máximos e predefinidos para o número de unidades de mensagens":::
    1. Se selecionar **Repetir dias específicos,** selecione os dias da semana, o timezone, a hora de início e a hora de fim quando a condição deve ser aplicada. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Repita dias específicos":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Escala para número específico de unidades de mensagens
1. Na página **de definição de definição de escala automática,** selecione **escala automática personalizada** para a escolha de como escalar a sua opção de **recurso.** 
1. **Selecione Adicione uma condição de escala** sob o bloco **Predefinido.** 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Personalizado - adicione uma ligação de condição de escala":::    
1. Especifique um **nome** para a condição. 
2. Selecione a escala para a opção **unidades de mensagens específicas** para **o modo escala**. 
1. Selecione o número de **unidades de mensagens** da lista de recuos. 
6. Para o **horário**, especifique as datas de início e fim para a condição (ou) selecione dias específicos (segunda, terça-feira, e assim por diante.) de uma semana e horários. 
    1. Se selecionar **Especifique as datas de início/fim**, selecione o **Timezone**, **Data e hora** de início e data e hora de **fim** para a condição estar em vigor. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="escala para unidades de mensagens específicas - datas de início e fim":::        
    1. Se selecionar **Repetir dias específicos,** selecione os dias da semana, o timezone, a hora de início e a hora de fim quando a condição deve ser aplicada.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="escala para unidades de mensagens específicas - repita dias específicos":::

> [!IMPORTANT]
> Para saber mais sobre como funcionam as definições de autoescala, especialmente como escolhe um perfil ou condição e avalia várias regras, consulte [as definições de Definições de Autoescala](../azure-monitor/autoscale/autoscale-understanding-settings.md).          

## <a name="next-steps"></a>Passos seguintes
Para conhecer as unidades de mensagens, consulte as [mensagens Premium](service-bus-premium-messaging.md)

