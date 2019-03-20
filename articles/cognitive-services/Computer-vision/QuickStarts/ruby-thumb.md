---
title: 'Início rápido: Gere uma miniatura - REST, Ruby'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura de uma imagem através da API de Imagem Digitalizada com o Ruby.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bc0941175538c36190999e8c6d191fb8c3701c2b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839632"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-ruby-in-computer-vision"></a>Início rápido: Gere uma miniatura com a REST API e o Ruby de imagem digitalizada

Neste guia de início rápido, irá gerar uma miniatura de uma imagem através da API REST de Imagem Digitalizada. Com o método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Obter Miniatura), pode gerar uma miniatura de uma imagem. O utilizador especifica a altura e a largura, que podem ser diferentes da proporção da imagem introduzida. Imagem digitalizada utiliza o corte inteligente para inteligentemente identificar a área de interesse e gerar as coordenadas de recorte com base na região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [Ruby](https://www.ruby-lang.org/en/downloads/) 2.4.x ou posterior instalado.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Para obter uma chave de subscrição, veja [Obter Chaves de Subscrição](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Copie o código seguinte para um editor de texto.
1. Faça as alterações seguintes ao código, onde for necessário:
    1. Substitua `<Subscription Key>` pela sua chave de subscrição.
    1. Substitua `https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze` pelo URL de ponto final do método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Obter Miniatura) da região do Azure onde obteve as chaves de subscrição, se necessário.
    1. Opcionalmente, substitua `https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\` pelo URL de uma imagem diferente para a qual pretende gerar uma miniatura.
1. Guarde o código como um ficheiro com uma extensão `.rb`. Por exemplo, `get-thumbnail.rb`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `ruby` para executar o exemplo. Por exemplo, `ruby get-thumbnail.rb`.

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail')
uri.query = URI.encode_www_form({
    # Request parameters
    'width' => '100',
    'height' => '100',
    'smartCropping' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/" +
    "Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

#puts response.body
```

## <a name="examine-the-response"></a>Examinar a resposta

É devolvida uma resposta com êxito como dados binários, que representa os dados da imagem da miniatura. Se o pedido falhar, a resposta é apresentada na janela da consola. A resposta do pedido falhado contém um código de erro e uma mensagem para ajudar a determinar o que correu mal.

## <a name="next-steps"></a>Passos Seguintes

Explore a API de Imagem Digitalizada utilizada para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto impresso e manuscrito. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
