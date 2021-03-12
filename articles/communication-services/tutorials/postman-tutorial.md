---
title: Tutorial - Assine e faça pedidos para a API SMS da ACS com carteiro
titleSuffix: An Azure Communication Services tutorial
description: Saiba como assinar e faz pedidos de ACS com Carteiro para enviar uma Mensagem SMS.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/08/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0d98ae1ef537b06858b8c03df65bbcdd27984c4f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022326"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Tutorial: Assine e faça pedidos com carteiro
Neste tutorial, vamos configurar e usar o Carteiro para fazer um pedido contra os serviços de comunicação da Azure (ACS) usando HTTP. No final deste tutorial, terá enviado com sucesso uma mensagem SMS usando ACS e Carteiro e poderá usar o Carteiro para explorar outras APIs dentro do ACS.

Neste tutorial estaremos:
> [!div class="checklist"]
> * Descarregar carteiro
> * Configuração do Carteiro para assinar pedidos HTTP
> * Fazendo um pedido contra a API de SMS da ACS para enviar uma mensagem.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) A conta gratuita dá-lhe 200 dólares em créditos Azure para experimentar qualquer combinação de serviços.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Saiba como criar um recurso de Serviços de Comunicação.](../quickstarts/create-communication-resource.md)
- Um número de telefone ACS que pode enviar mensagens SMS, consulte o nosso [Obter um número de telefone](../quickstarts/telephony-sms/get-phone-number.md) para obter um.

## <a name="downloading-and-installing-postman"></a>Descarregar e instalar carteiro

