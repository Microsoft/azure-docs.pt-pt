---
title: (DEPRECIADO) Gerir o cluster Azure Kubernetes com ui web
description: Utilização da UI web kubernetes no Serviço de Contentores Azure
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371142"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(DEPRECIADO) Utilização da UI web kubernetes com serviço de contentores Azure

> [!TIP]
> Para a versão atualizada este artigo que utiliza o Serviço Azure Kubernetes, consulte [Access the Kubernetes web dashboard no Serviço Azure Kubernetes (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este walkthrough pressupõe que [criou um cluster Kubernetes usando](container-service-kubernetes-walkthrough.md)o Serviço de Contentores Azure .


Também assume que tem o AZURE `kubectl` CLI e ferramentas instaladas.

Pode testar se tiver `az` a ferramenta instalada executando:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui.](https://github.com/azure/azure-cli#installation)

Pode testar se tiver `kubectl` a ferramenta instalada executando:

```console
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Descrição geral

### <a name="connect-to-the-web-ui"></a>Ligue-se à Web UI
Você pode lançar o Kubernetes web UI executando:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Isto deve abrir um navegador web configurado para falar com um representante seguro que liga a sua máquina local à UI web kubernetes.

### <a name="create-and-expose-a-service"></a>Criar e expor um serviço
1. Na UI web kubernetes, clique em **Criar** o botão na janela superior direita.

    ![Kubernetes Criam UI](./media/container-service-kubernetes-ui/create.png)

    Abre-se uma caixa de diálogo onde pode começar a criar a sua aplicação.

2. Dê-lhe `hello-nginx`o nome. Utilize [ `nginx` ](https://hub.docker.com/_/nginx/) o recipiente do Docker e implante três réplicas deste serviço web.

    ![Kubernetes Pod Criar Diálogo](./media/container-service-kubernetes-ui/nginx.png)

3. Em **Serviço,** selecione **Externo** e entre na porta 80.

    Esta definição de carga equilibra o tráfego para as três réplicas.

    ![Serviço Kubernetes Criar Diálogo](./media/container-service-kubernetes-ui/service.png)

4. Clique em **Implementar** para implantar estes contentores e serviços.

    ![Implementação no Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Veja os seus recipientes
Depois de clicar em **Implementar,** a UI mostra uma visão do seu serviço à medida que se implementa:

![Estado de Kubernetes](./media/container-service-kubernetes-ui/status.png)

Pode ver o estado de cada kubernetes objeto no círculo no lado esquerdo da UI, sob **Pods**. Se for um círculo parcialmente completo, então o objeto ainda está a ser implantado. Quando um objeto está totalmente implantado, apresenta uma marca de verificação verde:

![Kubernetes Implantados](./media/container-service-kubernetes-ui/deployed.png)

Uma vez que tudo esteja em execução, clique numa das suas cápsulas para ver detalhes sobre o serviço web em execução.

![Cápsulas Kubernetes](./media/container-service-kubernetes-ui/pods.png)

Na vista **pods,** pode ver informações sobre os contentores na cápsula, bem como o CPU e os recursos de memória utilizados por esses contentores:

![Recursos Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Se não vir os recursos, poderá ter de esperar alguns minutos para que os dados de monitorização se propaguem.

Para ver os registos do seu recipiente, clique em **Ver registos**.

![Diários de Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visualização do seu serviço
Além de executar os seus contentores, o Kubernetes UI criou um equilíbrio externo `Service` que prevê um equilíbrio de carga para levar o tráfego para os contentores do seu cluster.

No painel de navegação à esquerda, clique em **Serviços** para ver todos os serviços (deve haver apenas um).

![Serviços do Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

Nesta perspetiva, deve consultar um ponto final externo (endereço IP) que tenha sido atribuído ao seu serviço.
Se clicar nesse endereço IP, deve ver o seu recipiente Nginx a correr atrás do equilibrador de carga.

![nginx vista](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionar o seu serviço
Além de visualizar os seus objetos na UI, pode editar e atualizar os objetos Kubernetes API.

Primeiro, clique em **Implementações** no painel de navegação esquerdo para ver a implementação do seu serviço.

Assim que estiver nessa vista, clique no conjunto de réplicas e clique em **Editar** na barra de navegação superior:

![Edição kubernetes](./media/container-service-kubernetes-ui/edit.png)

Editar `spec.replicas` o campo `2`para ser, e clicar em **Atualizar**.

Isto faz com que o número de réplicas baixe para dois, apagando uma das suas cápsulas.

 

