---
title: Pré-visualização - Aprenda política azure para Kubernetes
description: Saiba como a Política Azure utiliza o Rego e o Open Policy Agent para gerir os clusters que gerem kubernetes em Azure ou no local. Esta é uma funcionalidade em pré-visualização.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 0d663d7bf7ce70c605551422f600258943d1efd7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828632"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>Compreender a Política Azure para os clusters kubernetes (pré-visualização)

A Política Azure alarga o [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, um webhook do controlador de _admissão_ para [o Open Policy Agent](https://www.openpolicyagent.org/) (OPA), para aplicar aplicações e salvaguardas em escala nos seus clusters de forma centralizada e consistente. A Política Azure permite gerir e reportar sobre o estado de conformidade dos seus clusters Kubernetes a partir de um lugar. O complemento decreta as seguintes funções:

- Verifica com o serviço de política azure para atribuições políticas ao cluster.
- Implementa definições de políticas no cluster como modelo de [restrição](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e [restrição](https://github.com/open-policy-agent/gatekeeper#constraints) de recursos personalizados.
- Relatórios de auditoria e conformidade detalham o serviço de Política Azure.

A Política Azure para kubernetes apoia os seguintes ambientes de cluster:

- [Serviço de Kubernetes do Azure (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes ativado pelo Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> A Política Azure para Kubernetes está em Pré-visualização e apenas suporta piscinas de nó Linux e definições políticas incorporadas. As definições políticas incorporadas estão na categoria **Kubernetes.** As definições de política de pré-visualização limitadas com efeito **EnforceRegoPolicy** e a categoria de **Serviço Kubernetes** relacionada são _depreciadas._ Em vez disso, utilize o efeito [EnforceOPAConstraint](./effects.md#enforceopaconstraint) atualizado.

## <a name="overview"></a>Descrição geral

Para ativar e utilizar a Política Azure com o seu cluster Kubernetes, tome as seguintes ações:

1. Configure o seu cluster Kubernetes e instale o complemento:
   - [Serviço de Kubernetes do Azure (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes ativado pelo Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Motor AKS](#install-azure-policy-add-on-for-aks-engine)

1. [Compreender a linguagem política azure para Kubernetes](#policy-language)

1. [Atribuir uma definição incorporada ao seu cluster Kubernetes](#assign-a-built-in-policy-definition)

1. [Aguarde a validação](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>Instale addon de política azure para AKS

Antes de instalar o Add-on de Política Azure ou ativar qualquer uma das funcionalidades do serviço, a sua subscrição deve ativar os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.**

1. Precisa da versão Azure CLI 2.0.62 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Registe os fornecedores de recursos e as funcionalidades de pré-visualização.

   > [!CAUTION]
   > Ao registar uma funcionalidade numa subscrição, não pode desregistar essa funcionalidade. Depois de ativar algumas funcionalidades de pré-visualização, podem ser utilizadas predefinições para todos os clusters AKS e depois criados na subscrição. Não ative funcionalidades de pré-visualização nas subscrições de produção. Utilize uma subscrição separada para testar funcionalidades de pré-visualização e recolher feedback.

   - Portal Azure:

     1. Registe os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.** Para passos, consulte [os fornecedores e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)de recursos.

     1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="Pesquisa de Política em Todos os Serviços" border="false":::

     1. **Selecione Participar** no lado esquerdo da página Política Azure.

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="Junte-se à política de pré-visualização aks" border="false":::

     1. Selecione a linha da subscrição que pretende adicionar à pré-visualização.

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

1. Se foram instaladas definições de política de pré-visualização limitadas, remova o addon com o botão **Desativar** no seu cluster AKS na página **Políticas (pré-visualização).**

1. O cluster AKS deve ser da versão _1.14_ ou superior. Utilize o seguinte script para validar a sua versão de cluster AKS:

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

Uma vez concluídos os passos pré-requisitos acima referidos, instale o Add-on de Política Azure no cluster AKS que pretende gerir.

- Portal do Azure

  1. Lance o serviço AKS no portal Azure clicando em **Todos os serviços,** procurando e selecionando os **serviços kubernetes.**

  1. Selecione um dos seus clusters AKS.

  1. Selecione **Políticas (pré-visualização)** no lado esquerdo da página de serviço kubernetes.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Definições políticas do cluster AKS" border="false":::

  1. Na página principal, selecione o botão **'adicionar'.**

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="Ativar a política azure para add-on AKS" border="false":::

     > [!NOTE]
     > Se o botão de adição Enable estiver acinzentado, a subscrição ainda não foi adicionada à **pré-visualização.** Se o botão **de desativar** estiver ativado e for apresentado um aviso de migração para a mensagem V2, o Gatekeepver v2 ainda está instalado e deve ser removido.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Para validar que a instalação add-on foi bem sucedida e que as cápsulas _de política azul_ e _gatekeeper_ estão em execução, executar o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Por último, verifique se o mais recente addon está instalado executando este comando Azure CLI, substituindo pelo nome do seu grupo de `<rg>` recursos e pelo nome do seu cluster `<cluster-name>` AKS: `az aks show -g <rg> -n <cluster-name>` . O resultado deve ser semelhante à seguinte saída e **a versão config.deve** `v2` ser:

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

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Instalar addon de política azure para Arco Azure ativado Kubernetes

Antes de instalar o Add-on de Política Azure ou ativar qualquer uma das funcionalidades do serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de funções para o principal do serviço de cluster.

1. Precisa da versão Azure CLI 2.0.62 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Para ativar o fornecedor de recursos, siga os passos em [fornecedores e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) de Recursos ou execute o comando Azure CLI ou Azure PowerShell:

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

1. O cluster Kubernetes deve ser da versão _1.14_ ou superior.

1. Instale [o Leme 3](https://v3.helm.sh/docs/intro/install/).

1. O seu cluster Kubernetes ativado para o Arco Azure. Para mais informações, consulte [o embarque de um cluster Kubernetes para O Arco De Azure.](../../../azure-arc/kubernetes/connect-cluster.md)

1. Ter o ID de recurso Azure totalmente qualificado do Azure Arc habilitado o cluster Kubernetes. 

1. Abrir portas para o complemento. O Add-on política azure utiliza estes domínios e portos para obter definições e atribuições políticas e reportar a conformidade do cluster de volta à Política Azure.

   |Domain |Porta |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Atribuir a atribuição de função de "Policy Insights Data Writer (Preview)" ao cluster Azure Arc permitiu o cluster Kubernetes. Substitua-o pelo seu ID de `<subscriptionId>` subscrição, com o Azure Arc ativado o grupo de `<rg>` recursos do cluster Kubernetes, e `<clusterName>` com o nome do cluster Azure Arc ativado por Kubernetes. Acompanhe os valores devolvidos para _appId,_ _password_e _inquilino_ para as etapas de instalação.

   - CLI do Azure

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azure powershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Saída da amostra dos comandos acima:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Uma vez concluídos os passos pré-requisitos acima referidos, instale o Add-on de Política Azure no seu cluster Azure Arc ativado por Kubernetes:

1. Adicione o repo de adição de política azure ao Leme:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Instale o Add-on de Política Azure utilizando o Gráfico de Leme:

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

   Para obter mais informações sobre o que o addon Helm Chart instala, consulte a definição de Helm Chart de Adição de [Política Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) no GitHub.

Para validar que a instalação add-on foi bem sucedida e que as cápsulas _de política azul_ e _gatekeeper_ estão em execução, executar o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>Instale addon de política azure para o motor AKS

Antes de instalar o Add-on de Política Azure ou ativar qualquer uma das funcionalidades do serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de funções para o principal do serviço de cluster.

1. Precisa da versão Azure CLI 2.0.62 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Para ativar o fornecedor de recursos, siga os passos em [fornecedores e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) de Recursos ou execute o comando Azure CLI ou Azure PowerShell:

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

1. Crie uma atribuição de funções para o diretor de serviço de cluster.

   - Se não conhece o ID da aplicação principal do cluster service, procure-o com o seguinte comando.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Atribuir a atribuição de funções de "Policy Insights Data Writer (Preview)" ao ID da app principal do serviço de cluster (valor _aadClientID_ de etapa anterior) com o Azure CLI. Substitua-o pelo seu ID de `<subscriptionId>` subscrição e `<aks engine cluster resource group>` pelo grupo de recursos em que o cluster Kubernetes autogerido do Motor AKS está.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Uma vez concluídos os passos pré-requisitos acima, instale o Add-on da Política Azure. A instalação pode ser durante o ciclo de criação ou atualização de um motor AKS ou como uma ação independente num cluster existente.

- Instalar durante o ciclo de criação ou atualização

  Para permitir o Add-on de Política Azure durante a criação de um novo cluster autogerido ou como uma atualização para um cluster existente, inclua a definição de cluster de propriedade de [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) para o Motor AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Para mais informações, consulte a definição de cluster do [motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)do guia externo .

- Instalar em cluster existente com gráficos de leme

  Utilize os seguintes passos para preparar o cluster e instale o complemento:

  1. Instale [o Leme 3](https://v3.helm.sh/docs/intro/install/).

  1. Adicione o repo da Política Azure ao Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Para mais informações, consulte [o Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Instale o complemento com um Gráfico de Leme. Substitua-o pelo seu ID de `<subscriptionId>` subscrição e `<aks engine cluster resource group>` pelo grupo de recursos em que o cluster Kubernetes autogerido do Motor AKS está.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Para obter mais informações sobre o que o addon Helm Chart instala, consulte a definição de Helm Chart de Adição de [Política Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) no GitHub.

     > [!NOTE]
     > Devido à relação entre o Add-on da Política Azure e o id do grupo de recursos, a Azure Policy suporta apenas um cluster aks engine para cada grupo de recursos.

Para validar que a instalação add-on foi bem sucedida e que as cápsulas _de política azul_ e _gatekeeper_ estão em execução, executar o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Linguagem política

A estrutura linguística da Política Azure para a gestão das Kubernetes segue a das definições políticas existentes. O efeito _EnforceOPAConstraint_ é usado para gerir os seus clusters Kubernetes e leva detalhes propriedades específicas para trabalhar com o [Opa Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e Gatekeeper v3. Para mais detalhes e exemplos, consulte o efeito [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)

Como parte dos _detalhes.constraintTemplate_ e _details.constraint_ properties na definição de política, a Política Azure passa as URIs [destas Definições de Recursos Personalizados](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) para o addon. Rego é a linguagem que opa e Gatekeeper suportam para validar um pedido ao cluster Kubernetes. Ao apoiar uma norma existente para a gestão da Kubernetes, a Política Azure permite reutilizar as regras existentes e emparelhá-las com a Política Azure para uma experiência unificada de reporte de conformidade com a nuvem. Para mais informações, veja [o que é Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="assign-a-built-in-policy-definition"></a>Atribuir uma definição política incorporada

Para atribuir uma definição de política ao seu cluster Kubernetes, deve ser-lhe atribuída a devida definição de controlo de acesso (RBAC) adequada. As funções rbac incorporadas **colaboradora** e **proprietário** têm estas operações. Para saber mais, consulte [as permissões RBAC na Política Azure.](../overview.md#rbac-permissions-in-azure-policy)

Encontre as definições políticas incorporadas para gerir o seu cluster utilizando o portal Azure com os seguintes passos:

1. Inicie o serviço De Política Azure no portal Azure. Selecione **todos os serviços** no painel esquerdo e, em seguida, procure e selecione **Política**.

1. No painel esquerdo da página Política Azure, selecione **Definições**.

1. A partir da caixa de lista seletiva categoria, utilize **Selecione tudo** para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de política e, em seguida, selecione o botão **Atribuir.**

1. Detete o **Âmbito** ao grupo de gestão, subscrição ou grupo de recursos do cluster Kubernetes onde a atribuição de políticas será aplicável.

   > [!NOTE]    
   > Ao atribuir a definição de Política Azure para a definição de Kubernetes, o **Âmbito** deve incluir o recurso de cluster. Para um cluster de motores AKS, o **Scope** deve ser o grupo de recursos do cluster.

1. Dê à atribuição de política um **nome** e **descrição** que possa usar para identificá-lo facilmente.    

1. Definir a [aplicação](./assignment-structure.md#enforcement-mode) da política para um dos valores    
   abaixo.   

   - **Habilitado** - Impor a política no cluster. Os pedidos de admissão de Kubernetes com violações são negados.    

   - **Deficientes** - Não aplique a política do cluster. Os pedidos de admissão de Kubernetes com violações não são negados. Os resultados da avaliação da conformidade ainda estão disponíveis. Ao lançar novas definições políticas para executar clusters, a opção _de deficientes_ é útil para testar a definição de política como pedidos de admissão com violações não são negados.

1. Selecione **Seguinte**. 

1. Definir **valores de parâmetros** 

   - Para excluir os espaços de nome kubernetes da avaliação de políticas, especifique a lista de espaços de nome em **exclusões**de espaço de nome paraparâmetros . Recomenda-se excluir: _sistema kube,_ _sistema gatekeeper_e _arco azul._

1. Selecione **Rever + criar**.

Alternadamente, utilize a [alferes - Portal](../assign-policy-portal.md) quickstart para encontrar e atribuir uma política kubernetes. Procurar uma definição de política kubernetes em vez da amostra "vms de auditoria".

> [!IMPORTANT]
> As definições políticas incorporadas estão disponíveis para os clusters Kubernetes na categoria **Kubernetes.** Para obter uma lista de definições políticas incorporadas, consulte [amostras kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Avaliação política

O complemento faz check-in com o serviço de Política Azure para alterações nas atribuições políticas a cada 15 minutos.
Durante este ciclo de atualização, o complemento verifica as alterações. Estas alterações criam, atualizam ou eliminam os modelos e constrangimentos de restrição.

Num aglomerado de Kubernetes, se um espaço de nome tem uma das seguintes etiquetas, os pedidos de admissão com violações não são negados. Os resultados da avaliação da conformidade ainda estão disponíveis.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Embora um administrador de cluster possa ter permissão para criar e atualizar modelos de restrição e recursos de restrições instalados pelo Add-on política azure, estes não são cenários suportados à medida que as atualizações manuais são substituídas. Gatekeeper continua a avaliar as políticas que existiam antes de instalar as definições de política de adição e atribuição de políticas de política azure.

A cada 15 minutos, o complemento pede uma varredura completa do cluster. Depois de reunir detalhes da varredura completa e de quaisquer avaliações em tempo real por parte da Gatekeeper de tentativas de alterações ao cluster, o addon reporta os resultados de volta à Política Azure para inclusão em detalhes de [conformidade](../how-to/get-compliance-data.md) como qualquer atribuição de Política Azure. Apenas os resultados para as atribuições de políticas ativas são devolvidos durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como [violações](https://github.com/open-policy-agent/gatekeeper#audit) enumeradas no campo de status da restrição falhada.

> [!NOTE]
> Cada relatório de conformidade na Política Azure para os seus clusters Kubernetes inclui todas as violações nos últimos 45 minutos. A marca indica quando ocorreu uma violação.

## <a name="logging"></a>Registo

Como controlador/contentor Kubernetes, tanto as cápsulas _de política azul_ como de _gatekeeper_ mantêm registos no aglomerado kubernetes. Os registos podem ser expostos na página **Insights** do cluster Kubernetes.
Para mais informações, consulte monitorize o desempenho do [cluster Kubernetes com o Monitor Azure para obter recipientes](../../../azure-monitor/insights/container-insights-analyze.md).

Para visualizar os registos adicionais, `kubectl` utilize:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Para mais informações, consulte [depurando gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) na documentação do Gatekeeper.

## <a name="remove-the-add-on"></a>Remova o addon

### <a name="remove-the-add-on-from-aks"></a>Remova o complemento do AKS

Para remover o Add-on da política Azure do seu cluster AKS, utilize o portal Azure ou o Azure CLI:

- Portal do Azure

  1. Lance o serviço AKS no portal Azure clicando em **Todos os serviços,** procurando e selecionando os **serviços kubernetes.**

  1. Selecione o seu cluster AKS onde pretende desativar o Add-on da Política Azure.

  1. Selecione **Políticas (pré-visualização)** no lado esquerdo da página de serviço kubernetes.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Definições políticas do cluster AKS" border="false":::

  1. Na página principal, selecione o botão **de adição desativar.**

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="Desativar a política azure para add-on AKS" border="false":::

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Remova o complemento do Azure Arc habilitado para kubernetes

Para remover o Add-on da Política Azure e gatekeeper do seu cluster Azure Arc habilitado kubernetes, executar o seguinte comando Helm:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Remova o complemento do motor AKS

Para remover o Add-on e gatekeeper da política Azure do seu cluster de motor AKS, utilize o método que se alinha com a forma como o addon foi instalado:

- Se instalado definindo a propriedade **addons** na definição de cluster para motor AKS:

  Reutilizar a definição de cluster para o Motor AKS depois de mudar a propriedade de **addons** para _a política azul_ para falsa:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Para mais informações, consulte [aks engine - Disable Azure Policy Add-on](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Se estiver instalado com gráficos de leme, execute o seguinte comando Helm:

  ```bash
  helm uninstall azure-policy-addon
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
- Número de definições de política gatekeeper não instaladas pelo Add-on política do Azure

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Veja a [Estrutura de definição do Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)