Carteiro, é uma aplicação de desktop que é capaz de fazer pedidos de API contra qualquer API HTTP. É comumente usado para testar e explorar APIs. Vamos descarregar a mais recente [versão desktop do site do Carteiro.](https://www.postman.com/downloads/) O Carteiro tem versões para Windows, Mac e Linux, por isso descarregue a versão apropriada para o seu sistema operativo. Uma vez descarregado, abra a aplicação. Será apresentado com um ecrã inicial, que lhe pede para iniciar sessão ou para criar uma conta do Carteiro. A criação de uma conta é opcional e pode ser ignorada clicando no link "Saltar e ir para a app". A criação de uma conta salvará as definições de pedido de API para o Carteiro, o que lhe permite recolher os seus pedidos noutros computadores.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="O ecrã Start do Carteiro mostrando a capacidade de criar uma conta ou de saltar e ir para a aplicação.":::

Uma vez criada uma conta ou ignorado a criação de uma, deve agora ver a janela principal do Carteiro.

## <a name="creating-and-configuring-a-postman-collection"></a>Criar e configurar uma coleção de Carteiro

Carteiro, pode organizar pedidos de muitas maneiras. Para efeitos deste tutorial. Vamos criar uma Coleção de Carteiros. Para isso, selecione o botão de cobranças no lado esquerdo da aplicação:

:::image type="content" source="media/postman/collections-tab.png" alt-text="O ecrã principal do carteiro com o separador Collections em destaque.":::

Uma vez selecionado, clique em "Criar nova Coleção", para iniciar o processo de criação de coleção. Uma nova guia abrirá na área central do Carteiro. Diga a coleção o que quiser. Aqui a coleção chama-se "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="Carteiro com uma Coleção ACS abriu e o nome da coleção em destaque.":::

Assim que a sua coleção for criada e nomeada, está pronta para a configurar.

### <a name="adding-collection-variables"></a>Adicionar variáveis de coleção

Para lidar com a autenticação e facilitar os pedidos, especificaremos duas variáveis de recolha dentro da recém-criada coleção ACS. Estas variáveis estão disponíveis para todos os pedidos dentro da sua coleção ACS. Para começar a criar variáveis, visite o Separador variável da Coleção.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Carteiro com separador de variáveis da ACS Collection.":::

Uma vez no separador de recolha, crie duas variáveis:
- chave - Esta variável deve ser uma das suas chaves da página-chave dos Serviços de Comunicação Azure dentro do portal Azure. Por exemplo, `oW...A==`.
- ponto final - Esta variável deve ser o ponto final dos serviços de comunicação Azure a partir da página-chave. **Certifique-se de que remove o corte de fuga**. Por exemplo, `https://contoso.communication.azure.com`.

Introduza estes valores na coluna "Valor Inicial" do ecrã de variáveis. Uma vez introduzido, prima o botão "Persistir Tudo" logo acima da mesa à direita. Quando configurado corretamente, o ecrã do carteiro deve ser parecido com isto:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Carteiro com variáveis da ACS Collection configurada corretamente.":::

Pode aprender mais sobre variáveis lendo a [documentação do Carteiro sobre elas.](https://learning.postman.com/docs/sending-requests/variables)

### <a name="creating-a-pre-request-script"></a>Criação de um script de pré-pedido

O próximo passo é criar um Script pré-pedido dentro do Carteiro. Um script de pré-pedido, é um script que é executado antes de cada pedido no Carteiro e pode modificar ou alterar parâmetros de pedido em seu nome. Vamos usar isto para assinar os nossos pedidos HTTP para que possam ser autorizados pelos Serviços da ACS. Para mais informações sobre os requisitos de Assinatura, pode ler o [nosso guia sobre autenticação.](https://docs.microsoft.com/rest/api/communication/authentication)

Vamos criar este script dentro da Coleção de modo a que seja executado em qualquer pedido dentro da coleção. Para isso, no separador de recolha clique no sub-separador "Script de pré-pedido".

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Carteiro com script pré-pedido da ACS Collection Sub-Tab Selecionado.":::

Neste Sub-Separador, pode criar um script de pré-pedido inserindo-o na área de texto abaixo. Pode ser mais fácil escrever isto, dentro de um editor de código completo, como [o Visual Studio Code,](https://code.visualstudio.com/) antes de o colar quando estiver concluído. Vamos analisar cada parte do guião neste tutorial. Sinta-se livre para saltar até o fim se quiser copiá-lo no Carteiro e começar. Vamos começar a escrever o guião.

### <a name="writing-the-pre-request-script"></a>Escrevendo o script de pré-pedido

A primeira coisa que vamos fazer é criar uma cadeia de tempo universal coordenado (UTC) e adicioná-lo ao cabeçalho HTTP "Data". Também armazenamos esta cadeia numa variável para usá-la mais tarde ao assinar:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Em seguida, vamos hash o corpo pedido usando SHA 256 e colocá-lo no `x-ms-content-sha256` cabeçalho. O carteiro inclui [algumas bibliotecas padrão](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) para hashing e assinatura global, por isso não precisamos instalá-las ou exigi-las:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Agora, vamos usar a nossa variável de ponto final previamente especificado para discernir o valor para o cabeçalho HTTP Host. Temos de o fazer porque o cabeçalho do anfitrião só é definido depois de este script ser processado:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Com esta informação criada, podemos agora criar a cadeia, que vamos assinar para o Pedido HTTP, esta é composta por vários valores previamente criados:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Por último, precisamos de assinar esta cadeia usando a nossa chave ACS e, em seguida, adicioná-lo ao nosso pedido no `Authorization` cabeçalho:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>O roteiro final de pré-pedido

O guião final de pré-pedido deve ser algo assim:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Introduza ou cole este script final, na área de texto dentro do Separador script pré-pedido:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Carteiro com um script de pré-pedido da ACS Collection introduzido.":::

Uma vez introduzido, prima CTRL + S ou prima o botão de guardar isto guardará o script para a coleção. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Botão de script de pré-pedido do Carteiro.":::

## <a name="creating-a-request-in-postman"></a>Criando um pedido no Carteiro

Agora que tudo está preparado, estamos prontos para criar um pedido de ACS dentro do Carteiro. Para começar, clique no ícone plus(+) ao lado da Coleção ACS:

:::image type="content" source="media/postman/create-request.png" alt-text="Botão de acompanhante do carteiro.":::

Isto criará um novo separador para o nosso pedido dentro do Carteiro. Com ele criado, precisamos configurá-lo. Vamos fazer um pedido contra a SMS Enviar API para que não se esqueça de remeter para a [documentação desta API para assistência.](https://docs.microsoft.com/rest/api/communication/sms/send) Vamos configurar o pedido do Carteiro.

Comece por configurar, o tipo de pedido para `POST` e insira `{{endpoint}}/sms?api-version=2021-03-07` no campo URL de pedido. Este URL utiliza a nossa variável previamente criada `endpoint` para enviá-la automaticamente para o seu Recurso ACS.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Um pedido de carteiro, com o tipo definido para POST e o URL definido corretamente.":::

Selecione agora o separador Corpo do pedido e, em seguida, altere o botão de rádio por baixo para "cru". À direita, há uma queda que diz "Texto", alterá-lo para JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="Definindo o corpo de pedido para cru e JSON":::

Isto configurará o pedido de envio e receção de informações em formato JSON.

Na área de texto abaixo terá de introduzir um órgão de pedido, deve estar no seguinte formato:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

Para o valor "a partir", terá de [obter um número de telefone](../quickstarts/telephony-sms/get-phone-number.md) no Portal ACS, como mencionado anteriormente. Introduza-a sem espaços e prefixada pelo seu código de país. Por exemplo: `+15555551234`. A sua "mensagem" pode ser o que quiser enviar, mas `Hello from ACS` é um bom exemplo. O valor "para" deve ser um telefone a que tenha acesso que possa receber mensagens SMS. Usar o seu próprio telemóvel é uma boa ideia.

Uma vez introduzidos, precisamos de guardar este pedido na AcS Collection que criámos anteriormente. Isto irá garantir que ele recolhe as variáveis e script de pré-pedido que criamos anteriormente. Para tal, clique no botão "guardar" no direito superior da área de pedido.

:::image type="content" source="media/postman/postman-save.png" alt-text="O botão de guardar para um pedido do Carteiro.":::

Isto fará aparecer uma janela de diálogo que lhe perguntará o que gostaria de chamar ao pedido e onde gostaria de guardá-lo. Pode dar-lhe o nome de tudo o que quiser, mas certifique-se de que seleciona a sua coleção ACS na metade inferior do diálogo:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="O Carteiro guarda o diálogo de pedido com a coleção ACS selecionada.":::

## <a name="sending-a-request"></a>Envio de um pedido

Agora que tudo está configurado, deverá poder enviar o pedido e receber uma mensagem SMS no seu telemóvel. Para isso, certifique-se de que o seu pedido criado está selecionado e, em seguida, prima o botão "Enviar" à direita:

:::image type="content" source="media/postman/postman-send.png" alt-text="Um pedido do Carteiro, com o botão Enviar em destaque.":::

Se tudo correr bem, deve agora ver a resposta da ACS, que deve ser o código de estado 202:

:::image type="content" source="media/postman/postman-202.png" alt-text="Um pedido do Carteiro, enviado com sucesso com um código de estado 202.":::

O telemóvel, que detém o número que forneceu no valor "a", também deveria ter recebido uma mensagem SMS. Tem agora um carteiro em funcionamento, que pode falar com os Serviços acs e enviar mensagens SMS.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore ACS APIs](https://docs.microsoft.com/rest/api/communication/) 
>  [Ler mais sobre Autenticação](https://docs.microsoft.com/rest/api/communication/authentication) 
>  [Saiba mais sobre o Carteiro](https://learning.postman.com/)

Também pode querer:

- [Adicionar o chat à aplicação](../quickstarts/chat/get-started.md)
- [Criar fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
- [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
- [Saiba mais sobre a autenticação](../concepts/authentication.md)