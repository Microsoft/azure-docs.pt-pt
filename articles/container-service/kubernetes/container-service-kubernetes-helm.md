---
title: (DEPRECIADO) Desloque contentores com Helm em Azure Kubernetes
description: Utilize a ferramenta de embalagem Helm para implantar contentores num cluster Kubernetes no Serviço de Contentores Azure
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271088"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(DEPRECIADO) Use o Helm para implantar contentores num aglomerado de Kubernetes

> [!TIP]
> Para a versão atualizada este artigo que utiliza o Serviço Azure Kubernetes, consulte [Instalar aplicações com a Helm no Serviço Azure Kubernetes (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[O Helm](https://github.com/kubernetes/helm/) é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações kubernetes. Semelhante aos gestores de pacotes Linux, como Apt-get e Yum, helm é usado para gerir gráficos Kubernetes, que são pacotes de recursos kubernetes reconfigurados. Este artigo mostra-lhe como trabalhar com helm num cluster Kubernetes implantado no Serviço de Contentores Azure.

O leme tem dois componentes: 
* O **Helm CLI** é um cliente que corre na sua máquina localmente ou na nuvem  

* **Tiller** é um servidor que funciona no cluster Kubernetes e gere o ciclo de vida das suas aplicações Kubernetes 
 
## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster Kubernetes](container-service-kubernetes-walkthrough.md) no Serviço de Contentores Azure

* [Instale e `kubectl` configure](../container-service-connect.md) num computador local

* [Instale o Leme](https://github.com/kubernetes/helm/blob/master/docs/install.md) num computador local

## <a name="helm-basics"></a>Helm básicos 

Para ver informações sobre o cluster Kubernetes que está a instalar a Tiller e a implementar as suas aplicações para, digitar o seguinte comando:

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Depois de ter instalado o Helm, instale o Tiller no seu cluster Kubernetes digitando o seguinte comando:

```bash
helm init --upgrade
```
Quando completa com sucesso, vê-se a saída como a seguinte:

![Instalação Tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Para ver todos os gráficos helm disponíveis no repositório, escreva o seguinte comando:

```bash 
helm search 
```

Vê-se a saída como a seguinte:

![Pesquisa de leme](./media/container-service-kubernetes-helm/helm-search.png)
 
Para atualizar os gráficos para obter as versões mais recentes, escreva:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Implementar um gráfico de controlador de ingresso Nginx 
 
Para implementar um gráfico de controlador de ingresso Nginx, escreva um único comando:

```bash
helm install stable/nginx-ingress 
```
![Implementar controlador de ingresso](./media/container-service-kubernetes-helm/nginx-ingress.png)

Se digitar `kubectl get svc` para ver todos os serviços que estão em execução no cluster, verá que um endereço IP é atribuído ao controlador de entrada. Enquanto a missão está em `<pending>`andamento, você vê. Leva alguns minutos para completar.) 

Depois de atribuído o endereço IP, navegue até ao valor do endereço IP externo para ver o backend nginx em funcionamento. 
 
![Endereço IP de ingresso](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Para ver uma lista de gráficos instalados no seu cluster, escreva:

```bash
helm list 
```

Pode abreviar o comando `helm ls`para.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Implementar um gráfico MariaDB e cliente

Agora implemente um gráfico MariaDB e um cliente MariaDB para ligar à base de dados.

Para implantar o gráfico MariaDB, digite o seguinte comando:

```bash
helm install --name v1 stable/mariadb
```

onde `--name` está uma etiqueta usada para lançamentos.

> [!TIP]
> Se o destacamento falhar, corra `helm repo update` e tente novamente.
>
 
 
Para ver todos os gráficos implantados no seu cluster, escreva:

```bash 
helm list
```
 
Para ver todas as implementações em execução no seu cluster, escreva:

```bash
kubectl get deployments 
``` 
 
 
Finalmente, para executar uma cápsula para aceder ao cliente, escreva:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Para ligar ao cliente, escreva o `v1-mariadb` seguinte comando, substituindo-o pelo nome da sua implantação:

```bash
sudo mysql –h v1-mariadb
```
 
 
Agora pode utilizar comandos SQL padrão para criar bases de dados, tabelas, etc. Por exemplo, `Create DATABASE testdb1;` cria uma base de dados vazia. 
 
 
 
## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a gestão dos gráficos da Kubernetes, consulte a [documentação Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

