---
title: Criar uma conta de serviços cognitivos com a CLI do Azure
titlesuffix: Azure Cognitive Services
description: Como criar uma conta de APIs serviços cognitivos do Azure com a CLI do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454897"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Criar uma conta de serviços cognitivos com Interface(CLI) de linha de comandos do Azure

Neste início rápido, irá aprender a inscrever-se para os serviços cognitivos do Azure e criar uma conta que tenha uma subscrição de serviço único ou múltiplos serviço, usando o [Interface(CLI) de linha de comandos do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Estes serviços são representados pelo Azure [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), que permitem-lhe ligar a um ou mais das APIs de serviços cognitivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure válida. [Criar uma conta](https://azure.microsoft.com/free/) gratuitamente.
* O [Interface(CLI) de linha de comandos do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Instalar a CLI do Azure e iniciar sessão 

Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Para iniciar sessão na sua instalação local da CLI, execute o [início de sessão az](https://docs.microsoft.com/cli/azure/reference-index#az-login) comando:

```console
az login
```

Também pode utilizar o verde **experimentar** botão para executar estes comandos no seu browser.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Criar um novo grupo de recursos de serviços cognitivos do Azure

As suas subscrições para os serviços cognitivos são representadas por recursos do Azure. Cada conta dos serviços cognitivos (e seu recurso do Azure associado) têm de pertencer a um grupo de recursos do Azure.

### <a name="choose-your-resource-group-location"></a>Escolha a localização do grupo de recursos

Para criar um recurso, terá uma das localizações do Azure disponíveis para a sua subscrição. Pode obter uma lista das localizações disponíveis com o [lista de conta de az-locations](/cli/azure/account#az_account_list) comando. A maioria dos serviços cognitivos podem ser acedidos a partir de várias localizações. Escolher o mais perto de si ou ver as localizações de estão disponíveis para o serviço.

> [!IMPORTANT]
> * Lembre-se a localização do Azure, pois irá precisar ao chamar os serviços cognitivos do Azure.
> * A disponibilidade de alguns serviços cognitivos pode variar consoante a região. Para obter mais informações, consulte [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Depois de ter sua localização do azure, crie um novo grupo de recursos na CLI do Azure utilizando o [criar grupo az](/cli/azure/group#az_group_create) comando.

No exemplo abaixo, substitua a localização do azure `westus2` com uma das localizações do Azure disponíveis para a sua subscrição.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Escolher um serviço cognitivo e o escalão de preço

Ao criar um novo recurso, precisará saber o "kind" do serviço que pretende utilizar, juntamente com o [escalão de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou sku) que pretende. Irá utilizar esta e outras informações como parâmetros ao criar o recurso.

> [!NOTE]
> Muitos serviços cognitivos tem um escalão gratuito que pode utilizar para experimentar o serviço. Para utilizar o escalão gratuito, utilize `F0` como o sku para o seu recurso.

### <a name="vision"></a>Visão

| Serviço                    | tipo                      |
|----------------------------|---------------------------|
| Imagem Digitalizada            | `ComputerVision`          |
| Visão personalizada - previsão | `CustomVision.Prediction` |
| Personalizado de visão - treinamento   | `CustomVision.Training`   |
| API Face                   | `Face`                    |
| Reconhecedor de Formato            | `FormRecognizer`          |
| Reconhecedor de Tinta Digital             | `InkRecognizer`           |

### <a name="search"></a>Pesquisa

| Serviço            | tipo                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidades do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Voz

| Serviço            | tipo                 |
|--------------------|----------------------|
| Serviços de Voz    | `SpeechServices`     |
| Reconhecimento de Voz | `SpeakerRecognition` |

### <a name="language"></a>Idioma

| Serviço            | tipo                |
|--------------------|---------------------|
| Compreensão de formulário | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de Texto     | `TextAnalytics`     |
| Tradução de Texto   | `TextTranslation`   |

### <a name="decision"></a>Decisão

| Serviço           | tipo               |
|-------------------|--------------------|
| Detetor de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |

Pode encontrar uma lista dos serviço cognitivos disponível "tipos" com o [az cognitiveservices account lista tipos](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) comando:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Adicionar um novo recurso ao seu grupo de recursos

Para criar e subscrever um novo recurso de serviços cognitivos, utilize o [criar conta de cognitiveservices az](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) comando. Este comando adiciona um novo recurso sujeito a faturação para o grupo de recursos que criou anteriormente. Ao criar o novo recurso, precisará saber o "kind" do serviço que pretende utilizar, juntamente com o seu escalão de preço (ou sku) e uma localização do Azure:

Pode criar um recurso (gratuito) F0 para detetor de anomalias, com o nome `anomaly-detector-resource` com o comando abaixo.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Obter as chaves para a sua subscrição

Para iniciar sessão na sua instalação local do Interface(CLI) a linha de comandos, utilize o [início de sessão az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) comando.

```console
az login
```

Utilize o [az cognitiveservices account lista de chaves](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) comando para obter as chaves para o seu recurso de serviço cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma subscrição de serviços cognitivos, é possível eliminar o recurso ou grupo de recursos. Eliminar o grupo de recursos também elimina a quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos e respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o comando de eliminação do grupo az.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Consulte também

* [Autenticar pedidos para os serviços cognitivos do Azure](authentication.md)
* [O que é o serviços cognitivos do Azure?](Welcome.md)
* [Suporte de linguagem natural](language-support.md)
* [Suporte de contentor do docker](cognitive-services-container-support.md)
