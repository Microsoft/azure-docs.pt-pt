---
title: Use a Política Azure para aplicar configurações de cluster à escala
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Use a Política Azure para aplicar configurações de cluster à escala
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121461"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Use a Política Azure para aplicar configurações de cluster à escala

Pode utilizar a Política Azure para aplicar configurações `Microsoft.KubernetesConfiguration/sourceControlConfigurations` (tipo de recurso) à escala nos agrupamentos de Kubernetes ativados por Azure Arc ( `Microsoft.Kubernetes/connectedclusters` ).

Para utilizar a Política Azure, selecione uma definição de política existente e crie uma atribuição de política. Ao criar a atribuição de políticas:
1. Desa estação de âmbito para a atribuição.
    * O âmbito será um grupo de recursos Azure ou subscrição. 
2. Desa estale os parâmetros para a configuração que será criada. 

Uma vez criada a atribuição, o motor Azure Policy identifica todos os clusters Azure Arc habilitados para Kubernetes localizados dentro do âmbito e aplica a configuração a cada cluster.

Pode criar várias configurações, cada uma apontando para um repo Git diferente, usando várias atribuições de política. Por exemplo, um repo para o operador central de TI/cluster e outros repositórios para equipas de aplicação.

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

Após a criação da atribuição de políticas, a configuração é aplicada aos novos clusters Azure Arc ativados por Kubernetes criados no âmbito da atribuição de políticas.

Para os clusters existentes, terá de executar manualmente uma tarefa de reparação. Esta tarefa normalmente leva 10 a 20 minutos para que a atribuição de política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verifique uma atribuição de política

1. No portal Azure, navegue para um dos seus aglomerados de Kubernetes.
1. Na secção **Definições** da barra lateral, selecione **Políticas**. 
    * Na lista de políticas, deverá ver a atribuição de política que criou anteriormente com o **estado de Conformidade** definido como *Conforme*.
1. Na secção **Definições** da barra lateral, selecione **Configurações**.
    * Na lista de configurações, deverá ver a configuração criada pela atribuição de políticas.
1. Use `kubectl` para interrogar o aglomerado. 
    * Você deve ver o espaço de nome e artefactos que foram criados pelos recursos de configurações.
    * Dentro de 5 minutos (assumindo que o cluster tem conectividade de rede com Azure), deve ver os objetos descritos pelos manifestos em Git repo, a ser criados no cluster.

## <a name="next-steps"></a>Passos seguintes

[Configurar o Monitor Azure para contentores com arco Azure habilitado para aglomerados Kubernetes](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
