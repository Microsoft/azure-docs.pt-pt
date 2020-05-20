---
title: Ligue um cluster Kubernetes ativado por Arco Azure (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Ligue um cluster Kubernetes ativado por Arco Azure com o Arco Azure
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: 9b37ad264dc8a8a6c653c25ddf6ac0fcb4065f9b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680814"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Ligue um cluster Kubernetes ativado por Arco Azure (Pré-visualização)

Ligue um aglomerado de Kubernetes ao Arco Azure. 

## <a name="before-you-begin"></a>Antes de começar

Verifique se tem os seguintes requisitos prontos:

* Um aglomerado de Kubernetes que está em funcionamento
* Vai precisar de acesso com kubeconfig e acesso a administração de clusters. 
* O utilizador ou o diretor de serviço utilizado `az login` e `az connectedk8s connect` os comandos devem ter as permissões 'Read' e 'Write' no tipo de recurso 'Microsoft.Kubernetes/connectedclusters'.
* Versão mais recente das extensões de configuração *conectadas k8s* e *k8s*

## <a name="supported-regions"></a>Regiões suportadas

* E.U.A. Leste
* Europa ocidental

## <a name="network-requirements"></a>Requisitos de rede

Os agentes do Arco Azure exigem que funcionem os seguintes protocolos/portas/URLs de saída.

* TCP na porta 443 -->`https://:443`
* TCP no porto 9418 -->`git://:9418`

| Ponto final (DNS)                                                                                               | Descrição                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Necessário para que o agente se ligue ao Azure e registe o cluster                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Ponto final do plano de dados para o agente empurrar o estado e obter informações de configuração                                      |
| `https://docker.io`                                                                                            | Necessário para puxar imagens de contentores                                                                                         |
| `https://github.com`git://github.com                                                                         | Exemplo GitOps repos são hospedados no GitHub. O agente de configuração requer conectividade com qualquer ponto final git que especifique. |
| `https://login.microsoftonline.com`                                                                            | Necessário para recolher e atualizar fichas do Gestor de Recursos Azure                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Obrigado a puxar imagens de contentores para agentes do Azure Arc                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registar os dois fornecedores da Azure Arc habilitado para a Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

O registo é um processo assíncrono. As inscrições podem demorar aproximadamente 10 minutos. Pode monitorizar o processo de registo com os seguintes comandos:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Instalar extensões Azure CLI

Instale a `connectedk8s` extensão, que ajuda a ligar os clusters Kubernetes ao Azure:

```console
az extension add --name connectedk8s
```

Instale a `k8sconfiguration` extensão:

```console
az extension add --name k8sconfiguration
```

Executar os seguintes comandos para atualizar as extensões para as versões mais recentes.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos

Utilize um grupo de recursos para armazenar metadados para o seu cluster.

Primeiro, crie um grupo de recursos para manter o recurso de cluster conectado.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Saída:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Ligar um cluster

Em seguida, ligaremos o nosso cluster Kubernetes ao Azure. O fluxo de trabalho para `az connectedk8s connect` é o seguinte:

1. Verifique a conectividade com o seu cluster Kubernetes: via `KUBECONFIG` `~/.kube/config` , ou`--kube-config`
1. Implante agentes Azure Arc para Kubernetes usando o Leme 3, para o espaço de `azure-arc` nome

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verificar cluster conectado

Enumerar os seus clusters conectados:

```console
az connectedk8s list -g AzureArcTest
```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

O Azure Arc permitiu que a Kubernetes implantasse alguns operadores no espaço de `azure-arc` nome. Pode ver estas implementações e cápsulas aqui:

```console
kubectl -n azure-arc get deploy,po
```

**Saída:**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Agentes do Azure Arc para kubernetes

O Azure Arc habilitado kubernetes é composto por alguns agentes (operadores) que funcionam no seu cluster implantado para o espaço de `azure-arc` nome.

* `deploy/config-agent`: observa o cluster conectado para os recursos de configuração do controlo de fonte aplicados no estado de conformidade do cluster e atualiza
* `deploy/controller-manager`: é operador de operadores e orquestra interações entre componentes do Arco Azure

## <a name="delete-a-connected-cluster"></a>Eliminar um cluster conectado

Pode eliminar um `Microsoft.Kubernetes/connectedcluster` recurso utilizando o portal Azure CLI ou Azure.

O comando Azure CLI `az connectedk8s delete` remove o recurso em `Microsoft.Kubernetes/connectedCluster` Azure. O Azure CLI elimina todos os recursos associados `sourcecontrolconfiguration` no Azure. O Azure CLI utiliza o desinstalação do leme para remover os agentes do cluster.

O portal Azure elimina o `Microsoft.Kubernetes/connectedcluster` recurso em Azure e elimina quaisquer recursos associados `sourcecontrolconfiguration` em Azure.

Para remover os agentes no aglomerado é necessário executar `az connectedk8s delete` ou `helm uninstall azurearcfork8s` .

## <a name="next-steps"></a>Passos seguintes

* [Use GitOps num cluster conectado](./use-gitops-connected-cluster.md)
* [Use a Política Azure para governar a configuração do cluster](./use-azure-policy.md)