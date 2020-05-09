---
title: Planear e gerir custos
titleSuffix: Azure Machine Learning
description: Planeie e gerencie os custos para o Azure Machine Learning com análise de custos no portal Azure. Ao construir modelos de aprendizagem automática, aprenda mais dicas de poupança de custos para baixar o seu custo.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 2161a9e4460526113aaf89609b72250a09fc6af3
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891208"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planear e gerir custos para o Azure Machine Learning

Este artigo descreve como planear e gerir custos para o Azure Machine Learning. Em primeiro lugar, usa a calculadora de preços Azure para ajudar a planear custos antes de adicionar quaisquer recursos. Em seguida, à medida que adiciona os recursos Azure, reveja os custos estimados. Por fim, utilize dicas de poupança de custos enquanto treina o seu modelo com clusters computacionais de Machine Learning Azure geridos.

Depois de começar a usar recursos de Aprendizagem automática Azure, use as funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos. Reveja também os custos previstos e identifique as tendências de gastos para identificar áreas onde possa querer agir.

Entenda que os custos para o Azure Machine Learning são apenas uma parte dos custos mensais na sua conta Azure. Se estiver a utilizar outros serviços Azure, é cobrado por todos os serviços e recursos do Azure utilizados na sua subscrição Azure, incluindo os serviços de terceiros. Este artigo explica como planear e gerir os custos para o Azure Machine Learning. Depois de conhecer os custos de gestão do Azure Machine Learning, aplique métodos semelhantes para gerir os custos de todos os serviços Azure utilizados na sua subscrição.

Quando treina os seus modelos de machine learning, utilize clusters computacionais geridos pelo Azure Machine Learning para tirar partido de dicas mais economizadoras de custos:

* Configure os seus clusters de treino para autoscalcificação
* Detete quotas na sua subscrição e espaços de trabalho
* Desajuste as políticas de rescisão na sua execução de formação
* Utilize máquinas virtuais de baixa prioridade (VM)
* Utilize uma instância VM reservada azure

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos suporta diferentes tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Para ver os dados dos custos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure. 

Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>Custos de estimativa

