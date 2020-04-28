---
title: Como utilizar o serviço de e-mail SendGrid (Node.js) [ Microsoft Docs
description: Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Amostras de código escritas com a API nó.js.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60931722"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Como enviar e-mail usando sendGrid de Node.js

Este guia demonstra como executar tarefas comuns de programação com o serviço de e-mail SendGrid no Azure. As amostras são escritas usando a API node.js. Os cenários abordados incluem a construção de **e-mails,** **envio de e-mails,** adição de **anexos,** **utilização de filtros**e **atualização**de propriedades. Para mais informações sobre sendGrid e envio de e-mail, consulte a secção [Next Steps.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>O que é o Serviço de E-mail SendGrid?

SendGrid é um [serviço de e-mail baseado na nuvem] que fornece entrega de [e-mail transacional]confiável, escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de utilização da SendGrid incluem:

* Envio automaticamente de recibos aos clientes
* Administrar listas de distribuição para envio de passageiros eletrónicos mensais e ofertas especiais
* Recolher métricas em tempo real para coisas como e-mail bloqueado e capacidade de resposta do cliente
* Gerar relatórios para ajudar a identificar tendências
* Reencaminhamento de inquéritos aos clientes
* Notificações de e-mail da sua aplicação

Para mais informações, consulte [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Criar uma conta SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referência o módulo DeNóso.js SendGrid

O módulo SendGrid para Node.js pode ser instalado através do gestor de pacotes de nó (npm) utilizando o seguinte comando:

```bash
npm install sendgrid
```

Após a instalação, pode exigir o módulo na sua aplicação utilizando o seguinte código:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

O módulo SendGrid exporta as funções **SendGrid** e **Email.**
**SendGrid** é responsável pelo envio de e-mail através da Web API, enquanto o **Email** encapsula uma mensagem de e-mail.

## <a name="how-to-create-an-email"></a>Como: Criar um e-mail

Criar uma mensagem de e-mail utilizando o módulo SendGrid envolve primeiro criar uma mensagem de e-mail utilizando a função email e, em seguida, enviá-la usando a função SendGrid. Segue-se um exemplo de criação de uma nova mensagem utilizando a função email:

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

A definição tanto do texto como das propriedades html proporciona uma recaída graciosa ao conteúdo de texto para clientes que não suportam mensagens HTML.

Para obter mais informações sobre todas as propriedades suportadas pela função Email, consulte [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Como: Enviar um e-mail

Depois de criar uma mensagem de e-mail utilizando a função email, pode enviá-la utilizando a API web fornecida pela SendGrid. 

### <a name="web-api"></a>API Web

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Embora os exemplos acima indicados mostrem passar um objeto de e-mail e função de callback, também pode invocar diretamente a função de envio especificando diretamente as propriedades de e-mail. Por exemplo:  
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
Os anexos podem ser adicionados a uma mensagem especificando o nome ou os caminhos do ficheiro na propriedade dos **ficheiros.** O exemplo que se segue demonstra o envio de um anexo:

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
> Ao utilizar a propriedade dos **ficheiros,** o ficheiro deve ser acessível através [de fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se o ficheiro que pretende anexar estiver hospedado no Armazenamento Azure, como num recipiente Blob, deve primeiro copiar o ficheiro para o armazenamento local ou para uma unidade Azure antes de poder ser enviado como anexo utilizando a propriedade dos **ficheiros.**
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Como: Utilizar filtros para ativar rodapés e rastreios

A SendGrid fornece uma funcionalidade adicional de e-mail através da utilização de filtros. Estas são configurações que podem ser adicionadas a uma mensagem de e-mail para ativar funcionalidades específicas, tais como permitir o rastreio de cliques, análise do Google, rastreio de subscrições, e assim por diante. Para obter uma lista completa de filtros, consulte [as Definições][Filter Settings]do Filtro .

Os filtros podem ser aplicados a uma mensagem utilizando a propriedade dos **filtros.**
Cada filtro é especificado por um hash que contém definições específicas do filtro.
Os seguintes exemplos demonstram o rodapé e clique nos filtros de rastreio:

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

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de email

Algumas propriedades de e-mail podem ser substituídas usando **setProperty** ou anexadas usando **addProperty**. Por exemplo, pode adicionar destinatários adicionais usando

```javascript
email.addTo('jeff@contoso.com');
```

ou definir um filtro usando

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Para mais informações, consulte [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Utilizar serviços adicionais de sendgrid

O SendGrid oferece APIs baseados na Web que pode utilizar para alavancar a funcionalidade adicional sendGrid a partir da sua aplicação Azure. Para mais detalhes, consulte a [documentação da SendGrid API][SendGrid API documentation].

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu o básico do serviço de e-mail SendGrid, siga estes links para saber mais.

* Repositório de módulos SendGrid Node.js: [sendgrid-nodejs][sendgrid-nodejs]
* Documentação da SendGrid API:<https://sendgrid.com/docs>
* Oferta especial SendGrid para clientes Azure:[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[serviço de e-mail baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email
