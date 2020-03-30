---
title: (DEPRECIADO) Monitor Azure Kubernetes cluster - Gestão de Operações
description: Monitorização do cluster Kubernetes no Serviço de Contentores Azure usando log analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371159"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(DEPRECIADO) Monitorize um cluster de serviço de contentores Azure com Log Analytics

> [!TIP]
> Para a versão atualizada, este artigo que utiliza o Serviço Azure Kubernetes, consulte [o Monitor Azure para obter recipientes.](../../azure-monitor/insights/container-insights-overview.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough pressupõe que [criou um cluster Kubernetes usando](container-service-kubernetes-walkthrough.md)o Serviço de Contentores Azure .

Também assume que tem `az` o Azure `kubectl` cli e ferramentas instaladas.

Pode testar se tiver `az` a ferramenta instalada executando:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui.](https://github.com/azure/azure-cli#installation)
Em alternativa, pode utilizar [a Azure Cloud Shell,](https://docs.microsoft.com/azure/cloud-shell/overview)que tem o `az` Cli Azure e `kubectl` as ferramentas já instaladas para si.

Pode testar se tiver `kubectl` a ferramenta instalada executando:

```console
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

Para testar se tem chaves kubernetes instaladas na sua ferramenta kubectl pode executar:

```console
kubectl get nodes
```

Se os erros de comando acima forem eliminados, é necessário instalar as teclas de cluster kubernetes na sua ferramenta kubectl. Pode fazê-lo com o seguinte comando:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Recipientes de monitorização com Log Analytics

O Log Analytics é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e proteger as suas infraestruturas no local e na nuvem.A Solução de Contentores é uma solução no Log Analytics, que o ajuda a visualizar o inventário, desempenho e registos do contentor num único local. Você pode auditar, resolver contentores de problemas visualizando os troncos em localização centralizada, e encontrar recipiente supérno consumindo excesso em um hospedeiro.

![](media/container-service-monitoring-oms/image1.png)

Para mais informações sobre a Solução de Contentores, consulte o [Registo de Solução](../../azure-monitor/insights/containers.md)de Contentores Analytics .

## <a name="installing-log-analytics-on-kubernetes"></a>Instalação de Log Analytics em Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obtenha o seu ID e chave do espaço de trabalho
Para que o agente Log Analytics fale com o serviço, tem de ser configurado com um ID do espaço de trabalho e uma chave de espaço de trabalho. Para obter o ID do espaço de trabalho <https://mms.microsoft.com>e a chave, você precisa criar uma conta em .
Por favor, siga os passos para criar uma conta. Uma vez feito a criação da conta, pode obter o seu ID e chave clicando na lâmina **Log Analytics,** em seguida, o nome do seu espaço de trabalho. Em seguida, em **Definições Avançadas**, **Fontes Conectadas,** e depois **servidores Linux,** encontrará a informação de que necessita, como mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instale o agente Log Analytics utilizando um DaemonSet
DaemonSets são usados por Kubernetes para executar uma única instância de um recipiente em cada hospedeiro no cluster.
São perfeitos para agentes de monitorização.

Aqui está o [ficheiro DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Guarde-o para `oms-daemonset.yaml` um ficheiro nomeado e `WSID` `KEY` substitua os valores do suporte de lugar para e com o seu ID do espaço de trabalho e chave no ficheiro.

Depois de ter adicionado o ID do espaço de trabalho e a chave à configuração `kubectl` DaemonSet, pode instalar o agente Log Analytics no seu cluster com a ferramenta de linha de comando:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalação do agente Log Analytics utilizando um Kubernetes Secret
Para proteger o id e a chave do espaço de trabalho do Log Analytics, pode utilizar o Kubernetes Secret como parte do ficheiro DaemonSet YAML.

- Copie o script, o ficheiro do modelo secreto e o ficheiro DaemonSet YAML (do [repositório)](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)e certifique-se de que estão no mesmo diretório.
  - script gerador secreto - secret-gen.sh
  - modelo secreto - secret-template.yaml
    - Arquivo DaemonSet YAML - omsagent-ds-secrets.yaml
- Execute o script. O script irá pedir o ID do espaço de trabalho de Log Analytics e a chave primária. Insira isso e o script criará um ficheiro yaml secreto para que possa executá-lo.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Crie a cápsula de segredos executando o seguinte:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Para verificar, faça o seguinte:

  ```console
  kubectl get secrets
  ```

  ```output
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Crie o seu conjunto de daemon omsagent executando o seguinte:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Conclusão
Já está! Após alguns minutos, deverá ser capaz de ver os dados a fluir para o seu painel de log Analytics.
