---
title: PRETERIDO Monitorar o cluster kubernetes do Azure – gerenciamento de operações
description: Monitorando o cluster kubernetes no serviço de contêiner do Azure usando Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3cb500d2f00d6657420d7f294a7318b339e1f81e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271061"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>PRETERIDO Monitorar um cluster do serviço de contêiner do Azure com o Log Analytics

> [!TIP]
> Para obter a versão atualizada deste artigo que usa o serviço kubernetes do Azure, consulte [Azure monitor para contêineres](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial pressupõe que você [criou um cluster kubernetes usando o serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Ele também pressupõe que você tenha as ferramentas `az` CLI do Azure e `kubectl` instaladas.

Você pode testar se tem a ferramenta de `az` instalada executando:

```console
$ az --version
```

Se você não tiver a ferramenta de `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).
Como alternativa, você pode usar [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que tem a CLI do Azure `az` e as ferramentas de `kubectl` já instaladas para você.

Você pode testar se tem a ferramenta de `kubectl` instalada executando:

```console
$ kubectl version
```

Se você não tiver `kubectl` instalado, poderá executar:
```console
$ az acs kubernetes install-cli
```

Para testar se você tem chaves kubernetes instaladas em sua ferramenta kubectl, você pode executar:
```console
$ kubectl get nodes
```

Se o comando acima tiver erros de saída, você precisará instalar as chaves de cluster kubernetes em sua ferramenta kubectl. Você pode fazer isso com o seguinte comando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorando contêineres com Log Analytics

Log Analytics é a solução de gerenciamento de ti baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e na nuvem. A solução de contêiner é uma solução no Log Analytics, que ajuda a exibir o inventário de contêiner, o desempenho e os logs em um único local. Você pode auditar, solucionar problemas de contêineres exibindo os logs no local centralizado e encontrar um excesso de consumo excessivo de contêiner em um host.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre a solução de contêiner, consulte a [solução de contêiner log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalando o Log Analytics no kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obter a ID e a chave do espaço de trabalho
Para que o agente de Log Analytics se comunique com o serviço, ele precisa ser configurado com uma ID de espaço de trabalho e uma chave de espaço de trabalho. Para obter a ID e a chave do espaço de trabalho, você precisa criar uma conta em <https://mms.microsoft.com>.
Siga as etapas para criar uma conta. Quando terminar de criar a conta, você poderá obter sua ID e a chave clicando na folha **log Analytics** , em seguida, no nome do seu espaço de trabalho. Em seguida, em **Configurações avançadas**, **fontes conectadas**e **servidores Linux**, você encontrará as informações necessárias, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalar o agente de Log Analytics usando um Daemonset
DaemonSets são usados pelo kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento.

Aqui está o [arquivo daemonset YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Salve-o em um arquivo chamado `oms-daemonset.yaml` e substitua os valores de espaço reservado para `WSID` e `KEY` com a ID e a chave do espaço de trabalho no arquivo.

Depois de adicionar a ID e a chave do espaço de trabalho à configuração do Daemonset, você poderá instalar o agente de Log Analytics no cluster com a ferramenta de linha de comando `kubectl`:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalando o agente de Log Analytics usando um segredo kubernetes
Para proteger sua ID do espaço de trabalho Log Analytics e a chave, você pode usar o segredo kubernetes como parte do arquivo YAML do Daemonset.

- Copie o script, o arquivo de modelo secreto e o arquivo Daemonset YAML (do [repositório](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) e verifique se eles estão no mesmo diretório.
  - script de geração de segredo-secret-gen.sh
  - modelo secreto - template.yaml do segredo
    - Arquivo daemonset YAML-omsagent-DS-segredos. YAML
- Execute o script. O script solicitará a ID do espaço de trabalho Log Analytics e a chave primária. Insira isso e o script criará um arquivo YAML secreto para que você possa executá-lo.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - Crie o pod dos segredos executando o seguinte: ```kubectl create -f omsagentsecret.yaml```

  - Para verificar, execute o seguinte:

  ```
  root@ubuntu16-13db:~# kubectl get secrets
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

  - Criar a sua omsagent daemon-set através da execução ```kubectl create -f omsagent-ds-secrets.yaml```

### <a name="conclusion"></a>Conclusão
Já está! Após alguns minutos, você deve ser capaz de ver os dados que fluem para seu painel de Log Analytics.
