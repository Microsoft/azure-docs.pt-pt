---
title: Aprenda a política azure para o motor AKS
description: Saiba como a Política Azure utiliza Definições Personalizadas de Recursos e Agente de Política Aberta do Gatekeeper v3 para gerir clusters com motor AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f1d6f08f07e015b1e7fe2886746bf9e8b43fdce0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654961"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Compreender a política azure para o motor AKS

A Azure Policy integra-se com o [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), um sistema que fornece ferramentas convenientes para rapidamente arrancar um cluster Kubernetes autogerido em Azure. Esta integração permite aplicações e salvaguardas em escala nos seus clusters autogeridos do motor AKS de forma centralizada e consistente. Ao alargar o uso do Agente de [Política Aberta](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta), um webhook do controlador de _admissão_ para kubernetes, a Azure Policy permite gerir e reportar sobre o estado de conformidade dos seus recursos Azure e clusters autogeridos AKS Engine de um só local.

> [!NOTE]
> A Política Azure para o Motor AKS está em Pré-visualização Pública e não tem SLA. Gatekeeper v3 está em Beta e é apoiado pela comunidade de código aberto. O serviço apenas suporta definições políticas incorporadas, piscinas de nó Linux e um único cluster aks engine para cada grupo de recursos configurado com um Diretor de Serviço.

