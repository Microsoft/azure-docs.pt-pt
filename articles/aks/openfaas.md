---
title: Utilizar o OpenFaaS com o serviço Kubernetes do Azure (AKS)
description: Implementar e utilizar o OpenFaaS com o Azure Kubernetes Service (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: dc0f4bd1e5b07e30f3c89807fbbbc908b3149810
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542536"
---
# <a name="using-openfaas-on-aks"></a>Utilizar o OpenFaaS no AKS

[OpenFaaS] [ open-faas] é uma estrutura para a criação de funções sem servidor com base no contentores. Como um projeto de código-fonte aberto, ele ganhou adoção em grande escala na Comunidade. Este documento detalha os instalar e utilizar o OpenFaas num cluster do Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, é necessário o seguinte.

* Noções básicas sobre do Kubernetes.
* Um cluster do Azure Kubernetes Service (AKS) e as credenciais do AKS configuradas no sistema de desenvolvimento.
* CLI do Azure instalada no sistema de desenvolvimento.
* Ferramentas da linha de comandos do Git instaladas no seu sistema.

## <a name="get-openfaas"></a>Obter o OpenFaaS

Clone o repositório de projeto OpenFaaS no sistema de desenvolvimento.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Mude para o diretório do repositório clonado.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>Implementar o OpenFaaS

Como uma boa prática, OpenFaaS e OpenFaaS funções devem ser armazenadas no seu próprio espaço de nomes do Kubernetes.

Crie um espaço de nomes para o sistema de OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Crie um segundo espaço de nomes para as funções de OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

Um gráfico Helm para OpenFaaS está incluído no repositório clonado. Utilize este gráfico para implementar o OpenFaaS no seu cluster do AKS.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

Saída:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Um endereço IP público é criado para aceder ao gateway de OpenFaaS. Para obter este endereço IP, utilize o [kubectl obter serviço] [ kubectl-get] comando. Pode demorar um minuto para o endereço IP a ser atribuída ao serviço.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Saída.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Para testar o sistema de OpenFaaS, navegue para o endereço IP externo na porta 8080, `http://52.186.64.52:8080` neste exemplo.

![Interface do Usuário de OpenFaaS](media/container-service-serverless/openfaas.png)

Por fim, instale a CLI de OpenFaaS. Este exemplo utilizou brew, consulte a [documentação da CLI de OpenFaaS] [ open-faas-cli] para obter mais opções.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Criar a primeira função

Agora que o OpenFaaS está operacional, crie uma função com o portal de OpenFaas.

Clique em **implementar a nova função** e procure **Figlet**. Selecione a função de Figlet e clique em **Deploy**.

![Figlet](media/container-service-serverless/figlet.png)

Utilize o curl para invocar a função. Substitua o endereço IP no exemplo a seguir a que o gateway de OpenFaas.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Saída:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Criar a segunda função

Agora, crie uma segunda função. Neste exemplo será implementado com a CLI OpenFaaS e inclui uma imagem de contentor personalizado e obter dados a partir de um Cosmos DB. Vários itens têm de ser configurados antes de criar a função.

Primeiro, crie um novo grupo de recursos para o Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implementar uma instância de cosmos DB do tipo `MongoDB`. A instância tem um nome exclusivo, atualizar `openfaas-cosmos` a algo especial ao seu ambiente.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Obter a cadeia de ligação de base de dados do Cosmos e o armazenamos numa variável.

Atualize o valor para o `--resource-group` argumento para o nome do seu grupo de recursos e o `--name` argumento para o nome do seu Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Agora a preencher o Cosmos DB com dados de teste. Crie um ficheiro denominado `plans.json` e copie o seguinte json.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Utilize o *mongoimport* ferramenta para carregar a instância de cosmos DB com dados.

Se necessário, instale as ferramentas do MongoDB. O exemplo seguinte instala essas ferramentas brew a utilizar, consulte a [documentação do MongoDB] [ install-mongo] para outras opções.

```azurecli-interactive
brew install mongodb
```

Carregar os dados para a base de dados.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Saída:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Execute o seguinte comando para criar a função. Atualize o valor da `-g` argumento com o seu endereço de gateway OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Uma vez implantado, deverá ver o ponto de final de OpenFaaS recentemente criado para a função.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Teste a função com o curl. Atualize o endereço IP com o endereço de gateway OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Saída:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Também pode testar a função na interface de Usuário OpenFaaS.

![texto alternativo](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Próximos Passos

A implementação padrão de OpenFaas tem de ser bloqueado para o Gateway de OpenFaaS e funções. [Postagem do Blog de Alex Ellis](https://blog.alexellis.io/lock-down-openfaas/) inclui mais detalhes sobre as opções de configuração segura.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
