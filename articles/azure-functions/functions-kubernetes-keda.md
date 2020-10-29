---
title: Funções Azure em Kubernetes com KEDA
description: Entenda como executar as Funções Azure em Kubernetes na nuvem ou no local usando KEDA, evento baseado em Kubernetes autoscaling.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 525635ef40437fe308c52e2d5aba2c97ed8f20e7
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927537"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funções Azure em Kubernetes com KEDA

O tempo de funcionamento das Funções Azure proporciona flexibilidade no alojamento onde e como deseja.  [A KEDA](https://keda.sh) (Autoscaling driven de eventos baseado em Kubernetes) emparelha perfeitamente com o tempo de execução e ferramentas das Funções Azure para fornecer escala orientada para eventos em Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Como funcionam as funções baseadas em Kubernetes

O serviço Azure Functions é composto por dois componentes-chave: um tempo de funcionamento e um controlador de escala.  O tempo de execução das Funções funciona e executa o seu código.  O tempo de execução inclui lógica sobre como desencadear, registar e gerir execuções de funções.  O tempo de execução das Funções Azure pode ser executado *em qualquer lugar* .  O outro componente é um controlador de escala.  O controlador de escala monitoriza a taxa de eventos que estão a direcionar a sua função e escala proativamente o número de casos que executam a sua aplicação.  Para saber mais, consulte [a escala e hospedagem de Funções Azure.](functions-scale.md)

As funções baseadas em Kubernetes proporcionam o tempo de funcionamento das funções num [recipiente Docker](functions-create-function-linux-custom-image.md) com escalas orientadas para o evento através da KEDA.  KEDA pode escalar em 0 casos (quando não ocorrerem eventos) e para *n* instâncias. Fá-lo expondo métricas personalizadas para o autoescalador Kubernetes (Horizontal Pod Autoscaler).  A utilização de recipientes de funções com KEDA permite replicar capacidades de função sem servidor em qualquer cluster Kubernetes.  Estas funções também podem ser implementadas usando a funcionalidade [de nós virtuais Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) para infraestruturas sem servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestão da KEDA e funções em Kubernetes

Para executar funções no seu cluster Kubernetes, tem de instalar o componente KEDA. Pode instalar este componente utilizando as [ferramentas principais do Azure Functions](functions-run-local.md).

### <a name="installing-with-helm"></a>Instalação com Leme

Existem várias formas de instalar KEDA em qualquer cluster Kubernetes, incluindo Helm.  As opções de implementação estão documentadas no site da [KEDA.](https://keda.sh/docs/1.4/deploy/)

## <a name="deploying-a-function-app-to-kubernetes"></a>Implementação de uma app de função para Kubernetes

Pode implementar qualquer aplicação de função num cluster Kubernetes que executa KEDA.  Uma vez que as suas funções funcionam num contentor Docker, o seu projeto precisa de `Dockerfile` um .  Se ainda não tiver um, pode adicionar um Dockerfile executando o seguinte comando na raiz do seu projeto Funções:

> [!NOTE]
> As Ferramentas Centrais criam automaticamente o Dockerfile para Funções Azure escritas em .NET, Nó, Python ou PowerShell. Para aplicações de funções escritas em Java, o Dockerfile deve ser criado manualmente. Utilize a lista de [imagens](https://github.com/Azure/azure-functions-docker) Azure Functions para encontrar a imagem correta para basear a Função Azure.

```cli
func init --docker-only
```

Para construir uma imagem e implementar as suas funções em Kubernetes, executar o seguinte comando:

> [!NOTE]
> As Ferramentas Core alavancarão o Estivador CLI para construir e publicar a imagem. Certifique-se de que já tem o Docker instalado e ligado à sua conta com `docker login` .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> `<name-of-function-deployment>`Substitua-o pelo nome da sua aplicação de função.

O comando de implantação executa uma série de ações:
1. O Dockerfile criado anteriormente é usado para construir uma imagem local para a aplicação de função.
2. A imagem local é marcada e empurrada para o registo do contentor onde o utilizador está registado.
3. Um manifesto é criado e aplicado ao cluster que define um recurso Kubernetes, `Deployment` um `ScaledObject` recurso, e `Secrets` , que inclui variáveis ambientais importadas do seu `local.settings.json` arquivo.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Implementação de uma aplicação de função a partir de um registo privado

O fluxo acima também funciona para registos privados.  Se estiver a retirar a sua imagem de contentor de um registo privado, inclua a `--pull-secret` bandeira que faz referência ao segredo de Kubernetes que guarda as credenciais de registo privado durante a execução `func kubernetes deploy` .

## <a name="removing-a-function-app-from-kubernetes"></a>Remoção de uma aplicação de função de Kubernetes

Após a implantação pode remover uma função removendo o `Deployment` , criado, `ScaledObject` `Secrets` criado.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalar KEDA de Kubernetes

As etapas para desinstalar a KEDA estão documentadas [no site da KEDA.](https://keda.sh/docs/1.4/deploy/)

## <a name="supported-triggers-in-keda"></a>Gatilhos suportados na KEDA

A KEDA tem suporte para os seguintes gatilhos da Função Azure:

* [Filas de Armazenamento do Azure](functions-bindings-storage-queue.md)
* [Filas de autocarros de serviço Azure](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Fila RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>SUPORTE HTTP Trigger

Pode utilizar funções Azure que expõem os gatilhos HTTP, mas a KEDA não as gere diretamente.  Pode alavancar o gatilho KEDA prometheus para [escalar as funções HTTP Azure de 1 a *n* instâncias](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, veja os seguintes recursos:

* [Criar uma função usando uma imagem personalizada](functions-create-function-linux-custom-image.md)
* [Criar código e testar as Funções do Azure localmente](functions-develop-local.md)
* [Como funciona o plano de consumo de funções Azure](functions-scale.md)