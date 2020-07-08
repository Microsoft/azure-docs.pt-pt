---
title: (PRECADO) Gerir o cluster Azure Kubernetes com web UI
description: Utilizando o UI web Kubernetes no Serviço de Contentores Azure
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371142"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(PRECADO) Utilizando o UI web Kubernetes com serviço de contentores Azure

> [!TIP]
> Para a versão atualizada deste artigo que utiliza o Serviço Azure Kubernetes, consulte [o painel web Kubernetes em Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough assume que [criou um cluster Kubernetes usando o Serviço de Contentores Azure.](container-service-kubernetes-walkthrough.md)


Também assume que tem o Azure CLI e `kubectl` ferramentas instaladas.

Pode testar se a ferramenta está `az` instalada em execução:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).

Pode testar se a ferramenta está `kubectl` instalada em execução:

```console
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Descrição geral

### <a name="connect-to-the-web-ui"></a>Ligue-se à UI web
Pode lançar a UI web Kubernetes executando:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Isto deve abrir um navegador web configurado para falar com um proxy seguro que liga a sua máquina local à UI web Kubernetes.

### <a name="create-and-expose-a-service"></a>Criar e expor um serviço
1. Na UI web de Kubernetes, clique em **Criar** o botão na janela superior direita.

    ![Kubernetes Criam UI](./media/container-service-kubernetes-ui/create.png)

    Abre-se uma caixa de diálogo onde pode começar a criar a sua aplicação.

2. Dê-lhe o `hello-nginx` nome. Utilize o [ `nginx` recipiente do Docker](https://hub.docker.com/_/nginx/) e implante três réplicas deste serviço web.

    ![Pod Kubernetes Criar Diálogo](./media/container-service-kubernetes-ui/nginx.png)

3. Em **Serviço**, selecione **External** e introduza a porta 80.

    Esta definição equilibra o tráfego das três réplicas.

    ![Serviço Kubernetes Criar Diálogo](./media/container-service-kubernetes-ui/service.png)

4. Clique **em Implementar** para implementar estes contentores e serviços.

    ![Implementação no Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Ver os seus contentores
Depois de clicar em **Implementar,** o UI mostra uma visão do seu serviço à medida que se implementa:

![Estado de Kubernetes](./media/container-service-kubernetes-ui/status.png)

Pode ver o estado de cada objeto Kubernetes no círculo no lado esquerdo da UI, em **Pods**. Se for um círculo parcialmente cheio, então o objeto ainda está a ser implantado. Quando um objeto está totalmente implantado, apresenta uma marca de verificação verde:

![Kubernetes Implantados](./media/container-service-kubernetes-ui/deployed.png)

Uma vez que tudo esteja em execução, clique numa das suas cápsulas para ver detalhes sobre o serviço web em execução.

![Kubernetes Pods](./media/container-service-kubernetes-ui/pods.png)

Na vista **Pods,** pode ver informações sobre os contentores na cápsula, bem como sobre o CPU e os recursos de memória utilizados por esses contentores:

![Recursos Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Se não vir os recursos, poderá ter de esperar alguns minutos para que os dados de monitorização se propaguem.

Para ver os registos do seu recipiente, clique em **Ver registos**.

![Diários de Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visualização do seu serviço
Além de executar os seus contentores, o UI Kubernetes criou um exterior `Service` que fornece um equilibrador de carga para trazer tráfego para os contentores do seu cluster.

No painel de navegação à esquerda, clique em **Serviços** para visualizar todos os serviços (deve haver apenas um).

![Serviços do Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

Nesse ponto de vista, deverá ver um ponto final externo (endereço IP) que tenha sido atribuído ao seu serviço.
Se clicar no endereço IP, deverá ver o seu contentor Nginx a funcionar atrás do equilibrador de carga.

![vista nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionar o seu serviço
Além de visualizar os seus objetos na UI, pode editar e atualizar os objetos API de Kubernetes.

Primeiro, clique em **Implementações** no painel de navegação esquerdo para ver a implementação do seu serviço.

Uma vez que esteja nessa vista, clique no conjunto de réplica e, em seguida, clique em **Editar** na barra de navegação superior:

![Edição de Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Editar o `spec.replicas` campo para ser , e clicar em `2` **Update**.

Isto faz com que o número de réplicas baixe para dois, eliminando uma das suas cápsulas.

 

