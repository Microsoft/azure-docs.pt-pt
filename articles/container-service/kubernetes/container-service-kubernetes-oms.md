---
title: (PRECADO) Monitor Azure Kubernetes cluster - Gestão de Operações
description: Monitorar o cluster Kubernetes no Serviço de Contentores Azure utilizando o Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371159"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(PRECADO) Monitorize um cluster de serviço de contentores Azure com Log Analytics

> [!TIP]
> Para a versão atualizada, este artigo que utiliza o Serviço Azure Kubernetes, consulte [o Azure Monitor para recipientes](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough assume que [criou um cluster Kubernetes usando o Serviço de Contentores Azure.](container-service-kubernetes-walkthrough.md)

Também assume que tem o `az` Cli Azure e `kubectl` as ferramentas instaladas.

Pode testar se a ferramenta está `az` instalada em execução:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).
Em alternativa, pode utilizar [o Azure Cloud Shell,](https://docs.microsoft.com/azure/cloud-shell/overview)que tem o `az` CLI Azure e `kubectl` ferramentas já instaladas para si.

Pode testar se a ferramenta está `kubectl` instalada em execução:

```console
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

Para testar se tem as teclas kubernetes instaladas na sua ferramenta kubectl, pode executar:

```console
kubectl get nodes
```

Se o comando acima se apagar, tem de instalar as teclas de cluster kubernetes na sua ferramenta kubectl. Pode fazê-lo com o seguinte comando:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Recipientes de monitorização com Log Analytics

Log Analytics é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e proteger as suas infraestruturas de nuvem e instalações.A Solução de Contentores é uma solução no Log Analytics, que o ajuda a visualizar o inventário, desempenho e registos do contentor num único local. Você pode auditar, resolver os recipientes, visualizando os registos em localização centralizada, e encontrar um recipiente em excesso de consumo ruidoso num hospedeiro.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre a Solução de Contentores, consulte o Registo de [Solução de Contentores Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalação do Log Analytics em Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obtenha o seu ID e chave do seu espaço de trabalho
Para que o agente Log Analytics fale com o serviço, precisa de ser configurado com um ID de espaço de trabalho e uma chave de espaço de trabalho. Para obter o ID do espaço de trabalho e a chave, você precisa criar uma conta em <https://mms.microsoft.com> .
Por favor, siga os passos para criar uma conta. Uma vez terminada a criação da conta, pode obter o seu ID e a sua tecla clicando na lâmina **Log Analytics** e, em seguida, o nome do seu espaço de trabalho. Em seguida, em **Definições Avançadas**, **Fontes Conectadas**, e, em seguida, **Servidores Linux**, encontrará as informações de que necessita, como mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instale o agente Log Analytics utilizando um DaemonSet
Os DaemonSets são utilizados pela Kubernetes para executar uma única instância de um recipiente em cada hospedeiro do cluster.
São perfeitos para agentes de monitorização.

Aqui está o [ficheiro DAemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Guarde-o para um ficheiro nomeado `oms-daemonset.yaml` e substitua os valores do suporte de lugar `WSID` para e com o seu `KEY` ID do espaço de trabalho e a chave no ficheiro.

Uma vez adicionado o seu ID do espaço de trabalho e a chave para a configuração DoemonSet, pode instalar o agente Log Analytics no seu cluster com a `kubectl` ferramenta da linha de comando:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalação do agente Log Analytics utilizando um Segredo de Kubernetes
Para proteger o seu ID e chave do espaço de trabalho Log Analytics, pode utilizar a Kubernetes Secret como parte do ficheiro YAML do DaemonSet.

- Copie o script, o ficheiro de modelo secreto e o ficheiro YAML do DaemonSet (do [repositório)](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)e certifique-se de que estão no mesmo diretório.
  - script gerador secreto - secret-gen.sh
  - modelo secreto - modelo secreto.yaml
    - Ficheiro YAML da DaemonSet - omsagent-ds-secrets.yaml
- Execute o script. O script pedirá o ID do Espaço de Trabalho do Log Analytics e a Chave Primária. Insira isso e o script criará um ficheiro yaml secreto para que possa executá-lo.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Crie o casulo de segredos executando o seguinte:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Para verificar, execute o seguinte:

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
Já está! Após alguns minutos, deverá ser possível ver os dados a fluir para o seu painel de registos.
