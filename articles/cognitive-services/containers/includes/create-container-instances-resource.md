---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de recipiente Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 24f6052c436b73d0075371fa74160d21826e2209
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97865955"
---
## <a name="create-an-azure-container-instance-resource-using-the-azure-portal"></a>Criar um recurso Azure Container Instance utilizando o portal Azure

1. Aceda à página [Criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) instâncias para contentores.

2. No separador **Básicos, insira** os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione a sua subscrição.|
    |Grupo de recursos|Selecione o grupo de recursos disponível ou crie um novo, como `cognitive-services` .|
    |Nome do contentor|Insira um nome como `cognitive-container-instance` . O nome deve estar em tampas mais baixas.|
    |Localização|Selecione uma região para implantação.|
    |Tipo de imagem|Se a imagem do seu recipiente estiver guardada num registo de contentores que não exija credenciais, escolha `Public` . Se aceder à imagem do seu contentor requer credenciais, escolha `Private` . Consulte [os repositórios e imagens](../container-image-tags.md) dos contentores para obter mais informações sobre se a imagem do contentor é ou não `Public` `Private` ("Visualização pública"). |
    |Nome da imagem|Insira o local do contentor dos Serviços Cognitivos. A localização é o que é usado como argumento para o `docker pull` comando. Consulte os [repositórios e imagens](../container-image-tags.md) dos recipientes para ver os nomes de imagem disponíveis e o respetivo repositório.<br><br>O nome da imagem deve estar totalmente qualificado especificando três partes. Primeiro, o registo do contentor, depois o repositório, finalmente o nome da imagem: `<container-registry>/<repository>/<image-name>` .<br><br>Aqui está um exemplo, `mcr.microsoft.com/azure-cognitive-services/keyphrase` representaria a imagem de extração de frase-chave no registo do contentor da Microsoft sob o repositório de Serviços Cognitivos Azure. Outro exemplo é `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` o que representaria a imagem do Discurso a Texto no repositório da Microsoft do registo do contentor de pré-visualização do contentor. |
    |Tipo de SO|`Linux`|
    |Tamanho|Altere o tamanho das recomendações sugeridas para o seu recipiente de Serviço Cognitivo específico:<br>2 núcleos de CPU<br>4GB

3. No **separador Networking,** insira os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Portas|Desa estale a porta TCP `5000` para . Expõe o contentor no porto 5000.|

4. No separador **Avançado,** **insira as variáveis ambientais necessárias** para as definições de faturação do contentor do recurso Instância do Recipiente Azure:

    | Chave | Valor |
    |--|--|
    |`ApiKey`|Copiado a partir das **Chaves e** página de ponta do recurso. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`Billing`| O url do ponto final copiado a partir da página **keys e ponto final** do recurso.|
    |`Eula`|`accept`|

5. Clique **em Rever e criar**
6. Após a validação passar, clique em **Criar** para terminar o processo de criação
7. Quando o recurso é implementado com sucesso, está pronto
