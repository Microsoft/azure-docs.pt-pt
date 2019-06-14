---
title: (PRETERIDO) Gerir o cluster de Kubernetes do Azure com a IU da web
description: Usando a IU web do Kubernetes no Azure Container Service
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c3a79b2e4fab807613a54d2792f5f5b97570293b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309771"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(PRETERIDO) Utilizar a IU web do Kubernetes com o Azure Container Service

> [!TIP]
> Para a versão atualizada, o que este artigo utiliza o serviço Kubernetes do Azure, veja [aceder ao dashboard de web do Kubernetes no Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo pressupõe que tenha [criado um cluster de Kubernetes com o Azure Container Service](container-service-kubernetes-walkthrough.md).


Também parte do princípio que tem a CLI do Azure e `kubectl` ferramentas instaladas.

Pode testar se tiver o `az` ferramenta instalada executando:

```console
$ az --version
```

Se não tiver o `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).

Pode testar se tiver o `kubectl` ferramenta instalada executando:

```console
$ kubectl version
```

Se não tiver `kubectl` instalado, pode executar:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Descrição geral

### <a name="connect-to-the-web-ui"></a>Ligar à IU da web
Pode iniciar a IU web do Kubernetes ao executar:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Esta deve abrir um navegador da web configurado para comunicar com um proxy seguro de ligar o seu computador local para a IU web do Kubernetes.

### <a name="create-and-expose-a-service"></a>Criar e expor um serviço
1. No Kubernetes da IU da web, clique em **criar** botão na janela superior direita.

    ![Kubernetes criar a interface do Usuário](./media/container-service-kubernetes-ui/create.png)

    Aberturas de caixa de diálogo onde pode começar a criar a sua aplicação.

2. Dê-lhe o nome `hello-nginx`. Utilize o [ `nginx` contentor de Docker](https://hub.docker.com/_/nginx/) e implementar a três réplicas desse web service.

    ![Caixa de diálogo Criar de Pod de Kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. Sob **serviço**, selecione **externo** e introduza a porta 80.

    Esta definição carga faz o balanceamento de tráfego para as três réplicas.

    ![Caixa de diálogo Criar de serviço do Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Clique em **Deploy** para implementar estes contentores e serviços.

    ![Implementar o Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Ver os seus contentores
Depois de clicar em **Deploy**, a interface do Usuário mostra uma exibição do seu serviço, como ele implementa:

![Estado do Kubernetes](./media/container-service-kubernetes-ui/status.png)

Pode ver o estado de cada objeto de Kubernetes no círculo no lado esquerdo da interface do Usuário, em **Pods**. Se for um círculo parcialmente completo, em seguida, o objeto ainda está em implementação. Quando um objeto totalmente é implantado, ele exibe uma marca de verificação verde:

![Kubernetes implementado](./media/container-service-kubernetes-ui/deployed.png)

Quando tudo estiver em execução, clique dos seus pods para ver detalhes sobre o serviço web em execução.

![Pods do Kubernetes](./media/container-service-kubernetes-ui/pods.png)

Na **Pods** vista, pode ver informações sobre os contentores no pod, bem como os recursos de CPU e memória utilizados por desses contentores:

![Recursos do Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Se não vir os recursos, poderá ter de aguardar alguns minutos para que os dados de monitorização propagar.

Para ver os registos para o seu contentor, clique em **ver registos**.

![Registos do Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visualizar o seu serviço
Além de executar os contentores, a IU do Kubernetes criou um externo `Service` que aprovisiona um balanceador de carga para trazer o tráfego para os contentores no seu cluster.

No painel de navegação esquerdo, clique em **serviços** para ver todos os serviços (deve haver apenas um).

![Serviços do Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

Nessa exibição, verá um ponto final externo (endereço IP) que foi alocado ao seu serviço.
Se clicar esse endereço IP, deverá ver o contentor de Nginx em execução por detrás do Balanceador de carga.

![modo de exibição do nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionar o seu serviço
Além de visualizar os objetos na interface do Usuário, pode editar e atualizar os objetos de API do Kubernetes.

Em primeiro lugar, clique em **implementações** no painel de navegação à esquerda para ver a implementação para o seu serviço.

Assim que estiver em exibição, clique no conjunto de réplicas e, em seguida, clique em **editar** na barra de navegação superior:

![Kubernetes Edit](./media/container-service-kubernetes-ui/edit.png)

Editar a `spec.replicas` campo esteja `2`e clique em **atualização**.

Isso faz com que o número de réplicas para remover a dois eliminando um dos seus pods.

 

