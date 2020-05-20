---
title: Aprenda a Política Azure para o Serviço Azure Kubernetes
description: Saiba como a Política Azure utiliza o Rego e o Open Policy Agent para gerir clusters no Serviço Azure Kubernetes.
ms.date: 03/18/2020
ms.topic: conceptual
ms.openlocfilehash: 7039220d5952dd6d54a9b109c865362e0d9c7163
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654878"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Compreender o Azure Policy para o Azure Kubernetes Service

A Azure Policy integra-se com o [Serviço Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS) para aplicar aplicações e salvaguardas em escala nos seus clusters de forma centralizada e consistente.
Ao alargar o uso do [Gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2, um webhook do controlador de _admissão_ para [o Open Policy Agent](https://www.openpolicyagent.org/) (OPA), a Política Azure permite gerir e reportar sobre o estado de conformidade dos seus recursos Azure e clusters AKS de um só local.

> [!NOTE]
> A Política Azure para AKS está em Pré-visualização Limitada e apenas suporta piscinas de nó Linux e definições políticas incorporadas.

## <a name="overview"></a>Descrição geral

Para ativar e utilizar a Política Azure para AKS com o seu cluster AKS, tome as seguintes ações:

- [Optar ativamente por participar na pré-visualização de funcionalidades](#opt-in-for-preview)
- [Instale o Add-on política azure](#installation-steps)
- [Atribuir uma definição de política para AKS](#built-in-policies)
- [Aguarde a validação](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Opt-in para pré-visualização

Antes de instalar o Add-on de Política Azure ou ativar qualquer uma das funcionalidades do serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.ContainerService** e o fornecedor de recursos **Microsoft.PolicyInsights,** para depois ser aprovado para aderir à pré-visualização. Para se juntar à pré-visualização, siga estes passos no portal Azure ou com o Azure CLI:

- Portal Azure:

  1. Registe os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.** Para passos, consulte [os fornecedores e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)de recursos.

  1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

     :::image type="content" source="../media/rego-for-aks/search-policy.png" alt-text="Pesquisa de Política em Todos os Serviços" border="false":::

  1. **Selecione Participar** no lado esquerdo da página Política Azure.

     :::image type="content" source="../media/rego-for-aks/join-aks-preview.png" alt-text="Junte-se à política de pré-visualização aks" border="false":::

  1. Selecione a linha da subscrição que pretende adicionar à pré-visualização.

  1. Selecione o botão **Opt-in** no topo da lista de subscrições.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataPlaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Add-on política azure

O _Add-on de política Azure_ para Kubernetes liga o serviço de Política Azure ao controlador de admissão gatekeeper. O complemento, que é instalado no espaço de nomes de _política azul,_ determina as seguintes funções:

- Verificações com política azure para atribuições ao cluster AKS
- Detalhes da política de downloads e caches, incluindo a definição de política de _rego,_ como **configmaps**
- Executa uma verificação completa da conformidade com o cluster AKS
- Relatórios de auditoria e conformidade detalham a Política Azure

### <a name="installing-the-add-on"></a>Instalação do addon

#### <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o complemento no seu cluster AKS, a extensão de pré-visualização deve ser instalada. Este passo é feito com o Azure CLI:

1. Precisa da versão Azure CLI 2.0.62 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. O cluster AKS deve ser da versão _1.10_ ou superior. Utilize o seguinte script para validar a sua versão de cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instale a versão _0.4.0_ da extensão de pré-visualização Azure CLI para `aks-preview` AKS,

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se tiver instalado previamente a extensão _de pré-visualização de aks,_ instale quaisquer atualizações utilizando o `az extension update --name aks-preview` comando.

#### <a name="installation-steps"></a>Passos de instalação

Uma vez preenchidos os pré-requisitos, instale o Add-on de Política Azure no cluster AKS que pretende gerir.

- Portal do Azure

  1. Lance o serviço AKS no portal Azure clicando em **Todos os serviços,** procurando e selecionando os **serviços kubernetes.**

  1. Selecione um dos seus clusters AKS.

  1. Selecione **Políticas (pré-visualização)** no lado esquerdo da página de serviço kubernetes.

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Políticas do cluster AKS" border="false":::

  1. Na página principal, selecione o botão **'adicionar'.**

     :::image type="content" source="../media/rego-for-aks/enable-policy-add-on.png" alt-text="Ativar a política azure para add-on AKS" border="false":::

     > [!NOTE]
     > Se o botão de adição Enable estiver acinzentado, a subscrição ainda não foi adicionada à **pré-visualização.** Consulte [o Opt-in para visualização](#opt-in-for-preview) dos passos necessários.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e reporte

O complemento faz check-in com a Política Azure para alterações nas atribuições políticas a cada 5 minutos. Durante este ciclo de atualização, o addon remove todos os _configmaps_ no espaço de nome da _política azul_ e recria os _mapas configmaps_ para a utilização do Gatekeeper.

> [!NOTE]
> Embora um administrador de _cluster_ possa ter permissão para o espaço de nome da _política azul,_ não é recomendado ou suportado para fazer alterações no espaço de nome. Durante o ciclo de atualização, perdem-se quaisquer alterações manuais efetuadas.

A cada 5 minutos, o complemento pede uma varredura completa do cluster. Depois de reunir detalhes da varredura completa e de quaisquer avaliações em tempo real por parte da Gatekeeper de tentativas de alterações ao cluster, o addon reporta os resultados de volta à Política Azure para inclusão em detalhes de [conformidade](../how-to/get-compliance-data.md) como qualquer atribuição de Política Azure. Apenas os resultados para as atribuições de políticas ativas são devolvidos durante o ciclo de auditoria.

## <a name="policy-language"></a>Linguagem política

A estrutura linguística da Política Azure para a gestão do AKS segue a das políticas existentes. O efeito _EnforceRegoPolicy_ é usado para gerir os seus clusters AKS e leva _detalhes_ propriedades específicas para trabalhar com OPA e Gatekeeper v2. Para mais detalhes e exemplos, consulte o efeito [EnforceRegoPolicy.](effects.md#enforceregopolicy)

Como parte dos _detalhes.propriedade política_ na definição de política, a Política Azure passa o URI de uma política de rego para o addon. Rego é a linguagem que opa e Gatekeeper suportam para validar ou mutar um pedido para o cluster Kubernetes. Ao apoiar uma norma existente para a gestão da Kubernetes, a Política Azure permite reutilizar as regras existentes e emparelhá-las com a Política Azure para uma experiência unificada de reporte de conformidade com a nuvem. Para mais informações, veja [o que é Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="built-in-policies"></a>Políticas incorporadas

Para encontrar as políticas incorporadas para gerir o AKS utilizando o portal Azure, siga estes passos:

1. Inicie o serviço De Política Azure no portal Azure. Selecione **todos os serviços** no painel esquerdo e, em seguida, procure e selecione **Política**.

1. No painel esquerdo da página Política Azure, selecione **Definições**.

1. A partir da caixa de lista seletiva categoria, utilize **Selecione tudo** para limpar o filtro e, em seguida, selecione o **serviço Kubernetes**.

1. Selecione a definição de política e, em seguida, selecione o botão **Atribuir.**

> [!NOTE]
> Ao atribuir a política azure para a definição de AKS, o **Âmbito** deve incluir o recurso de cluster AKS.

Alternadamente, utilize a [alferes - Portal](../assign-policy-portal.md) quickstart para encontrar e atribuir uma política AKS. Procurar uma definição de política kubernetes em vez da amostra "vms de auditoria".

> [!IMPORTANT]
> As políticas incorporadas no **serviço Kubernetes** de categoria são apenas para uso com AKS.

## <a name="logging"></a>Registo

### <a name="azure-policy-add-on-logs"></a>Registos adicionais da política azure

Como controlador/contentor Kubernetes, o Add-on de Política Azure mantém os registos no cluster AKS. Os registos estão expostos na página **Insights** do cluster AKS. Para mais informações, consulte [o desempenho do cluster DA Coma AKS com o Monitor Azure para obter recipientes.](../../../azure-monitor/insights/container-insights-analyze.md)

### <a name="gatekeeper-logs"></a>Registos do gatekeeper

Para ativar os registos gatekeeper para novos pedidos de recursos, siga os passos em Enable e reveja os [registos de nó mestre kubernetes em AKS](../../../aks/view-master-logs.md).
Aqui está um exemplo de consulta para ver eventos negados em novos pedidos de recursos:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Para ver os registos dos recipientes gatekeeper, siga os passos em Enable e reveja os [registos de nó mestre kubernetes no AKS](../../../aks/view-master-logs.md) e verifique a opção _kube-apiserver_ no painel de **definições** de diagnóstico.

## <a name="remove-the-add-on"></a>Remova o addon

Para remover o Add-on da política Azure do seu cluster AKS, utilize o portal Azure ou o Azure CLI:

- Portal do Azure

  1. Lance o serviço AKS no portal Azure clicando em **Todos os serviços,** procurando e selecionando os **serviços kubernetes.**

  1. Selecione o seu cluster AKS onde pretende desativar o Add-on da Política Azure.

  1. Selecione **Políticas (pré-visualização)** no lado esquerdo da página de serviço kubernetes.

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Políticas do cluster AKS" border="false":::

  1. Na página principal, selecione o botão **de adição desativar.**

     :::image type="content" source="../media/rego-for-aks/disable-policy-add-on.png" alt-text="Desativar a política azure para add-on AKS" border="false":::

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico recolhidos pelo Add-on política do Azure

O Add-on de Política Azure para Kubernetes recolhe dados de diagnóstico de cluster limitados. Estes dados de diagnóstico são dados técnicos vitais relacionados com software e desempenho. É usado das seguintes formas:

- Mantenha a política azure Add-on atualizada
- Mantenha o Add-on de Política Azure seguro, fiável, executante
- Melhorar o Add-on da Política Azure - através da análise agregada da utilização do addon

A informação recolhida pelo addon não é de dados pessoais. Os seguintes detalhes são atualmente recolhidos:

- Versão de agente add-on de política azure
- Tipo de cluster
- Região de aglomerados
- Grupo de recursos de cluster
- ID de recurso cluster
- ID de subscrição de cluster
- Cluster OS (Exemplo: Linux)
- Cidade do aglomerado (Exemplo: Seattle)
- Estado de aglomerado ou província (Exemplo: Washington)
- País ou região de aglomerado (exemplo: Estados Unidos)
- Exceções/erros encontrados pelo Add-on da Política Azure durante a instalação do agente na avaliação de políticas
- Número de políticas gatekeeper não instaladas pelo Azure Policy Add-on

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Veja a [Estrutura de definição do Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)