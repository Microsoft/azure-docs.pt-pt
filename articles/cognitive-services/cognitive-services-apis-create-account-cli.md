---
title: Criar um recurso de Serviços Cognitivos utilizando o Azure CLI
titleSuffix: Azure Cognitive Services
description: Inicie-se com os Serviços Cognitivos Azure criando e subscrevendo um recurso utilizando a interface da linha de comando Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços ai
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: c1e8b260f6c06d8f26f5ba99b4fb02edfa5e8656
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472100"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Quickstart: Criar um recurso de Serviços Cognitivos utilizando a Interface Command-Line Azure(CLI)

Utilize este quickstart para começar com os Serviços Cognitivos Azure utilizando a [Interface da Linha de Comando Azure (CLI)](/cli/azure/install-azure-cli).

Os Azure Cognitive Services são serviços de base na nuvem com APIs rest, e SDKs da biblioteca de clientes disponíveis para ajudar os desenvolvedores a construir inteligência cognitiva em aplicações sem ter inteligência artificial direta (IA) ou habilidades ou conhecimentos de ciência de dados. O Azure Cognitive Services permite que os desenvolvedores adicionem facilmente funcionalidades cognitivas nas suas aplicações com soluções cognitivas que podem ver, ouvir, falar, entender e até começar a raciocinar.

Os Serviços Cognitivos são representados pelos [recursos](../azure-resource-manager/management/manage-resources-portal.md) Azure que cria na sua subscrição Azure. Depois de criar o recurso, utilize as teclas e o ponto final gerados para autenticar as suas aplicações.

Neste arranque rápido, você aprenderá a se inscrever para a Azure Cognitive Services e criar uma conta que tenha uma subscrição de serviço único ou multi-serviço, utilizando a [Interface da Linha de Comando Azure (CLI)](/cli/azure/install-azure-cli). Estes serviços são representados por [recursos](../azure-resource-manager/management/manage-resources-portal.md)Azure, que lhe permitem ligar a uma ou mais das APIs dos Serviços Cognitivos Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição válida do Azure - [Crie uma](https://azure.microsoft.com/free/cognitive-services) gratuitamente.
* [A Interface da Linha de Comando Azure (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Instale o CLI Azure e inscreva-se

Instale o [Azure CLI](/cli/azure/install-azure-cli). Para iniciar sessão na instalação local do CLI, execute o comando [de login az:](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

Também pode utilizar o botão verde **Try It** para executar estes comandos no seu navegador.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Criar um novo grupo de recursos Azure Cognitive Services

Antes de criar um recurso de Serviços Cognitivos, deve ter um grupo de recursos Azure para conter o recurso. Quando cria um novo recurso, tem a opção de criar um novo grupo de recursos ou de utilizar um existente. Este artigo mostra como criar um novo grupo de recursos.

### <a name="choose-your-resource-group-location"></a>Escolha a localização do seu grupo de recursos

Para criar um recurso, precisará de uma das localizações do Azure disponíveis para a sua subscrição. Pode recuperar uma lista de locais disponíveis com o comando [de localizações da lista de conta az.](/cli/azure/account#az-account-list-locations) A maioria dos Serviços Cognitivos pode ser acedida a partir de vários locais. Escolha o mais próximo de si, ou veja quais os locais disponíveis para o serviço.

> [!IMPORTANT]
> * Lembre-se da sua localização Azure, como vai precisar quando ligar para os Serviços Cognitivos Azure.
> * A disponibilidade de alguns Serviços Cognitivos pode variar por região. Para mais informações, consulte [os produtos Azure por região.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Depois de ter a sua localização Azure, crie um novo grupo de recursos no CLI Azure utilizando o comando de criação do [grupo AZ.](/cli/azure/group#az-group-create)

No exemplo abaixo, substitua a localização Azure `westus2` por uma das localizações Azure disponíveis para a sua subscrição.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Escolha um nível de serviço cognitivo e preços

Ao criar um novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou sku) que pretende. Utilizará esta e outras informações como parâmetros ao criar o recurso.

### <a name="multi-service"></a>Multi-serviço

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Vários serviços. Consulte [a](https://azure.microsoft.com/pricing/details/cognitive-services/) página de preços para mais detalhes.            | `CognitiveServices`     |


> [!NOTE]
> Muitos dos Serviços Cognitivos abaixo têm um nível gratuito que você pode usar para experimentar o serviço. Para utilizar o nível livre, utilize `F0` como sku para o seu recurso.

### <a name="vision"></a>Visão

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Imagem Digitalizada            | `ComputerVision`          |
| Visão Personalizada - Previsão | `CustomVision.Prediction` |
| Visão Personalizada - Treino   | `CustomVision.Training`   |
| Rostos                       | `Face`                    |
| Reconhecedor de Formato            | `FormRecognizer`          |
| Reconhecedor de Tinta Digital             | `InkRecognizer`           |

### <a name="speech"></a>Voz

| Serviço            | Tipo                 |
|--------------------|----------------------|
| Serviços de Voz    | `SpeechServices`     |
| Reconhecimento de Voz | `SpeakerRecognition` |

### <a name="language"></a>Linguagem

| Serviço            | Tipo                |
|--------------------|---------------------|
| Compreensão de formulários | `FormUnderstanding` |
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

Pode encontrar uma lista de "tipos" disponíveis do Serviço Cognitivo com o comando [de tipos de listas de contas az cognitiveservices:](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Adicione um novo recurso ao seu grupo de recursos

Para criar e subscrever um novo recurso de Serviços Cognitivos, utilize a [conta az cognitiveservices criar](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create) comando. Este comando adiciona um novo recurso faturado ao grupo de recursos criado anteriormente. Ao criar o seu novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o seu nível de preços (ou sku) e uma localização Azure:

Pode criar um recurso F0 (gratuito) para detetor de anomalias, nomeado `anomaly-detector-resource` com o comando abaixo.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Obtenha as chaves do seu recurso

Para iniciar sessão na instalação local da Interface Command-Line (CLI), utilize o comando [de login az.](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

Use o comando da lista de chaves de [conta az cognitiveservices](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) para obter as chaves do seu recurso de Serviço Cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Níveis de preços e faturação

Os níveis de preços (e o valor que recebe faturado) baseiam-se no número de transações que envia usando as suas informações de autenticação. Cada nível de preços especifica:
* número máximo de transações permitidas por segundo (TPS).
* funcionalidades de serviço ativadas dentro do nível de preços.
* O custo de um número predefinido de transações. Ir acima deste valor irá causar uma taxa extra, conforme especificado nos detalhes de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do seu serviço.

## <a name="get-current-quota-usage-for-your-resource"></a>Obtenha o uso atual da quota para o seu recurso

Utilize o comando [de utilização da lista de serviços cognitivos az](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-usage) para obter o uso do seu recurso de Serviço Cognitivo.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover um recurso de Serviços Cognitivos, pode eliminá-lo ou ao grupo de recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos contidos no grupo.

Para remover o grupo de recursos e os seus recursos associados, utilize o comando de eliminação do grupo az.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Ver também

* [Autenticar pedidos aos Serviços Cognitivos da Azure](authentication.md)
* [O que é a Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Suporte linguístico natural](language-support.md)
* [Suporte de contentor estivador](cognitive-services-container-support.md)
