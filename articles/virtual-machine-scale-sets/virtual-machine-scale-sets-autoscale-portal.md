---
title: Conjuntos de dimensionamento de máquina virtual de dimensionamento automático no portal do Azure | Documentos da Microsoft
description: Como criar regras de dimensionamento automático para dimensionamento de máquinas virtuais define no portal do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 648bc0295cd5435e9c3e44f33b7ae80522fa8e0e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618883"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Dimensionar automaticamente um conjunto de dimensionamento no portal do Azure
Quando criar um conjunto de dimensionamento, pode definir o número de instâncias de VM que quer executar. À medida que a sua aplicação exige alterações, pode aumentar ou reduzir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a par da exigência do cliente ou responder às alterações de desempenho durante todo o ciclo de vida da aplicação.

Este artigo mostra-lhe como criar regras de dimensionamento automático no portal do Azure que monitorizar o desempenho das instâncias de VM no conjunto de dimensionamento. Estas regras de dimensionamento automático aumentam ou diminuir o número de instâncias VM em resposta a estas métricas de desempenho. Também pode concluir estes passos com [do Azure PowerShell](tutorial-autoscale-powershell.md) ou o [CLI do Azure](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de dimensionamento automático, precisa de uma máquina virtual atual conjunto de dimensionamento. Pode criar um conjunto de dimensionamento com o [portal do Azure](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md), ou [da CLI do Azure](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Criar uma regra para dimensionar automaticamente
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controla as métricas a monitorizar, como a CPU ou o disco, quando a carga da aplicação tem de cumprir um determinado limiar, e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

1. Abra o portal do Azure e selecione **grupos de recursos** no menu no lado esquerdo do dashboard.
2. Selecione o grupo de recursos que contém o conjunto de dimensionamento, em seguida, escolha o seu conjunto de dimensionamento da lista de recursos.
3. Escolher **Scaling** no menu no lado esquerdo da escala de definir a janela. Selecione o botão para **ativar o dimensionamento automático**:

    ![Ativar o dimensionamento automático no portal do Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Introduza um nome para as suas definições, como *dimensionamento automático*, em seguida, selecione a opção de **adicionar uma regra**.

5. Vamos criar uma regra que aumenta o número de instâncias de VM no conjunto de dimensionamento quando a carga média da CPU é superior a 70% durante um período de 10 minutos. Quando a regra for acionada, o número de instâncias VM é aumentado em 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, é possível definir o **operação** ao *aumentar contagem em* e, em seguida, especifique *1* ou *2* para o *contagem de instâncias*. Em conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 10% ou % de 20 instâncias de VM podem ser mais adequadas.

    Especifique as seguintes definições para a regra:
    
    | Parâmetro              | Explicação                                                                                                         | Value          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregação de tempo*     | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média        |
    | *Nome da métrica*          | A métrica de desempenho para monitorizar e aplicar ações ao conjunto de dimensionamento.                                                   | Percentagem da CPU |
    | *Estatística de intervalo de agregação de tempo* | Define a forma como as métricas recolhidas em cada intervalo de agregação devem ser agregadas para análise.                             | Média        |
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                     | Maior que   |
    | *Limiar*            | A percentagem que faz com que a regra de dimensionamento automático acionar uma ação.                                                 | 70             |
    | *Duração*             | A quantidade de tempo monitorizado antes de os valores de métrica e limiar serem comparados.                                   | 10 minutos     |
    | *Operação*            | Define se o conjunto de dimensionamento deve aumentar ou diminuir quando se aplica a regra e em que o incremento                        | Aumentar percentagem em |
    | *Contagem de instâncias*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                            | 20             |
    | *Repouso (minutos)*  | A quantidade de tempo de espera antes de a regra ser aplicada novamente, para que as ações de dimensionamento automático tenham tempo de entrar em vigor. | 5 minutos      |

    Os exemplos seguintes mostram uma regra que criou no portal do Azure, que corresponde a estas definições:

    ![Criar uma regra de dimensionamento automático para aumentar o número de instâncias de VM](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Para criar a regra, selecione **adicionar**


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para dimensionar automaticamente num
À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.

1. Optar por **adicionar uma regra** novamente.
2. Crie uma regra que diminui o número de instâncias de VM no conjunto de dimensionamento quando a carga média da CPU passa a ser inferior a 30% durante um período de 10 minutos. Quando a regra for acionada, o número de instâncias VM é diminuído 20%.

    Utilize a mesma abordagem tal como acontece com a regra anterior. Ajuste as seguintes definições para a regra:
    
    | Parâmetro              | Explicação                                                                                                          | Value          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                      | Menor que   |
    | *Limiar*            | A percentagem que faz com que a regra de dimensionamento automático acionar uma ação.                                                 | 30             |
    | *Operação*            | Define se o conjunto de dimensionamento deve aumentar ou diminuir quando se aplica a regra e em que o incremento                         | Diminuir percentagem em |
    | *Contagem de instâncias*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                             | 20             |

3. Para criar a regra, selecione **adicionar**


## <a name="define-autoscale-instance-limits"></a>Definir limites de instância de dimensionamento automático
Seu perfil de dimensionamento automático tem de definir um mínimo, máximo e número predefinido de instâncias de VM. Quando são aplicadas as regras de dimensionamento automático, estes limites de instância, certifique-se de que não ampliar para além do número máximo de instâncias ou reduzir horizontalmente para além do mínimo de instâncias.

1. Defina os limites de instância seguintes:

    | Mínimo | Máximo | Predefinição|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Para aplicar as suas regras de dimensionamento automático e limites de instância, selecione **guardar**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorizar o número de instâncias num conjunto de dimensionamento
Para ver o número e o estado de instâncias de VM, selecione **instâncias** no menu no lado esquerdo da escala de definir a janela. O estado indica se a instância VM está *Creating* porque o automaticamente de conjunto de dimensionamento aumenta horizontalmente ou está *a eliminação* como o dimensionamento é dimensionado automaticamente no.

![Ver uma lista de instâncias VM do conjunto de dimensionamento](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático com base numa agenda
Os exemplos anteriores dimensionado automaticamente um conjunto de dimensionamento dentro ou para fora com métricas de anfitrião básicas, como a utilização da CPU. Também pode criar regras de dimensionamento automático com base em agendas. Estas regras com base na agenda permitem-lhe automaticamente aumentar horizontalmente o número de instâncias de VM à frente de um aumento prevista demanda de aplicativos, como as horas de trabalho principal e, em seguida, dimensionar automaticamente o número de instâncias de cada vez que antecipam menos pedido, por exemplo, a fim de semana.

1. Escolher **Scaling** no menu no lado esquerdo da escala de definir a janela. Para eliminar as regras de dimensionamento automático existente, criadas nos exemplos anteriores, selecione o caixote do ícone.

    ![Eliminar as regras de dimensionamento automático existente](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Optar por **adicionar uma condição de escala**. Selecione o ícone de lápis junto ao nome da regra e forneça um nome como *aumentar horizontalmente durante cada dia de trabalho*.

    ![Mudar o nome da regra predefinida do dimensionamento automático](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecione o botão de opção para **Dimensionar para uma contagem de instâncias específica**.
4. Para aumentar verticalmente o número de instâncias, introduza *10* como a contagem de instâncias.
5. Escolher **repetir dias específicos** para o **agenda** tipo.
6. Selecione todos os dias úteis, segunda-feira a sexta-feira.
7. Selecione o fuso horário adequado, em seguida, especifique um **hora de início** dos *09:00*.
8. Optar por **adicionar uma condição de escala** novamente. Repita o processo para criar uma agenda chamada *reduzir horizontalmente durante a noite* que pode ser dimensionada para *3* instâncias, repete a cada dia da semana e começa às *18:00*.
9. Para aplicar as regras de dimensionamento automático com base na agenda, selecione **guardar**.

    ![Criar regras de dimensionamento automático que podem ser dimensionadas com base numa agenda](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Para ver como são aplicadas as regras de dimensionamento automático, selecione **histórico de execuções** na parte superior do **Scaling** janela. Os eventos e um gráfico listam mostra quando o acionador de regras de dimensionamento automático e o número de instâncias VM no seu dimensionamento definido aumentos ou diminuições.


## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar regras de dimensionamento automático para aumentar horizontalmente e aumentar ou diminuir a *número* de instâncias VM no seu dimensionamento definido. Também pode aumentar verticalmente para aumentar ou diminuir a instância VM *tamanho*. Para obter mais informações, consulte [dimensionamento automático Vertical com conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as suas instâncias de VM, consulte [conjuntos de dimensionamento de máquina virtual de gerir com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando o acionador de regras de seu dimensionamento automático, veja [utilizar ações de dimensionamento automático para enviar e-mail e webhook notificações de alertas no Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Também pode [registos de auditoria de uso para enviar e-mail e webhook notificações de alertas no Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
