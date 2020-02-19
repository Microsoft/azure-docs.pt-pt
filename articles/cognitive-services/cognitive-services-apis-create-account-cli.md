---
title: Criar um recurso de Serviços Cognitivos utilizando o Azure CLI
titleSuffix: Azure Cognitive Services
description: Inicie-se com os Serviços Cognitivos Azure criando e subscrevendo um recurso utilizando a interface da linha de comando Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 3e5fd2315a5d352ed5abee9253b5c84eea3b73a9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462280"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Criar um recurso de Serviços Cognitivos utilizando a Interface de Linha de Comando Azure (CLI)

Utilize este quickstart para começar com os Serviços Cognitivos Azure utilizando a [Interface da Linha de Comando Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Os Serviços Cognitivos são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) do Azure que cria na sua subscrição Azure. Depois de criar o recurso, utilize as chaves e o ponto final gerados para autenticar as suas aplicações. 


Neste arranque rápido, você vai aprender a se inscrever para serviços cognitivos Azure e criar uma conta que tem uma subscrição de serviço único ou multi-serviço, utilizando a Interface de Linha de [Comando Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Estes serviços são representados por [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)Azure, que lhe permitem ligar a uma ou mais das APIs dos Serviços Cognitivos Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure válida - [Crie uma](https://azure.microsoft.com/free/) gratuitamente.
* Interface da linha de [comando Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Instale o Azure CLI e inscreva-se 

Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Para iniciar sessão na sua instalação local do CLI, execute o comando [de login az:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```console
az login
```

Também pode utilizar o botão **'Experimentar'** verde para executar estes comandos no seu navegador.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Criar um novo grupo de recursos dos Serviços Cognitivos Azure

Antes de criar um recurso dos Serviços Cognitivos, deve ter um grupo de recursos Azure para conter o recurso. Quando cria um novo recurso, tem a opção de criar um novo grupo de recursos ou de utilizar um existente. Este artigo mostra como criar um novo grupo de recursos.

### <a name="choose-your-resource-group-location"></a>Escolha a localização do seu grupo de recursos

Para criar um recurso, você precisará de uma das localizações Azure disponíveis para a sua subscrição. Pode recuperar uma lista de locais disponíveis com o comando de localização da [lista az.](/cli/azure/account#az-account-list-locations) A maioria dos Serviços Cognitivos pode ser acedida a partir de vários locais. Escolha o mais próximo de si ou veja quais os locais disponíveis para o serviço.

> [!IMPORTANT]
> * Lembre-se da sua localização Azure, pois vai precisar quando ligar para os Serviços Cognitivos Azure.
> * A disponibilidade de alguns Serviços Cognitivos pode variar por região. Para mais informações, consulte [os produtos Azure por região.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Depois de ter a sua localização azul, crie um novo grupo de recursos no Azure CLI utilizando o [grupo Az criar](/cli/azure/group#az-group-create) comando.

No exemplo abaixo, substitua a localização azul `westus2` por uma das localizações do Azure disponíveis para a sua subscrição.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Escolha um serviço cognitivo e um nível de preços

Ao criar um novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o [nível](https://azure.microsoft.com/pricing/details/cognitive-services/) de preços (ou sku) que deseja. Utilizará esta e outras informações como parâmetros na criação do recurso.

### <a name="multi-service"></a>Multi-serviço

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Múltiplos serviços. Consulte a página [de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) para mais detalhes.            | `CognitiveServices`     |


> [!NOTE]
> Muitos dos Serviços Cognitivos abaixo têm um nível gratuito que pode usar para experimentar o serviço. Para utilizar o nível livre, use `F0` como sku para o seu recurso.

### <a name="vision"></a>Visão

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Imagem Digitalizada            | `ComputerVision`          |
| Visão Personalizada - Previsão | `CustomVision.Prediction` |
| Visão Personalizada - Formação   | `CustomVision.Training`   |
| Rostos                       | `Face`                    |
| Reconhecedor de Formato            | `FormRecognizer`          |
| Reconhecedor de Tinta Digital             | `InkRecognizer`           |

### <a name="search"></a>Pesquisa

| Serviço            | Tipo                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidades do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Voz

| Serviço            | Tipo                 |
|--------------------|----------------------|
| Serviços de Voz    | `SpeechServices`     |
| Reconhecimento de Voz | `SpeakerRecognition` |

### <a name="language"></a>Idioma

| Serviço            | Tipo                |
|--------------------|---------------------|
| Compreensão da forma | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de Texto     | `TextAnalytics`     |
| Tradução de Texto   | `TextTranslation`   |

### <a name="decision"></a>Decisão

| Serviço           | Tipo               |
|-------------------|--------------------|
| Detetor de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |

Você pode encontrar uma lista de "tipos" do Serviço Cognitivo disponível com o comando de listas de tipos [de conta az cognitivos:](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Adicione um novo recurso ao seu grupo de recursos

Para criar e subscrever um novo recurso dos Serviços Cognitivos, utilize a [conta az cognitiveservices criar](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) comando. Este comando adiciona um novo recurso faturado ao grupo de recursos criado anteriormente. Ao criar o seu novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o seu nível de preços (ou sku) e uma localização Azure:

Pode criar um recurso F0 (gratuito) para o Detetor de Anomalias, denominado `anomaly-detector-resource` com o comando abaixo.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Pegue as chaves para o seu recurso

Para iniciar sessão na instalação local da Interface de Linha de Comando (CLI), utilize o comando [de login az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```console
az login
```

Utilize o comando da lista de chaves da [conta az cognitiveservices](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) para obter as chaves do seu recurso do Serviço Cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Níveis de preços e faturação

Os níveis de preços (e o valor que obtém faturado) baseiam-se no número de transações que envia utilizando as suas informações de autenticação. Cada nível de preços especifica:
* número máximo de transações permitidas por segundo (TPS).
* funcionalidades de serviço ativadas dentro do nível de preços.
* O custo de uma quantidade predefinida de transações. Ir acima deste valor irá causar uma taxa extra, conforme especificado nos detalhes de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) para o seu serviço.

## <a name="get-current-quota-usage-for-your-resource"></a>Obtenha o uso atual da quota para o seu recurso

Utilize o comando de utilização da lista de [serviços cognitivos az](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) para obter o uso do seu recurso do Serviço Cognitivo.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover um recurso dos Serviços Cognitivos, pode eliminá-lo ou ao grupo de recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos contidos no grupo.

Para remover o grupo de recursos e os seus recursos associados, utilize o grupo AZ eliminar o comando.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Veja também

* [Autenticação de pedidos aos Serviços Cognitivos Azure](authentication.md)
* [O que é serviços cognitivos Azure?](Welcome.md)
* [Apoio à linguagem natural](language-support.md)
* [Suporte de contentores Docker](cognitive-services-container-support.md)
