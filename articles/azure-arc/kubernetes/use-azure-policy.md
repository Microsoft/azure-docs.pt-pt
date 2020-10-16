---
title: Utilize a Política Azure para aplicar configurações de cluster à escala (Visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Use a Política Azure para aplicar configurações de cluster à escala
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050035"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Utilize a Política Azure para aplicar configurações de cluster à escala (Visualização)

## <a name="overview"></a>Descrição geral

Utilize a Política Azure para impor que cada `Microsoft.Kubernetes/connectedclusters` recurso ou Git-Ops recurso habilitado tenha aplicado `Microsoft.ContainerService/managedClusters` especificamente `Microsoft.KubernetesConfiguration/sourceControlConfigurations` nele. Para utilizar a Política Azure selecione uma definição de política existente e crie uma atribuição de política. Ao criar a atribuição de política, definiu o âmbito para a atribuição: este será um grupo de recursos Azure ou subscrição. Também definiu os parâmetros para o `sourceControlConfiguration` que será criado. Uma vez criada a atribuição, o motor Policy identificará todos `connectedCluster` ou `managedCluster` recursos que estão localizados dentro do âmbito e aplicará o `sourceControlConfiguration` a cada um.

Se estiver a utilizar vários repos git como fontes de verdade para cada cluster (por exemplo, um repo para operador central de TI/cluster e outros repos para equipas de aplicação), pode permitir isso utilizando várias atribuições políticas, cada atribuição de política configurada para usar um repo Git diferente.

## <a name="prerequisite"></a>Pré-requisito

Certifique-se de que tem `Microsoft.Authorization/policyAssignments/write` permissões no âmbito (grupo de subscrição ou recursos) onde pretende criar esta atribuição de política.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

1. No portal Azure, navegue para a Política, e na secção de **Autorização** da barra lateral, selecione **Definições**.
2. Escolha a política incorporada "Implementar GitOps para Kubernetes" na categoria "Kubernetes" e clique em **Atribuir**.
3. Desaprote o **Âmbito** para o grupo de gestão, subscrição ou grupo de recursos onde a atribuição de política se aplicará.
4. Se pretender excluir quaisquer recursos do âmbito de aplicação da política, então desa um conjunto **de exclusões**.
5. Dê à atribuição de política um **nome** e **descrição** que pode usar para identificá-lo facilmente.
6. Certifique-se de que **a aplicação da política** está definida como *Ativada*.
7. Selecione **Seguinte**.
8. Definir valores de parâmetros que serão utilizados durante a criação do `sourceControlConfiguration` .
9. Selecione **Seguinte**.
10. Ativar **Criar uma tarefa de remediação**.
11. Garanta que **a Criação de uma identidade gerida** é verificada e que a identidade terá permissões do **Contribuinte.** Consulte [este doc](../../governance/policy/assign-policy-portal.md) e o comentário neste [doc](../../governance/policy/how-to/remediate-resources.md) para obter mais informações sobre as permissões necessárias.
12. Selecione **Rever + criar**.

Após a criação da atribuição da política, para qualquer novo `connectedCluster` recurso (ou `managedCluster` recurso com os agentes GitOps instalados) que esteja localizado no âmbito da atribuição, o `sourceControlConfiguration` será aplicado. Para os clusters existentes, terá de executar manualmente uma tarefa de reparação. Normalmente demora de 10 a 20 minutos para que a atribuição da política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verifique uma atribuição de política

1. No portal Azure, navegue para um dos seus `connectedCluster` recursos, e na secção **Definições** da barra lateral, selecione **Políticas**. (O cluster UX para AKS ainda não está implementado, mas está a chegar.)
2. Na lista, deverá ver a atribuição de política que criou acima, e o **estado de Conformidade** deve ser *conforme.*
3. Na secção **Definições** da barra lateral, selecione **Configurações**.
4. Na lista, deve ver o `sourceControlConfiguration` que a atribuição de política criou.
5. Use **kubectl** para interrogar o cluster: você deve ver o espaço de nome e artefactos que foram criados pelo `sourceControlConfiguration` .
6. Dentro de 5 minutos, você deve ver no aglomerado os artefactos que são descritos nos manifestos no repo Git configurado.

## <a name="next-steps"></a>Passos seguintes

* [Configurar o Monitor Azure para contentores com arco habilitado a clusters Kubernetes](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
