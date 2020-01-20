---
title: PRETERIDO Monitorar o cluster kubernetes do Azure-Sysdig
description: Monitorando o cluster kubernetes no serviço de contêiner do Azure usando o Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3cb9c628993201553b8da1d1bd37b4705e0f23dc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271644"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>PRETERIDO Monitorar um cluster kubernetes do serviço de contêiner do Azure usando o Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial pressupõe que você [criou um cluster kubernetes usando o serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Ele também pressupõe que você tenha a CLI do Azure e as ferramentas do kubectl instaladas.

Você pode testar se tem a ferramenta de `az` instalada executando:

```console
$ az --version
```

Se você não tiver a ferramenta de `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).

Você pode testar se tem a ferramenta de `kubectl` instalada executando:

```console
$ kubectl version
```

Se você não tiver `kubectl` instalado, poderá executar:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig é um monitoramento externo como uma empresa de serviço que pode monitorar contêineres em seu cluster kubernetes em execução no Azure. O uso de Sysdig requer uma conta Sysdig ativa.
Você pode se inscrever para uma conta em seu [site](https://app.sysdigcloud.com).

Após ter iniciado sessão no site na nuvem do Sysdig, clique no seu nome de utilizador e deverá ver a sua "Chave de Acesso" na página. 

![Chave de API do Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalando os agentes do Sysdig no kubernetes
Para monitorar seus contêineres, o Sysdig executa um processo em cada computador usando um `DaemonSet`de kubernetes.
DaemonSets são objetos de API kubernetes que executam uma única instância de um contêiner por computador.
Eles são perfeitos para a instalação de ferramentas como o agente de monitoramento do Sysdig.

Para instalar o daemonset do Sysdig, você deve primeiro baixar [o modelo](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) do Sysdig. Salve esse arquivo como `sysdig-daemonset.yaml`.

No Linux e OS X, você pode executar:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

No PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Em seguida, edite esse arquivo para inserir sua chave de acesso, que você obteve da sua conta do Sysdig.

Por fim, crie o Daemonset:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Exibir seu monitoramento
Depois de instalado e em execução, os agentes devem bombear os dados de volta para o Sysdig.  Volte para o [painel do sysdig](https://app.sysdigcloud.com) e você deverá ver informações sobre seus contêineres.

Você também pode instalar painéis específicos do kubernetes por meio do [Assistente de novo painel](https://app.sysdigcloud.com/#/dashboards/new).