> [!IMPORTANT]
> Para obter suporte para a Política Azure para o Motor AKS, motor AKS ou Gatekeeper v3, criar um [novo problema](https://github.com/Azure/aks-engine/issues/new/choose) no repositório AKS Engine GitHub.

## <a name="overview"></a>Descrição geral

Para ativar e utilizar a Política Azure para o Motor AKS com o seu cluster Kubernetes autogerido em Azure, tome as seguintes ações:

- [Pré-requisitos](#prerequisites)
- [Instale o Add-on política azure](#installing-the-add-on)
- [Atribuir uma definição de política para o motor AKS](#built-in-policies)
- [Aguarde a validação](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o Add-on de Política Azure ou ativar qualquer uma das funcionalidades do serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de funções para o principal do serviço de cluster. 

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

1. Criar uma atribuição de funções para o diretor de serviço de cluster

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

## <a name="azure-policy-add-on"></a>Add-on política azure

O _Add-on de política Azure_ para Kubernetes liga o serviço de Política Azure ao controlador de admissão gatekeeper. O complemento, que é instalado no espaço de nome do _sistema kube,_ decreta as seguintes funções:

- Verificações com política azure para atribuições ao cluster aks engine
- Descarregamentos e instala detalhes de políticas, modelos de restrição e constrangimentos
- Executa uma verificação completa de conformidade com o cluster do motor AKS
- Relatórios de auditoria e conformidade detalham a Política Azure

### <a name="installing-the-add-on"></a>Instalação do addon

Uma vez preenchidos os pré-requisitos, pode ser instalado o Add-on da Política Azure. A instalação pode ser durante o ciclo de criação ou atualização de um motor AKS ou como uma ação independente num cluster existente.

- Instalar durante o ciclo de criação ou atualização

  Para permitir o Add-on de Política Azure durante a criação de um novo cluster autogerido ou como uma atualização para um cluster existente, inclua a definição de cluster de propriedade de **addons** para o Motor AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Para mais informações, consulte a definição de cluster do [motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)do guia externo .

- Instalar em cluster existente com gráficos de leme

  Utilize os seguintes passos para preparar o cluster e instale o complemento:

  1. Instale gatekeeper no espaço de nome do _sistema gatekeeper._

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Adicione a etiqueta _de plano de controlo_ ao sistema _kube_. Este rótulo exclui a auditoria de cápsulas e serviços do _sistema kube_ pela Gatekeeper e pelo Add-on da Política Azure.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Sync Kubernetes dados (Namespace, Pod, Ingress, Serviço) com OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Para mais informações, consulte [OPA - Replicação de dados](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Adicione o repo da Política Azure ao Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Para mais informações, consulte [o Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Instale o complemento com um Gráfico de Leme. Substitua-o pelo seu ID de `<subscriptionId>` subscrição e `<aks engine cluster resource group>` pelo grupo de recursos em que o cluster Kubernetes autogerido do Motor AKS está.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Para obter mais informações sobre o que o addon Helm Chart instala, consulte a definição de Helm Chart de Adição de [Política Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) no GitHub.

     > [!NOTE]
     > Devido à relação entre o Add-on da Política Azure e o id do grupo de recursos, a Azure Policy suporta apenas um cluster aks engine para cada grupo de recursos.

Para validar que a instalação add-on foi bem sucedida e que a cápsula _de política azul_ está em execução, executar o seguinte comando:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e reporte

O complemento faz check-in com a Política Azure para alterações nas atribuições políticas a cada 5 minutos. Durante este ciclo de atualização, o complemento verifica as alterações. Estas alterações criam, atualizam ou eliminam os modelos e constrangimentos de restrição.

> [!NOTE]
> Embora um administrador de _cluster_ possa ter permissão para fazer alterações aos modelos e restrições de restrição, não é recomendado ou suportado para fazer alterações aos modelos de restrição ou restrições criadas pela Política Azure. Durante o ciclo de atualização, perdem-se quaisquer alterações manuais efetuadas.

A cada 5 minutos, o complemento pede uma varredura completa do cluster. Depois de reunir detalhes da varredura completa e de quaisquer avaliações em tempo real por parte da Gatekeeper de tentativas de alterações ao cluster, o addon reporta os resultados de volta à Política Azure para inclusão em detalhes de [conformidade](../how-to/get-compliance-data.md) como qualquer atribuição de Política Azure. Apenas os resultados para as atribuições de políticas ativas são devolvidos durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como violações enumeradas no campo de status da restrição falhada.

## <a name="policy-language"></a>Linguagem política

A estrutura linguística da Política Azure para a gestão do Motor AKS segue a das políticas existentes. O efeito _EnforceOPAConstraint_ é usado para gerir os seus clusters de motores AKS e leva _detalhes_ propriedades específicas para trabalhar com o [Opa Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e Gatekeeper v3. Para mais detalhes e exemplos, consulte o efeito [EnforceOPAConstraint.](effects.md#enforceopaconstraint)

Como parte dos _detalhes.constraintTemplate_ e _details.constraint_ properties na definição de política, a Política Azure passa as URIs [destas Definições de Recursos Personalizados](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) para o addon. Rego é a linguagem que opa e Gatekeeper suportam para validar um pedido ao cluster Kubernetes. Ao apoiar uma norma existente para a gestão da Kubernetes, a Política Azure permite reutilizar as regras existentes e emparelhá-las com a Política Azure para uma experiência unificada de reporte de conformidade com a nuvem. Para mais informações, veja [o que é Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)

## <a name="built-in-policies"></a>Políticas incorporadas

Para encontrar as políticas incorporadas para gerir o seu cluster AKS Engine utilizando o portal Azure, siga estes passos:

1. Inicie o serviço De Política Azure no portal Azure. Selecione **todos os serviços** no painel esquerdo e, em seguida, procure e selecione **Política**.

1. No painel esquerdo da página Política Azure, selecione **Definições**.

1. A partir da caixa de lista seletiva categoria, utilize **Selecione tudo** para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de política e, em seguida, selecione o botão **Atribuir.**

> [!NOTE]
> Ao atribuir a política Azure para a definição do motor AKS, o **Âmbito** deve ser o grupo de recursos do cluster aks engine.

Alternadamente, utilize a [alferes - Portal](../assign-policy-portal.md) quickstart para encontrar e atribuir uma política de motor AKS. Procurar uma definição de política do motor AKS em vez da amostra 'vms de auditoria'.

> [!IMPORTANT]
> As políticas incorporadas na categoria **Kubernetes** são apenas para uso com o AkS Engine.

## <a name="logging"></a>Registo

### <a name="azure-policy-add-on-logs"></a>Registos adicionais da política azure

Como controlador/contentor Kubernetes, o Add-on de Política Azure mantém os registos no cluster do motor AKS.

Para visualizar os registos de add-on da política Azure, `kubectl` utilize:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Registos do gatekeeper

O gatekeeper pod, _gatekeeper-controller-manager-0,_ está geralmente no espaço ou no espaço de nome, mas pode estar em um espaço de nome diferente dependendo de `gatekeeper-system` como é `kube-system` implantado.

Para ver os registos do Gatekeeper, `kubectl` utilize:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Para mais informações, consulte [o Gatekeeper Debugging](https://github.com/open-policy-agent/gatekeeper#debugging) na documentação da OPA.

## <a name="remove-the-add-on"></a>Remova o addon

Para remover o Add-on e gatekeeper da política Azure do seu cluster de motor AKS, utilize o método que se alinha com a forma como o addon foi instalado:

- Se instalado definindo a propriedade **addons** na definição de cluster para motor AKS:

  Reutilizar a definição de cluster para o Motor AKS depois de mudar a propriedade de **addons** para _a política azul_ para falsa:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Se instalado com gráficos de leme:

  1. Remover constrangimentos antigos

     Atualmente, o mecanismo de desinstalação apenas remove o sistema Gatekeeper, não remove nenhum Modelo de _Restrição,_ _Restrição_ou recursos _Config_ que tenham sido criados pelo utilizador, nem remove os seus _CRDs acompanhantes_.

     Quando o Gatekeeper está em funcionamento, é possível remover constrangimentos indesejados por:

     - Apagar todas as instâncias do recurso de restrição
     - Eliminar o recurso _Limitação Modelo,_ que deve limpar automaticamente o _CRD_
     - Eliminação do recurso _Config_ remove finalizadores em recursos sincronizados

  1. Desinstalar o Add-on da Política Azure
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Desinstalar gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico recolhidos pelo Add-on política do Azure

O Add-on de Política Azure para Kubernetes recolhe dados de diagnóstico de cluster limitados. Estes dados de diagnóstico são dados técnicos vitais relacionados com software e desempenho. É usado das seguintes formas:

- Mantenha a política azure Add-on atualizado
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