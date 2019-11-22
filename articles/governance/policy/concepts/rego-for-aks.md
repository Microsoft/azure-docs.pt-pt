---
title: Saiba Azure Policy do serviço kubernetes do Azure
description: Saiba como Azure Policy usa o rego e o agente de política aberto para gerenciar clusters no serviço kubernetes do Azure.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: d8d5c1ebeded62f475804e4e704f823aba2c10eb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279401"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Entender Azure Policy para o serviço kubernetes do Azure

O Azure Policy integra-se com o AKs ( [serviço kubernetes do Azure](../../../aks/intro-kubernetes.md) ) para aplicar imposição e garantias em escala em seus clusters de maneira centralizada e consistente.
Ao estender o uso do [gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2, um _webhook do controlador de admissão_ para Opa ( [Open Policy Agent](https://www.openpolicyagent.org/) ), Azure Policy torna possível gerenciar e relatar o estado de conformidade de seus recursos do Azure e clusters AKs de um único lugar.

> [!NOTE]
> Azure Policy para AKS está em visualização limitada e só dá suporte a definições de políticas internas.

## <a name="overview"></a>Descrição geral

Para habilitar e usar Azure Policy para AKS com o cluster AKS, execute as seguintes ações:

- [Aceitar recursos de visualização](#opt-in-for-preview)
- [Instalar o complemento Azure Policy](#installation-steps)
- [Atribuir uma definição de política para AKS](#built-in-policies)
- [Aguardar validação](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Aceitar para visualização

Antes de instalar o complemento Azure Policy ou habilitar qualquer um dos recursos de serviço, sua assinatura deve habilitar o provedor de recursos **Microsoft. ContainerService** e o provedor de recursos **Microsoft. PolicyInsights** , em seguida, ser aprovada para Participe da versão prévia. Para ingressar na versão prévia, siga estas etapas na portal do Azure ou com CLI do Azure:

- portal do Azure:

  1. Registre os provedores de recursos **Microsoft. ContainerService** e **Microsoft. PolicyInsights** . Para obter as etapas, consulte [provedores de recursos e tipos](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

     ![Pesquisar política em todos os serviços](../media/rego-for-aks/search-policy.png)

  1. Selecione a **visualização de junção** no lado esquerdo da página Azure Policy.

     ![Ingressar na política para visualização do AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Selecione a linha da assinatura que você deseja adicionar à visualização.

  1. Selecione o botão **aceitar** na parte superior da lista de assinaturas.

- CLI do Azure:

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
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Azure Policy complemento

O _complemento Azure Policy_ para kubernetes conecta o serviço de Azure Policy ao controlador de admissão do gatekeeper. O complemento, que é instalado no namespace da _política do Azure_ , atua nas seguintes funções:

- Verifica com Azure Policy de atribuições para o cluster AKS
- Baixa e armazena em cache detalhes da política, incluindo a definição de política _rego_ , como **configmaps**
- Executa uma verificação de conformidade de verificação completa no cluster AKS
- Relata detalhes de auditoria e de conformidade de volta para o Azure Policy

### <a name="installing-the-add-on"></a>Instalando o complemento

#### <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o complemento no cluster do AKS, a extensão de visualização deve ser instalada. Esta etapa é feita com CLI do Azure:

1. Você precisa do CLI do Azure versão 2.0.62 ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. O cluster AKS deve ser a versão _1,10_ ou superior. Use o script a seguir para validar a versão do cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instale a versão _0.4.0_ da extensão de visualização de CLI do Azure para AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se você já tiver instalado a extensão _AKs-Preview_ , instale todas as atualizações usando o comando `az extension update --name aks-preview`.

#### <a name="installation-steps"></a>Etapas de instalação

Depois que os pré-requisitos forem concluídos, instale o complemento Azure Policy no cluster AKS que você deseja gerenciar.

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure clicando em **todos os serviços**, em seguida, procurando e selecionando **Serviços Kubernetess**.

  1. Selecione um dos clusters do AKS.

  1. Selecione **políticas (versão prévia)** no lado esquerdo da página do serviço kubernetes.

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o botão **habilitar complemento** .

     ![Habilitar o Azure Policy para o complemento AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Se o botão **habilitar complemento** estiver esmaecido, a assinatura ainda não foi adicionada à versão prévia. Consulte [aceitar para visualizar](#opt-in-for-preview) as etapas necessárias.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e relatório

O complemento faz check-in com Azure Policy para alterações nas atribuições de política a cada 5 minutos. Durante esse ciclo de atualização, o complemento remove todos os _configmaps_ no namespace de _política do Azure_ e, em seguida, recria o _configmaps_ para uso do gatekeeper.

> [!NOTE]
> Embora um _administrador de cluster_ possa ter permissão para o namespace de _política do Azure_ , não é recomendável nem tem suporte para fazer alterações no namespace. Quaisquer alterações manuais feitas são perdidas durante o ciclo de atualização.

A cada 5 minutos, o complemento chama uma verificação completa do cluster. Depois de coletar detalhes da verificação completa e de quaisquer avaliações em tempo real pelo gatekeeper de tentativas de alterações no cluster, o complemento relata os resultados de volta para Azure Policy para inclusão em [detalhes de conformidade](../how-to/get-compliance-data.md) , como qualquer atribuição de Azure Policy. Somente os resultados de atribuições de política ativas são retornados durante o ciclo de auditoria.

## <a name="policy-language"></a>Idioma da política

A estrutura de linguagem Azure Policy para gerenciar AKS segue as políticas existentes. O efeito _EnforceRegoPolicy_ é usado para gerenciar seus clusters AKs e usa propriedades de _detalhes_ específicas para trabalhar com Opa e o gatekeeper v2. Para obter detalhes e exemplos, consulte o efeito [EnforceRegoPolicy](effects.md#enforceregopolicy) .

Como parte da propriedade _Details. Policy_ na definição de política, Azure Policy passa o URI de uma política rego para o complemento. Rego é a linguagem que o OPA e o gatekeeper dão suporte para validar ou mutar uma solicitação para o cluster kubernetes. Ao dar suporte a um padrão existente para o gerenciamento de kubernetes, Azure Policy torna possível reutilizar as regras existentes e emparelhar com Azure Policy para uma experiência unificada de relatórios de conformidade de nuvem. Para obter mais informações, consulte [o que é rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Políticas incorporadas

Para localizar as políticas internas de gerenciamento de AKS usando o portal do Azure, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure. Selecione **todos os serviços** no painel esquerdo e, em seguida, procure e selecione **política**.

1. No painel esquerdo da página Azure Policy, selecione **definições**.

1. Na caixa de listagem suspensa categoria, use **selecionar tudo** para limpar o filtro e, em seguida, selecione **serviço kubernetes**.

1. Selecione a definição de política e, em seguida, selecione o botão **atribuir** .

> [!NOTE]
> Ao atribuir o Azure Policy para definição de AKS, o **escopo** deve incluir o recurso de cluster AKs.

Como alternativa, use o guia de início rápido [atribuir um portal de política](../assign-policy-portal.md) para localizar e atribuir uma política de AKs. Procure uma definição de política kubernetes em vez do exemplo ' auditar VMs '.

> [!IMPORTANT]
> As políticas internas no serviço de **kubernetes** de categoria são apenas para uso com AKs.

## <a name="logging"></a>Registo

### <a name="azure-policy-add-on-logs"></a>Azure Policy logs de complemento

Como um controlador/contêiner kubernetes, o complemento Azure Policy mantém os logs no cluster AKS. Os logs são expostos na página **insights** do cluster AKs. Para obter mais informações, consulte [entender o desempenho do cluster AKs com Azure monitor para contêineres](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Logs do gatekeeper

Para habilitar os logs do gatekeeper para novas solicitações de recursos, siga as etapas em [habilitar e examine os logs do nó mestre do kubernetes em AKs](../../../aks/view-master-logs.md).
Aqui está um exemplo de consulta para exibir eventos negados sobre novas solicitações de recursos:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Para exibir logs de contêineres do Gatekeeper, siga as etapas em [habilitar e examine os logs do nó mestre do kubernetes em AKs](../../../aks/view-master-logs.md) e marque a opção _Kube-apiserver_ no painel **configurações de diagnóstico** .

## <a name="remove-the-add-on"></a>Remover o complemento

Para remover o complemento Azure Policy do cluster do AKS, use o portal do Azure ou CLI do Azure:

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure clicando em **todos os serviços**, em seguida, procurando e selecionando **Serviços Kubernetess**.

  1. Selecione o cluster do AKS no qual você deseja desabilitar o complemento de Azure Policy.

  1. Selecione **políticas (versão prévia)** no lado esquerdo da página do serviço kubernetes.

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o botão **desabilitar complemento** .

     ![Desabilitar o Azure Policy para o complemento AKS](../media/rego-for-aks/disable-policy-add-on.png)

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico coletados pelo complemento Azure Policy

O complemento Azure Policy do kubernetes coleta dados de diagnóstico de cluster limitados. Esses dados de diagnóstico são dados técnicos vitais relacionados ao software e ao desempenho. Ele é usado das seguintes maneiras:

- Manter Azure Policy complemento atualizado
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