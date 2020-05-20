---
title: Utilize a Política Azure para aplicar configurações de cluster à escala (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utilize a Política Azure para aplicar configurações de cluster em escala
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: 64be872e4e76545c015378417410cbbfad95ebfd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680666"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Utilize a Política Azure para aplicar configurações de cluster à escala (Pré-visualização)

## <a name="overview"></a>Descrição geral

Utilize a Política Azure para impor cada um dos recursos ativados pela `Microsoft.Kubernetes/connectedclusters` Git-Ops `Microsoft.ContainerService/managedClusters` tem aplicado `Microsoft.KubernetesConfiguration/sourceControlConfigurations` específicomente.  Para utilizar a Política Azure, selecione uma definição de política existente e crie uma atribuição de políticas.  Ao criar a atribuição de políticas, definiu o âmbito para a atribuição: este será um grupo de recursos Azure ou subscrição.  Também definiu os parâmetros para o `sourceControlConfiguration` que será criado.  Uma vez criada a atribuição, o motor Policy identificará todos `connectedCluster` ou recursos que estejam `managedCluster` localizados dentro do âmbito e aplicarão o `sourceControlConfiguration` a cada um.

Se estiver a utilizar vários repos Git como fontes de verdade para cada cluster (por exemplo, um repo para operador central de TI/cluster e outros repos para equipas de aplicação), pode permitir isso utilizando múltiplas atribuições políticas, cada tarefa configurada para utilizar um repo Git diferente.

## <a name="create-a-custom-policy-definition"></a>Criar uma definição de política personalizada

1. No portal Azure, navegue para Política, e na secção **de autor** da barra lateral, selecione **Definições**.
2. Selecione **+ Definição de política**.
3. Detete a **localização Definição** para a sua subscrição ou grupo de gestão.  Isto determinará o âmbito mais amplo onde a definição de política pode ser utilizada.
4. Dê à apólice um **nome** e **uma descrição.**
5. Na categoria, escolha **Criar novo**, e escrever *Kubernetes Cluster - Azure Arc*
6. Na caixa de edição da **regra política,** copie/cole o conteúdo desta [definição de política](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json)de exemplo .
7. **Salvar.**

Este passo para a criação de uma definição de política aduaneira não será necessário quando o trabalho estiver concluído para tornar esta política integrada.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

1. No portal Azure, navegue para Política, e na secção **de autor** da barra lateral, selecione **Definições**.
2. Encontre a definição que acabou de criar e selecione-a.
3. Nas ações da página, **selecione Atribuir**.
4. Detete o **Âmbito** ao grupo de gestão, subscrição ou grupo de recursos onde a atribuição de políticas será aplicável.
5. Se quiser excluir quaisquer recursos do âmbito de aplicação da política, então estabeleça **Exclusões**.
6. Dê à atribuição de política um **nome** e **descrição** que possa usar para identificá-lo facilmente.
7. Certifique-se de que a **aplicação da política** está definida para *ativar*.
8. Selecione **Seguinte**.
9. Defina os valores dos parâmetros que serão utilizados durante a criação do `sourceControlConfiguration` .
10. Selecione **Seguinte**.
11. Ativar Criar uma tarefa de **reparação**.
12. Assegure-se de que a **Criação** de uma identidade gerida é verificada e que a identidade terá permissões **do Contribuinte.**  Consulte [este médico](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) e o comentário neste [doc](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) para obter mais informações sobre as permissões de que necessita.
13. Selecione **Rever + criar**.

Após a criação da atribuição de políticas, para qualquer novo `connectedCluster` recurso (ou `managedCluster` recurso com os agentes GitOps instalados) que esteja localizado no âmbito da atribuição, será `sourceControlConfiguration` aplicado.  Para os clusters existentes, terá de executar manualmente uma tarefa de reparação.  Normalmente demora de 10 a 20 minutos para que a atribuição da política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verificar uma atribuição de política

1. No portal Azure, navegue para um dos seus `connectedCluster` recursos e na secção **Definições** da barra lateral, selecione **Políticas**. (O cluster gerido pela AKS ainda não foi implementado, mas está a chegar.)
2. Na lista, deve ver a atribuição de política que criou acima, e o Estado de **Conformidade** deve ser *conforme*.
3. Na secção **Definições** da barra lateral, selecione **Configurações**.
4. Na lista, deve ver o `sourceControlConfiguration` que a atribuição de políticas criou.
5. Use **kubectl** para interrogar o cluster: você deve ver o espaço de nome e artefactos que foram criados pelo `sourceControlConfiguration` .
6. Dentro de 5 minutos, deve ver no aglomerado os artefactos descritos nos manifestos do repo git configurado.

## <a name="next-steps"></a>Passos seguintes

* [Use a Política Azure para governar a configuração do cluster](./use-azure-policy.md)
