---
title: Utilizar o Azure Policy para aplicar configurações de cluster em escala (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Use a Política Azure para aplicar configurações de cluster à escala
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: b80e50cb4823632f054de3b7f9da71392f8578d7
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560194"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Utilizar o Azure Policy para aplicar configurações de cluster em escala (Pré-visualização)

## <a name="overview"></a>Descrição Geral

Pode utilizar a Política Azure para impor qualquer um dos seguintes recursos para ter `Microsoft.KubernetesConfiguration/sourceControlConfigurations` aplicação específica:
*  `Microsoft.Kubernetes/connectedclusters` recurso.
* Recurso habilitado para `Microsoft.ContainerService/managedClusters` GitOps. 

Para utilizar a Política Azure, selecione uma definição de política existente e crie uma atribuição de política. Ao criar a atribuição de políticas:
1. Desa estação de âmbito para a atribuição.
    * O âmbito será um grupo de recursos Azure ou subscrição. 
2. Desa estalem os parâmetros para o `sourceControlConfiguration` que será criado. 

Uma vez criada a atribuição, o motor Azure Policy identifica todos `connectedCluster` ou `managedCluster` recursos localizados dentro do âmbito e aplica o `sourceControlConfiguration` a cada um.

Você pode ativar vários repos Git como as fontes da verdade para cada cluster usando várias atribuições políticas. Cada atribuição de política seria configurada para usar um repo Git diferente; por exemplo, um repo para o operador central de TI/cluster e outros repos para as equipas de aplicação.

## <a name="prerequisite"></a>Pré-requisito

Verifique se tem `Microsoft.Authorization/policyAssignments/write` permissões no âmbito (grupo de subscrição ou recursos) onde irá criar esta atribuição de política.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

1. No portal Azure, navegue para a **Política.**
1. Na secção **de Autoria** da barra lateral, selecione **Definições**.
1. Na categoria "Kubernetes", escolha a política incorporada "Implementar GitOps para Kubernetes". 
1. Clique em **Atribuir**.
1. Definir o **Âmbito** para o grupo de gestão, subscrição ou grupo de recursos ao qual a atribuição de política se aplicará.
    * Se pretender excluir quaisquer recursos do âmbito de aplicação da política, desa um conjunto **de exclusões**.
1. Dê à atribuição de política um **nome** e **descrição** facilmente identificáveis.
1. Certifique-se de que **a aplicação da política** está definida como **Ativada**.
1. Selecione **Seguinte**.
1. Desa estale os valores dos parâmetros a utilizar enquanto cria o `sourceControlConfiguration` .
1. Selecione **Seguinte**.
1. Ativar **Criar uma tarefa de remediação**.
1. Verifique **se a criação de uma identidade gerida** é verificada e que a identidade terá permissões do **Contribuinte.** 
    * Para obter mais informações, consulte o Arranque rápido de [uma atribuição de políticas](../../governance/policy/assign-policy-portal.md) e os recursos não conformes com o artigo da Política [Azure.](../../governance/policy/how-to/remediate-resources.md)
1. Selecione **Rever + criar**.

Após a criação da atribuição de políticas, `sourceControlConfiguration` será aplicado qualquer um dos seguintes recursos localizados no âmbito da atribuição:
* Novos `connectedCluster` recursos.
* Novos `managedCluster` recursos com os agentes gitOps instalados. 

Para os clusters existentes, terá de executar manualmente uma tarefa de reparação. Esta tarefa normalmente leva 10 a 20 minutos para que a atribuição de política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verifique uma atribuição de política

1. No portal Azure, navegue para um dos seus `connectedCluster` recursos.
1. Na secção **Definições** da barra lateral, selecione **Políticas**. 
    * O cluster AKS UX ainda não foi implementado.
    * Na lista de políticas, deverá ver a atribuição de política que criou anteriormente com o **estado de Conformidade** definido como *Conforme*.
1. Na secção **Definições** da barra lateral, selecione **Configurações**.
    * Na lista de configurações, deve ver o `sourceControlConfiguration` que a atribuição de política criou.
1. Use `kubectl` para interrogar o aglomerado. 
    * Você deve ver o espaço de nome e os artefactos que foram criados pelo `sourceControlConfiguration` .
    * Dentro de 5 minutos, você deve ver no aglomerado os artefactos que são descritos nos manifestos no repo Git configurado.

## <a name="next-steps"></a>Passos seguintes

* [Configurar o Monitor Azure para contentores com arco habilitado a clusters Kubernetes](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
