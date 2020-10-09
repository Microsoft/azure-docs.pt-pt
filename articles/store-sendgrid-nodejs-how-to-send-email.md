---
title: Como utilizar o serviço de e-mail SendGrid (Node.js) Microsoft Docs
description: Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Amostras de código escritas usando a API Node.js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "60931722"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Como enviar e-mail usando SendGrid a partir de Node.js

Este guia demonstra como executar tarefas de programação comuns com o serviço de e-mail SendGrid em Azure. As amostras são escritas com a API Node.js. Os cenários abrangidos incluem **a construção de e-mails,** **envio de e-mail,** **adição de anexos,** **utilização de filtros**e **atualização de propriedades.** Para mais informações sobre SendGrid e envio de e-mail, consulte a secção [Etapas Seguintes.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>O que é o Serviço de E-mail SendGrid?

SendGrid é um [serviço de e-mail baseado na nuvem] que fornece entrega de [e-mail transacional]fiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de utilização da SendGrid incluem:

* Envio automaticamente de recibos aos clientes
* Gestão de listas de distribuição para envio de clientes e-fliers mensais e ofertas especiais
* Recolher métricas em tempo real para coisas como e-mail bloqueado e capacidade de resposta do cliente
* Gerar relatórios para ajudar a identificar tendências
* Encaminhamento de inquéritos aos clientes
* Notificações de e-mail da sua aplicação

Para obter mais informações, veja [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Criar uma conta SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referência do Módulo de Node.js SendGrid

O módulo SendGrid para Node.js pode ser instalado através do gestor de pacotes de nó (npm) utilizando o seguinte comando:

```bash
npm install sendgrid
```

Após a instalação, pode requerer o módulo na sua aplicação utilizando o seguinte código:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

O módulo SendGrid exporta as funções **SendGrid** e **Email.**
**A SendGrid** é responsável pelo envio de e-mail através da Web API, enquanto **o Email** encapsula uma mensagem de e-mail.

## <a name="how-to-create-an-email"></a>Como: Criar um email

A criação de uma mensagem de correio eletrónico utilizando o módulo SendGrid envolve primeiro criar uma mensagem de correio eletrónico utilizando a função Email e, em seguida, enviá-la utilizando a função SendGrid. Segue-se um exemplo de criação de uma nova mensagem utilizando a função Email:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Também pode especificar uma mensagem HTML para clientes que a suportam definindo a propriedade html. Por exemplo:

```javascript
html: This is a sample <b>HTML<b> email message.
```

A definição tanto do texto como das propriedades html proporciona um recuo gracioso ao conteúdo de texto para clientes que não suportam mensagens HTML.

Para obter mais informações sobre todos os imóveis suportados pela função Email, consulte [os sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Como: Enviar um e-mail

Depois de criar uma mensagem de e-mail utilizando a função Email, pode enviá-la utilizando a API Web fornecida pela SendGrid. 

### <a name="web-api"></a>API Web

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Embora os exemplos acima mostram a passagem de um objeto de e-mail e uma função de retorno de chamada, também pode invocar diretamente a função de envio especificando diretamente propriedades de e-mail. Por exemplo:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo
Os anexos podem ser adicionados a uma mensagem especificando o(s) nomes de ficheiros e caminhos(s) na propriedade dos **ficheiros.** O exemplo a seguir demonstra o envio de um anexo:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> Ao utilizar a propriedade dos **ficheiros,** o ficheiro deve estar acessível através [do fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se o ficheiro que pretende anexar estiver alojado no Azure Storage, como num recipiente Blob, deve primeiro copiar o ficheiro para o armazenamento local ou para uma unidade Azure antes de poder ser enviado como anexo utilizando a propriedade dos **ficheiros.**
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Como: Utilizar filtros para permitir rodapés e rastreios

A SendGrid fornece uma funcionalidade adicional de e-mail através da utilização de filtros. Estas são configurações que podem ser adicionadas a uma mensagem de e-mail para ativar funcionalidades específicas, tais como permitir o rastreio de cliques, análise do Google, rastreio de subscrição, e assim por diante. Para obter uma lista completa de filtros, consulte [as definições do filtro][Filter Settings].

Os filtros podem ser aplicados a uma mensagem utilizando a propriedade dos **filtros.**
Cada filtro é especificado por um haxixe que contém configurações específicas do filtro.
Os seguintes exemplos demonstram o rodapé e clicam nos filtros de rastreio:

### <a name="footer"></a>Rodapé

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Clique em Rastreio

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de e-mail

Algumas propriedades de e-mail podem ser substituídas através do **setProperty** ou anexados usando **addProperty**. Por exemplo, pode adicionar recetores adicionais usando

```javascript
email.addTo('jeff@contoso.com');
```

ou definir um filtro usando

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Para mais informações, consulte [os sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Utilizar serviços adicionais de SendGrid

A SendGrid oferece APIs baseados na Web que pode utilizar para alavancar a funcionalidade Adicional SendGrid a partir da sua aplicação Azure. Para mais detalhes, consulte a documentação da [SendGrid API][SendGrid API documentation].

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu o básico do serviço de email SendGrid, siga estes links para saber mais.

* Repositório de módulos sendGrid Node.js: [sendgrid-nodejs][sendgrid-nodejs]
* Documentação da SendGrid API: <https://sendgrid.com/docs>
* Oferta especial sendGrid para clientes Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[serviço de e-mail baseado na nuvem]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email
