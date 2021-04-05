---
title: Use OpenFaaS com serviço Azure Kubernetes (AKS)
description: Aprenda a implementar e a utilizar o OpenFaaS num cluster Azure Kubernetes Service (AKS) para construir funções sem servidor com contentores.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747770"
---
# <a name="using-openfaas-on-aks"></a>Utilização do OpenFaaS em AKS

[OpenFaaS][open-faas] é uma estrutura para construir funções sem servidor através da utilização de contentores. Como um projeto de código aberto, ganhou adoção em larga escala dentro da comunidade. Este documento detalha a instalação e utilização do OpenFaas num cluster Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos dentro deste artigo, você precisa do seguinte.

* Compreensão básica de Kubernetes.
* Um cluster de serviço Azure Kubernetes (AKS) e credenciais AKS configuradas no seu sistema de desenvolvimento.
* Azure CLI instalado no seu sistema de desenvolvimento.
* Ferramentas de linha de comando Git instaladas no seu sistema.

## <a name="add-the-openfaas-helm-chart-repo"></a>Adicione o repo de gráfico de leme OpenFaaS

Vá [https://shell.azure.com](https://shell.azure.com) para abrir a Azure Cloud Shell no seu navegador.

O OpenFaaS mantém os seus próprios gráficos de leme para se manter atualizado com todas as mudanças mais recentes.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Implementar OpenFaaS

Como uma boa prática, as funções OpenFaaS e OpenFaaS devem ser armazenadas no seu próprio espaço de nome Kubernetes.

Criar um espaço de nome para o sistema e funções OpenFaaS:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Gere uma palavra-passe para o Portal OpenFaaS UI e para a API REST:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Pode obter o valor do segredo `echo $PASSWORD` com.

A palavra-passe que criamos aqui será usada pelo gráfico de leme para permitir a autenticação básica no Gateway OpenFaaS, que está exposto à Internet através de um LoadBalancer em nuvem.

Um gráfico de helm para OpenFaaS está incluído no repositório clonado. Utilize este gráfico para implantar o OpenFaaS no seu cluster AKS.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Resultado:

```output
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

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

É criado um endereço IP público para aceder ao gateway OpenFaaS. Para recuperar este endereço IP, utilize o [comando de serviço kubectl.][kubectl-get] Pode levar um minuto para o endereço IP ser atribuído ao serviço.

```console
kubectl get service -l component=gateway --namespace openfaas
```

A saída.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Para testar o sistema OpenFaaS, consulte o endereço IP externo na porta 8080, `http://52.186.64.52:8080` neste exemplo. Serão solicitados a fazer login. Para obter a sua senha, insira `echo $PASSWORD` .

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

Finalmente, instale o OpenFaaS CLI. Este exemplo usado, consulte a [documentação Do CLI OpenFaaS][open-faas-cli] para mais opções.

```console
brew install faas-cli
```

Definir `$OPENFAAS_URL` para o IP público encontrado acima.

Faça login com o Azure CLI:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Criar primeira função

Agora que o OpenFaaS está operacional, crie uma função utilizando o portal OpenFaas.

Clique em **Implementar Nova Função** e procure **por Figlet**. Selecione a função Figlet e clique em **Implementar**.

![A screenshot mostra a caixa de diálogo de nova função com a figleta de texto na linha de pesquisa.](media/container-service-serverless/figlet.png)

Use caracóis para invocar a função. Substitua o endereço IP no exemplo seguinte pelo do seu gateway OpenFaas.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Resultado:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Criar segunda função

Agora criar uma segunda função. Este exemplo será implementado usando o OpenFaaS CLI e inclui uma imagem personalizada do recipiente e obtenção de dados de um Cosmos DB. Vários itens precisam de ser configurados antes de criar a função.

Primeiro, criar um novo grupo de recursos para o Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implementar uma instância cosmosdb do tipo `MongoDB` . O caso precisa de um nome único, atualização `openfaas-cosmos` para algo único para o seu ambiente.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Obtenha a cadeia de ligação da base de dados Cosmos e guarde-a numa variável.

Atualize o valor do `--resource-group` argumento para o nome do seu grupo de recursos e o argumento para o nome do seu `--name` Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Agora povoam o Cosmos DB com dados de teste. Crie um ficheiro com o nome `plans.json` e copie no seguinte json.

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

Utilize a ferramenta *de mongoimport* para carregar a instância CosmosDB com dados.

Se necessário, instale as ferramentas MongoDB. O exemplo a seguir instala estas ferramentas utilizando a cerveja, consulte a [documentação mongoDB][install-mongo] para outras opções.

```console
brew install mongodb
```

Coloque os dados na base de dados.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Resultado:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Executar o seguinte comando para criar a função. Atualize o valor do `-g` argumento com o seu endereço de gateway OpenFaaS.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Uma vez implantado, deverá ver o seu recém-criado ponto final OpenFaaS para a função.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Teste a função com caracóis. Atualize o endereço IP com o endereço de gateway OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Resultado:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Também pode testar a função dentro do OpenFaaS UI.

![texto alternativo](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Passos Seguintes

Você pode continuar a aprender com o workshop OpenFaaS através de um conjunto de laboratórios práticos que abrangem tópicos como como criar o seu próprio bot GitHub, consumindo segredos, métricas de visualização e auto-escala.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
