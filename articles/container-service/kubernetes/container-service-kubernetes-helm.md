---
title: (PRETERIDO) Implementar contentores com Helm no Kubernetes do Azure
description: Utilize a ferramenta de empacotamento do Helm para implementar contentores num cluster do Kubernetes no Azure Container Service
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 05edbf40e8cd5f8edbdc8b74b540962b1a25c8de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712313"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(PRETERIDO) Utilizar o Helm para implementar contentores num cluster do Kubernetes

> [!TIP]
> Para a versão atualizada, o que este artigo utiliza o serviço Kubernetes do Azure, veja [instalar aplicações com Helm no Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) é uma ferramenta de empacotamento de código-fonte aberto que ajuda a instalar e gerenciar o ciclo de vida de aplicações do Kubernetes. Assim como gestores de pacotes do Linux, como Apt-get e Yum, Helm é utilizado para gerir gráficos de Kubernetes, que são pacotes de recursos do Kubernetes pré-configurada. Este artigo mostra como trabalhar com Helm num cluster de Kubernetes implementado no Azure Container Service.

Helm tem dois componentes: 
* O **Helm CLI** é um cliente que é executado no seu computador local ou na cloud  

* **Tiller** é um servidor que é executado no cluster de Kubernetes e gere o ciclo de vida das suas aplicações do Kubernetes 
 
## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster de Kubernetes](container-service-kubernetes-walkthrough.md) no Azure Container Service

* [Instalar e configurar `kubectl` ](../container-service-connect.md) num computador local

* [Instale o Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) num computador local

## <a name="helm-basics"></a>Noções básicas do Helm 

Para ver informações sobre o que está a instalar Tiller e implementar as suas aplicações para o cluster de Kubernetes, escreva o seguinte comando:

```bash
kubectl cluster-info 
```
![informações de cluster de kubectl](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Depois de ter instalado o Helm, instale Tiller no seu cluster do Kubernetes, escrevendo o seguinte comando:

```bash
helm init --upgrade
```
Quando for concluída com êxito, ver um resultado semelhante ao seguinte:

![Instalação do tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Para ver todos os gráficos de Helm disponíveis no repositório, escreva o seguinte comando:

```bash 
helm search 
```

Pode ver o resultado semelhante ao seguinte:

![Pesquisa de Helm](./media/container-service-kubernetes-helm/helm-search.png)
 
Para atualizar os gráficos para obter as versões mais recentes, escreva:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Implementar um gráfico de controlador de entrada do Nginx 
 
Para implementar um gráfico de controlador de entrada do Nginx, escreva um único comando:

```bash
helm install stable/nginx-ingress 
```
![Implementar o controlador de entrada](./media/container-service-kubernetes-helm/nginx-ingress.png)

Se digitar `kubectl get svc` para ver todos os serviços que estão em execução no cluster, verá que é atribuído um endereço IP para o controlador de entrada. (Enquanto a tarefa está em curso, verá `<pending>`. Demora alguns minutos a concluir.) 

Depois do IP é atribuído um endereço, navegue para o valor do endereço IP externo para ver o back-end de Nginx em execução. 
 
![Endereço IP de entrada](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Para ver uma lista de gráficos instalado no seu cluster, escreva:

```bash
helm list 
```

Pode abreviar o comando para `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Implementar um gráfico da MariaDB e cliente

Agora pode implemente um gráfico de MariaDB e um cliente MariaDB para ligar à base de dados.

Para implementar o gráfico de MariaDB, escreva o seguinte comando:

```bash
helm install --name v1 stable/mariadb
```

onde `--name` é uma etiqueta utilizada para versões.

> [!TIP]
> Se a implementação falhar, execute `helm repo update` e tente novamente.
>
 
 
Para ver todos os gráficos implementados num cluster, escreva:

```bash 
helm list
```
 
Para ver todas as implementações em execução no seu cluster, escreva:

```bash
kubectl get deployments 
``` 
 
 
Por fim, para executar um pod para acessar o cliente, escreva:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Para ligar ao cliente, escreva o seguinte comando, substituindo `v1-mariadb` com o nome da sua implementação:

```bash
sudo mysql –h v1-mariadb
```
 
 
Agora, pode utilizar os comandos SQL padrão para criar bases de dados, tabelas, etc. Por exemplo, `Create DATABASE testdb1;` cria uma base de dados vazia. 
 
 
 
## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a gestão de gráficos do Kubernetes, consulte a [executar Helm documentação](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

