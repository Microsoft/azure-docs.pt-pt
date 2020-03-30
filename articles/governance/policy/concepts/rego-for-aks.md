---
title: Aprenda a Política Azure para o Serviço Azure Kubernetes
description: Saiba como a Política Azure utiliza o Rego e o Open Policy Agent para gerir clusters no Serviço Azure Kubernetes.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372648"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Compreender o Azure Policy para o Azure Kubernetes Service

A Azure Policy integra-se com o [Serviço Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS) para aplicar aplicações e salvaguardas em escala nos seus clusters de forma centralizada e consistente.
Ao alargar o uso do [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, um webhook do controlador de _admissão_ para [o Open Policy Agent](https://www.openpolicyagent.org/) (OPA), a Política Azure permite gerir e reportar sobre o estado de conformidade dos seus recursos Azure e clusters AKS de um só local.

> [!IMPORTANT]
> A Política Azure para aks está em Pré-visualização e apenas apoia definições políticas incorporadas. As políticas incorporadas estão na categoria **Kubernetes.** O efeito **EnforceRegoPolicy** e as políticas relacionadas da categoria **de serviço kubernetes** estão a ser _depreciadas._ Em vez disso, utilize o efeito [EnforceOPAConstraint](./effects.md#enforceopaconstraint) atualizado.

> [!WARNING]
> Esta funcionalidade ainda não está disponível em todas as regiões. Para obter um estado no lançamento, consulte [questões AKS - Breaking Change for Policy Add-on](https://github.com/Azure/AKS/issues/1529).

## <a name="overview"></a>Descrição geral

Para ativar e utilizar a Política Azure para AKS com o seu cluster AKS, tome as seguintes ações:

- [Optar ativamente por participar na pré-visualização de funcionalidades](#opt-in-for-preview)
- [Instale o Add-on política azure](#installation-steps)
- [Atribuir uma definição de política para AKS](#built-in-policies)
- [Aguarde a validação](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Opt-in para pré-visualização

Antes de instalar o Add-on de Política Azure ou ativar qualquer uma das funcionalidades do serviço, a sua subscrição deve ativar o fornecedor de recursos **Microsoft.ContainerService** e o fornecedor de recursos **Microsoft.PolicyInsights,** e depois ser aprovado para aderir à pré-visualização. Para se juntar à pré-visualização, siga estes passos no portal Azure ou com o Azure CLI:

- Portal Azure:

  1. Registe os fornecedores de recursos **Microsoft.ContainerService** e **Microsoft.PolicyInsights.** Para passos, consulte [os fornecedores e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)de recursos.

  1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

     ![Pesquisa de Política em Todos os Serviços](../media/rego-for-aks/search-policy.png)

  1. **Selecione Participar** no lado esquerdo da página Política Azure.

     ![Junte-se à política de pré-visualização aks](../media/rego-for-aks/join-aks-preview.png)

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
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Add-on política azure

O _Add-on de política Azure_ para Kubernetes liga o serviço de Política Azure ao controlador de admissão gatekeeper. O complemento, que é instalado no espaço de nome do _sistema kube,_ decreta as seguintes funções:

- Verifica com o serviço de Política Azure para atribuições ao cluster.
- Implementa políticas no cluster como modelo de [restrição](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e [restrição](https://github.com/open-policy-agent/gatekeeper#constraints) de recursos personalizados.
- Relatórios de auditoria e conformidade detalham o serviço de Política Azure.

### <a name="installing-the-add-on"></a>Instalação do addon

#### <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o complemento no seu cluster AKS, a extensão de pré-visualização deve ser instalada. Este passo é feito com o Azure CLI:

1. Se as políticas de Gatekeeper v2 foram instaladas, remova o addon com o botão **Desativar** no seu cluster AKS sob a página **Políticas (pré-visualização).**

1. Precisa da versão Azure CLI 2.0.62 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. O cluster AKS deve ser da versão _1.14_ ou superior. Utilize o seguinte script para validar a sua versão de cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instale a versão _0.4.0_ da extensão de `aks-preview`pré-visualização Azure CLI para AKS,

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

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o botão **'adicionar'.**

     ![Ativar a política azure para add-on AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Se o botão de adição Enable estiver acinzentado, a subscrição ainda não foi adicionada à **pré-visualização.** Consulte [o Opt-in para visualização](#opt-in-for-preview) dos passos necessários. Se estiver disponível um botão **de desativação,** o Gatekeeper v2 ainda está instalado e deve ser removido.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Frequência de validação e reporte

O complemento faz check-in com o serviço de Política Azure para alterações nas atribuições políticas a cada 15 minutos.
Durante este ciclo de atualização, o complemento verifica as alterações. Estas alterações criam, atualizam ou eliminam os modelos e constrangimentos de restrição.

> [!NOTE]
> Embora um administrador de cluster possa ter permissão para criar e atualizar modelos de restrição e recursos de restrições, estes não são cenários suportados, uma vez que as atualizações manuais serão substituídas.

A cada 15 minutos, o complemento pede uma varredura completa do cluster. Depois de reunir detalhes da varredura completa e de quaisquer avaliações em tempo real por parte da Gatekeeper de tentativas de alterações ao cluster, o addon reporta os resultados ao serviço de Política Azure para inclusão em detalhes de [conformidade](../how-to/get-compliance-data.md#portal) como qualquer atribuição de Política Azure. Apenas os resultados para as atribuições de políticas ativas são devolvidos durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como [violações](https://github.com/open-policy-agent/gatekeeper#audit) enumeradas no campo de status da restrição falhada.

## <a name="policy-language"></a>Linguagem política

A estrutura linguística da Política Azure para a gestão das Kubernetes segue a das políticas existentes. O efeito _EnforceOPAConstraint_ é usado para gerir os seus clusters Kubernetes e leva detalhes propriedades específicas para trabalhar com o [Opa Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e Gatekeeper v3. Para mais detalhes e exemplos, consulte o efeito [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
  
Como parte dos _detalhes.constraintTemplate_ e _details.constraint_ properties na definição de política, a Política Azure passa as URIs [destas Definições de Recursos Personalizados](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) para o addon. Rego é a linguagem que opa e Gatekeeper suportam para validar um pedido ao cluster Kubernetes. Ao apoiar uma norma existente para a gestão da Kubernetes, a Política Azure permite reutilizar as regras existentes e emparelhá-las com a Política Azure para uma experiência unificada de reporte de conformidade com a nuvem. Para mais informações, veja [o que é Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="built-in-policies"></a>Políticas incorporadas

Para encontrar as políticas incorporadas para gerir o seu cluster utilizando o portal Azure, siga estes passos:

1. Inicie o serviço De Política Azure no portal Azure. Selecione todos os serviços no painel esquerdo e, em seguida, procure e selecione **Política**.

1. No painel esquerdo da página Política Azure, selecione **Definições**.

1. A partir da caixa de lista seletiva categoria, utilize Selecione tudo para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de política e, em seguida, selecione o botão **Atribuir.**

1. Detete o **Âmbito** ao grupo de gestão, subscrição ou grupo de recursos do cluster Kubernetes onde a atribuição de políticas será aplicável.

   > [!NOTE]
   > Ao atribuir a política azure para a definição de AKS, o **Âmbito** deve incluir o recurso de cluster AKS.

1. Dê à atribuição de política um **nome** e **descrição** que possa usar para identificá-lo facilmente.

1. Desloque a [aplicação](./assignment-structure.md#enforcement-mode) da política a um dos valores abaixo.

   - **Habilitado** - Impor a política no cluster. Os pedidos de admissão de Kubernetes com violações são negados.
   
   - **Deficientes** - Não aplique a política do cluster. Os pedidos de admissão de Kubernetes com violações não são negados. Os resultados da avaliação da conformidade ainda estão disponíveis. Ao lançar novas políticas para executar clusters, a opção _de deficientes_ é útil para testar as políticas, uma vez que os pedidos de admissão com violações não são negados.

1. Selecione **Next**.

1. Definir **valores de parâmetros**
   
   - Para excluir os espaços de nome kubernetes da avaliação de políticas, especifique a lista de espaços de nome em **exclusões**de espaço de nome paraparâmetros . É recomendado excluir: _kube-system_

1. Selecione **Rever + criar**.

Alternadamente, utilize a [alferes - Portal](../assign-policy-portal.md) quickstart para encontrar e atribuir uma política AKS. Procurar uma definição de política kubernetes em vez da amostra "vms de auditoria".

> [!IMPORTANT]
> As políticas incorporadas na categoria **Kubernetes** são apenas para uso com AKS. Para obter uma lista de políticas incorporadas, consulte [amostras kubernetes.](../samples/built-in-policies.md#kubernetes)

## <a name="logging"></a>Registo

### <a name="azure-policy-add-on-logs"></a>Registos adicionais da política azure

Como controlador/contentor Kubernetes, tanto o Add-on da Política Azure como o Gatekeeper mantêm os registos no cluster AKS. Os registos estão expostos na página **Insights** do cluster AKS. Para mais informações, consulte [o desempenho do cluster DA Coma AKS com o Monitor Azure para obter recipientes.](../../../azure-monitor/insights/container-insights-analyze.md)

## <a name="remove-the-add-on"></a>Remova o addon

Para remover o Add-on da política Azure do seu cluster AKS, utilize o portal Azure ou o Azure CLI:

- Portal do Azure

  1. Lance o serviço AKS no portal Azure clicando em **Todos os serviços,** procurando e selecionando os **serviços kubernetes.**

  1. Selecione o seu cluster AKS onde pretende desativar o Add-on da Política Azure.

  1. Selecione **Políticas (pré-visualização)** no lado esquerdo da página de serviço kubernetes.

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione o botão **de adição desativar.**

     ![Desativar a política azure para add-on AKS](../media/rego-for-aks/disable-policy-add-on.png)

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
