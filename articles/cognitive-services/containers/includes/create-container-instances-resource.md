---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contêiner do Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1679862b1660d3c8b2505c6e0c54f203f5d4665d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383522"
---
## <a name="create-an-azure-container-instance-resource"></a>Criar um recurso de instância de contêiner do Azure

1. Vá para a página [criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) para instâncias de contêiner.

2. Na guia **noções básicas** , insira os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione a sua subscrição.|
    |Grupo de recursos|Selecione o grupo de recursos disponível ou crie um novo, como `cognitive-services`.|
    |Nome do contentor|Insira um nome como `cognitive-container-instance`. O nome deve estar em minúsculas.|
    |Localização|Selecione uma região para implantação.|
    |Tipo de imagem|Se a imagem de contêiner estiver armazenada em um registro de contêiner que não requer credenciais, escolha `Public`. Se o acesso à sua imagem de contêiner exigir credenciais, escolha `Private`. Consulte [repositórios e imagens de contêiner](../../cognitive-services-container-support.md#container-repositories-and-images) para obter detalhes sobre se a imagem de contêiner está ou não `Public` ou `Private` ("visualização pública"). |
    |Nome da imagem|Insira o local do contêiner de serviços cognitivas. O local é o que é usado como um argumento para o comando `docker pull`. Consulte os [repositórios e as imagens do contêiner](../../cognitive-services-container-support.md#container-repositories-and-images) para obter os nomes de imagem disponíveis e seu repositório correspondente.<br><br>O nome da imagem deve ser totalmente qualificado, especificando três partes. Primeiro, o registro de contêiner, em seguida, o repositório, por fim, o nome da imagem: `<container-registry>/<repository>/<image-name>`.<br><br>Aqui está um exemplo, `mcr.microsoft.com/azure-cognitive-services/keyphrase` representaria a imagem de Extração de Frases-chave no registro de contêiner da Microsoft no repositório de serviços cognitivas do Azure. Outro exemplo é, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` que representaria a imagem de fala para texto no repositório da Microsoft do registro de contêiner de visualização do contêiner. |
    |Tipo de so|`Linux`|
    |Tamanho|Altere o tamanho para as recomendações sugeridas para seu contêiner de serviço cognitiva específico:<br>2 núcleos de CPU<br>4GB

3. Na guia **rede** , insira os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Portas|Defina a porta TCP como `5000`. Expõe o contêiner na porta 5000.|

4. Na guia **avançado** , insira as variáveis de **ambiente** necessárias para as configurações de cobrança de contêiner do recurso de instância de contêiner do Azure:

    | Chave | Valor |
    |--|--|
    |`apikey`|Copiado da página de **chaves** do recurso. É uma cadeia de caracteres de caractere alfanumérico 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiado da página de **visão geral** do recurso.|
    |`eula`|`accept`|

1. Clique em **revisar e criar**
1. Após a aprovação da validação, clique em **criar** para concluir o processo de criação
1. Quando o recurso for implantado com êxito, ele estará pronto