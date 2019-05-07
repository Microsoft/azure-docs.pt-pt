---
title: Funções do Azure no Kubernetes com o KEDA
description: Compreender como executar as funções do Azure no Kubernetes na cloud ou no local com KEDA, o dimensionamento automático com base em Kubernetes o condicionada por eventos.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure funções, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077624"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funções do Azure no Kubernetes com o KEDA

O runtime das funções do Azure proporciona flexibilidade na hospedagem onde e como desejar.  [KEDA](https://github.com/kedacore/kore) (Kubernetes eventos controlados por dimensionamento automático baseado em) pares de forma totalmente integrada com o runtime das funções do Azure e as ferramentas para fornecer dimensionamento condicionada por eventos no Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Trabalho de funções como baseado no Kubernetes

O serviço de funções do Azure é constituído por dois componentes principais: um tempo de execução e um controlador de dimensionamento.  O runtime das funções é executado e executa o seu código.  O tempo de execução inclui uma lógica em como acionar, inicie sessão e gerir execuções de função.  O outro componente é um controlador de dimensionamento.  O controlador de escala monitoriza a taxa de eventos que visam a sua função e dimensiona proativamente o número de instâncias a executar a sua aplicação.  Para obter mais informações, consulte [as funções do Azure dimensionamento e alojamento](functions-scale.md).

Funções baseadas em Kubernetes fornece o runtime das funções numa [contentor do Docker](functions-create-function-linux-custom-image.md) com condicionada por eventos dimensionamento através de KEDA.  KEDA pode reduzir verticalmente para 0 instâncias (quando não existem eventos estão a ocorrer) e até *n* instâncias. Ele faz isso expondo métricas personalizadas para o dimensionamento automático do Kubernetes (dimensionamento Horizontal automático Pod).  Através de contentores de funções com KEDA torna possível replicar as capacidades de função sem servidores em qualquer cluster de Kubernetes.  Essas funções também podem ser implementadas com [nós virtuais de serviços de Kubernetes do Azure (AKS)](../aks/virtual-nodes-cli.md) funcionalidade para a infraestrutura sem servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gerir KEDA e funções no Kubernetes

Para executar funções no seu cluster de Kubernetes, tem de instalar o componente KEDA. Pode instalar este componente usando [ferramentas de núcleo de funções do Azure](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>A instalar as ferramentas de núcleo com as funções do Azure

Por predefinição, as ferramentas de núcleo instala os componentes KEDA e Osiris, que suportam condicionada por eventos e o dimensionamento de HTTP, respectivamente.  A instalação utiliza `kubectl` em execução no contexto atual.

Instale KEDA no seu cluster, executando o seguinte comando de instalação:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Implementar uma aplicação de funções para o Kubernetes

Pode implementar qualquer aplicação de funções para um cluster de Kubernetes com KEDA.  Uma vez que a execução das funções num contentor do Docker, o projeto precise um `Dockerfile`.  Se ainda não tiver uma, pode adicionar um Dockerfile, executando o seguinte comando na raiz do seu projeto de funções:

```cli
func init --docker-only
```

Para criar uma imagem e implementar as suas funções para o Kubernetes, execute o seguinte comando:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Substitua `<name-of-function-deployment>` com o nome da sua aplicação de função.

Esta ação cria um Kubernetes `Deployment` recurso, uma `ScaledObject` recurso, e `Secrets`, que inclui as variáveis de ambiente importadas a partir do seu `local.settings.json` ficheiro.

## <a name="removing-a-function-app-from-kubernetes"></a>Remover uma aplicação de funções do Kubernetes

Depois de implementar pode remover uma função, removendo associada `Deployment`, `ScaledObject`, um `Secrets` criado.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalar KEDA do Kubernetes

Pode executar o seguinte comando de ferramentas de núcleo para remover KEDA de um cluster de Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Acionadores suportados no KEDA

KEDA está atualmente na versão beta com suporte para os acionadores de função do Azure seguintes:

* [Filas de armazenamento do Azure](functions-bindings-storage-queue.md)
* [Filas do Azure Service Bus](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações, consulte os seguintes recursos:

* [Criar uma função com uma imagem personalizada](functions-create-function-linux-custom-image.md)
* [Criar código e testar as Funções do Azure localmente](functions-develop-local.md)
* [Como funciona o plano de consumo de função do Azure](functions-scale.md)