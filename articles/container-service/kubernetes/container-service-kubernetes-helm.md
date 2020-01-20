---
title: PRETERIDO Implantar contêineres com Helm no Azure kubernetes
description: Usar a ferramenta de empacotamento Helm para implantar contêineres em um cluster kubernetes no serviço de contêiner do Azure
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271088"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>PRETERIDO Usar o Helm para implantar contêineres em um cluster kubernetes

> [!TIP]
> Para obter a versão atualizada deste artigo que usa o serviço kubernetes do Azure, consulte [instalar aplicativos com Helm no serviço kubernetes do Azure (AKs)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

O [Helm](https://github.com/kubernetes/helm/) é uma ferramenta de empacotamento de software livre que ajuda você a instalar e gerenciar o ciclo de vida de aplicativos kubernetes. Assim como os gerenciadores de pacotes do Linux, como apt-get e yum, o Helm é usado para gerenciar gráficos do kubernetes, que são pacotes de recursos de kubernetes pré-configurados. Este artigo mostra como trabalhar com o Helm em um cluster kubernetes implantado no serviço de contêiner do Azure.

O Helm tem dois componentes: 
* A **CLI do Helm** é um cliente executado em seu computador localmente ou na nuvem  

* O **gaveta** é um servidor executado no cluster kubernetes e gerencia o ciclo de vida de seus aplicativos kubernetes 
 
## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster kubernetes](container-service-kubernetes-walkthrough.md) no serviço de contêiner do Azure

* [Instalar e configurar `kubectl`](../container-service-connect.md) em um computador local

* [Instalar o Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) em um computador local

## <a name="helm-basics"></a>Noções básicas do Helm 

Para exibir informações sobre o cluster kubernetes que você está instalando e implantando seus aplicativos no, digite o seguinte comando:

```bash
kubectl cluster-info 
```
![cluster kubectl – informações](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Depois de instalar o Helm, instale o gaveta no cluster do kubernetes digitando o seguinte comando:

```bash
helm init --upgrade
```
Quando ele for concluído com êxito, você verá uma saída semelhante à seguinte:

![Instalação do gaveta](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Para exibir todos os gráficos do Helm disponíveis no repositório, digite o seguinte comando:

```bash 
helm search 
```

Você verá uma saída semelhante à seguinte:

![Pesquisa do Helm](./media/container-service-kubernetes-helm/helm-search.png)
 
Para atualizar os gráficos para obter as versões mais recentes, digite:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Implantar um gráfico do controlador de entrada do Nginx 
 
Para implantar um gráfico do controlador de entrada do nginx, digite um único comando:

```bash
helm install stable/nginx-ingress 
```
![Implantar controlador de entrada](./media/container-service-kubernetes-helm/nginx-ingress.png)

Se você digitar `kubectl get svc` para exibir todos os serviços em execução no cluster, verá que um endereço IP é atribuído ao controlador de entrada. (Enquanto a atribuição estiver em andamento, você verá `<pending>`. Leva alguns minutos para ser concluído.) 

Depois que o endereço IP for atribuído, navegue até o valor do endereço IP externo para ver o back-end Nginx em execução. 
 
![Endereço IP de entrada](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Para ver uma lista de gráficos instalados em seu cluster, digite:

```bash
helm list 
```

Você pode abreviar o comando para `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Implantar um gráfico e um cliente do MariaDB

Agora, implante um gráfico do MariaDB e um cliente do MariaDB para se conectar ao banco de dados.

Para implantar o gráfico MariaDB, digite o seguinte comando:

```bash
helm install --name v1 stable/mariadb
```

onde `--name` é uma marca usada para versões.

> [!TIP]
> Se a implantação falhar, execute `helm repo update` e tente novamente.
>
 
 
Para exibir todos os gráficos implantados em seu cluster, digite:

```bash 
helm list
```
 
Para exibir todas as implantações em execução no cluster, digite:

```bash
kubectl get deployments 
``` 
 
 
Por fim, para executar um pod para acessar o cliente, digite:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Para se conectar ao cliente, digite o seguinte comando, substituindo `v1-mariadb` pelo nome da sua implantação:

```bash
sudo mysql –h v1-mariadb
```
 
 
Agora você pode usar comandos SQL padrão para criar bancos de dados, tabelas, etc. Por exemplo, `Create DATABASE testdb1;` cria um banco de dados vazio. 
 
 
 
## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como gerenciar gráficos do kubernetes, consulte a [documentação do Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

