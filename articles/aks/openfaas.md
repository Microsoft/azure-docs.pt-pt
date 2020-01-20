---
title: Usar o OpenFaaS com o AKS (serviço kubernetes do Azure)
description: Implantar e usar o OpenFaaS com o AKS (serviço kubernetes do Azure)
author: justindavies
ms.service: container-service
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 7949735eff4478d2d04700e1c6df69d28fe25979
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278483"
---
# <a name="using-openfaas-on-aks"></a>Usando OpenFaaS em AKS

[OpenFaaS][open-faas] é uma estrutura para a criação de funções sem servidor por meio do uso de contêineres. Como um projeto de software livre, ele ganhou adoção em larga escala na Comunidade. Este documento detalha como instalar e usar o OpenFaas em um cluster do AKS (serviço kubernetes do Azure).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Compreensão básica do kubernetes.
* Um cluster do AKS (serviço kubernetes do Azure) e as credenciais do AKS configuradas no seu sistema de desenvolvimento.
* CLI do Azure instalado no seu sistema de desenvolvimento.
* Ferramentas de linha de comando git instaladas em seu sistema.

## <a name="add-the-openfaas-helm-chart-repo"></a>Adicionar o repositório de gráfico OpenFaaS Helm

O OpenFaaS mantém seus próprios gráficos de Helm para manter-se atualizado com todas as alterações mais recentes.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Implantar OpenFaaS

Como uma prática recomendada, as funções OpenFaaS e OpenFaaS devem ser armazenadas em seu próprio namespace kubernetes.

Crie um namespace para o sistema e as funções do OpenFaaS:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Gere uma senha para o portal de interface do usuário do OpenFaaS e a API REST:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Você pode obter o valor do segredo com `echo $PASSWORD`.

A senha que criamos aqui será usada pelo gráfico do Helm para habilitar a autenticação básica no gateway OpenFaaS, que é exposta à Internet por meio de um balanceador de nuvem.

Um gráfico Helm para OpenFaaS está incluído no repositório clonado. Use este gráfico para implantar o OpenFaaS em seu cluster do AKS.

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

Um endereço IP público é criado para acessar o gateway OpenFaaS. Para recuperar esse endereço IP, use o comando [kubectl Get Service][kubectl-get] . Pode levar um minuto para que o endereço IP seja atribuído ao serviço.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Der.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Para testar o sistema OpenFaaS, navegue até o endereço IP externo na porta 8080, `http://52.186.64.52:8080` neste exemplo. Você será solicitado a fazer logon. Para buscar sua senha, insira `echo $PASSWORD`.

![Interface do usuário do amOpenFaaS](media/container-service-serverless/openfaas.png)

Por fim, instale a CLI do OpenFaaS. Este exemplo usou Brew, consulte a [documentação da CLI do OpenFaaS][open-faas-cli] para obter mais opções.

```console
brew install faas-cli
```

Defina `$OPENFAAS_URL` para o IP público encontrado acima.

Faça logon com o CLI do Azure:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Criar primeira função

Agora que o OpenFaaS está operacional, crie uma função usando o portal do OpenFaas.

Clique em **implantar nova função** e procure **FIGlet**. Selecione a função FIGlet e clique em **implantar**.

![FIGlet](media/container-service-serverless/figlet.png)

Use a rotação para invocar a função. Substitua o endereço IP no exemplo a seguir com o do seu gateway OpenFaas.

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

Agora, crie uma segunda função. Este exemplo será implantado usando a CLI do OpenFaaS e incluirá uma imagem de contêiner personalizada e recuperando dados de um Cosmos DB. Vários itens precisam ser configurados antes da criação da função.

Primeiro, crie um novo grupo de recursos para o Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implante uma instância CosmosDB do tipo `MongoDB`. A instância precisa de um nome exclusivo, atualizar `openfaas-cosmos` para algo exclusivo para seu ambiente.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Obtenha a cadeia de conexão do banco de dados Cosmos e armazene-a em uma variável.

Atualize o valor do argumento `--resource-group` para o nome do seu grupo de recursos e o argumento `--name` como o nome do seu Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Agora, popule o Cosmos DB com dados de teste. Crie um arquivo chamado `plans.json` e copie-o no JSON a seguir.

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

Use a ferramenta *mongoimport* para carregar a instância do CosmosDB com dados.

Se necessário, instale as ferramentas do MongoDB. O exemplo a seguir instala essas ferramentas usando o Brew, consulte a [documentação do MongoDB][install-mongo] para outras opções.

```azurecli-interactive
brew install mongodb
```

Carregue os dados no banco de dado.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Saída:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Execute o comando a seguir para criar a função. Atualize o valor do argumento `-g` com seu endereço de gateway OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Depois de implantado, você deverá ver seu ponto de extremidade OpenFaaS recém-criado para a função.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Teste a função usando a ondulação. Atualize o endereço IP com o endereço do gateway OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Saída:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Você também pode testar a função na interface do usuário do amOpenFaaS.

![texto alternativo](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Próximos Passos

Você pode continuar a aprender com o workshop OpenFaaS por meio de um conjunto de laboratórios práticos que abordam tópicos como, por exemplo, como criar seu próprio bot do GitHub, consumindo segredos, exibindo métricas e dimensionamento automático.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
