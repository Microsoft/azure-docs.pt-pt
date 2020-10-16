---
title: Conjuntos de escala de máquina virtual de escala de escala automática no portal Azure
description: Como criar regras de autoescalação para conjuntos de escala de máquina virtual no portal Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms:service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: ac42fe3265163a5a967524fe11063803c9ca91d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080595"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Escalar automaticamente uma escala de máquina virtual definida no portal Azure
Quando criar um conjunto de dimensionamento, pode definir o número de instâncias de VM que quer executar. À medida que a sua aplicação exige alterações, pode aumentar ou reduzir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a par da exigência do cliente ou responder às alterações de desempenho durante todo o ciclo de vida da aplicação.

Este artigo mostra-lhe como criar regras de autoescalação no portal Azure que monitorizam o desempenho das instâncias VM no seu conjunto de escala. Estas regras de autoescala aumentam ou diminuem o número de casos de VM em resposta a estas métricas de desempenho. Também pode completar estes passos com [a Azure PowerShell](tutorial-autoscale-powershell.md) ou o [Azure CLI](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de autoescala, precisa de um conjunto de escala de máquina virtual existente. Pode criar um conjunto de escala com o [portal Azure](quick-create-portal.md), [Azure PowerShell,](quick-create-powershell.md)ou [Azure CLI](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Criar uma regra para escalar automaticamente
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controla as métricas a monitorizar, como a CPU ou o disco, quando a carga da aplicação tem de cumprir um determinado limiar e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

1. Abra o portal Azure e selecione **grupos** de recursos do menu do lado esquerdo do painel.
2. Selecione o grupo de recursos que contém o seu conjunto de escala e, em seguida, escolha a sua escala definida na lista de recursos.
3. Escolha **o escalonamento** do menu no lado esquerdo da janela de conjunto de escala. Selecione o botão para ativar a **autoescalação:**

    ![Ativar a autoescalação no portal Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Introduza um nome para as suas definições, como *a escala automática,* e, em seguida, selecione a opção para **Adicionar uma regra**.

5. Vamos criar uma regra que aumente o número de instâncias VM numa escala definida quando a carga média de CPU é superior a 70% durante um período de 10 minutos. Quando a regra dispara, o número de instâncias em VM é aumentado em 20%. Em conjuntos de escala com um pequeno número de instâncias VM, pode definir a contagem de **operação** para *aumentar* e, em seguida, especificar *1* ou *2* para a *contagem de exemplos*. Em conjuntos de escala com um grande número de instâncias VM, um aumento de 10% ou 20% de instâncias VM pode ser mais adequado.

    Especificar as seguintes definições para a sua regra:
    
    | Parâmetro              | Explicação                                                                                                         | Valor          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregação do tempo*     | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média        |
    | *Nome métrico*          | A métrica de desempenho para monitorizar e aplicar ações ao conjunto de dimensionamento.                                                   | Percentagem da CPU |
    | *Estatística do intervalo de agregação* | Define como as métricas recolhidas em cada vez que os grãos devem ser agregados para análise.                             | Média        |
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                     | Maior do que   |
    | *Limiar*            | A percentagem que faz com que a regra da autoescala desencadeie uma ação.                                                 | 70             |
    | *Duration*             | A quantidade de tempo monitorizado antes dos valores de métrica e limiar serem comparados. Não inclui período de arrefecimento.                                   | 10 minutos     |
    | *Operação*            | Define se a balança definida deve escalar para cima ou para baixo quando a regra se aplica e por que incremento.                        | Aumentar por cento em |
    | *Contagem de exemplos*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                            | 20             |
    | *Repouso (minutos)*  | A quantidade de tempo de espera antes de a regra ser aplicada novamente para que as ações de dimensionamento automático tenham tempo de entrar em vigor. | 5 minutos      |

    Os exemplos a seguir mostram uma regra criada no portal Azure que corresponde a estas configurações:

    ![Criar uma regra de escala automática para aumentar o número de instâncias VM](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Para criar a regra, **selecione Adicionar**


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para escalar automaticamente em
À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.

1. Escolha **adicionar uma regra** novamente.
2. Crie uma regra que diminua o número de instâncias VM numa escala definida quando a carga média de CPU cai abaixo de 30% durante um período de 10 minutos. Quando a regra dispara, o número de casos de VM diminui em 20%.

    Utilize a mesma abordagem que a regra anterior. Ajuste as seguintes definições para a sua regra:
    
    | Parâmetro              | Explicação                                                                                                          | Valor          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                      | Menor do que   |
    | *Limiar*            | A percentagem que faz com que a regra da autoescala desencadeie uma ação.                                                 | 30             |
    | *Operação*            | Define se o conjunto de escala deve escalar para cima ou para baixo quando a regra se aplica e por que incremento                         | Diminuir por cento em |
    | *Contagem de exemplos*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                             | 20             |

3. Para criar a regra, **selecione Adicionar**


## <a name="define-autoscale-instance-limits"></a>Definir limites de instância de escala automática
O seu perfil de autoescalação deve definir um número mínimo, máximo e predefinido de instâncias VM. Quando as suas regras de autoescala são aplicadas, estes limites de instância certificam-se de que não se escala para além do número máximo de casos, ou escala para além do mínimo de casos.

1. Definir os seguintes limites de instância:

    | Mínimo | Máximo | Predefinição|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Para aplicar as suas regras de autoescala e limites de instância, **selecione Guardar**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitor número de casos em um conjunto de escala
Para ver o número e o estado das instâncias VM, selecione **Instâncias** do menu no lado esquerdo da janela definida pela balança. O estado indica se a instância VM está *a criar* à medida que a escala se escala automaticamente, ou se está *a excluir* à medida que a escala se escala automaticamente.

![Ver uma lista de instâncias VM definidas em escala](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Autoescala com base num horário
Os exemplos anteriores escalaram automaticamente uma escala definida dentro ou fora com métricas básicas de hospedeiro, como a utilização do CPU. Também pode criar regras de autoescala com base em horários. Estas regras baseadas em horários permitem-lhe reduzir automaticamente o número de instâncias em VM antes de um aumento previsto da procura de aplicações, como horários de trabalho essenciais, e depois escalar automaticamente o número de casos numa altura em que se antecipa menos procura, como o fim de semana.

1. Escolha **o escalonamento** do menu no lado esquerdo da janela de conjunto de escala. Para eliminar as regras de autoescala existentes criadas nos exemplos anteriores, escolha o ícone do caixote do lixo.

    ![Eliminar as regras de autoescala existentes](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Escolha **adicionar uma condição de escala**. Selecione o ícone do lápis ao lado do nome da regra e forneça um nome como *Scale out durante cada dia de trabalho*.

    ![Mude o nome da regra de autoescalação predefinida](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecione o botão de rádio para **escalar para uma contagem de instâncias específica**.
4. Para aumentar o número de ocorrências, insira *10* como a contagem de exemplos.
5. Escolha **Repita os dias específicos** para o tipo **Agendamento.**
6. Selecione todos os dias de trabalho, de segunda a sexta-feira.
7. Escolha o devido intervalo de tempo e, em seguida, especifique uma hora de **início** de *09:00*.
8. Opte por **adicionar novamente uma condição de escala.** Repita o processo para criar um horário chamado *Scale durante a noite* que escala para *3* instâncias, repete todos os dias da semana e começa às *18:00*.
9. Para aplicar as suas regras de autoescala baseadas no horário, **selecione Save**.

    ![Criar regras de autoescala que escalam em um horário](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Para ver como são aplicadas as suas regras de autoescala, selecione **Executar o histórico** através da janela de **escala.** A lista de gráficos e eventos mostra quando as regras de autoescala disparam e o número de instâncias VM na sua escala aumenta ou diminui.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar regras de autoescala para escalar horizontalmente e aumentar ou diminuir o *número* de instâncias em VM no seu conjunto de escala. Também pode escalar verticalmente para aumentar ou diminuir o *tamanho*da instância VM . Para obter mais informações, consulte [autoescala vertical com conjuntos de escala de máquina virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as suas instâncias em VM, consulte [Gerir conjuntos de balanças de máquinas virtuais com Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Para aprender a gerar alertas quando as suas regras de autoescalação disparam, consulte [utilizar ações de autoescala para enviar notificações de alerta de email e webhook no Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Também pode [utilizar registos de auditoria para enviar notificações de alerta de email e webhook no Azure Monitor](../azure-monitor/platform/alerts-log-webhook.md).
