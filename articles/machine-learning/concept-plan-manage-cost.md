---
title: Planear e gerir custos
titleSuffix: Azure Machine Learning
description: Planeie e gere os custos para a Azure Machine Learning com análise de custos no portal Azure. Aprenda mais dicas de poupança de custos para baixar o seu custo ao construir modelos ML.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: d8f74e438175758b1f09e1809b5eba15c1b26c3c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873636"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planear e gerir os custos para a Azure Machine Learning

Este artigo descreve como planear e gerir custos para a Azure Machine Learning. Primeiro, usa a calculadora de preços Azure para ajudar a planear os custos antes de adicionar quaisquer recursos. Em seguida, ao adicionar os recursos Azure, reveja os custos estimados. Por fim, utilize dicas de poupança de custos enquanto treina o seu modelo com clusters de cálculo Azure Machine Learning geridos.

Depois de ter começado a usar recursos de Aprendizagem automática Azure, use as funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos. Reveja também os custos previstos e identifique as tendências de gastos para identificar áreas onde possa querer atuar.

Entenda que os custos para a Azure Machine Learning são apenas uma parte dos custos mensais na sua conta Azure. Se estiver a utilizar outros serviços Azure, é cobrado por todos os serviços e recursos da Azure utilizados na sua subscrição Azure, incluindo os serviços de terceiros. Este artigo explica como planear e gerir os custos para a Azure Machine Learning. Depois de estar familiarizado com os custos de gestão da Azure Machine Learning, aplique métodos semelhantes para gerir custos para todos os serviços Azure utilizados na sua subscrição.

Quando treina os seus modelos de machine learning, utilize clusters de cálculo Azure Machine Learning geridos para tirar partido de mais dicas de poupança de custos:

* Configure os seus clusters de formação para autoscaling
* Definir quotas na sua subscrição e espaços de trabalho
* Desaça as políticas de rescisão na sua formação
* Utilize máquinas virtuais de baixa prioridade (VM)
* Use uma instância VM reservada a azulada

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos suporta diferentes tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para ver os dados dos custos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure. 

Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Estimativa de custos antes de usar Azure Machine Learning

Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para estimar os custos antes de criar os recursos numa conta Azure Machine Learning. À esquerda, selecione **AI + Machine Learning** e, em seguida, selecione **Azure Machine Learning** para começar.  

A imagem que se segue mostra a estimativa de custos utilizando a calculadora:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custos na calculadora Azure":::

Ao adicionar novos recursos ao seu espaço de trabalho, volte a esta calculadora e adicione aqui o mesmo recurso para atualizar as suas estimativas de custos.

