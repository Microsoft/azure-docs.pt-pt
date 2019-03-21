---
title: (PRETERIDO) Monitorizar o cluster de Kubernetes do Azure - gestão de operações
description: Monitorização de cluster de Kubernetes no Azure Container Service com o Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0d9f13003a9b81085fda9635fc8b07566a1c0c66
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107530"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(PRETERIDO) Monitorizar um cluster do Azure Container Service com o Log Analytics

> [!TIP]
> Para a versão atualizada, o que este artigo utiliza o serviço Kubernetes do Azure, veja [Monitor do Azure para contentores](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo pressupõe que tenha [criado um cluster de Kubernetes com o Azure Container Service](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem o `az` cli do Azure e `kubectl` ferramentas instaladas.

Pode testar se tiver o `az` ferramenta instalada executando:

```console
$ az --version
```

Se não tiver o `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).
Em alternativa, pode utilizar [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que tem o `az` da cli do Azure e `kubectl` ferramentas já instaladas para.

Pode testar se tiver o `kubectl` ferramenta instalada executando:

```console
$ kubectl version
```

Se não tiver `kubectl` instalado, pode executar:
```console
$ az acs kubernetes install-cli
```

Para testar se tiver as chaves de kubernetes instaladas na sua ferramenta de kubectl que pode executar:
```console
$ kubectl get nodes
```

Se os erros de comando acima horizontalmente, tem de instalar as chaves de cluster do kubernetes em sua ferramenta de kubectl. Pode fazê-lo com o seguinte comando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorização de contentores com o Log Analytics

Log Analytics é uma solução da Microsoft com base na cloud IT management que ajuda a gerenciar e proteger no local e a infraestrutura na cloud. Solução de contentor é uma solução no Log Analytics, que lhe permite ver o inventário de contentor, desempenho e registos numa única localização. Pode de auditoria, resolver problemas relacionados com contentores visualizando os registos de localização centralizada e encontrar ruidosos consumir em excesso contentor num anfitrião.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre solução de contentor, consulte a [contentor solução Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalar o Log Analytics no Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obter o ID de área de trabalho e a chave
Para o Log Analytics agente para comunicar com o serviço tem de ser configurado com um ID de área de trabalho e uma chave de área de trabalho. Para obter o ID de área de trabalho e a chave tem de criar uma conta no <https://mms.microsoft.com>.
Siga os passos para criar uma conta. Assim que estiver concluído a criação da conta, pode obter o ID e chave ao clicar no **do Log Analytics** painel, em seguida, o nome da sua área de trabalho. Em seguida, em **definições avançadas**, **origens ligadas**e, em seguida **servidores Linux**, encontrará as informações necessárias, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalar o agente de Log Analytics, utilizando um DaemonSet
DaemonSets são utilizados pelo Kubernetes para executar uma única instância de um contentor em cada anfitrião no cluster.
Eles são perfeitos para a execução de agentes de monitorização.

Aqui está o [ficheiro DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Guarde-o num ficheiro denominado `oms-daemonset.yaml` e substitua os valores de marcador de posição para `WSID` e `KEY` com o ID de área de trabalho e a chave no ficheiro.

Depois de adicionar o ID de área de trabalho e a chave para a configuração de DaemonSet, pode instalar o agente Log Analytics no seu cluster com o `kubectl` ferramenta da linha de comandos:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalação do agente de Log Analytics com o segredo do Kubernetes
Para proteger o seu ID de área de trabalho do Log Analytics e a chave pode utilizar o segredo do Kubernetes como parte do ficheiro DaemonSet YAML.

- Copie o script, ficheiro de modelo secreta e o ficheiro DaemonSet YAML (partir [repositório](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) e certifique-se de que estão no mesmo diretório.
  - Gerar script - gen.sh segredo do segredo
  - modelo secreto - template.yaml do segredo
    - Ficheiro DaemonSet YAML - omsagent-ds-secrets.yaml
- Execute o script. O script solicitará o ID de área de trabalho do Log Analytics e a chave primária. Inserir que e o script irá criar um ficheiro yaml secreta, para que pode executá-lo.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - Crie o pod segredos ao executar o seguinte: ``` kubectl create -f omsagentsecret.yaml ```

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

  - Criar a sua omsagent daemon-set através da execução ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Conclusão
Já está! Após alguns minutos, deve ser capaz de ver o fluxo de dados para o dashboard do Log Analytics.
