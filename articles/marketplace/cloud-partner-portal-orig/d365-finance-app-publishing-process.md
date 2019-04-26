---
title: Passos de publicação de aplicações | Documentos da Microsoft
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 0fc82229e158ed35b97203b11d08841c683c45ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398060"
---
<a name="app-publishing-steps"></a>Passos de publicação de aplicações
====================

Para iniciar o processo de publicação, clicar em "Publicar" no separador Editor.

![Botão de aplicação de publicar CPP](./media/d365-financials/image014.jpg)


No separador Estado, verá os passos de publicação que indica em que a sua oferta está no processo de publicação. Em qualquer momento no processo de publicação, também pode iniciar sessão e clique no separador de todas as ofertas para ver o estado mais recente para qualquer uma das suas ofertas. Pode clicar diretamente no status da sua oferta e consulte os detalhes sobre onde a sua oferta está no processo de publicação.

Vamos examinar cada um dos passos publicação, abordarei o que acontece em cada passo e quanto deve calcular cada passo irá demorar.

![Diagrama do processo de publicação](./media/d365-financials/image017.png)


**Validar a pré-requisitos**

Quando clica em "Publicar", uma verificação automatizada terá lugar para se certificar de que já preencheu todos os campos obrigatórios na sua oferta. Se quaisquer campos não são preenchidos, será apresentado um aviso junto ao campo da e precisará para preenchê-lo com precisão, em seguida, clique em 'Publish' novamente.

Depois de concluir este passo corretamente, um pop-up aparecerá pedir um endereço de e-mail, que será utilizado para enviar-lhe publicar notificações. Depois de submeter o seu endereço de e-mail, este passo é concluído.


**Validação de automatizada de aplicativos**

Neste passo, o nosso serviço de certificação automatizado verifica a extensões de aplicação fornecidas com uma oferta que seu conteúdo se alinha com a oferecem de metadados. Certifique-se de que o nome da aplicação, versão, Editor e ID corresponda à fornecida no manifesto de extensão com o nome sempre `app.json`.


**Validação de unidade de teste**

Se tiver optado por configurar a versão de teste, nesta fase é onde as definições de versão de teste são validadas.


**Levar a validação de gestão e registo**

Durante esta fase, se tiver configurado a funcionalidade de geração de levar, iremos validar que a integração de CRM está a funcionar, enviando um líder de teste para seu sistema de CRM. Verá um registo com dados falsos preencher no seu CRM ou tabelas do Azure após a conclusão deste passo. Toda a documentação para a geração de levar está localizada aqui.


**Empacotamento de AppSource**

Os artefactos de detalhes de loja estão a ser verificados e pacote de pré-visualização de AppSource está a ser gerado.


**Fim de sessão de publicador**

Durante esta fase, o **Go Live** botão agora se tornará ativo. Também terá uma ligação para pré-visualizar a sua oferta (com seu hidekey). Assim que estiver satisfeito com o aspeto de pré-visualização, clique no botão Go Live.
Tenha em mente, este pedido não faz a aplicação em direto na origem de aplicação, mas em vez disso, ela aciona o nosso processo de validação interno.


**De marketing e a validação técnica da aplicação**

Este passo é onde podemos realizar as validações de marketing, técnicas e em paralelo. Consulte a [lista de verificação para submeter o seu aplicativo](https://aka.ms/CheckBeforeYouSubmit) e [desenvolver aplicativos para o Dynamics 365 para white paper de finanças e operações](https://go.microsoft.com/fwlink/?linkid=841518) documentos de diretrizes para obrigatórios requisitos e recomendações. Durante o processo de validação, iremos:
-  trabalhar no quaisquer problemas e questões pendentes que precisem.  
- fornecer-lhe uma data de publicação da aplicação e notificá-lo quando a sua aplicação for publicada. 
- Fornece um primeira comentários sobre as técnicas e a validação de marketing dentro de 5 a 7 dias de negócios.

Estes passos, normalmente, podem assumir uma semana, e não é necessário para que possa manter-se continuamente com sessão iniciada no Portal de parceiro de Cloud.


**Publicar a aplicação com o serviço**

A oferta está a passar por parte do processamento final. Seu aplicativo passou a validação de marketing e técnica, mas agora precisa passar por parte do processamento final para torná-lo pronto para a origem de aplicação.


**Live**

A oferta está agora em direto no AppSource e os clientes poderão ver e implementar a sua aplicação nas suas subscrições do Microsoft Dynamics 365 Business Central. Receberá um email de nós, notificá-lo que a aplicação se tornou pública na origem de aplicação. Em qualquer momento, pode clicar na guia todas as ofertas e ver o estado para todas as suas ofertas listadas na coluna da direita. Pode clicar no estado para ver o estado de fluxo de publicação em detalhes para a sua oferta.


<a name="error-handling"></a>Tratamento de erros
--------------

Durante o processo de publicação, pode ser encontrado um erro. Se for encontrado um erro, receberá um e-mail de notificação informando-o de que ocorreu um erro com instruções sobre os passos seguintes. Também pode ver erros em qualquer altura durante este processo ao clicar no separador de estado. Verá a partir do qual o processo que ocorreu o erro, juntamente com uma mensagem de erro que descreva o que precisa de ser resolvido.

Se encontrar erros durante o processo de publicação, é necessário para corrigir esses erros, em seguida, clique em **publicar** para reiniciar o processo. Tem de iniciar no início dos passos de publicação no **validar pré-requisitos** quando apagarmos após qualquer correção de erro.

Se estiver a ter problemas de resolução de um erro, deve abrir um pedido de suporte para receber ajuda de nossos engenheiros de suporte.


<a name="canceling-the-publishing-request"></a>A cancelar o pedido de publicação
--------------------------------

Pode iniciar o processo de publicação e têm uma necessidade para cancelar o pedido. Apenas pode cancelar um pedido de publicação depois do pedido de publicação atinge a etapa de aprovação do publicador. Para cancelar, clique em **Cancelar publicar**. O estado de publicação será reposto para o passo 1, e para publicar novamente, deve clicar em publicar e siga os passos no estado.

![Publicação de botão Cancelar](./media/d365-financials/image013.png)