Para mais informações, consulte [os preços de Aprendizagem automática Azure.](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Compreenda o modelo completo de faturação para Azure Machine Learning

O Azure Machine Learning funciona na infraestrutura Azure que acumula custos juntamente com a Azure Machine Learning quando implementa o novo recurso. É importante entender que infraestruturas adicionais podem acumular custos. Tens de gerir esse custo quando fazes alterações aos recursos implantados. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Custos que normalmente se acumulam com a Azure Machine Learning

Quando cria recursos para um espaço de trabalho Azure Machine Learning, também são criados recursos para outros serviços Azure. A saber:

* [Registo do Contentor de Azure](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Conta básica
* [Armazenamento do blob do bloco Azure](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (finalidade geral v1)
* [Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Os custos podem acumular-se após a supressão de recursos

Quando elimina um espaço de trabalho de Aprendizagem automática Azure no portal Azure ou com o Azure CLI, os seguintes recursos continuam a existir. Continuam a acumular custos até os eliminares.

* Registo de Contentores do Azure
* Armazenamento blob bloco Azure
* Key Vault
* Application Insights

Para eliminar o espaço de trabalho juntamente com estes recursos dependentes, utilize o SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Se criar o Serviço Azure Kubernetes (AKS) no seu espaço de trabalho, ou se anexar quaisquer recursos de computação ao seu espaço de trabalho, deve eliminá-los separadamente no [portal Azure.](https://portal.azure.com)

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Usando o crédito de pré-pagamento Azure com Azure Machine Learning

Você pode pagar os custos de Azure Machine Learning com o seu crédito Azure Prepayment (anteriormente chamado de compromisso monetário). No entanto, não pode utilizar o Azure Prepayment para pagar os custos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.


## <a name="create-budgets"></a>Criar orçamentos

Pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não crie acidentalmente novos recursos que lhe custem dinheiro adicional. Para saber mais sobre as opções do filtro quando criar um orçamento, consulte [opções de Grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer análise adicional de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Outras formas de gerir e reduzir custos para a Azure Machine Learning

Utilize estas dicas para conter os custos dos seus recursos de cálculo de aprendizagem automática.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Use o cluster compute de aprendizagem automática Azure (AmlCompute)

Com a mudança constante de dados, é necessário treino e reciclagem de modelos rápidos e simplificados para manter modelos precisos. No entanto, o treino contínuo tem um custo, especialmente para modelos de aprendizagem profunda em GPUs. 

Os utilizadores do Azure Machine Learning podem utilizar o cluster de cálculo Azure Machine Learning, também chamado AmlCompute. A AmlCompute suporta uma variedade de opções de GPU e CPU. O AmlCompute está hospedado internamente em nome da sua subscrição pela Azure Machine Learning. Fornece a mesma segurança de nível empresarial, conformidade e governação na escala de nuvem Azure IaaS.

Como estas piscinas de computação estão dentro da infraestrutura IaaS da Azure, você pode implantar, escalar e gerir a sua formação com os mesmos requisitos de segurança e conformidade que o resto da sua infraestrutura.  Estas implementações ocorrem na sua subscrição e obedecem às suas regras de governação. Saiba mais sobre [o cálculo Azure Machine Learning](how-to-create-attach-compute-cluster.md).

### <a name="configure-training-clusters-for-autoscaling"></a>Configurar agrupamentos de formação para autoscaling

Os agrupamentos de autoscalagem baseados nos requisitos da sua carga de trabalho ajudam a reduzir os seus custos para que utilize apenas o que precisa.

Os clusters AmlCompute são projetados para escalar dinamicamente com base na sua carga de trabalho. O cluster pode ser dimensionado até ao número máximo de nós que configurar. À medida que cada execução termina, o cluster libertará nós e escala para a contagem mínima de nós configuradas.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Também pode configurar a quantidade de tempo que o nó está inativo antes de reduzir a escala. Por predefinição, o tempo de marcha lenta antes da escala para baixo é definido para 120 segundos.

+ Se realizar menos experimentação iterativa, reduza este tempo para economizar custos.
+ Se realizar uma experimentação altamente iterativa dev/teste, poderá ter de aumentar o tempo para não pagar por uma escalada constante após cada alteração ao seu script de treino ou ambiente.

Os clusters AmlCompute podem ser configurados para os seus requisitos de carga de trabalho em mudança no portal Azure, utilizando a [classe AmlCompute SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), [AmlCompute CLI,](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute)com as [APIs REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Definir quotas em recursos

AmlCompute vem com uma [configuração de quota (ou limite).](how-to-manage-quotas.md#azure-machine-learning-compute) Esta quota é da família VM (por exemplo, série Dv2, série NCv3) e varia por região para cada subscrição. As subscrições começam com pequenos predefinidos para o fazer andar, mas use esta definição para controlar a quantidade de recursos Amlcompute disponíveis para ser fiado na sua subscrição. 

Também configurar [quota de nível de espaço de trabalho pela família VM,](how-to-manage-quotas.md#workspace-level-quotas)para cada espaço de trabalho dentro de uma subscrição. Ao fazê-lo, permite-lhe ter mais controlo granular sobre os custos que cada espaço de trabalho pode potencialmente incorrer e restringir certas famílias de VM. 

Para definir quotas ao nível do espaço de trabalho, comece no [portal Azure](https://portal.azure.com).  Selecione qualquer espaço de trabalho na sua subscrição e selecione **Usages + quotas** no painel esquerdo. Em seguida, selecione o **separador quotas configurar** para visualizar as quotas. Você precisa de privilégios no âmbito de subscrição para definir a quota, uma vez que é um cenário que afeta vários espaços de trabalho.

### <a name="set-run-autotermination-policies"></a>Definir políticas de autotermímia de execução 

Em alguns casos, deve configurar as suas corridas de treino para limitar a sua duração ou terminá-las mais cedo. Por exemplo, quando estiver a utilizar o hiperparametro incorporado da Azure Machine Learning ou a aprendizagem automática de máquinas.

Aqui estão algumas opções que tem:
* Defina um parâmetro chamado `max_run_duration_seconds` no seu RunConfiguration para controlar a duração máxima que uma corrida pode estender-se ao cálculo que escolher (computação de nuvem local ou remota).
* Para [afinação de hiperparímetros,](how-to-tune-hyperparameters.md#early-termination)defina uma política de rescisão antecipada de uma política de bandit, uma política de paragem mediana ou uma política de seleção de truncação. Para controlar ainda mais as varreduras de hiperparímetro, utilize parâmetros como `max_total_runs` ou `max_duration_minutes` .
* Para [aprendizagem automática de máquinas,](how-to-configure-auto-train.md#exit)desateia políticas de rescisão semelhantes utilizando a  `enable_early_stopping` bandeira. Utilize também propriedades como `iteration_timeout_minutes` e para controlar a `experiment_timeout_minutes` duração máxima de uma corrida ou para toda a experiência.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Use VMs de baixa prioridade

O Azure permite-lhe utilizar a capacidade excedentária não utilizada à medida que Low-Priority VMs em conjuntos de escala de máquinas virtuais, Batch e o serviço de Machine Learning. Estas dotações são preventivas, mas têm um preço reduzido em comparação com os VM dedicados. Em geral, recomendamos a utilização de VMs Low-Priority para cargas de trabalho em lote. Deve também utilizá-las quando as interrupções forem recuperáveis através de resubmitamentos (para inferencing de lote) ou através de recomeços (para treinos de aprendizagem profunda com verificação de pontos de verificação).

Low-Priority VM têm uma quota única separada do valor de quota dedicado, que é da família VM. Saiba [mais sobre as quotas AmlCompute](how-to-manage-quotas.md).

 Low-Priority VMs não funcionam para casos de computação, uma vez que precisam de suportar experiências de cadernos interativos.

### <a name="use-reserved-instances"></a>Utilizar instâncias reservadas

Outra forma de economizar em recursos computacional é a Azure Reserved VM Instance. Com esta oferta, compromete-se a cumprir um ano ou três anos de mandato. Estes descontos variam até 72% dos preços de pay-as-you-go e são aplicados diretamente na sua fatura mensal do Azure.

A azure Machine Learning Compute suporta casos reservados inerentemente. Se comprar uma instância reservada de um ano ou três anos, aplicaremos automaticamente desconto contra o seu computo gerido Azure Machine Learning.


## <a name="next-steps"></a>Passos seguintes

- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.
