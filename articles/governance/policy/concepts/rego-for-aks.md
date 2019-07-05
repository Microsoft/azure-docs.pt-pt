---
title: Compreender como o conteúdo de uma máquina virtual de auditoria
description: Saiba como o Azure Policy utiliza Rego e abra o agente de política para gerir clusters no Azure Kubernetes Service.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453908"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Compreender a política do Azure para o serviço Kubernetes do Azure

Política do Azure integra-se com o [do Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) para aplicar enforcements à escala e salvaguarda nos seus clusters de maneira centralizada e consistente.
Ao expandir a utilização de [controlador de chamadas](https://github.com/open-policy-agent/gatekeeper), uma _admissão controlador webhook_ para [aberto de política de agente](https://www.openpolicyagent.org/) (OPA), o Azure Policy permite gerir e gerar relatórios sobre a compatibilidade Estado dos seus recursos do Azure e os clusters do AKS num único local.

> [!NOTE]
> Política do Azure para o AKS está em pré-visualização limitada e só suporta definições de política incorporada.

## <a name="overview"></a>Descrição geral

Para ativar e utilizar o Azure Policy para o AKS com o seu cluster do AKS, siga as seguintes ações:

- [Participar de funcionalidades de pré-visualização](#opt-in-for-preview)
- [Instalar o suplemento de política do Azure](#installation-steps)
- [Atribuir uma definição de política para o AKS](#built-in-policies)
- [Aguardar validação](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Optar Ativamente pela pré-visualização

Antes de instalar o suplemento de política do Azure ou ativar qualquer uma das funcionalidades de serviço, tem de ativar a sua subscrição do **containerservice** fornecedor de recursos e o **policyinsights**fornecedor de recursos, em seguida, ser aprovado para aderir à pré-visualização. Para aderir à pré-visualização, siga estes passos no portal do Azure ou com a CLI do Azure:

- Portal do Azure:

  1. Registe-se a **containerservice** e **policyinsights** fornecedores de recursos. Para obter os passos, consulte [fornecedores de recursos e os tipos de](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

     ![Pesquisar política em todos os serviços](../media/rego-for-aks/search-policy.png)

  1. Selecione **Junte-se a pré-visualização** no lado esquerdo da página política do Azure.

     ![Junte-se a política para a pré-visualização do AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Selecione a linha da subscrição que pretende adicionado à pré-visualização.

  1. Selecione o **participação ativa** botão na parte superior da lista de subscrições.

- CLI do Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Suplemento de política do Azure

O _suplemento de política do Azure_ para Kubernetes liga-se o serviço de política do Azure para o controlador de admissão de controlador de chamadas. O suplemento, o que é instalado para o _do azure policy_ enacts de espaço de nomes, as seguintes funções:

- Verificações com o Azure Policy para atribuições para o cluster do AKS
- Transfere e coloca em cache os detalhes da política, incluindo o _rego_ definição de política, como **configmaps**
- Executa uma verificação de conformidade de análise completa no cluster do AKS
- Relatórios de auditoria e detalhes de conformidade de volta para o Azure Policy

### <a name="installing-the-add-on"></a>Instalar o suplemento

#### <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o suplemento no cluster do AKS, tem de ser instalada a extensão de pré-visualização. Este passo é realizado com a CLI do Azure:

1. Precisa da versão 2.0.62 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. O cluster do AKS tem de ser a versão _1.10_ ou superior. Utilize o seguinte script para validar a sua versão de cluster do AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instalar a versão _0.4.0_ da CLI do Azure de pré-visualização extensão para o AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se instalou anteriormente a _pré-visualização do aks_ extensão, instale quaisquer atualizações utilizando o `az extension update --name aks-preview` comando.

#### <a name="installation-steps"></a>Passos de instalação

Depois de concluir os pré-requisitos, instale o suplemento do Azure Policy no cluster do AKS que pretende gerir.

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure clicando **todos os serviços**, em seguida, procurar e selecionar **Kubernetes serviços**.

  1. Selecione um dos seus clusters do AKS.

  1. Selecione **políticas (pré-visualização)** no lado esquerdo da página de serviço do Kubernetes.

     ![Políticas do AKS cluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o **ativar o suplemento** botão.

     ![Ativar a política do Azure para o suplemento do AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Se o **ativar o suplemento** botão está a cinzento, a subscrição ainda não foram adicionada à pré-visualização. Ver [participação ativa-in para pré-visualização](#opt-in-for-preview) para os passos necessários.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validação e a frequência do relatório

O suplemento verifica com a política do Azure para que as alterações em atribuições de políticas a cada 5 minutos. Durante este ciclo de atualização, o suplemento Remove todos _configmaps_ no _política do azure_ espaço de nomes, em seguida, recria a _configmaps_ para utilização do controlador de chamadas.

> [!NOTE]
> Embora uma _administrador do cluster_ pode ter permissão para o _política do azure_ espaço de nomes, ele não recomendada ou suportada para efetuar alterações ao espaço de nomes. Quaisquer alterações manuais efetuadas serão perdidas durante o ciclo de atualização.

A cada 5 minutos, o suplemento chama-se para uma análise completa do cluster. Após coletar os detalhes sobre a verificação completa e qualquer avaliações em tempo real pelo controlador de chamadas de alteração para o cluster, o suplemento relata os resultados ao Azure Policy para inclusão no [detalhes de conformidade](../how-to/get-compliance-data.md) como qualquer política do Azure atribuição. Apenas os resultados para atribuições de política de Active Directory são devolvidos durante o ciclo de auditoria.

## <a name="policy-language"></a>Linguagem de política

A estrutura de linguagem de política do Azure para gerir o AKS segue-se que as políticas existentes. O efeito _EnforceRegoPolicy_ é utilizado para gerir os seus clusters do AKS e demora _detalhes_ propriedades específicas para trabalhar com OPA e controlador de chamadas. Para obter detalhes e exemplos, consulte a [EnforceRegoPolicy](effects.md#enforceregopolicy) efeito.

Como parte da _details.policy_ propriedade na definição de política, o Azure Policy passa o URI de uma política de rego para o suplemento. Rego é o idioma que OPA e controlador de chamadas de suporte para validar ou modifica um pedido para o cluster de Kubernetes. Ao suportar uma norma existente para a gestão de Kubernetes, o Azure Policy possibilita a reutilizar as regras existentes e pareá-las com o Azure Policy para a conformidade de cloud unificada experiência de geração de relatórios. Para obter mais informações, consulte [o que é Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Políticas incorporadas

Para encontrar as políticas incorporadas para gerir o AKS no portal do Azure, siga estes passos:

1. Inicie o serviço de política do Azure no portal do Azure. Selecione **todos os serviços** no painel esquerdo e, em seguida, procure e selecione **política**.

1. No painel esquerdo da página política do Azure, selecione **definições**.

1. A partir da caixa de lista pendente de categoria, utilize **Selecionar tudo** para limpar o filtro e, em seguida, selecione **serviço Kubernetes**.

1. Selecione a definição de política, em seguida, selecione o **atribuir** botão.

> [!NOTE]
> Ao atribuir a política do Azure para a definição do AKS, o **âmbito** tem de incluir o recurso de cluster do AKS.

Em alternativa, utilizar o [atribuir uma política - Portal](../assign-policy-portal.md) início rápido para localizar e atribuir uma política AKS. Procurar por uma definição de política do Kubernetes em vez das exemplo "vms de auditoria".

## <a name="logging"></a>Registo

### <a name="azure-policy-add-on-logs"></a>Registos de complemento de política do Azure

Como um controlador/contentor do Kubernetes, o suplemento de política do Azure mantém registos do cluster do AKS. Os registos são expostos na **Insights** página do AKS cluster. Para obter mais informações, consulte [desempenho com o Azure Monitor para contentores de cluster do AKS compreender](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Registos de controlador de chamadas

Para ativar registos de controlador de chamadas para novos pedidos de recursos, siga os passos em [ativar e rever o Kubernetes no AKS registos do nó principal](../../../aks/view-master-logs.md).
Eis um exemplo de consulta para ver eventos negados em novos pedidos de recursos:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Para ver os registos de contentores do controlador de chamadas, siga os passos em [ativar e rever os registos de nó principal no AKS do Kubernetes](../../../aks/view-master-logs.md) e verifique o _kube apiserver_ opção o **definiçõesdediagnóstico** painel.

## <a name="remove-the-add-on"></a>Remover o suplemento

Para remover o suplemento de política do Azure do seu cluster do AKS, utilize o portal do Azure ou a CLI do Azure:

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure clicando **todos os serviços**, em seguida, procurar e selecionar **Kubernetes serviços**.

  1. Selecione o seu cluster do AKS em que pretende desativar o suplemento do Azure Policy.

  1. Selecione **políticas (pré-visualização)** no lado esquerdo da página de serviço do Kubernetes.

     ![Políticas do AKS cluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o **desativar suplemento** botão.

     ![Desativar a política do Azure para o suplemento do AKS](../media/rego-for-aks/disable-policy-add-on.png)

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md).
- Veja a [Estrutura de definição do Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Compreender como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [remediar recursos incompatíveis](../how-to/remediate-resources.md).
- Revisão que um grupo de gestão é com [organizar os recursos com grupos de gestão do Azure](../../management-groups/index.md).