---
title: Como utilizar o serviço de correio eletrónico do SendGrid (node. js) | Documentos da Microsoft
description: Saiba como enviar um e-mail com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de código escrito com a API de node. js.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931722"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Como enviar E-mails com o SendGrid do node. js

Este guia demonstra como realizar tarefas comuns de programação com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos usando a API de node. js. Os cenários abrangidos incluem **construir e-mail**, **enviar mensagem de e-mail**, **adicionar anexos**, **usando filtros de**e o **a atualizar as propriedades**. Para obter mais informações sobre o SendGrid e o envio de e-mail, consulte a [passos seguintes](#next-steps) secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?

O SendGrid é um [serviço de e-mail com base na cloud] que fornece fiável [entrega de e-mail transacional], escalabilidade e análise em tempo real, juntamente com APIs flexíveis que facilitam a integração personalizada. Os cenários de utilização do SendGrid comuns incluem:

* Enviar automaticamente os recibos aos clientes
* Administrando a distribuição de lista para o envio de clientes e-Panfleto mensal e ofertas especiais
* Recolher métricas em tempo real para coisas como email bloqueada e a capacidade de resposta do cliente
* Geração de relatórios para ajudar a identificar tendências
* Consultas de cliente de reencaminhamento
* Notificações de e-mail da sua aplicação

Para mais informações, consulte [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referência do módulo de node. js do SendGrid

O módulo do SendGrid para node. js pode ser instalado através do node package manager (npm) utilizando o seguinte comando:

```bash
npm install sendgrid
```

Após a instalação, pode exigir o módulo em seu aplicativo usando o seguinte código:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

O módulo de SendGrid exporta os **SendGrid** e **E-Mail** funções.
**O SendGrid** é responsável por enviar e-mail através da Web API, enquanto **E-Mail** encapsula uma mensagem de e-mail.

## <a name="how-to-create-an-email"></a>Como: Criar uma mensagem de E-Mail

A criação de uma mensagem de e-mail utilizando o módulo de SendGrid envolve criar primeiro uma mensagem de e-mail com a função de E-Mail e, em seguida, enviando-lo usando a função do SendGrid. Segue-se um exemplo de como criar uma nova mensagem usando a função de E-Mail:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Também pode especificar uma mensagem em HTML para clientes que o suportam, definindo a propriedade de html. Por exemplo:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Definir propriedades de texto e html fornece anulações normal contingência para conteúdo de texto para clientes que não é possível suportar a mensagens HTML.

Para obter mais informações sobre todas as propriedades compatíveis com a função de E-Mail, consulte [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Como: Enviar um E-Mail

Depois de criar uma mensagem de e-mail com a função de E-Mail, pode enviá-lo a usar a API Web fornecidos pela SendGrid. 

### <a name="web-api"></a>API Web

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Embora os exemplos acima mostram passagem numa função de objeto e o retorno de chamada de e-mail, também diretamente pode invocar a função de envio especificando as propriedades de e-mail diretamente. Por exemplo:  
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
Anexos podem ser adicionados a uma mensagem ao especificar o nome de ficheiro (s) e o caminho (s) na **ficheiros** propriedade. O exemplo seguinte demonstra o envio de um anexo:

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
> Ao utilizar o **arquivos** propriedade, o ficheiro tem de ser acessível através de [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se o ficheiro que pretende anexar estiver alojado no armazenamento do Azure, como um contentor de BLOBs, primeiro tem de copiar o ficheiro para o armazenamento local ou a uma unidade do Azure antes que ela pode ser enviada como um anexo a utilizar o **ficheiros** propriedade.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Como: Utilizar filtros para ativar rodapés e controlo

O SendGrid disponibiliza a funcionalidade de e-mail adicionais através da utilização de filtros. Estas são as definições que podem ser adicionadas a uma mensagem de e-mail para ativar a funcionalidade específica, como ativar o controlo de cliques em, do Google analytics, subscrição de controlo e assim por diante. Para obter uma lista completa de filtros de mensagens em fila, consulte [definições de filtro][Filter Settings].

Filtros podem ser aplicados a uma mensagem utilizando a **filtros** propriedade.
Cada filtro é especificado por um valor de hash que contém definições específicas do filtro.
Os exemplos seguintes demonstram o rodapé e clique em filtros de controlo:

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

### <a name="click-tracking"></a>Clique em controle

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

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de E-Mail

Algumas propriedades de e-mail podem ser substituídas pelos **setProperty** ou acrescentados com **addProperty**. Por exemplo, pode adicionar os destinatários adicionais através da utilização

```javascript
email.addTo('jeff@contoso.com');
```

ou definir um filtro com

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Para obter mais informações, consulte [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Utilizar os serviços adicionais do SendGrid

O SendGrid oferece as APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do SendGrid desde a sua aplicação do Azure. Para mais informações, consulte a [documentação da API de SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Próximos Passos

Agora que aprendeu as noções básicas do serviço de correio eletrónico SendGrid, siga estas ligações para saber mais.

* Repositório de módulo de node. js do SendGrid: [sendgrid nodejs][sendgrid-nodejs]
* Documentação da API de SendGrid: <https://sendgrid.com/docs>
* O SendGrid oferta especial para os clientes do Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[serviço de e-mail com base na cloud]: https://sendgrid.com/email-solutions
[entrega de e-mail transacional]: https://sendgrid.com/transactional-email
