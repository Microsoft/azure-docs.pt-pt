---
title: PRETERIDO Gerenciar o cluster kubernetes do Azure com a interface do usuário da Web
description: Usando a interface do usuário da Web do amKubernetes no serviço de contêiner do Azure
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6ce78ca19458b497980cf2cfc374f787d3a5d9f5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276985"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>PRETERIDO Usando a interface do usuário do amKubernetes Web com o serviço de contêiner do Azure

> [!TIP]
> Para obter a versão atualizada deste artigo que usa o serviço kubernetes do Azure, consulte [acessar o painel da Web do kubernetes no serviço kubernetes do Azure (AKs)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial pressupõe que você [criou um cluster kubernetes usando o serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).


Ele também pressupõe que você tenha as ferramentas CLI do Azure e `kubectl` instaladas.

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

## <a name="overview"></a>Visão geral

### <a name="connect-to-the-web-ui"></a>Conectar-se à interface do usuário da Web
Você pode iniciar a interface do usuário da Web do amKubernetes executando:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Isso deve abrir um navegador da Web configurado para se comunicar com um proxy seguro conectando seu computador local à interface do usuário da Web do amKubernetes.

### <a name="create-and-expose-a-service"></a>Criar e expor um serviço
1. Na interface do usuário da Web do amKubernetes, clique no botão **criar** na janela superior direita.

    ![Kubernetes criar interface do usuário](./media/container-service-kubernetes-ui/create.png)

    Uma caixa de diálogo é aberta, onde você pode começar a criar seu aplicativo.

2. Dê a ele o nome `hello-nginx`. Use o [contêiner`nginx` do Docker](https://hub.docker.com/_/nginx/) e implante três réplicas deste serviço Web.

    ![Caixa de diálogo Criar Pod kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. Em **serviço**, selecione **externo** e insira a porta 80.

    Essa configuração equilibra o tráfego para as três réplicas.

    ![Caixa de diálogo de criação do serviço kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Clique em **implantar** para implantar esses contêineres e serviços.

    ![Kubernetes implantar](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Exibir seus contêineres
Depois de clicar em **implantar**, a interface do usuário mostra uma exibição do serviço à medida que ele é implantado:

![Status do kubernetes](./media/container-service-kubernetes-ui/status.png)

Você pode ver o status de cada objeto kubernetes no círculo no lado esquerdo da interface do usuário, em **pods**. Se for um círculo parcialmente completo, o objeto ainda estará sendo implantado. Quando um objeto é totalmente implantado, ele exibe uma marca de seleção verde:

![Kubernetes implantado](./media/container-service-kubernetes-ui/deployed.png)

Depois que tudo estiver em execução, clique em um dos pods para ver detalhes sobre o serviço Web em execução.

![Pods kubernetes](./media/container-service-kubernetes-ui/pods.png)

Na exibição **pods** , você pode ver informações sobre os contêineres no pod, bem como os recursos de CPU e memória usados por esses contêineres:

![Recursos do kubernetes](./media/container-service-kubernetes-ui/resources.png)

Se você não vir os recursos, talvez seja necessário aguardar alguns minutos para que os dados de monitoramento sejam propagados.

Para ver os logs do seu contêiner, clique em **Exibir logs**.

![Logs do kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Exibindo seu serviço
Além de executar seus contêineres, a interface do usuário do kubernetes criou um `Service` externo que provisiona um balanceador de carga para trazer o tráfego para os contêineres no cluster.

No painel de navegação esquerdo, clique em **Serviços** para exibir todos os serviços (deve haver apenas um).

![Serviços Kubernetess](./media/container-service-kubernetes-ui/service-deployed.png)

Nessa exibição, você deve ver um ponto de extremidade externo (endereço IP) que foi alocado para seu serviço.
Se você clicar nesse endereço IP, deverá ver o contêiner Nginx em execução por trás do balanceador de carga.

![exibição de Nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionando seu serviço
Além de exibir os objetos na interface do usuário, você pode editar e atualizar os objetos da API kubernetes.

Primeiro, clique em **implantações** no painel de navegação esquerdo para ver a implantação do seu serviço.

Quando você estiver nesse modo de exibição, clique no conjunto de réplicas e, em seguida, clique em **Editar** na barra de navegação superior:

![Kubernetes editar](./media/container-service-kubernetes-ui/edit.png)

Edite o campo `spec.replicas` a ser `2`e clique em **Atualizar**.

Isso faz com que o número de réplicas seja Descartado para dois excluindo um de seus pods.

 

