---
title: Funções Azure em Kubernetes com KEDA
description: Compreenda como executar funções Azure em Kubernetes na nuvem ou no local usando KEDA, evento baseado em Kubernetes impulsionar autoscalagem.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301680"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funções Azure em Kubernetes com KEDA

O tempo de funcionamento das Funções Azure proporciona flexibilidade no alojamento onde e como quiser.  [Keda](https://keda.sh) (Autoscaling baseado em Eventos Kubernetes) combina perfeitamente com o tempo de funcionamento das Funções Azure e ferramentas para fornecer escala orientada para eventos em Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Como funcionam as funções baseadas em Kubernetes

O serviço Funções Azure é composto por dois componentes-chave: um tempo de funcionamento e um controlador de escala.  O tempo de execução das Funções executa e executa o seu código.  O tempo de execução inclui lógica sobre como desencadear, registar e gerir execuções de funções.  O tempo de funcionamento das Funções Azure pode ser executado *em qualquer lugar.*  O outro componente é um controlador de escala.  O controlador de escala monitoriza a taxa de eventos que estão a visar a sua função e escala proativamente o número de casos que executam a sua aplicação.  Para saber mais, consulte a escala e o alojamento das [Funções Azure.](functions-scale.md)

As Funções baseadas em Kubernetes fornecem o tempo de funcionamento das funções num [recipiente Docker](functions-create-function-linux-custom-image.md) com escalação orientada por eventos através da KEDA.  Keda pode escalar em 0 instâncias (quando não há eventos ocorridas) e fora em instâncias *n.* Fá-lo expondo métricas personalizadas para o autoscaler Kubernetes (Horizontal Pod Autoscaler).  A utilização de recipientes de funções com KEDA permite replicar as capacidades de função sem servidores em qualquer cluster Kubernetes.  Estas funções também podem ser implementadas utilizando a funcionalidade de [nós virtuais azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) para infraestruturas sem servidores.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestão da KEDA e funções em Kubernetes

Para executar funções no seu cluster Kubernetes, tem de instalar o componente KEDA. Pode instalar este componente utilizando [ferramentas nucleares de funções Azure](functions-run-local.md).

### <a name="installing-with-helm"></a>Instalação com Leme

Existem várias formas de instalar keda em qualquer cluster Kubernetes, incluindo Helm.  As opções de implementação estão documentadas no site da [KEDA](https://keda.sh/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Implementação de uma aplicação de função para kubernetes

Pode implementar qualquer aplicação de função para um cluster Kubernetes que executa a KEDA.  Uma vez que as suas funções funcionam num contentor do Docker, o seu projeto precisa de uma `Dockerfile`.  Se ainda não tiver um, pode adicionar um Dockerfile executando o seguinte comando na raiz do seu projeto Funções:

```cli
func init --docker-only
```

Para construir uma imagem e implementar as suas funções para Kubernetes, execute o seguinte comando:

> [!NOTE]
> As Ferramentas Core alavancarão o DOCKER CLI para construir e publicar a imagem. Certifique-se de que já instalou o estivador e está ligado à sua conta com `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Substitua `<name-of-function-deployment>` pelo nome da sua aplicação de funções.

Isto cria um recurso `Deployment` Kubernetes, um recurso `ScaledObject` e `Secrets`, que inclui variáveis ambientais importadas do seu ficheiro `local.settings.json`.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Implementação de uma aplicação de função a partir de um registo privado

O fluxo acima funciona também para os registos privados.  Se estiver a retirar a sua imagem de contentor de um registo privado, inclua a bandeira `--pull-secret` que faz referência ao segredo kubernetes que detém as credenciais de registo privado ao executar `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Remover uma aplicação de função da Kubernetes

Após a implantação, pode remover uma função removendo o `Deployment`associado , `ScaledObject`, um `Secrets` criado.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalação keda da Kubernetes

As medidas para desinstalar a KEDA estão documentadas [no site da KEDA](https://keda.sh/deploy/).

## <a name="supported-triggers-in-keda"></a>Gatilhos suportados na KEDA

A KEDA tem suporte para os seguintes gatilhos da Função Azure:

* [Filas de armazenamento azure](functions-bindings-storage-queue.md)
* [Filas de ônibus de serviço azure](functions-bindings-service-bus.md)
* [Evento Azure / Hubs IoT](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Fila RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>SUPORTE HTTP Trigger

Pode utilizar funções Azure que expõem os gatilhos HTTP, mas a KEDA não as gere diretamente.  Pode alavancar o gatilho de prometheus KEDA para [escalar as funções HTTP Azure de 1 a *n* casos](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Criar uma função usando uma imagem personalizada](functions-create-function-linux-custom-image.md)
* [Criar código e testar as Funções do Azure localmente](functions-develop-local.md)
* [Como funciona o plano de consumo de funções Azure](functions-scale.md)