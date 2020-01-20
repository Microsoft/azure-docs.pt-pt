---
title: Dimensionamento automático de conjuntos de dimensionamento de máquinas virtuais no portal do Azure
description: Como criar regras de dimensionamento automático para conjuntos de dimensionamento de máquinas virtuais no portal do Azure
author: cynthn
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: ecd80f49f0161c8bbc6ab7309f2af89e2ded1fe9
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278189"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais no portal do Azure
Quando criar um conjunto de dimensionamento, pode definir o número de instâncias de VM que quer executar. À medida que a sua aplicação exige alterações, pode aumentar ou reduzir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a par da exigência do cliente ou responder às alterações de desempenho durante todo o ciclo de vida da aplicação.

Este artigo mostra como criar regras de dimensionamento automático no portal do Azure que monitoram o desempenho das instâncias de VM em seu conjunto de dimensionamento. Essas regras de dimensionamento automático aumentam ou diminuem o número de instâncias de VM em resposta a essas métricas de desempenho. Você também pode concluir essas etapas com [Azure PowerShell](tutorial-autoscale-powershell.md) ou o [CLI do Azure](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de dimensionamento automático, você precisa de um conjunto de dimensionamento de máquinas virtuais existente. Você pode criar um conjunto de dimensionamento com o [portal do Azure](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)ou [CLI do Azure](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Criar uma regra para escalar horizontalmente automaticamente
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controla as métricas a monitorizar, como a CPU ou o disco, quando a carga da aplicação tem de cumprir um determinado limiar e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

1. Abra o portal do Azure e selecione **grupos de recursos** no menu no lado esquerdo do painel.
2. Selecione o grupo de recursos que contém o conjunto de dimensionamento e escolha o conjunto de dimensionamento na lista de recursos.
3. Escolha **dimensionamento** no menu no lado esquerdo da janela do conjunto de dimensionamento. Selecione o botão para **habilitar o dimensionamento automático**:

    ![Habilitar o dimensionamento automático no portal do Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Insira um nome para suas configurações, como *dimensionamento automático*e selecione a opção para **Adicionar uma regra**.

5. Vamos criar uma regra que aumente o número de instâncias de VM em um conjunto de dimensionamento quando a carga de CPU média for maior que 70% por um período de 10 minutos. Quando a regra é disparada, o número de instâncias de VM aumenta em 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, você pode definir a **operação** para *aumentar a contagem por* e, em seguida, especificar *1* ou *2* para a *contagem de instâncias*. Em conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 10% ou 20% de instâncias de VM pode ser mais apropriado.

    Especifique as seguintes configurações para sua regra:
    
    | Parâmetro              | Explicação                                                                                                         | Valor          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregação de tempo*     | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média        |
    | *Nome da métrica*          | A métrica de desempenho para monitorizar e aplicar ações ao conjunto de dimensionamento.                                                   | Percentagem da CPU |
    | *Estatística de intervalo de tempo* | Define como as métricas coletadas em cada intervalo de tempo devem ser agregadas para análise.                             | Média        |
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                     | Mais do que   |
    | *Os*            | A porcentagem que faz com que a regra de dimensionamento automático dispare uma ação.                                                 | 70             |
    | *Permanência*             | A quantidade de tempo monitorizado antes dos valores de métrica e limiar serem comparados.                                   | 10 minutos     |
    | *Operação*            | Define se o conjunto de dimensionamento deve ser escalado verticalmente ou reduzido quando a regra se aplica e por qual incremento                        | Aumentar porcentagem por |
    | *Contagem de instâncias*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                            | 20             |
    | *Resfriamento (minutos)*  | A quantidade de tempo de espera antes de a regra ser aplicada novamente para que as ações de dimensionamento automático tenham tempo de entrar em vigor. | 5 minutos      |

    Os exemplos a seguir mostram uma regra criada no portal do Azure que corresponde a essas configurações:

    ![Criar uma regra de dimensionamento automático para aumentar o número de instâncias de VM](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Para criar a regra, selecione **Adicionar**


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para dimensionar automaticamente
À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.

1. Escolha **Adicionar uma regra** novamente.
2. Crie uma regra que diminua o número de instâncias de VM em um conjunto de dimensionamento quando a carga da CPU média for inferior a 30% em um período de 10 minutos. Quando a regra é disparada, o número de instâncias de VM é reduzido em 20%.

    Use a mesma abordagem da regra anterior. Ajuste as seguintes configurações para sua regra:
    
    | Parâmetro              | Explicação                                                                                                          | Valor          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operador*             | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                      | Menos do que   |
    | *Os*            | A porcentagem que faz com que a regra de dimensionamento automático dispare uma ação.                                                 | 30             |
    | *Operação*            | Define se o conjunto de dimensionamento deve ser escalado verticalmente ou reduzido quando a regra se aplica e por qual incremento                         | Diminuir porcentagem por |
    | *Contagem de instâncias*       | A percentagem de instâncias de VM deve ser alterada quando a regra for acionada.                                             | 20             |

3. Para criar a regra, selecione **Adicionar**


## <a name="define-autoscale-instance-limits"></a>Definir limites de instância de dimensionamento automático
Seu perfil de dimensionamento automático deve definir um número mínimo, máximo e padrão de instâncias de VM. Quando as regras de dimensionamento automático são aplicadas, esses limites de instância garantem que você não expanda além do número máximo de instâncias ou reduza a escala para além do mínimo de instâncias.

1. Defina os seguintes limites de instância:

    | Mínimo | Máximo | Predefinição|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Para aplicar suas regras de dimensionamento automático e limites de instância, selecione **salvar**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorar o número de instâncias em um conjunto de dimensionamento
Para ver o número e o status das instâncias de VM, selecione **instâncias** no menu no lado esquerdo da janela do conjunto de dimensionamento. O status indica se a instância de VM está sendo *criada* conforme o conjunto de dimensionamento é dimensionado automaticamente ou é *excluído* à medida que a escala é dimensionada automaticamente.

![Exibir uma lista de instâncias de VM do conjunto de dimensionamento](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático com base em uma agenda
Os exemplos anteriores dimensionaram automaticamente um conjunto de dimensionamento para dentro ou para fora com métricas de host básicas, como o uso da CPU. Você também pode criar regras de dimensionamento automático com base em agendas. Essas regras baseadas em agendamento permitem que você Escale automaticamente o número de instâncias de VM à frente de um aumento previsto na demanda do aplicativo, como as horas de trabalho principais e, em seguida, dimensione automaticamente o número de instâncias de cada vez que você prevê menos demanda, como o fim de semana.

1. Escolha **dimensionamento** no menu no lado esquerdo da janela do conjunto de dimensionamento. Para excluir as regras de dimensionamento automático existentes criadas nos exemplos anteriores, escolha o ícone de lixeira.

    ![Excluir as regras de dimensionamento automático existentes](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Escolha **Adicionar uma condição de dimensionamento**. Selecione o ícone de lápis ao lado de nome da regra e forneça um nome como *escalar horizontalmente durante cada dia útil*.

    ![Renomear a regra padrão de dimensionamento automático](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecione o botão de opção para **Dimensionar para uma contagem de instâncias específica**.
4. Para escalar verticalmente o número de instâncias, insira *10* como a contagem de instâncias.
5. Escolha **repetir dias específicos** para o tipo de **agendamento** .
6. Selecione todos os dias úteis, de segunda-feira a sexta-feira.
7. Escolha o fuso horário apropriado e especifique uma **hora de início** de *09:00*.
8. Escolha **Adicionar uma condição de dimensionamento** novamente. Repita o processo para criar um agendamento chamado *Scale in durante a noite* que é dimensionada para *3* instâncias, repete todos os dias da semana e começa às *18:00*.
9. Para aplicar suas regras de dimensionamento automático com base em agenda, selecione **salvar**.

    ![Criar regras de dimensionamento automático que são dimensionadas em uma agenda](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Para ver como as regras de dimensionamento automático são aplicadas, selecione **histórico de execução** na parte superior da janela de **dimensionamento** . A lista de eventos e de grafo mostra quando as regras de dimensionamento automático são disparadas e o número de instâncias de VM no seu conjunto de dimensionamento aumenta ou diminui.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu a usar as regras de dimensionamento automático para dimensionar horizontalmente e aumentar ou diminuir o *número* de instâncias de VM em seu conjunto de dimensionamento. Você também pode dimensionar verticalmente para aumentar ou diminuir o *tamanho*da instância da VM. Para obter mais informações, consulte [autoescala vertical com conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerenciar suas instâncias de VM, consulte [gerenciar conjuntos de dimensionamento de máquinas virtuais com Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando suas regras de dimensionamento automático forem disparadas, consulte [usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure monitor](../azure-monitor/platform/autoscale-webhook-email.md). Você também pode [usar logs de auditoria para enviar notificações de alerta por email e webhook no Azure monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
