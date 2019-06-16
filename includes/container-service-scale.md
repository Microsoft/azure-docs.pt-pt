---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a4af53e035929a44f74a95b8e9897cb1dc0c6d8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814923"
---
[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

Depois de [implementar um cluster do Azure Container Service](../articles/container-service/dcos-swarm/container-service-deployment.md), poderá ter de alterar o número de nós de agente. Por exemplo, poderá precisar de mais agentes, de modo a poder executar mais aplicações de contentores ou instâncias. 

Pode alterar o número de nós de agentes num cluster DC/OS, Docker Swarm ou Kubernetes com o portal do Azure ou a CLI do Azure. 

## <a name="scale-with-the-azure-portal"></a>Dimensionar com o portal do Azure

1. No [portal do Azure](https://portal.azure.com), procure **Serviços de contentores** e clique no serviço de contentores que pretende modificar.
2. No painel **Serviço de contentores**, clique em **Agentes**.
3. Em **Contagem de VMs**, introduza o número pretendido de nós de agentes.

    ![Dimensionar um agrupamento no portal](./media/container-service-scale/container-service-scale-portal.png)

4. Para guardar a configuração, clique em **Guardar**.

## <a name="scale-with-the-azure-cli"></a>Dimensionamento com a CLI do Azure

[Instalar a CLI do Azure](/cli/azure/install-azure-cli) e inicie sessão na conta do Azure com `az login`.

### <a name="see-the-current-agent-count"></a>Ver a contagem de nós atuais
Para ver o número de agentes atualmente no cluster, execute o comando `az acs show`. É apresentada configuração do cluster. Por exemplo, o comando seguinte mostra a configuração do serviço de contentores com o nome `containerservice-myACSName` no grupo de recursos `myResourceGroup`:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

O comando devolve o número de agentes no valor `Count`, em `AgentPoolProfiles`.

### <a name="use-the-az-acs-scale-command"></a>Utilizar o comando az acs scale
Para alterar o número de nós de agentes, execute o comando `az acs scale` e forneça o **grupo de recursos**, o **nome do serviço de contentores** e a **contagem de agentes nova** pretendida. Ao utilizar um número mais pequeno ou maior, pode reduzir ou aumentar de forma vertical, respetivamente.

Por exemplo, para alterar o número de agentes no cluster anterior para dez, escreva o comando seguinte:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

A CLI do Azure devolve uma cadeia de caracteres do JSON que representa a nova configuração do serviço de contentor, incluindo a contagem de agentes nova.

Para obter mais opções de comandos, execute `az acs scale --help`.

## <a name="scaling-considerations"></a>Considerações sobre o dimensionamento

* O número de nós de agentes tem de ser entre 1 e 100, inclusive. 

* A quota de núcleos pode limitar o número de nós de agentes num cluster.

* As operações de dimensionamento de nós de agentes são aplicadas a conjuntos de dimensionamento de máquinas virtuais do Azure que contenham o conjunto de agentes. Num cluster do DC/OS, só os nós de agentes no conjunto privado são dimensionados pelas operações mostradas neste artigo.

* Consoante o orchestrator que implementar no seu cluster, pode dimensionar separadamente o número de instâncias de um contentor em execução no cluster. Por exemplo, num cluster do DC/OS, utilize a [IU do Marathon](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) para alterar o número de instâncias de uma aplicação de contentores.


## <a name="next-steps"></a>Passos Seguintes
* Ver [mais exemplos](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) da utilização de comandos da CLI do Azure com o Azure Container Service.
* Saiba mais sobre os [conjuntos de agentes do DC/SO](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) no Azure Container Service.

