---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contêiner do Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051189"
---
## <a name="create-an-azure-container-instance-resource"></a>Criar um recurso de instância de contêiner do Azure

1. Vá para a página [criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) para instâncias de contêiner.

2. Na guia **noções básicas** , insira os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Subscription|Selecione a sua subscrição.|
    |Resource group|Selecione o grupo de recursos disponível ou crie um novo, `cognitive-services`como.|
    |Nome do contentor|Insira um nome como `cognitive-container-instance`. O nome deve estar em minúsculas.|
    |Location|Selecione uma região para implantação.|
    |Tipo de imagem|Se a imagem de contêiner estiver armazenada em um registro de contêiner que não requer `Public`credenciais, escolha. Se o acesso à sua imagem de contêiner exigir `Private`credenciais, escolha. Consulte [repositórios e imagens de contêiner](../../cognitive-services-container-support.md#container-repositories-and-images) para obter detalhes sobre se a imagem de contêiner é `Public` ou não ou `Private` ("visualização pública"). |
    |Nome de imagem|Insira o local do contêiner de serviços cognitivas. O local é o que é usado como um argumento para `docker pull` o comando. Consulte os [repositórios e as imagens do contêiner](../../cognitive-services-container-support.md#container-repositories-and-images) para obter os nomes de imagem disponíveis e seu repositório correspondente.<br><br>O nome da imagem deve ser totalmente qualificado, especificando três partes. Primeiro, o registro de contêiner, em seguida, o repositório, por fim `<container-registry>/<repository>/<image-name>`, o nome da imagem:.<br><br>Aqui está um exemplo, `mcr.microsoft.com/azure-cognitive-services/keyphrase` que representaria a imagem de extração de frases-chave no registro de contêiner da Microsoft no repositório de serviços cognitivas do Azure. Outro exemplo é, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` que representaria a imagem de fala para texto no repositório da Microsoft do registro de contêiner de visualização do contêiner. |
    |Tipo de SO|`Linux`|
    |Size|Altere o tamanho para as recomendações sugeridas para seu contêiner de serviço cognitiva específico:<br>2 núcleos de CPU<br>4 GB

3. Na guia **rede** , insira os seguintes detalhes:

    |Definição|Value|
    |--|--|
    |Portas|Defina a porta TCP como `5000`. Expõe o contêiner na porta 5000.|

4. Na guia **avançado** , insira as variáveis de **ambiente** necessárias para as configurações de cobrança de contêiner do recurso de instância de contêiner do Azure:

    | Chave | Value |
    |--|--|
    |`apikey`|Copiado da página de **chaves** do recurso. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiado da página de **visão geral** do recurso.|
    |`eula`|`accept`|

1. Clique em revisar **e criar**
1. Após a aprovação da validação, clique em **criar** para concluir o processo de criação
1. Quando o recurso for implantado com êxito, ele estará pronto