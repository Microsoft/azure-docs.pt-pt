---
title: Conjuntos de escala automática de máquinas virtuais no portal Azure
description: Como criar regras de escala automática para conjuntos de escala de máquinas virtuais no portal Azure
author: ju-shim
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 1915b144aec5a5447504c70d18dbf420d255a08e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010295"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Escala automaticamente uma escala de máquina virtual definida no portal Azure
Quando criar um conjunto de dimensionamento, pode definir o número de instâncias de VM que quer executar. À medida que a sua aplicação exige alterações, pode aumentar ou reduzir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a par da exigência do cliente ou responder às alterações de desempenho durante todo o ciclo de vida da aplicação.

Este artigo mostra-lhe como criar regras de escala automática no portal Azure que monitorizam o desempenho das instâncias VM no seu conjunto de escala. Estas regras de escala automática aumentam ou diminuem o número de casos de VM em resposta a estas métricas de desempenho. Também pode completar estes passos com o [Azure PowerShell](tutorial-autoscale-powershell.md) ou o [Azure CLI](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de escala automática, precisa de um conjunto de escala virtual de máquinaexistente. Pode criar um conjunto de escala com o [portal Azure,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)ou [Azure CLI](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Criar uma regra para escalar automaticamente
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controla as métricas a monitorizar, como a CPU ou o disco, quando a carga da aplicação tem de cumprir um determinado limiar e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

1. Abra o portal Azure e selecione **Grupos de Recursos** do menu do lado esquerdo do tablier.
2. Selecione o grupo de recursos que contém o seu conjunto de escala e, em seguida, escolha o seu conjunto de escala da lista de recursos.
3. Escolha **o escalonamento** do menu do lado esquerdo da janela de conjunto de escala. Selecione o botão para ativar a **escala automática:**

    ![Ativar a escala automática no portal Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Introduza um nome para as suas definições, como *escala automática,* e, em seguida, selecione a opção de **adicionar uma regra**.

5. Vamos criar uma regra que aumenta o número de casos de VM numa escala definida quando a carga média de CPU é superior a 70% num período de 10 minutos. Quando a regra dispara, o número de casos de VM é aumentado em 20%. Em conjuntos de escala com um pequeno número de casos vM, pode definir a **contagem de funcionamento** para *aumentar e,* em seguida, especificar *1* ou *2* para a contagem de *exemplo*. Em conjuntos de escala com um grande número de casos vm, um aumento de 10% ou 20% de instâncias vm pode ser mais apropriado.

    Especifique as seguintes definições para a sua regra:
    
    | Parâmetro              | Explicação                                                                                                         | Valor          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregação do tempo*     | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média        |
    | *Nome métrico*          | A métrica de desempenho para monitorizar e aplicar ações ao conjunto de dimensionamento.                                                   | Percentagem da CPU |
    | *Estatística do intervalo de agregação* | Define como as métricas recolhidas em cada vez que os grãos devem ser agregados para análise.                             | Média        |
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                     | Maior que   |
    | *Limiar*            | A percentagem que faz com que a regra da escala automática desencadeie uma ação.                                                 | 70             |
    | *Duração*             | A quantidade de tempo monitorizado antes dos valores de métrica e limiar serem comparados.                                   | 10 minutos     |
    | *Operação*            | Define se o conjunto de escala deve escalar para cima ou para baixo quando a regra se aplica e por que incremento                        | Aumentar por cento por |
    | *Contagem de exemplos*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                            | 20             |
    | *Repouso (minutos)*  | A quantidade de tempo de espera antes de a regra ser aplicada novamente para que as ações de dimensionamento automático tenham tempo de entrar em vigor. | 5 minutos      |

    Os seguintes exemplos mostram uma regra criada no portal Azure que corresponde a estas definições:

    ![Criar uma regra de escala automática para aumentar o número de casos vm](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Para criar a regra, selecione **Adicionar**


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para escalar automaticamente
À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.

1. Escolha **adicionar uma regra** novamente.
2. Crie uma regra que diminua o número de casos de VM numa escala definida quando a carga média de CPU cai abaixo de 30% num período de 10 minutos. Quando a regra dispara, o número de casos de VM diminui 20%.

    Use a mesma abordagem que com a regra anterior. Ajuste as seguintes definições para a sua regra:
    
    | Parâmetro              | Explicação                                                                                                          | Valor          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                      | Menor do que   |
    | *Limiar*            | A percentagem que faz com que a regra da escala automática desencadeie uma ação.                                                 | 30             |
    | *Operação*            | Define se o conjunto de escala deve escalar para cima ou para baixo quando a regra se aplica e por que incremento                         | Diminuir por cento por |
    | *Contagem de exemplos*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                             | 20             |

3. Para criar a regra, selecione **Adicionar**


## <a name="define-autoscale-instance-limits"></a>Definir limites de instância de escala automática
O seu perfil de escala automática deve definir um número mínimo, máximo e padrão de instâncias VM. Quando as suas regras de escala automática são aplicadas, estes limites de instância certificam-se de que não se ultrapassa o número máximo de ocorrências, ou escala para além do mínimo de instâncias.

1. Estabeleça os seguintes limites de instância:

    | Mínimo | Máximo | Predefinição|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Para aplicar as suas regras de escala automática e os limites de instância, selecione **Guardar**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorizar o número de ocorrências num conjunto de escala
Para ver o número e o estado das instâncias VM, selecione **Instâncias** do menu no lado esquerdo da janela definida pela balança. O estado indica se a instância VM está *a criar* à medida que a balança se esescala automaticamente, ou se está *a eliminar* à medida que a balança entra automaticamente.

![Ver uma lista de instâncias VM definidas em escala](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Escala automática com base num horário
Os exemplos anteriores escalaram automaticamente uma escala definida dentro ou fora com métricas básicas do hospedeiro, como a utilização do CPU. Também pode criar regras de escala automática com base em horários. Estas regras baseadas em horários permitem-lhe reduzir automaticamente o número de casos de VM antes de um aumento previsto da procura de pedidos, como o horário de trabalho central, e depois escalar automaticamente o número de casos numa altura em que se antecipa menos procura, como o fim de semana.

1. Escolha **o escalonamento** do menu do lado esquerdo da janela de conjunto de escala. Para eliminar as regras de escala automática existentes criadas nos exemplos anteriores, escolha o ícone do caixote do lixo.

    ![Eliminar as regras de escala automática existentes](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Escolha **adicionar uma condição de escala**. Selecione o ícone do lápis ao lado do nome da regra e forneça um nome como *Scale out durante cada dia de trabalho*.

    ![Renomear a regra de escala automática padrão](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecione o botão de rádio para **Escalar para uma contagem de instâncias específica**.
4. Para aumentar o número de ocorrências, insira *10* como contagem de ocorrências.
5. Escolha **Repetir dias específicos** para o tipo **'Agendar'.**
6. Selecione todos os dias de trabalho, de segunda a sexta-feira.
7. Escolha o fuso horário adequado e, em seguida, especifique uma hora de **início** das *09:00*.
8. Escolha **adicionar novamente uma condição de escala.** Repita o processo para criar um horário chamado *Escala durante a noite* que escala para *3* instâncias, repete todos os dias da semana, e começa às *18:00*.
9. Para aplicar as suas regras de escala automática baseadas em horários, selecione **Save**.

    ![Criar regras de escala automática que escalam em um horário](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Para ver como são aplicadas as suas regras de escala automática, selecione **o histórico de corridas** através do topo da janela **de escala.** A lista de gráficos e eventos mostra quando as regras de escala automática disparam e o número de instâncias vM na sua escala aumenta ou diminui.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar regras de escala automática para escalar horizontalmente e aumentar ou diminuir o *número* de casos de VM no seu conjunto de escala. Também pode escalar verticalmente para aumentar ou diminuir o *tamanho*da instância VM . Para mais informações, consulte a [escala automática vertical com conjuntos de escala](virtual-machine-scale-sets-vertical-scale-reprovision.md)de máquina virtual .

Para obter informações sobre como gerir os seus casos vm, consulte Gerir conjuntos de [escala de máquinas virtuais com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para aprender a gerar alertas quando as suas regras de escala automática disparam, consulte Utilizar ações de escala automática para enviar notificações de alerta de [e-mail e webhook no Monitor Azure](../azure-monitor/platform/autoscale-webhook-email.md). Também pode utilizar registos de auditoria para enviar notificações de alerta de [e-mail e webhook no Monitor Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
