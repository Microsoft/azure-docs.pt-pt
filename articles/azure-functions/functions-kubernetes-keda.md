---
title: Azure Functions em kubernetes com KEDA
description: Entenda como executar o Azure Functions no kubernetes na nuvem ou no local usando o KEDA, o dimensionamento automático controlado por evento baseado em kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096101"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions em kubernetes com KEDA

O tempo de execução de Azure Functions fornece flexibilidade na Hospedagem de onde e como você deseja.  [Keda](https://github.com/kedacore/kore) (Autoescala orientada a eventos baseados em kubernetes) diretamente com o tempo de execução Azure Functions e ferramentas para fornecer escala controlada por evento em kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Como funcionam as funções baseadas em kubernetes

O serviço de Azure Functions é composto por dois componentes principais: um tempo de execução e um controlador de escala.  O tempo de execução do Functions é executado e executa seu código.  O tempo de execução inclui a lógica sobre como disparar, registrar e gerenciar execuções de função.  O outro componente é um controlador de escala.  O controlador de escala monitora a taxa de eventos que estão direcionando sua função e dimensiona de forma proativa o número de instâncias que executam seu aplicativo.  Para saber mais, consulte [Azure Functions escala e hospedagem](functions-scale.md).

As funções baseadas em kubernetes fornecem o tempo de execução de funções em um [contêiner](functions-create-function-linux-custom-image.md) do Docker com dimensionamento controlado por eventos por meio de Keda.  KEDA pode reduzir verticalmente para 0 instâncias (quando nenhum evento está ocorrendo) e até *n* instâncias. Ele faz isso expondo métricas personalizadas para o kubernetes AutoScaler (dimensionamento de escala horizontal).  O uso de contêineres de funções com KEDA torna possível replicar recursos de função sem servidor em qualquer cluster kubernetes.  Essas funções também podem ser implantadas usando o recurso de [nós virtuais do AKS (serviços Kubernetess do Azure)](../aks/virtual-nodes-cli.md) para a infraestrutura sem servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gerenciando KEDA e funções no kubernetes

Para executar funções em seu cluster do kubernetes, você deve instalar o componente KEDA. Você pode instalar esse componente usando [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Instalando com o Azure Functions Core Tools

Por padrão, as ferramentas principais instalam os componentes KEDA e Osiris, que dão suporte ao dimensionamento de HTTP e orientado a eventos, respectivamente.  A instalação usa `kubectl` em execução no contexto atual.

Instale o KEDA no cluster executando o seguinte comando de instalação:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Implantando um aplicativo de funções no kubernetes

Você pode implantar qualquer aplicativo de funções em um cluster kubernetes que executa o KEDA.  Como suas funções são executadas em um contêiner do Docker, seu projeto `Dockerfile`precisa de um.  Se ele ainda não tiver um, você poderá adicionar um Dockerfile executando o seguinte comando na raiz do seu projeto do Functions:

```cli
func init --docker-only
```

Para criar uma imagem e implantar suas funções no kubernetes, execute o seguinte comando:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Substitua `<name-of-function-deployment>` pelo nome do seu aplicativo de funções.

Isso cria um recurso `Deployment` kubernetes, um `ScaledObject` recurso, e `Secrets`, que inclui as variáveis de ambiente importadas do seu `local.settings.json` arquivo.

## <a name="removing-a-function-app-from-kubernetes"></a>Removendo um aplicativo de funções do kubernetes

Depois de implantar, você pode remover uma função removendo o `Deployment`associado `ScaledObject`,, `Secrets` um criado.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalando o KEDA do kubernetes

Você pode executar o seguinte comando de ferramentas principais para remover o KEDA de um cluster kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Gatilhos com suporte no KEDA

O KEDA está atualmente em beta com suporte para os seguintes gatilhos de função do Azure:

* [Filas do armazenamento do Azure](functions-bindings-storage-queue.md)
* [Filas do barramento de serviço do Azure](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações, consulte os seguintes recursos:

* [Criar uma função usando uma imagem personalizada](functions-create-function-linux-custom-image.md)
* [Criar código e testar as Funções do Azure localmente](functions-develop-local.md)
* [Como funciona o plano de consumo da função do Azure](functions-scale.md)