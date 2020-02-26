---
title: Utilizar OpenFaaS com serviço Azure Kubernetes (AKS)
description: Implementar e utilizar O OpenFaaS com o Serviço Azure Kubernetes (AKS)
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: e684aee1469f855ec651567b805262c71aaf32e5
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594928"
---
# <a name="using-openfaas-on-aks"></a>Utilização de OpenFaaS no AKS

[OpenFaaS][open-faas] é uma estrutura para construir funções sem servidor através da utilização de recipientes. Como um projeto de código aberto, ganhou adoção em larga escala dentro da comunidade. Este documento detalha a instalação e utilização de OpenFaas num cluster do Serviço Azure Kubernetes (AKS).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos dentro deste artigo, precisa dos seguintes.

* Compreensão básica de Kubernetes.
* Um cluster azure Kubernetes Service (AKS) e credenciais AKS configuradas no seu sistema de desenvolvimento.
* Azure CLI instalado no seu sistema de desenvolvimento.
* Ferramentas de linha de comando Git instaladas no seu sistema.

## <a name="add-the-openfaas-helm-chart-repo"></a>Adicione o repo de gráfico de leme OpenFaaS

A OpenFaaS mantém as suas próprias tabelas de leme para manter-se atualizada com todas as mudanças mais recentes.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Implementar OpenFaaS

Como uma boa prática, as funções OpenFaaS e OpenFaaS devem ser armazenadas no seu próprio espaço de nome Kubernetes.

Criar um espaço de nome para o sistema OpenFaaS e funções:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Gere uma palavra-passe para o Portal UI OpenFaaS e a API REST:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Pode obter o valor do segredo com `echo $PASSWORD`.

A palavra-passe que criamos aqui será usada pelo gráfico de leme para permitir a autenticação básica no OpenFaaS Gateway, que é exposto à Internet através de um LoadBalancer em nuvem.

Um gráfico helm para OpenFaaS está incluído no repositório clonado. Use este gráfico para implantar openFaaS no seu cluster AKS.

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
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

É criado um endereço IP público para aceder ao gateway OpenFaaS. Para recuperar este endereço IP, utilize o comando de [serviço kubectl get.][kubectl-get] Pode levar um minuto para que o endereço IP seja atribuído ao serviço.

```console
kubectl get service -l component=gateway --namespace openfaas
```

A saída.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Para testar o sistema OpenFaaS, navegue para o endereço IP externo na porta 8080, `http://52.186.64.52:8080` neste exemplo. Será solicitado a fazer login. Para obter a sua senha, insira `echo $PASSWORD`.

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

Por fim, instale o OPENFaaS CLI. Este exemplo usado brew, consulte a [documentação OPENFaaS CLI][open-faas-cli] para mais opções.

```console
brew install faas-cli
```

Coloque `$OPENFAAS_URL` para o IP público acima encontrado.

Inicie sessão com o Azure CLI:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Criar a primeira função

Agora que o OpenFaaS está operacional, crie uma função usando o portal OpenFaas.

Clique em **Implementar Nova Função** e procure **Figlet**. Selecione a função Figlet e clique em **Implementar**.

![Figlet](media/container-service-serverless/figlet.png)

Use caracóis para invocar a função. Substitua o endereço IP no seguinte exemplo pelo do seu gateway OpenFaas.

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

## <a name="create-second-function"></a>Criar segunda função

Agora crie uma segunda função. Este exemplo será implementado usando o OpenFaaS CLI e inclui uma imagem de recipiente personalizada e recuperando dados de um Cosmos DB. Vários itens precisam de ser configurados antes de criar a função.

Primeiro, crie um novo grupo de recursos para o Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implementar uma instância cosmosDB de tipo `MongoDB`. A instância precisa de um nome único, atualizar `openfaas-cosmos` a algo único para o seu ambiente.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Pegue a cadeia de ligação à base de dados Cosmos e guarde-a numa variável.

Atualize o valor do argumento `--resource-group` para o nome do seu grupo de recursos, e o `--name` argumento para o nome do seu Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Agora povoa o Cosmos DB com dados de teste. Crie um ficheiro chamado `plans.json` e copie no seguinte json.

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

Utilize a ferramenta *mongoimport* para carregar a instância CosmosDB com dados.

Se necessário, instale as ferramentas MongoDB. O exemplo seguinte instala estas ferramentas utilizando cerveja, consulte a [documentação do MongoDB][install-mongo] para outras opções.

```azurecli-interactive
brew install mongodb
```

Carregue os dados na base de dados.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Saída:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Executar o seguinte comando para criar a função. Atualize o valor do argumento `-g` com o seu endereço de gateway OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Uma vez implantado, deverá ver o seu recém-criado ponto final OpenFaaS para a função.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Teste a função com caracóis. Atualize o endereço IP com o endereço de gateway OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Saída:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Também pode testar a função dentro do OpenFaaS UI.

![texto alternativo](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Passos Seguintes

Você pode continuar a aprender com o workshop OpenFaaS através de um conjunto de laboratórios práticos que cobrem tópicos como como criar o seu próprio bot GitHub, consumir segredos, visualizar métricas e auto-escala.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
