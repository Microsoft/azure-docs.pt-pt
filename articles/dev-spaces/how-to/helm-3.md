---
title: Configure o seu cluster Dev Spaces Azure para utilizar o Helm 3 (pré-visualização)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Saiba como configurar o seu cluster Dev Spaces para usar o Helm 3
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202263"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Configure o seu cluster Dev Spaces Azure para utilizar o Helm 3 (pré-visualização)

A Azure Dev Spaces utiliza o Helm 2 por padrão para instalar serviços de utilizador em espaços de v no seu cluster AKS. Pode ativar o Azure Dev Spaces a utilizar o Helm 3 em vez de o Helm 2 instalar serviços de utilizador em espaços de v. Independentemente da versão que a Helm Azure Dev Spaces utiliza para instalar serviços de utilizador, pode continuar a utilizar o cliente Helm 2 ou 3 para gerir os seus próprios lançamentos no mesmo cluster.

Quando ativa o Helm 3, o Azure Dev Spaces comporta-se de forma diferente ao instalar serviços de utilizador em espaços de v de v das seguintes formas:

* Tiller já não está implantado para o seu aglomerado no espaço de nome *azds.*
* As lojas Helm divulgam informações no espaço de nome onde um serviço é instalado em vez do espaço de nome *azds.*
* As informações de libertação do leme 3 permanecem no espaço de nome onde um serviço é instalado após a eliminação de um controlador.
* Pode interagir diretamente com qualquer versão gerida pela Azure Dev Spaces no seu cluster utilizando o cliente Helm 3.

Neste guia, você aprenderá a permitir que o Helm 3 para a Azure Dev Spaces instale serviços de utilizador em espaços de v.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Registe a função de pré-visualização Helm3Preview

Para permitir que o Azure Dev Spaces utilize o Helm 3 para instalar serviços de utilizador em espaços de v, primeiro ative a bandeira da funcionalidade *Helm3Preview* na sua subscrição utilizando o comando de registo de *funcionalidades az:*

> [!WARNING]
> Qualquer cluster AKS que ative o Azure Dev Spaces com a bandeira de recurso *Helm3Preview* utilizará esta experiência de pré-visualização. Para continuar a permitir espaços Azure Dev totalmente suportados em clusters AKS, não permita funcionalidades de pré-visualização nas subscrições de produção. Utilize uma assinatura Azure de teste ou desenvolvimento separada para testar funcionalidades de pré-visualização.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Leva alguns minutos para a inscrição ser completada. Verifique o estado de registo utilizando o comando de apresentação de *recurso az:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Quando o *estado* estiver *registado,* refresque o registo da *Microsoft.DevSpaces* utilizando o registo do *fornecedor az:*

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações enquanto esta funcionalidade se encontra em pré-visualização:

* Não pode utilizar esta funcionalidade em clusters AKS com cargas de trabalho existentes. Tens de criar um novo aglomerado AKS.

## <a name="create-your-cluster"></a>Crie o seu cluster

Crie um novo cluster AKS numa região que tenha esta funcionalidade de pré-visualização. Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um novo cluster AKS chamado *MyAKS* na região *centro-sul dos EUA:*

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Ativar espaços Azure Dev

Utilize o comando *de espaços de utilização para* ativar os Espaços Dev no seu cluster AKS e siga as instruções. O comando abaixo permite a Dev Spaces no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço de dev padrão.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Verifique se a Dev Spaces está a executar o Leme 3

Verifique se o leme não está a funcionar enumerando as implementações no espaço de nome *szds:*

```cmd
kubectl get deployment -n azds
```

Confirme que o *espaço de identificação do leme* não está a funcionar no espaço de nome szds. Por exemplo:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md