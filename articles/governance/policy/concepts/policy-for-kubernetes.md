---
title: Aprenda Azure Policy para Kubernetes
description: Saiba como a Azure Policy usa o Rego e o Open Policy Agent para gerir clusters que executam Kubernetes em Azure ou no local.
ms.date: 09/29/2020
ms.topic: conceptual
ms.openlocfilehash: bd0dc08583b126b6260999ace14d8fc13c52c1f7
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676703"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Compreender o Azure Policy para clusters do Kubernetes

A Azure Policy estende [o Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, um _webhook do controlador de admissão_ para [o Agente de Política Aberta](https://www.openpolicyagent.org/) (OPA), para aplicar aplicações e salvaguardas em escala nos seus clusters de forma centralizada e consistente. A Azure Policy permite gerir e reportar sobre o estado de conformidade dos seus aglomerados Kubernetes a partir de um local. O addon promulga as seguintes funções:

- Verifica com o serviço Azure Policy para atribuições de políticas para o cluster.
- Implementa definições de política no cluster como [modelo de restrição](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e [limita recursos](https://github.com/open-policy-agent/gatekeeper#constraints) personalizados.
- Relatórios de auditoria e conformidade de volta ao serviço de Política Azure.

A Azure Policy for Kubernetes suporta os seguintes ambientes de cluster:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes ativado pelo Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Os addons para AKS Engine e Arc ativados Kubernetes estão em **pré-visualização** . A Azure Policy for Kubernetes suporta apenas piscinas de nól de Linux e definições políticas incorporadas. As definições políticas incorporadas estão na categoria **Kubernetes.** As definições de política de pré-visualização limitadas com o efeito **EnforceOPAConstraint** e **EnforceRegoPolicy** e a categoria **de Serviço Kubernetes conexa** são _depreciadas._ Em vez disso, utilize a _auditoria_ de efeitos e _negue_ com o modo Fornecedor de Recursos `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Descrição geral

Para ativar e utilizar a Política Azure com o seu cluster Kubernetes, tome as seguintes ações:

1. Configure o seu cluster Kubernetes e instale o addon:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes ativado pelo Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Motor AKS](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Para questões comuns com a instalação, consulte [Troubleshoot - Azure Policy Add-on](../troubleshoot/general.md#add-on-installation-errors).

1. [Compreender a linguagem política do Azure para Kubernetes](#policy-language)

1. [Atribua uma definição incorporada ao seu cluster Kubernetes](#assign-a-built-in-policy-definition)

1. [Aguarde a validação](#policy-evaluation)

## <a name="limitations"></a>Limitações

As seguintes limitações gerais aplicam-se ao Add-on de Política Azure para os clusters Kubernetes:

- O Azure Policy Add-on for Kubernetes é suportado na versão **1.14** ou superior de Kubernetes.
- Azure Policy Add-on para Kubernetes só pode ser implantado em piscinas de nól de linux
- Apenas as definições políticas incorporadas são apoiadas
- Número máximo de registos não conformes por política por agrupamento: **500**
- Número máximo de registos não conformes por subscrição: **1 milhão**
- As instalações do Gatekeeper fora do Azure Policy Add-on não são suportadas. Desinstale quaisquer componentes instalados por uma instalação anterior do Gatekeeper antes de ativar o Add-on de Política Azure.
- [As razões para o incumprimento](../how-to/determine-non-compliance.md#compliance-reasons) não estão disponíveis para o `Microsoft.Kubernetes.Data` 
   [modo Fornecedor de Recursos.](./definition-structure.md#resource-provider-modes) Utilizar [detalhes do Componente](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [As isenções](./exemption-structure.md) não são suportadas para [os modos fornecedores de recursos.](./definition-structure.md#resource-provider-modes)

As seguintes limitações aplicam-se apenas ao Add-on da Política Azure para a AKS:

- [A política de segurança AKS Pod](../../../aks/use-pod-security-policies.md) e o Add-on de Política Azure para a AKS não podem ser ambos ativados. Para obter mais informações, consulte [a limitação de segurança da cápsula AKS](../../../aks/use-pod-security-on-azure-policy.md#limitations).
- Espaços de nome automaticamente excluídos pelo Azure Policy Add-on para avaliação: _kube-system,_ _gatekeeper-system_ e _aks-periscope_ .

## <a name="recommendations"></a>Recomendações

Seguem-se recomendações gerais relativas à utilização do Complemento político Azure:

- O Add-on de Política Azure requer 3 componentes gatekeeper para executar: 1 cápsula de auditoria e 2 réplicas de pod webhook. Estes componentes consomem mais recursos à medida que a contagem de recursos da Kubernetes e atribuições de políticas aumenta no cluster, o que requer operações de auditoria e execução.

  - Por menos de 500 cápsulas num único cluster com um máximo de 20 restrições: 2 vCPUs e 350 MB de memória por componente.
  - Para mais de 500 cápsulas num único cluster com um máximo de 40 restrições: 3 vCPUs e 600 MB de memória por componente.

- As cápsulas windows [não suportam contextos de segurança.](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods)
  Assim, algumas das definições da Política Azure, como a desaferição de privilégios de raiz, não podem ser escaladas em pods Windows e apenas se aplicam a cápsulas Linux.

A seguinte recomendação aplica-se apenas à AKS e ao Addon da Política Azure:

- Utilize a piscina de nó do sistema com `CriticalAddonsOnly` mancha para agendar cápsulas gatekeeper. Para obter mais informações, consulte [Usando as piscinas de nó do sistema.](../../../aks/use-system-pools.md#system-and-user-node-pools)
- Proteja o tráfego de saída dos seus clusters AKS. Para obter mais informações, consulte [o controle do tráfego para os nós de cluster](../../../aks/limit-egress-traffic.md).
- Se o cluster `aad-pod-identity` tiver ativado, as cápsulas de identidade gerida do nó (NMI) modificam os iptables dos nós para intercetar chamadas para o ponto final dos metadados de instância Azure. Esta configuração significa que qualquer pedido feito ao ponto final dos metadados é intercetado pelo NMI mesmo que a cápsula não utilize `aad-pod-identity` . AzurePodIdentityException CRD pode ser configurado para informar `aad-pod-identity` que quaisquer pedidos para o ponto final de metadados originários de um pod que corresponda às etiquetas definidas em CRD devem ser proxiited sem qualquer processamento em NMI. As cápsulas do sistema com `kubernetes.azure.com/managedby: aks` etiqueta no espaço de _nomes do sistema kube_ devem ser excluídas `aad-pod-identity` configurando o CRD AzurePodIdentityException. Para obter mais informações, consulte [Desativar a identidade do aad-pod para uma cápsula ou aplicação específica.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Para configurar uma exceção, instale a [yaML de exceção ao microfone.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="install-azure-policy-add-on-for-aks"></a>Instale add-on de política Azure para AKS

Antes de instalar o Add-on de Política Azure ou permitir qualquer uma das funcionalidades de serviço, a sua subscrição deve ativar os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.**

1. Precisa da versão Azure CLI 2.12.0 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Registe os fornecedores de recursos e as funcionalidades de pré-visualização.

   - Portal Azure:

     Registe-se os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.** Para obter etapas, consulte [fornecedores e tipos de recursos.](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Se forem instaladas definições de política de pré-visualização limitadas, remova o add-on com o botão **Desativar** no seu cluster AKS na página **Políticas.**

1. O cluster AKS deve ser a versão _1.14_ ou superior. Utilize o seguinte script para validar a sua versão de cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instale a versão _2.12.0_ ou superior do Azure CLI. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Uma vez concluídos os passos pré-requisitos acima, instale o Add-on de Política Azure no cluster AKS que pretende gerir.

- Portal do Azure

  1. Inicie o serviço AKS no portal Azure selecionando **todos os serviços,** procurando e selecionando **os serviços kubernetes.**

  1. Selecione um dos seus clusters AKS.

  1. Selecione **Políticas** no lado esquerdo da página de serviço de Kubernetes.

  1. Na página principal, selecione o botão **de alimentação ativa.**

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Se o botão **de desativação de desativar** estiver ativado e for visualizada uma mensagem v2 de aviso de migração, o add-on V1 é instalado e deve ser removido antes de atribuir definições de política v2. O addon v1 _prectado_ será automaticamente substituído pelo addon v2 a partir de 24 de agosto,
     > 2020. Devem então ser atribuídas novas versões v2 das definições políticas. Para atualizar agora, siga estes passos:
     >
     > 1. Validar o seu cluster AKS tem o add-on v1 instalado visitando a página **Políticas** no seu cluster AKS e tem o "O cluster atual usa add-on Azure Policy v1..." Mensagem.
     > 1. [Retire o addon](#remove-the-add-on-from-aks).
     > 1. Selecione o botão **de adicionar Ativar** para instalar a versão V2 do add-on.
     > 1. [Atribua versões v2 das suas definições de política incorporadas v1](#assign-a-built-in-policy-definition)

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

Por último, verifique se o último add-on é instalado executando este comando Azure CLI, substituindo `<rg>` pelo nome do seu grupo de recursos e com o nome do seu cluster `<cluster-name>` AKS: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . O resultado deve ser semelhante à seguinte saída e **config.versão** deve `v2` ser:

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

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Instalar Add-on de política Azure para Azure Arc ativado Kubernetes (pré-visualização)

Antes de instalar o Azure Policy Add-on ou permitir qualquer uma das funcionalidades de serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de funções para o principal do serviço de cluster.

1. Precisa da versão Azure CLI 2.12.0 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

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

   |Domínio |Porta |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Atribua a atribuição de funções 'Policy Insights Data Writer (Preview)' ao Azure Arc ativado pelo cluster Kubernetes. `<subscriptionId>`Substitua-se pelo seu ID de subscrição, `<rg>` com o Azure Arc ativado pelo grupo de recursos do cluster Kubernetes, e `<clusterName>` com o nome do Azure Arc ativado o cluster Kubernetes. Acompanhe os valores devolvidos para _appId,_ _senha_ e _inquilino_ para as etapas de instalação.

   - CLI do Azure

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
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

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Instalar Add-on de política Azure para o motor AKS (pré-visualização)

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

Para atribuir uma definição de política ao seu cluster Kubernetes, deve ser-lhe atribuídas as operações de atribuição de dados de acesso baseados em funções Azure (Azure RBAC). As funções incorporadas Azure **Colaborador e** **Proprietário** têm estas operações. Para saber mais, consulte [as permissões do Azure RBAC na Política Azure](../overview.md#azure-rbac-permissions-in-azure-policy).

Encontre as definições de política incorporadas para gerir o seu cluster utilizando o portal Azure com os seguintes passos:

1. Inicie o serviço Azure Policy no portal Azure. Selecione **Todos os serviços** no painel esquerdo e, em seguida, procure e selecione **Política** .

1. No painel esquerdo da página Política Azure, selecione **Definições** .

1. A partir da caixa de lista de categorias, utilize **Selecione tudo** para limpar o filtro e, em seguida, selecione **Kubernetes** .

1. Selecione a definição de política e, em seguida, selecione o botão **Atribuir.**

1. Desaprote o **Âmbito** para o grupo de gestão, subscrição ou grupo de recursos do cluster Kubernetes onde a atribuição de políticas será aplicada.

   > [!NOTE]
   > Ao atribuir a definição Azure Policy for Kubernetes, o **Âmbito** deve incluir o recurso cluster. Para um cluster de motores AKS, o **Scope** deve ser o grupo de recursos do cluster.

1. Dê à atribuição de política um **nome** e **descrição** que pode usar para identificá-lo facilmente.

1. Desa cos assim o coloque na [aplicação da Política](./assignment-structure.md#enforcement-mode) um dos valores abaixo.

   - **Habilitado** - Impor a política no cluster. Os pedidos de admissão de Kubernetes com violações são negados.

   - **Desativado** - Não aplique a política no cluster. Os pedidos de admissão de Kubernetes com violações não são negados. Os resultados da avaliação da conformidade ainda estão disponíveis. Ao lançar novas definições de política para executar clusters, a opção _de desativada_ é útil para testar a definição de política, uma vez que os pedidos de admissão com violações não são negados.

1. Selecione **Seguinte** .

1. Definir **valores de parâmetros**

   - Para excluir os espaços de nomes de Kubernetes da avaliação de políticas, especifique a lista de espaços de nome em exclusões de espaço **de nome de parâmetros** . É recomendado excluir: _sistema kube,_ _gatekeeper-system,_ e _azure-arc_ .

1. Selecione **Rever + criar** .

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

A cada 15 minutos, o complemento pede uma varredura completa do aglomerado. Depois de reunir detalhes da varredura completa e quaisquer avaliações em tempo real por Gatekeeper de tentativas de alterações ao cluster, o add-on relata os resultados de volta à Azure Policy para inclusão em [detalhes de conformidade](../how-to/get-compliance-data.md) como qualquer atribuição da Política Azure. Apenas os resultados para atribuições de políticas ativas são devolvidos durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como [violações listadas](https://github.com/open-policy-agent/gatekeeper#audit) no campo de estado da restrição falhada. Para obter informações sobre recursos _não conformes,_ consulte [os detalhes do Componente para os modos de Fornecedor de Recursos.](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)

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

  1. Inicie o serviço AKS no portal Azure selecionando **todos os serviços,** procurando e selecionando **os serviços kubernetes.**

  1. Selecione o seu cluster AKS onde pretende desativar o Add-on de Política Azure.

  1. Selecione **Políticas** no lado esquerdo da página de serviço de Kubernetes.

  1. Na página principal, selecione o botão **de desativação para desativar.**

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
