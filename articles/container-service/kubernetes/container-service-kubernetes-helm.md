---
title: (PRECADO) Instalar contentores com Helm em Azure Kubernetes
description: Utilize a ferramenta de embalagem Helm para implantar contentores num aglomerado de Kubernetes no Serviço de Contentores Azure
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 2a3423b908ac5e089faa30b19914d3b74ae9a547
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82735424"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(PRECADO) Use o Helm para implantar contentores num aglomerado de Kubernetes

> [!TIP]
> Para a versão atualizada, este artigo que utiliza o Serviço Azure Kubernetes, consulte [aplicações de instalação com Helm in Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações Kubernetes. Semelhante aos gestores de pacotes Linux, como Apt-get e Yum, Helm é usado para gerir gráficos Kubernetes, que são pacotes de recursos kubernetes pré-configurados. Este artigo mostra-lhe como trabalhar com Helm num cluster Kubernetes implantado no Serviço de Contentores Azure.

O leme tem dois componentes: 
* O **Helm CLI** é um cliente que funciona na sua máquina localmente ou na nuvem  

* **Tiller** é um servidor que funciona no cluster Kubernetes e gere o ciclo de vida das suas aplicações Kubernetes 
 
## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster Kubernetes](container-service-kubernetes-walkthrough.md) no Serviço de Contentores Azure

* [Instalação e `kubectl` configuração](../container-service-connect.md) em um computador local

* [Instale o Leme](https://helm.sh/docs/intro/quickstart/) num computador local

## <a name="helm-basics"></a>Princípios do leme 

Para visualizar informações sobre o cluster Kubernetes a que está a instalar o Tiller e a implementar as suas aplicações para, digite o seguinte comando:

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Depois de ter instalado o Helm, instale o Tiller no seu cluster Kubernetes digitando o seguinte comando:

```bash
helm init --upgrade
```
Quando termina com sucesso, vê-se a saída como a seguinte:

![Instalação Tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Para visualizar todos os gráficos Helm disponíveis no repositório, digite o seguinte comando:

```bash 
helm search 
```

Vê-se a saída como as seguintes:

![Pesquisa de leme](./media/container-service-kubernetes-helm/helm-search.png)
 
Para atualizar as tabelas para obter as versões mais recentes, escreva:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Implementar um gráfico de controlador de entrada Nginx 
 
Para implementar um gráfico de controlador de entrada Nginx, digite um único comando:

```bash
helm install stable/nginx-ingress 
```
![Implementar controlador de entrada](./media/container-service-kubernetes-helm/nginx-ingress.png)

Se escrever `kubectl get svc` para visualizar todos os serviços que estão em execução no cluster, verá que um endereço IP é atribuído ao controlador de entrada. (Enquanto a atribuição está em andamento, está a `<pending>` ver. Leva alguns minutos para ser concluído.) 

Após a atribuição do endereço IP, navegue para o valor do endereço IP externo para ver o backend Nginx em funcionamento. 
 
![Endereço IP ingress](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Para ver uma lista de gráficos instalados no seu cluster, escreva:

```bash
helm list 
```

Pode abreviar o comando para `helm ls` .
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Implementar um gráfico e cliente MariaDB

Agora, implante um gráfico MariaDB e um cliente MariaDB para ligar à base de dados.

Para implementar o gráfico MariaDB, digite o seguinte comando:

```bash
helm install --name v1 stable/mariadb
```

onde `--name` é uma etiqueta usada para lançamentos.

> [!TIP]
> Se a colocação falhar, corra `helm repo update` e tente novamente.
>
 
 
Para visualizar todos os gráficos implantados no seu cluster, escreva:

```bash 
helm list
```
 
Para visualizar todas as implementações em execução no seu cluster, escreva:

```bash
kubectl get deployments 
``` 
 
 
Finalmente, para executar uma cápsula para aceder ao cliente, escreva:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Para ligar ao cliente, digite o seguinte comando, `v1-mariadb` substituindo-o pelo nome da sua implantação:

```bash
sudo mysql –h v1-mariadb
```
 
 
Agora pode utilizar comandos SQL padrão para criar bases de dados, tabelas, etc. Por exemplo, `Create DATABASE testdb1;` cria uma base de dados vazia. 
 
 
 
## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a gestão dos gráficos de Kubernetes, consulte a [documentação helm](https://helm.sh/docs/). 

