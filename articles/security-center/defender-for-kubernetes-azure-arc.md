---
title: Proteja as implementações híbridas e multi-cloud kubernetes com o Azure Defender para Kubernetes
description: Use o Azure Defender para Kubernetes com os seus agrupamentos de Kubernetes multi-cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029204"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>DefendA O Arco de Azure permitiu que os aglomerados de Kubernetes funcionassem em ambientes no local e em ambientes multi-nuvens

A **extensão de clusters Azure Defender for Kubernetes** pode defender os seus clusters no local com as mesmas capacidades de deteção de ameaças oferecidas para os clusters de Serviço Azure Kubernetes. Ativar [o Azure Arc ativado kubernetes](../azure-arc/kubernetes/overview.md) nos seus clusters e implementar a extensão conforme descrito nesta página. 

A extensão também pode proteger os clusters kubernetes em outros fornecedores de nuvem, embora não nos seus serviços geridos kubernetes.

> [!TIP]
> Colocamos alguns ficheiros de amostras para ajudar no processo de instalação em [exemplos de instalação no GitHub.](https://aka.ms/kubernetes-extension-installation-examples)

## <a name="availability"></a>Disponibilidade

| Aspeto | Detalhes |
|--------|---------|
| Estado de libertação | **Pré-visualizar**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Funções e permissões necessárias | [Administrador de segurança](../role-based-access-control/built-in-roles.md#security-admin) pode dispensar alertas<br>[O leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader) pode ver as descobertas |
| Preços | Requer [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md) |
| Distribuição apoiada de Kubernetes | [Serviço Azure Kubernetes em Azure Stack HCI](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [Motor AKS](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (versão 4.6 ou mais recente) |
| Limitações | O Azure Arc permitiu que a Kubernetes e a extensão do Azure Defender **não suportassem** ofertas geridas de Kubernetes como o Google Kubernetes Engine e o Elastic Kubernetes Service. [O Azure Defender está na sua mente disponível para o Serviço Azure Kubernetes (AKS)](defender-for-kubernetes-introduction.md) e não necessita de ligar o cluster ao Arco Azure. |
| Ambientes e regiões | A disponibilidade para esta extensão é a mesma que [a Azure Arc permitiu que kubernetes](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Descrição geral da arquitetura

Para todos os clusters Kubernetes que não a AKS, você precisará ligar o seu cluster ao Arco Azure. Uma vez ligado, o Azure Defender for Kubernetes pode ser implantado no [Arco Azure, ativando](../azure-arc/kubernetes/overview.md) os recursos de Kubernetes como [uma extensão de cluster](../azure-arc/kubernetes/extensions.md).

Os componentes de extensão recolhem dados de auditoria de Kubernetes de todos os nós do avião de controlo no cluster e enviam-nos para o Azure Defender para kubernetes backend na nuvem para uma análise mais aprofundada. A extensão é registada com um espaço de trabalho Log Analytics utilizado como um pipeline de dados, mas os dados do registo de auditoria não são armazenados no espaço de trabalho do Log Analytics.

Este diagrama mostra a interação entre o Azure Defender para Kubernetes e o arco Azure habilitado para o cluster Kubernetes:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Um diagrama de arquitetura de alto nível que descreve a interação entre O Azure Defender para Kubernetes e um Arco Azure permitiu clusters kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Pré-requisitos

- O Azure Defender for Kubernetes está [ativado na sua subscrição](enable-azure-defender.md)
- O seu cluster Kubernetes está [ligado ao Arco de Azure](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Encontrou os pré-requisitos listados sob a [documentação genérica](../azure-arc/kubernetes/extensions.md#prerequisites)de extensões de cluster.

## <a name="deploy-the-azure-defender-extension"></a>Implementar a extensão do Azure Defender

Pode implantar a extensão Azure Defender utilizando uma gama de métodos. Para etapas detalhadas, selecione o separador relevante.

### <a name="azure-portal"></a>[**Portal Azure**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Utilize a opção "Quick fix" a partir da recomendação do Centro de Segurança

Uma recomendação dedicada no Azure Security Center fornece:

- **Visibilidade** sobre qual dos seus clusters tem o Defender para extensão Kubernetes implantado
- **Uma opção de "correção rápida"** para implantá-lo para os clusters sem a extensão

1. A partir da página de recomendações do Azure Security Center, abra o controlo de segurança **Enable Azure Defender.**

1. Utilize o filtro para encontrar a recomendação chamada **Azure Arc ativada pelos clusters Kubernetes deve ter a extensão do Azure Defender instalada**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="A recomendação do Azure Security Center para a implementação da extensão do Azure Defender para o Arco Azure permitiu clusters kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Note o ícone Quick Fix na coluna de ações

1. Selecione a extensão para ver os detalhes dos recursos saudáveis e insalubres - clusters com e sem a extensão.

1. A partir da lista de recursos não saudáveis, selecione um cluster e selecione **Remediate** para abrir o painel com as opções de reparação.

1. Selecione o espaço de trabalho do Log Analytics relevante e selecione **Remediate x recurso**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Implementar a extensão do Azure Defender para O Arco Azure com a opção de correção rápida do Security Center.":::


### <a name="azure-cli"></a>[**CLI do Azure**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Utilize o CLI do Azure para implantar a extensão do Azure Defender

1. Faça login em Azure:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Certifique-se de que utiliza o mesmo ID de subscrição para ``<your-subscription-id>`` o que foi utilizado ao ligar o seu cluster ao Azure Arc.

1. Executar o seguinte comando para implantar a extensão em cima do seu arco Azure ativado cluster Kubernetes:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Abaixo é dada uma descrição de todas as definições de configuração suportadas no tipo de extensão Azure Defender:

    | Propriedade | Descrição |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Opcional.** ID de recursos completos do seu próprio espaço de trabalho Log Analytics.<br>Quando não for fornecido, será utilizado o espaço de trabalho predefinido da região.<br><br>Para obter o ID completo do recurso, execute o seguinte comando para exibir a lista de espaços de trabalho nas suas subscrições no formato JSON predefinido:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>O ID do espaço de trabalho Log Analytics tem a seguinte sintaxe:<br>/subscrições/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>Saiba mais em [log analytics espaços de trabalho](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) |
    | auditLogPath |**Opcional.** O caminho completo para os ficheiros de registo de auditoria.<br>Quando não for fornecida, o caminho predefinido ``/var/log/kube-apiserver/audit.log`` será utilizado.<br>Para o motor AKS, o caminho padrão é ``/var/log/kubeaudit/audit.log`` |

    O comando abaixo mostra um exemplo de utilização de todos os campos opcionais:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Utilize o Gestor de Recursos Azure para implementar a extensão do Azure Defender

Para utilizar o Azure Resource Manager para implementar a extensão Azure Defender, precisará de um espaço de trabalho log Analytics na sua subscrição. Saiba mais em [espaços de trabalho log analytics.](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces)

Pode utilizar o **azure-defender-extension-arm-template.jsno** modelo de Gestor de Recursos a partir dos exemplos de [instalação](https://aka.ms/kubernetes-extension-installation-examples)do Security Center .

> [!TIP]
> Se você é novo em modelos de Gestor de Recursos, comece aqui: [O que são modelos do Gestor de Recursos Azure?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**API REST**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Utilize a API REST para implementar a extensão do Azure Defender 

Para utilizar a API REST para implementar a extensão Azure Defender, necessitará de um espaço de trabalho log Analytics na sua subscrição. Saiba mais em [espaços de trabalho log analytics.](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces)

> [!TIP]
> A forma mais simples de utilizar a API para implantar a extensão do Azure Defender é com o exemplo JSON da **Coleção carteiro** fornecida a partir dos exemplos de [instalação](https://aka.ms/kubernetes-extension-installation-examples)do Security Center .
- Para modificar o Carteiro Collection JSON, ou para implementar manualmente a extensão com a API REST, executar o seguinte comando PUT:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Em que:

    | Name            | Em   | Necessário | Tipo   | Description                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | ID da subscrição | caminho | Verdadeiro     | string | O seu Azure Arc permitiu o ID de subscrição do recurso Kubernetes |
    | Grupo de Recursos  | caminho | Verdadeiro     | string | Nome do grupo de recursos que contém o seu recurso Azure Arc habilitado kubernetes |
    | Nome do Cluster    | caminho | Verdadeiro     | string | Nome do seu Azure Arc habilitado recurso Kubernetes  |


    Para **autenticação,** o seu cabeçalho deve ter um token ao portador (como com outras APIs Azure). Para obter um símbolo portador, executar o seguinte comando:

    ```az account get-access-token --subscription <your-subscription-id>``` Utilize a seguinte estrutura para o corpo da sua mensagem:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    A descrição das propriedades é dada abaixo:

    | Propriedade | Descrição | 
    | -------- | ----------- |
    | logAnalytics.workspaceId | ID do espaço de trabalho do recurso Log Analytics |
    | logAnalytics.key         | Chave do recurso Log Analytics | 
    | auditLogPath             | **Opcional.** O caminho completo para os ficheiros de registo de auditoria. O valor predefinido é ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>Verificar a implementação

Para verificar se o seu cluster tem a extensão Azure Defender instalada nele, siga os passos de um dos separadores abaixo:

### <a name="azure-portal---security-center"></a>[**Portal Azure - Centro de Segurança**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Use recomendação do Centro de Segurança para verificar o estado da sua extensão

1. A partir da página de recomendações do Azure Security Center, abra o controlo de segurança **Enable Azure Defender.**

1. Selecione a recomendação chamada **Azure Arc habilitado para os clusters Kubernetes devem ter a extensão do Azure Defender instalada**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="A recomendação do Azure Security Center para a implementação da extensão do Azure Defender para o Arco Azure permitiu clusters kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Verifique se o cluster em que implementou a extensão está listado como **Healthy**.


### <a name="azure-portal---azure-arc"></a>[**Azure portal - Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Utilize as páginas Azure Arc para verificar o estado da sua extensão

1. A partir do portal Azure, abra **o Arco Azure.**
1. Na lista de infraestruturas, selecione **os clusters Kubernetes** e, em seguida, selecione o cluster específico.
1. Abra a página de extensões. As extensões do cluster estão listadas. Verifique a coluna **de estado de instalação** para confirmar que a extensão do Azure Defender foi instalada corretamente.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Página Azure Arc para verificar o estado de todas as extensões instaladas num cluster Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Para mais detalhes, selecione a extensão.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Detalhes completos de uma extensão do Arco Azure num cluster Kubernetes.":::


### <a name="azure-cli"></a>[**CLI do Azure**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Utilize o CLI do Azure para verificar se a extensão está implantada

1. Executar o seguinte comando em Azure CLI:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. Na resposta, procure "extensionType": "microsoft.azuredefender.kubernetes" e "installState": "Instalado".

    > [!NOTE]
    > Pode mostrar "installState": "Pendente" durante os primeiros minutos.
    
1. Se o estado mostrar **instalado,** executar o seguinte comando na sua máquina com o ficheiro apontado para o `kubeconfig` seu cluster para verificar se um pod chamado "azuredefender-XXXXX" está em estado de 'Execução':
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**API REST**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Utilize a API REST para verificar se a extensão é implantada

Para confirmar uma implementação bem sucedida, ou para validar o estado da sua extensão a qualquer momento:

1. Executar o seguinte comando GET:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. Na resposta, procure em "extensionType": "microsoft.azuredefender.kubernetes" para "installState": "InstallEd".

    > [!TIP]
    > Pode mostrar "installState": "Pendente" durante os primeiros minutos.
    
1. Se o estado mostrar **instalado,** executar o seguinte comando na sua máquina com o ficheiro apontado para o `kubeconfig` seu cluster para verificar se um pod chamado "azuredefender-XXXXX" está em estado de 'Execução':
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Simular alertas de segurança do Azure Defender para Kubernetes

Uma lista completa de alertas suportados está disponível na [tabela de referência de todos os alertas de segurança no Centro de Segurança Azure.](alerts-reference.md#alerts-akscluster)

1. Para simular um alerta Azure Defender, executar o seguinte comando:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    A resposta esperada é "Nenhum recurso encontrado".

    Dentro de 30 minutos, o Azure Defender detetará esta atividade e desencadeará um alerta de segurança.

1. No portal Azure, abra a página de alertas de segurança do Azure Security Center e procure o alerta sobre o recurso relevante:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Alerta de amostra do Azure Defender para Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Remoção da extensão do Azure Defender

Pode remover a extensão utilizando o portal Azure, Azure CLI ou REST API, conforme explicado nos separadores abaixo.

### <a name="azure-portal---arc"></a>[**Azure portal - Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Use o portal Azure para remover a extensão

1. Do portal Azure, abra o Arco Azure.
1. Na lista de infraestruturas, selecione **os clusters Kubernetes** e, em seguida, selecione o cluster específico.
1. Abra a página de extensões. As extensões do cluster estão listadas.
1. Selecione o cluster e selecione **Desinstalar.**

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Remover uma extensão do seu arco permitiu o cluster Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**CLI do Azure**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Utilize o CLI do Azure para remover a extensão do Azure Defender

1. Remova o Defender Azure para extensão de Arco de Kubernetes com os seguintes comandos:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    A remoção da extensão pode demorar alguns minutos. Recomendamos que espere antes de tentar verificar se foi um sucesso.

1. Para verificar se a extensão foi removida com sucesso, execute os seguintes comandos:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Não deve haver atraso no recurso de extensão ser eliminado do Azure Resource Manager. Depois disso, valide que não existem cápsulas chamadas "azuredefender-XXXXX" no cluster executando o seguinte comando com o ficheiro apontado para o `kubeconfig` seu cluster: 

    ```console
    kubectl get pods -n azuredefender
    ```

    Pode levar alguns minutos para as cápsulas serem apagadas.

### <a name="rest-api"></a>[**API REST**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Utilize a API REST para remover a extensão do Azure Defender 

Para remover a extensão utilizando a API REST, executar o seguinte comando DELETE:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | Em   | Necessário | Tipo   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| ID da subscrição | caminho | Verdadeiro     | string | O seu Arco permitiu o ID de subscrição do cluster Kubernetes |
| Grupo de Recursos  | caminho | Verdadeiro     | string | O seu Arco permitiu o grupo de recursos do cluster Kubernetes  |
| Nome do Cluster    | caminho | Verdadeiro     | string | O seu Arco permitiu o nome do cluster Kubernetes            |

Para **autenticação,** o seu cabeçalho deve ter um token ao portador (como com outras APIs Azure). Para obter um símbolo portador, executar o seguinte comando:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

O pedido pode demorar vários minutos a ser concluído.

---

## <a name="next-steps"></a>Passos seguintes

Esta página explicou como implementar a extensão do Azure Defender para o Arco Azure permitiu que os clusters de Kubernetes. Saiba mais sobre as funcionalidades de segurança do Azure Defender e do Azure Security Center nas seguintes páginas:

- [Segurança do contentor no Centro de Segurança](container-security.md)
- [Introdução ao Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Proteger cargas de trabalho do Kubernetes](kubernetes-workload-protections.md)
