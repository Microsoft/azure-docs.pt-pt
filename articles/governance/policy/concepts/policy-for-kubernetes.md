---
title: Pré-visualização - Saiba a política do Azure para Kubernetes
description: Saiba como a Azure Policy usa o Rego e o Open Policy Agent para gerir clusters que executam Kubernetes em Azure ou no local. Esta é uma funcionalidade em pré-visualização.
ms.date: 06/12/2020
ms.topic: conceptual
ms.openlocfilehash: ab18b85fc24deb58a6c65ca038d47120056eaa75
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791712"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>Compreender a política do Azure para os clusters Kubernetes (pré-visualização)

A Azure Policy estende [o Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, um _webhook do controlador de admissão_ para [o Agente de Política Aberta](https://www.openpolicyagent.org/) (OPA), para aplicar aplicações e salvaguardas em escala nos seus clusters de forma centralizada e consistente. A Azure Policy permite gerir e reportar sobre o estado de conformidade dos seus aglomerados Kubernetes a partir de um local. O addon promulga as seguintes funções:

- Verifica com o serviço Azure Policy para atribuições de políticas para o cluster.
- Implementa definições de política no cluster como [modelo de restrição](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e [limita recursos](https://github.com/open-policy-agent/gatekeeper#constraints) personalizados.
- Relatórios de auditoria e conformidade de volta ao serviço de Política Azure.

A Azure Policy for Kubernetes suporta os seguintes ambientes de cluster:

- [Serviço de Kubernetes do Azure (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes ativado pelo Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> A Azure Policy for Kubernetes está em Pré-visualização e apenas suporta piscinas de nól de Linux e definições políticas incorporadas. As definições políticas incorporadas estão na categoria **Kubernetes.** As definições de política de pré-visualização limitadas com o efeito **EnforceOPAConstraint** e **EnforceRegoPolicy** e a categoria **de Serviço Kubernetes conexa** são _depreciadas._ Em vez disso, utilize a _auditoria_ de efeitos e _negue_ com o modo Fornecedor de Recursos `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Descrição geral

Para ativar e utilizar a Política Azure com o seu cluster Kubernetes, tome as seguintes ações:

1. Configure o seu cluster Kubernetes e instale o addon:
   - [Serviço de Kubernetes do Azure (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes ativado pelo Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Motor AKS](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Para questões comuns com a instalação, consulte [troubleshoot - Azure Policy add-on](../troubleshoot/general.md#add-on-installation-errors).

1. [Compreender a linguagem política do Azure para Kubernetes](#policy-language)

1. [Atribua uma definição incorporada ao seu cluster Kubernetes](#assign-a-built-in-policy-definition)

1. [Aguarde a validação](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>Instale add-on de política Azure para AKS

Antes de instalar o Add-on de Política Azure ou permitir qualquer uma das funcionalidades de serviço, a sua subscrição deve ativar os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.**

1. Precisa da versão 2.0.62 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Registe os fornecedores de recursos e as funcionalidades de pré-visualização.

   > [!CAUTION]
   > Quando regista uma funcionalidade numa subscrição, não é possível dess registar essa funcionalidade. Depois de ativar algumas funcionalidades de pré-visualização, podem ser utilizados predefinidos para todos os clusters AKS criados na subscrição. Não ative funcionalidades de pré-visualização em subscrições de produção. Utilize uma subscrição separada para testar funcionalidades de pré-visualização e recolher feedback.

   - Portal Azure:

     1. Registe-se os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.** Para obter etapas, consulte [fornecedores e tipos de recursos.](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

     1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="Pesquisa de Política em Todos os Serviços" border="false":::

     1. Selecione **'Sintolque' pré-visualização** no lado esquerdo da página Política Azure.

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="Junte-se à política de pré-visualização da AKS" border="false":::

     1. Selecione a linha da subscrição que deseja adicionada à pré-visualização.

     1. Selecione o botão **Opt-in** no topo da lista de subscrições.

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
   
     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService
   
     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
   
     # Feature register: enables installing the add-on
     az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
     
     # Use the following to confirm the feature has registered
     az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].   {Name:name,State:properties.state}"
     
     # Once the above shows 'Registered' run the following to propagate the update
     az provider register -n Microsoft.ContainerService
     ```

1. Se forem instaladas definições de política de pré-visualização limitadas, remova o add-on com o botão **Desativar** no seu cluster AKS na página **Políticas (pré-visualização).**

1. O cluster AKS deve ser a versão _1.14_ ou superior. Utilize o seguinte script para validar a sua versão de cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instalar a versão _0.4.0_ da extensão de pré-visualização do Azure CLI para aKS, `aks-preview` :

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se já instalou previamente a extensão _de pré-visualização de aks,_ instale quaisquer atualizações utilizando o `az extension update --name aks-preview` comando.

Uma vez concluídos os passos pré-requisitos acima, instale o Add-on de Política Azure no cluster AKS que pretende gerir.

- Portal do Azure

  1. Lance o serviço AKS no portal Azure clicando em **todos os serviços,** procurando e selecionando **os serviços kubernetes.**

  1. Selecione um dos seus clusters AKS.

  1. Selecione **Políticas (pré-visualização)** no lado esquerdo da página de serviço De Kubernetes.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Definições políticas do cluster AKS" border="false":::

  1. Na página principal, selecione o botão **de alimentação ativa.**

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="Ativar a Política Azure para o add-on AKS" border="false":::

     > [!NOTE]
     > Se o botão **de complemento Ativar** estiver acinzentado, a subscrição ainda não foi adicionada à pré-visualização. Se o botão **de desativação desativar** estiver ativado e for apresentado um aviso de migração para a mensagem V2, o Gatekeepver v2 ainda está instalado e deve ser removido.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Para validar que a instalação de complemento foi bem sucedida e que as cápsulas _de política_ azul e _gatekeeper_ estão em funcionamento, executar o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Por último, verifique se o último add-on é instalado executando este comando Azure CLI, substituindo `<rg>` pelo nome do seu grupo de recursos e com o nome do seu cluster `<cluster-name>` AKS: `az aks show -g <rg> -n <cluster-name>` . O resultado deve ser semelhante à seguinte saída e **config.versão** deve `v2` ser:

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Instale add-on de política Azure para Azure Arc habilitado Kubernetes

Antes de instalar o Azure Policy Add-on ou permitir qualquer uma das funcionalidades de serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de funções para o principal do serviço de cluster.

1. Precisa da versão 2.0.62 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Para ativar o fornecedor de recursos, siga os passos nos [fornecedores e tipos de recursos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou execute o comando Azure CLI ou Azure PowerShell:

   - CLI do Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. O cluster Kubernetes deve ser a versão _1.14_ ou superior.

1. Instalar [o Leme 3](https://v3.helm.sh/docs/intro/install/).

1. O seu cluster Kubernetes habilitado para o Arco Azure. Para mais informações, consulte [a bordo de um cluster Kubernetes para O Arco de Azure.](../../../azure-arc/kubernetes/connect-cluster.md)

1. Ter o ID de Recursos Azure totalmente qualificado do Arco Azure habilitado para o cluster Kubernetes. 

1. Abra as portas para o add-on. O Azure Policy Add-on utiliza estes domínios e portas para obter definições e atribuições políticas e reportar o cumprimento do cluster de volta à Política Azure.

   |Domain |Porta |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Atribua a atribuição de funções 'Policy Insights Data Writer (Preview)' ao Azure Arc ativado pelo cluster Kubernetes. `<subscriptionId>`Substitua-se pelo seu ID de subscrição, `<rg>` com o Azure Arc ativado pelo grupo de recursos do cluster Kubernetes, e `<clusterName>` com o nome do Azure Arc ativado o cluster Kubernetes. Acompanhe os valores devolvidos para _appId,_ _senha_e _inquilino_ para as etapas de instalação.

   - CLI do Azure

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azure powershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Saída de amostra dos comandos acima referidos:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Uma vez concluídos os passos pré-requisitos acima, instale o Add-on de Política Azure no seu agrupamento Azure Arc ativado por Kubernetes:

1. Adicione o repo adicional da política Azure ao Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Instale o Add-on de política Azure utilizando o Gráfico de Timão:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Para obter mais informações sobre o que o Add-on Helm Chart instala, consulte a [definição de Azure Policy Add-on Helm Chart](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) no GitHub.

Para validar que a instalação de complemento foi bem sucedida e que as cápsulas _de política_ azul e _gatekeeper_ estão em funcionamento, executar o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>Instale add-on de política Azure para o motor AKS

Antes de instalar o Azure Policy Add-on ou permitir qualquer uma das funcionalidades de serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de funções para o principal do serviço de cluster.

1. Precisa da versão 2.0.62 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Para ativar o fornecedor de recursos, siga os passos nos [fornecedores e tipos de recursos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou execute o comando Azure CLI ou Azure PowerShell:

   - CLI do Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Crie uma atribuição de papel para o diretor de serviço de cluster.

   - Se não conhece o ID principal do serviço de cluster, procure-o com o seguinte comando.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Atribua a atribuição de funções 'Policy Insights Data Writer (Preview)' à iD principal do serviço de cluster (valor _aadClientID_ from previous step) com a Azure CLI. `<subscriptionId>`Substitua-se pelo seu ID de subscrição e `<aks engine cluster resource group>` pelo grupo de recursos onde se encontra o cluster Kubernetes auto-gerido pelo Motor AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Uma vez concluídos os passos pré-requisitos acima, instale o Add-on de Política Azure. A instalação pode ser durante o ciclo de criação ou atualização de um motor AKS ou como uma ação independente num cluster existente.

- Instalar durante o ciclo de criação ou atualização

  Para permitir o Add-on de Política Azure durante a criação de um novo cluster auto-gerido ou como uma atualização para um cluster existente, inclua a definição de cluster de propriedades [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) para O Motor AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Para obter mais informações sobre, consulte a definição de [cluster do motor AKS do](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)guia externo .

- Instale em cluster existente com Gráficos de Leme

  Utilize os seguintes passos para preparar o cluster e instalar o add-on:

  1. Instalar [o Leme 3](https://v3.helm.sh/docs/intro/install/).

  1. Adicione a repo política de Azure ao Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Para mais informações, consulte [Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Instale o addon com um Gráfico de Leme. `<subscriptionId>`Substitua-se pelo seu ID de subscrição e `<aks engine cluster resource group>` pelo grupo de recursos onde se encontra o cluster Kubernetes auto-gerido pelo Motor AKS.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Para obter mais informações sobre o que o Add-on Helm Chart instala, consulte a [definição de Azure Policy Add-on Helm Chart](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) no GitHub.

     > [!NOTE]
     > Devido à relação entre o Azure Policy Add-on e o id do grupo de recursos, a Azure Policy suporta apenas um cluster de motores AKS para cada grupo de recursos.

Para validar que a instalação de complemento foi bem sucedida e que as cápsulas _de política_ azul e _gatekeeper_ estão em funcionamento, executar o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Linguagem política

A estrutura linguística da Política Azure para a gestão de Kubernetes segue a das definições políticas existentes. Com um [modo de Fornecedor](./definition-structure.md#resource-provider-modes) de `Microsoft.Kubernetes.Data` Recursos, a [auditoria](./effects.md#audit) de efeitos e [a negação](./effects.md#deny) são usadas para gerir os seus clusters Kubernetes. _A auditoria_ e _a negação_ devem fornecer **propriedades específicas** para trabalhar com [o Quadro de Restrição da OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e o Gatekeeper v3.

Como parte dos _detalhes.restriçõesTemplate_ e _details.restrição_ propriedades na definição de política, Azure Policy passa os URIs [destas CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) para o add-on. Rego é a língua que o OPA e o Gatekeeper apoiam para validar um pedido ao cluster Kubernetes. Ao apoiar um padrão existente para a gestão de Kubernetes, a Azure Policy permite reutilizar as regras existentes e emparelhá-las com a Azure Policy para uma experiência unificada de reporte de conformidade em nuvem. Para mais informações, veja [o que é Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="assign-a-built-in-policy-definition"></a>Atribuir uma definição de política incorporada

Para atribuir uma definição de política ao seu cluster Kubernetes, deve ser-lhe atribuídas as operações de atribuição de políticas adequadas de controlo de acesso baseado em funções (RBAC). As funções incorporadas da RBAC **Resource Policy Contributor** e **Owner** têm estas operações. Para saber mais, consulte [as permissões do RBAC na Política Azure.](../overview.md#rbac-permissions-in-azure-policy)

Encontre as definições de política incorporadas para gerir o seu cluster utilizando o portal Azure com os seguintes passos:

1. Inicie o serviço Azure Policy no portal Azure. Selecione **Todos os serviços** no painel esquerdo e, em seguida, procure e selecione **Política**.

1. No painel esquerdo da página Política Azure, selecione **Definições**.

1. A partir da caixa de lista de categorias, utilize **Selecione tudo** para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de política e, em seguida, selecione o botão **Atribuir.**

1. Desaprote o **Âmbito** para o grupo de gestão, subscrição ou grupo de recursos do cluster Kubernetes onde a atribuição de políticas será aplicada.

   > [!NOTE]    
   > Ao atribuir a definição Azure Policy for Kubernetes, o **Âmbito** deve incluir o recurso cluster. Para um cluster de motores AKS, o **Scope** deve ser o grupo de recursos do cluster.

1. Dê à atribuição de política um **nome** e **descrição** que pode usar para identificá-lo facilmente.    

1. Definir a [aplicação da política](./assignment-structure.md#enforcement-mode) para um dos valores    
   abaixo.   

   - **Habilitado** - Impor a política no cluster. Os pedidos de admissão de Kubernetes com violações são negados.    

   - **Desativado** - Não aplique a política no cluster. Os pedidos de admissão de Kubernetes com violações não são negados. Os resultados da avaliação da conformidade ainda estão disponíveis. Ao lançar novas definições de política para executar clusters, a opção _de desativada_ é útil para testar a definição de política, uma vez que os pedidos de admissão com violações não são negados.

1. Selecione **Seguinte**. 

1. Definir **valores de parâmetros** 

   - Para excluir os espaços de nomes de Kubernetes da avaliação de políticas, especifique a lista de espaços de nome em exclusões de espaço **de nome de parâmetros**. É recomendado excluir: _sistema kube,_ _gatekeeper-system,_ e _azure-arc_.

1. Selecione **Rever + criar**.

Alternadamente, utilize a [política De atribuir uma política - Portal](../assign-policy-portal.md) quickstart para encontrar e atribuir uma política de Kubernetes. Procure uma definição de política de Kubernetes em vez da amostra 'audit vms'.

> [!IMPORTANT]
> As definições de política incorporada estão disponíveis para clusters Kubernetes na categoria **Kubernetes.** Para obter uma lista de definições políticas incorporadas, consulte [as amostras de Kubernetes.](../samples/built-in-policies.md#kubernetes)

## <a name="policy-evaluation"></a>Avaliação de políticas

O add-on faz check-in com o serviço Azure Policy para alterações nas atribuições de políticas a cada 15 minutos.
Durante este ciclo de atualização, o add-on verifica as alterações. Estas alterações disparam cria, atualizações ou elimina os modelos e constrangimentos de restrição.

Num cluster kubernetes, se um espaço de nome tiver uma das seguintes etiquetas, os pedidos de admissão com violações não são negados. Os resultados da avaliação da conformidade ainda estão disponíveis.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Embora um administrador de cluster possa ter permissão para criar e atualizar modelos de restrições e restrições de recursos instalados pelo Azure Policy Add-on, estes não são cenários suportados à medida que as atualizações manuais são substituídas. Gatekeeper continua a avaliar políticas que existiam antes de instalar o addon e atribuir definições políticas da Política Azure.

A cada 15 minutos, o complemento pede uma varredura completa do aglomerado. Depois de reunir detalhes da varredura completa e quaisquer avaliações em tempo real por Gatekeeper de tentativas de alterações ao cluster, o add-on relata os resultados de volta à Azure Policy para inclusão em [detalhes de conformidade](../how-to/get-compliance-data.md) como qualquer atribuição da Política Azure. Apenas os resultados para atribuições de políticas ativas são devolvidos durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como [violações listadas](https://github.com/open-policy-agent/gatekeeper#audit) no campo de estado da restrição falhada.

> [!NOTE]
> Cada relatório de conformidade na Azure Policy para os seus clusters Kubernetes inclui todas as violações nos últimos 45 minutos. A hora indica quando ocorreu uma violação.

## <a name="logging"></a>Registo

Como controlador/contentor Kubernetes, tanto as cápsulas _de política_ azul como de _gatekeeper_ mantêm registos no cluster Kubernetes. Os registos podem ser expostos na página **Insights** do cluster Kubernetes.
Para obter mais informações, consulte [o desempenho do seu cluster Kubernetes com o Azure Monitor para obter recipientes](../../../azure-monitor/insights/container-insights-analyze.md).

Para visualizar os registos adicionais, `kubectl` utilize:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Para mais informações, consulte [o Gatekeeper de Debugging](https://github.com/open-policy-agent/gatekeeper#debugging) na documentação gatekeeper.

## <a name="remove-the-add-on"></a>Remova o addon

### <a name="remove-the-add-on-from-aks"></a>Remova o addon da AKS

Para remover o Add-on da Política Azure do seu cluster AKS, utilize o portal Azure ou o Azure CLI:

- Portal do Azure

  1. Lance o serviço AKS no portal Azure clicando em **todos os serviços,** procurando e selecionando **os serviços kubernetes.**

  1. Selecione o seu cluster AKS onde pretende desativar o Add-on de Política Azure.

  1. Selecione **Políticas (pré-visualização)** no lado esquerdo da página de serviço De Kubernetes.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Definições políticas do cluster AKS" border="false":::

  1. Na página principal, selecione o botão **de desativação para desativar.**

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="Desative a Política de Azure para o addon AKS" border="false":::

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Remova o addon do Arco Azure ativado Kubernetes

Para remover o Add-on de Política Azure e gatekeeper do seu Azure Arc ativado pelo cluster Kubernetes, executar o seguinte comando Helm:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Remova o addon do motor AKS

Para remover o Add-on e Gatekeeper da Política Azure do seu cluster de motores AKS, utilize o método que se alinha com a forma como o add-on foi instalado:

- Se instalado definindo a propriedade **addons** na definição de cluster para motor AKS:

  Reimplantar a definição de cluster para AKS Engine depois de alterar a propriedade **addons** para _a política azul para_ falso:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Para obter mais informações, consulte [o motor AKS - Desativar o Add-on de Política Azure](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Se instalado com Helm Charts, executar o seguinte comando Helm:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico recolhidos pela Azure Policy Add-on

O Azure Policy Add-on para Kubernetes recolhe dados limitados de diagnóstico de cluster. Estes dados de diagnóstico são dados técnicos vitais relacionados com software e desempenho. É usado das seguintes formas:

- Mantenha o Add-on da Política Azure atualizado
- Mantenha o Azure Policy Add-on seguro, fiável, performant
- Melhorar o Add-on da Política Azure - através da análise agregada da utilização do add-on

A informação recolhida pelo addon não são dados pessoais. Os seguintes detalhes são atualmente recolhidos:

- Versão do agente add-on da política Azure
- Tipo de cluster
- Região de aglomerados
- Grupo de recursos de cluster
- ID de recursos de cluster
- ID de assinatura de cluster
- Cluster OS (Exemplo: Linux)
- Cidade do Cluster (Exemplo: Seattle)
- Estado ou província do cluster (Exemplo: Washington)
- País ou região de cluster (Exemplo: Estados Unidos)
- Exceções/erros encontrados pelo Azure Policy Add-on durante a instalação do agente na avaliação de políticas
- Número de definições de política gatekeeper não instaladas pelo Add-on da Política Azure

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Veja a [Estrutura de definição do Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)