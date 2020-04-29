---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um recurso de instância de contentores Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876457"
---
## <a name="create-an-azure-container-instance-resource"></a>Criar um recurso azure container instance

1. Vá à página [Criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) instâncias de contentores.

2. No separador **Basics,** introduza os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione a sua subscrição.|
    |Grupo de recursos|Selecione o grupo de recursos `cognitive-services`disponíveis ou crie um novo, como .|
    |Nome do contentor|Introduza um `cognitive-container-instance`nome como . O nome deve estar nas tampas inferiores.|
    |Localização|Selecione uma região para implantação.|
    |Tipo de imagem|Se a sua imagem de contentor for armazenada num registo `Public`de contentores que não exija credenciais, escolha . Se aceder à imagem do seu `Private`recipiente requer credenciais, escolha . Consulte [os repositórios e imagens](../../cognitive-services-container-support.md#container-repositories-and-images) do contentor para `Public` `Private` obter detalhes sobre se a imagem do recipiente é ou não ("Visualização pública"). |
    |Nome da imagem|Insira a localização do contentor dos Serviços Cognitivos. A localização é o que é `docker pull` usado como argumento para o comando. Consulte os [repositórios e imagens](../../cognitive-services-container-support.md#container-repositories-and-images) do recipiente para os nomes de imagem disponíveis e o seu repositório correspondente.<br><br>O nome da imagem deve ser totalmente qualificado especificando três partes. Primeiro, o registo do contentor, depois o repositório, finalmente o nome da imagem: `<container-registry>/<repository>/<image-name>`.<br><br>Aqui está um `mcr.microsoft.com/azure-cognitive-services/keyphrase` exemplo, representaria a imagem de extração de frases-chave no Registo de Contentores da Microsoft sob o repositório dos Serviços Cognitivos Azure. Outro exemplo `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` é, que representaria a imagem de Discurso a Texto no repositório da Microsoft do registo de contentores de visualização de contentores. |
    |Tipo OS|`Linux`|
    |Tamanho|Mude o tamanho para as recomendações sugeridas para o seu recipiente específico do Serviço Cognitivo:<br>2 núcleos CPU<br>4GB

3. No separador **Networking,** introduza os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Portas|Coloque a porta `5000`TCP para . Expõe o recipiente na porta 5000.|

4. No separador **Advanced,** introduza as **variáveis ambientais necessárias** para as definições de faturação do contentor do recurso Deporde:

    | Chave | Valor |
    |--|--|
    |`apikey`|Copiado **da** página keys do recurso. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiado da página **geral** do recurso.|
    |`eula`|`accept`|

5. Clique em **Rever e Criar**
6. Depois de passar os passes de validação, clique **em Criar** para terminar o processo de criação
7. Quando o recurso é implantado com sucesso, está pronto