Utilize a calculadora de [preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar os recursos numa conta azure machine learning. À esquerda, selecione **AI + Machine Learning**e, em seguida, selecione **Azure Machine Learning** para começar.  

A imagem seguinte mostra a estimativa de custos utilizando a calculadora:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custos na calculadora Azure":::

À medida que adiciona novos recursos ao seu espaço de trabalho, volte a esta calculadora e adicione o mesmo recurso aqui para atualizar as suas estimativas de custos.

Enquanto a edição da Enterprise está em pré-visualização, não há sobretaxa ML. Quando a edição da Enterprise estiver geralmente disponível, terá uma sobretaxa de aprendizagem automática (para formação e inferência).  Para mais detalhes, preços de [Aprendizagem automática Azure.](https://azure.microsoft.com/pricing/details/machine-learning/)

## <a name="get-cost-alerts"></a>Receba alertas de custos

Crie [orçamentos](../cost-management/tutorial-acm-create-budgets.md) para gerir custos e criar [alertas](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) que notifiquem automaticamente as partes interessadas de gastar anomalias e riscos de gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições do Azure e grupos de recursos, por isso são úteis como parte de uma estratégia global de monitorização de custos. No entanto, os orçamentos e alertas podem ter uma funcionalidade limitada para gerir os custos individuais do serviço Azure porque são projetados para rastrear custos a um nível mais elevado.

## <a name="monitor-costs"></a>Monitorizar os custos

Ao utilizar recursos com a Azure Machine Learning, incorre em custos. Os custos da unidade de utilização de recursos Azure variam de acordo com intervalos de tempo (segundos, minutos, horas e dias) ou por pedido de utilização da unidade. Assim que a utilização do Azure Machine Learning começa, os custos são incorridos. Veja estes custos no painel de análise de [custos](../cost-management/quick-acm-cost-analysis.md) no portal Azure.

Veja os custos em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são de dia, corrente, mês anterior e ano. Encara também os custos com orçamentos e os custos previstos. Mudar para visões mais longas ao longo do tempo ajuda-o a identificar as tendências de gastos e a ver onde poderia ter ocorrido gastos excessivos. Se criou orçamentos, veja onde excedeu.  

Não verá uma área de serviço separada para machine learning.  Em vez disso, verá os vários recursos que adicionou aos seus espaços de trabalho de Machine Learning.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Utilize o cluster de computação azure machine learning (AmlCompute)

Com dados em constante mudança, é necessário um treino e reconversão rápidos e simplificados para manter modelos precisos. No entanto, o treino contínuo tem um custo, especialmente para modelos de aprendizagem profunda em GPUs. 

Os utilizadores de Azure Machine Learning podem usar o cluster de computação azure machine learning gerido, também chamado AmlCompute. A AmlCompute suporta uma variedade de opções de GPU e CPU. O AmlCompute é hospedado internamente em nome da sua subscrição pela Azure Machine Learning, mas fornece a mesma segurança, conformidade e governação de nível empresarial na escala de nuvem Azure IaaS.

Como estas piscinas de computação estão dentro da infraestrutura IaaS do Azure, você pode implementar, escalar e gerir o seu treino com os mesmos requisitos de segurança e conformidade que o resto da sua infraestrutura.  Estas implementações ocorrem na sua subscrição e obedecem às suas regras de governação. Saiba mais sobre a Computação de [Aprendizagem automática Azure.](how-to-set-up-training-targets.md#amlcompute)

## <a name="configure-training-clusters-for-autoscaling"></a>Configure clusters de treino para autoscalcificação

Autoscalcificação de clusters com base nos requisitos da sua carga de trabalho ajuda a reduzir os seus custos para que utilize apenas o que precisa. Os clusters AmlCompute são projetados para escalar de forma dinâmica automaticamente com base nos requisitos da sua carga de trabalho. O cluster pode ser dimensionado até ao número máximo de nós provisionados e dentro do contingente designado para a subscrição. À medida que cada execução se completa, o cluster libertará nós e escala automática para a contagem mínima de nó designado.

Além de definir o número mínimo e máximo de nós, ajuste o tempo que o nó está inativo antes da balança para baixo. Por defeito, o tempo de inatividade antes da descida da balança está definido para 120 segundos.

+ Se realizar uma experimentação menos iterativa, reduza este tempo para economizar custos. 
+ Se realizar uma experiência de dev/teste altamente iterativa, poderá ter de aumentar isto para que não esteja a pagar por uma escala constante para cima e para baixo após cada alteração ao seu script ou ambiente de treino.

Os clusters AmlCompute podem ser configurados para a alteração dos requisitos de carga de trabalho no portal Azure, utilizando a [classe AmlCompute SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py), [AmlCompute CLI,](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)com as [APIs REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Definir quotas sobre recursos

Tal como outros recursos computacionais da Azure, o AmlCompute vem com uma configuração inerente [(ou limite).](how-to-manage-quotas.md#azure-machine-learning-compute) Esta quota é da família VM (por exemplo, série Dv2, série NCv3) e varia por região para cada subscrição. As subscrições começam com pequenos incumprimentos para fazê-lo andar, mas use esta configuração para controlar a quantidade de recursos Amlcompute disponíveis para ser fiado na sua subscrição. 

Configurar também a quota de nível de espaço de [trabalho por família VM,](how-to-manage-quotas.md#workspace-level-quota)para cada espaço de trabalho dentro de uma subscrição. Isto permite-lhe ter um controlo mais granular sobre os custos que cada espaço de trabalho pode potencialmente incorrer e restringir certas famílias vM. 

Para definir quotas ao nível do espaço de trabalho, comece no [portal Azure.](https://portal.azure.com)  Selecione qualquer espaço de trabalho na sua subscrição e selecione **Utilizações + quotas** no painel esquerdo. Em seguida, selecione o separador **de quotas Configure** para visualizar as quotas. Você precisa de privilégios no âmbito de subscrição para definir esta quota, uma vez que é um cenário que afeta vários espaços de trabalho.

## <a name="set-run-auto-termination-policies"></a>Definir executar políticas de auto-rescisão automática 

Configure os seus ensaios para limitar a sua duração ou terminá-los mais cedo em caso de certas condições, especialmente quando estiver a utilizar as capacidades de afinação de hiperparâmetros integrados do Azure Machine Learning ou de machine learning automatizados. 

Aqui estão algumas opções que tem:
* Defina um `max_run_duration_seconds` parâmetro chamado na sua Configuração Run para controlar a duração máxima que uma corrida pode estender-se à computação que escolher (computação em nuvem local ou remota).
* Para [afinação](how-to-tune-hyperparameters.md#early-termination)de hiperparâmetros, defina uma política de rescisão antecipada a partir de uma política de bandidos, uma política de paragem mediana ou uma política de seleção de Truncation. Além disso, utilize também `max_total_runs` `max_duration_minutes` parâmetros como ou para controlar ainda mais as várias varreduras de hiperparâmetros.
* Para aprendizagem automática de [máquinas,](how-to-configure-auto-train.md#exit)detete políticas de rescisão semelhantes utilizando a `enable_early_stopping` bandeira. Utilize também propriedades `iteration_timeout_minutes` `experiment_timeout_minutes` como e para controlar a duração máxima de uma corrida ou para toda a experiência.

## <a name="use-low-priority-vms"></a>Utilizar VMs de baixa prioridade

O Azure permite-lhe utilizar a capacidade excessiva não utilizada como VMs de baixa prioridade em conjuntos de escala de máquinas virtuais, Lote e serviço de Machine Learning. Estas dotações são preventivas, mas têm um preço reduzido em comparação com os VMs dedicados. Em geral, recomendamos a utilização de VMs de baixa prioridade para cargas de trabalho em lote ou quando as interrupções são recuperáveis, quer através de reenvies (para inferência de lote) quer através de reinícios (para treino de aprendizagem profunda com checkpointing).

Os VMs de baixa prioridade têm uma única quota separada do valor de quota dedicado, que é da família VM. Saiba [mais sobre as quotas da AmlCompute.](how-to-manage-quotas.md)

Desemqualquer desta forma, desemmelhore a prioridade do seu VM:

* No estúdio, escolha **Low Priority** quando criar um VM.

* Com o Python SDK, detete o `vm_priority` atributo na sua configuração de provisionamento.  

    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                               vm_priority='lowpriority',
                                                               max_nodes=4)
    ```

* Utilizando o CLI, `vm-priority`derpor o seguinte:

    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 VMs de baixa prioridade não funcionam para instâncias de computação, uma vez que precisam apoiar experiências de cadernos interativos. 

## <a name="use-reserved-instances"></a>Utilizar instâncias reservadas

A Azure Reserved VM Instance oferece outra forma de obter poupanças enormes nos recursos computacionais, comprometendo-se a termos de um ano ou três anos. Estes descontos variam até 72% dos preços de pagamento e são aplicados diretamente à sua fatura mensal do Azure.

A Azure Machine Learning Compute suporta casos reservados inerentemente. Assim, se adquiriu uma instância reservada de um ano ou três anos, aplicaremos automaticamente esse desconto reservado contra o cálculo gerido que é usado no Azure Machine Learning sem exigir qualquer configuração adicional a partir do seu fim.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:
* [Gerir e aumentar as quotas de recursos](how-to-manage-quotas.md)
* [Gerir os custos com análise de [custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md)
* [Computação azure machine learning.](how-to-set-up-training-targets.md#amlcompute)
