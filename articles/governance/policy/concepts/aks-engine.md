---
title: Saiba Azure Policy para o mecanismo AKS
description: Saiba como Azure Policy usa o CustomResourceDefinitions e o agente de política aberto do gatekeeper V3 para gerenciar clusters com o mecanismo do AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 2d1ae33755dcb52c5fe65ec46f0d02e090f6f417
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267258"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Entender Azure Policy para o mecanismo AKS

O Azure Policy integra-se ao [AKs Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), um sistema que fornece ferramentas convenientes para inicializar rapidamente um cluster kubernetes autogerenciado no Azure. Essa integração permite imposição e garantias em escala em seus clusters autogerenciados do mecanismo do AKS de maneira centralizada e consistente. Ao estender o uso do protocolo OPA ( [Open Policy Agent](https://www.openpolicyagent.org/) [) v3 (](https://github.com/open-policy-agent/gatekeeper) beta), um _webhook do controlador de admissão_ para kubernetes, Azure Policy torna possível gerenciar e relatar o estado de conformidade de seus recursos do Azure e do mecanismo de AKs clusters autogerenciados de um único lugar.

> [!NOTE]
> Azure Policy para o mecanismo AKS está em visualização pública e não tem SLA. O gatekeeper v3 está em beta e tem suporte da comunidade de software livre. O serviço só dá suporte a definições de políticas internas e a um único cluster de mecanismo AKS para cada grupo de recursos configurado com uma entidade de serviço.

> [!IMPORTANT]
> Para obter suporte para Azure Policy para AKS Engine, AKS Engine ou gatekeeper v3, crie um [novo problema](https://github.com/Azure/aks-engine/issues/new/choose) no repositório GitHub do mecanismo de AKs.

## <a name="overview"></a>Descrição geral

Para habilitar e usar Azure Policy para o mecanismo AKS com seu cluster kubernetes autogerenciado no Azure, execute as seguintes ações:

- [Pré-requisitos](#prerequisites)
- [Instalar o complemento Azure Policy](#installing-the-add-on)
- [Atribuir uma definição de política para o mecanismo de AKS](#built-in-policies)
- [Aguardar validação](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o complemento Azure Policy ou habilitar qualquer um dos recursos de serviço, sua assinatura deve habilitar o provedor de recursos **Microsoft. PolicyInsights** e criar uma atribuição de função para a entidade de serviço de cluster. 

1. Para habilitar o provedor de recursos, siga as etapas em [provedores de recursos e tipos](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) ou execute o comando CLI do Azure ou Azure PowerShell:

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

1. Criar uma atribuição de função para a entidade de serviço de cluster

   - Se você não souber a ID do aplicativo principal de serviço de cluster, procure o comando a seguir.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Atribua a atribuição de função ' gravador de dados de informações de política (versão prévia) ' à ID do aplicativo principal de serviço de cluster (valor _aadClientID_ da etapa anterior) com CLI do Azure. Substitua `<subscriptionId>` pela sua ID de assinatura e `<aks engine cluster resource group>` com o grupo de recursos no qual o cluster kubernetes do mecanismo de AKS é gerenciado.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure Policy complemento

O _complemento Azure Policy_ para kubernetes conecta o serviço de Azure Policy ao controlador de admissão do gatekeeper. O complemento, que é instalado no namespace _Kube-System_ , funciona com as seguintes funções:

- Verifica com Azure Policy de atribuições para o cluster do mecanismo AKS
- Baixa e instala detalhes da política, modelos de restrição e restrições
- Executa uma verificação de conformidade de verificação completa no cluster do mecanismo do AKS
- Relata detalhes de auditoria e de conformidade de volta para o Azure Policy

### <a name="installing-the-add-on"></a>Instalando o complemento

Depois que os pré-requisitos forem concluídos, o complemento Azure Policy poderá ser instalado. A instalação pode ser durante o ciclo de criação ou atualização de um mecanismo do AKS ou como uma ação independente em um cluster existente.

- Instalar durante a criação ou o ciclo de atualização

  Para habilitar o complemento de Azure Policy durante a criação de um novo cluster autogerenciado ou como uma atualização para um cluster existente, inclua a definição de cluster de propriedade **addons** para o mecanismo AKs.

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

  Para obter mais informações sobre o, consulte o guia externo [definição de cluster do mecanismo AKs](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalar no cluster existente com gráficos do Helm

  Use as etapas a seguir para preparar o cluster e instalar o complemento:

  1. Instale o gatekeeper para o namespace do _sistema gatekeeper_ .

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Adicione o rótulo _de plano de controle_ a _Kube-System_. Esse rótulo exclui a auditoria de pods e serviços do _Kube System_ pelo gatekeeper e o complemento Azure Policy.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Sincronizar dados do kubernetes (namespace, Pod, entrada, serviço) com OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Para obter mais informações, consulte [Opa-replicando dados](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Adicione o repositório de Azure Policy a Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Para obter mais informações, consulte [Helm Chart-guia de início rápido](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Instale o complemento com um gráfico do Helm. Substitua `<subscriptionId>` pela sua ID de assinatura e `<aks engine cluster resource group>` com o grupo de recursos no qual o cluster kubernetes do mecanismo de AKS é gerenciado.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Para obter mais informações sobre o que o gráfico de Helm do complemento instala, consulte a [definição do gráfico de Helm do complemento de Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) no github.

     > [!NOTE]
     > Devido à relação entre o complemento Azure Policy e a ID do grupo de recursos, Azure Policy dá suporte a apenas um cluster do mecanismo AKS para cada grupo de recursos.

Para validar se a instalação do complemento foi bem-sucedida e se o Pod da _política do Azure_ está em execução, execute o seguinte comando:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e relatório

O complemento faz check-in com Azure Policy para alterações nas atribuições de política a cada 5 minutos. Durante esse ciclo de atualização, o complemento verifica se há alterações. Essas alterações disparam criações, atualizações ou exclusões dos modelos de restrição e restrições.

> [!NOTE]
> Embora um _administrador de cluster_ possa ter permissão para fazer alterações em modelos de restrição e restrições, não é recomendável nem tem suporte para fazer alterações em modelos de restrição ou restrições criadas por Azure Policy. Quaisquer alterações manuais feitas são perdidas durante o ciclo de atualização.

A cada 5 minutos, o complemento chama uma verificação completa do cluster. Depois de coletar detalhes da verificação completa e de quaisquer avaliações em tempo real pelo gatekeeper de tentativas de alterações no cluster, o complemento relata os resultados de volta para Azure Policy para inclusão em [detalhes de conformidade](../how-to/get-compliance-data.md) , como qualquer atribuição de Azure Policy. Somente os resultados de atribuições de política ativas são retornados durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como violações listadas no campo status da restrição com falha.

## <a name="policy-language"></a>Idioma da política

A estrutura de linguagem Azure Policy para gerenciar o mecanismo AKS segue as políticas existentes. O efeito _EnforceOPAConstraint_ é usado para gerenciar seus clusters de mecanismo do AKs e usa propriedades de _detalhes_ específicas para trabalhar com a [estrutura de restrição Opa](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e o gatekeeper v3. Para obter detalhes e exemplos, consulte o efeito [EnforceOPAConstraint](effects.md#enforceopaconstraint) .

Como parte das propriedades _Details. constrainttemplate_ e _Details. Constraint_ na definição de política, Azure Policy passa os URIs desses [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) para o complemento. Rego é a linguagem que o OPA e o gatekeeper dão suporte para validar uma solicitação para o cluster kubernetes. Ao dar suporte a um padrão existente para o gerenciamento de kubernetes, Azure Policy torna possível reutilizar as regras existentes e emparelhar com Azure Policy para uma experiência unificada de relatórios de conformidade de nuvem. Para obter mais informações, consulte [o que é rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Políticas incorporadas

Para localizar as políticas internas para gerenciar seu cluster do mecanismo do AKS usando o portal do Azure, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure. Selecione **todos os serviços** no painel esquerdo e, em seguida, procure e selecione **política**.

1. No painel esquerdo da página Azure Policy, selecione **definições**.

1. Na caixa de listagem suspensa categoria, use **selecionar tudo** para limpar o filtro e, em seguida, selecione **kubernetes**.

1. Selecione a definição de política e, em seguida, selecione o botão **atribuir** .

> [!NOTE]
> Ao atribuir a Azure Policy para a definição do mecanismo AKS, o **escopo** deve ser o grupo de recursos do cluster do mecanismo do AKS.

Como alternativa, use o guia de início rápido [atribuir um portal de política](../assign-policy-portal.md) para localizar e atribuir uma política de mecanismo AKs. Procure uma definição de política do mecanismo AKS em vez do exemplo ' auditar VMs '.

> [!IMPORTANT]
> As políticas internas na categoria **kubernetes** são apenas para uso com o mecanismo AKs.

## <a name="logging"></a>Registo

### <a name="azure-policy-add-on-logs"></a>Azure Policy logs de complemento

Como um controlador/contêiner kubernetes, o complemento Azure Policy mantém os logs no cluster do mecanismo AKS.

Para exibir os logs de complemento Azure Policy, use `kubectl`:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Logs do gatekeeper

O Pod do Gatekeeper, o _gatekeeper-Controller-Manager-0_, geralmente está no namespace `gatekeeper-system` ou `kube-system`, mas pode estar em um namespace diferente, dependendo de como ele é implantado.

Para exibir os logs do Gatekeeper, use `kubectl`:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Para obter mais informações, consulte [Depurando o gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) na documentação do Opa.

## <a name="remove-the-add-on"></a>Remover o complemento

Para remover o complemento de Azure Policy e o gatekeeper do seu cluster de mecanismo do AKS, use o método que se alinha com o modo como o complemento foi instalado:

- Se instalado, definindo a propriedade **addons** na definição de cluster para o mecanismo AKs:

  Reimplante a definição de cluster no mecanismo AKS depois de alterar a propriedade **addones** para _Azure-Policy_ para false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Se instalado com gráficos do Helm:

  1. Remover restrições antigas

     Atualmente, o mecanismo de desinstalação remove apenas o sistema gatekeeper, ele não remove nenhum recurso _constrainttemplate_, de _restrição_ou de _configuração_ criado pelo usuário, nem remove o _crds_que o acompanha.

     Quando o gatekeeper está em execução, é possível remover restrições indesejadas da:

     - Excluindo todas as instâncias do recurso de restrição
     - Excluindo o recurso _constrainttemplate_ , que deve limpar automaticamente o _CRD_
     - Excluir o recurso de _configuração_ remove os finalizadores em recursos sincronizados

  1. Desinstalar o complemento Azure Policy
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Desinstalar o gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico coletados pelo complemento Azure Policy

O complemento Azure Policy do kubernetes coleta dados de diagnóstico de cluster limitados. Esses dados de diagnóstico são dados técnicos vitais relacionados ao software e ao desempenho. Ele é usado das seguintes maneiras:

- Manter o complemento Azure Policy atualizado
- Mantenha Azure Policy complemento seguro, confiável e com bom desempenho
- Melhorar o complemento Azure Policy por meio da análise agregada do uso do complemento

As informações coletadas pelo complemento não são dados pessoais. Os detalhes a seguir são coletados no momento:

- Versão do agente de complemento Azure Policy
- Tipo de cluster
- Região do cluster
- Grupo de recursos de cluster
- ID de recurso de cluster
- ID da assinatura do cluster
- Sistema operacional do cluster (exemplo: Linux)
- Cidade do cluster (exemplo: Seattle)
- Estado do cluster ou província (exemplo: Washington)
- País ou região do cluster (exemplo: Estados Unidos)
- Exceções/erros encontrados por Azure Policy complemento durante a instalação do agente na avaliação da política
- Número de políticas de gatekeeper não instaladas pelo complemento Azure Policy

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Veja a [Estrutura de definição do Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).