---
title: SMS FAQ
titleSuffix: An Azure Communication Services concept document
description: SMS FAQ
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646317"
---
# <a name="sms-faq"></a>SMS FAQ

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Um cliente pode utilizar os Serviços de Comunicação Azure para fins de emergência?

Os Serviços de Comunicação Azure não suportam a funcionalidade text-to-911 nos Estados Unidos, mas é possível que tenha a obrigação de o fazer de acordo com as regras da Comissão Federal de Comunicações (FCC).  Deve avaliar se as regras de texto-para-911 da FCC se aplicam ao seu serviço ou aplicação. Na medida em que estiver coberto por estas regras, será responsável por encaminhar mensagens de texto para os centros de chamadas de emergência que as solicitem. Você é livre de determinar o seu próprio modelo de entrega de texto-para-911, mas uma abordagem aceite pela FCC envolve o lançamento automaticamente do marcador nativo no dispositivo móvel do utilizador para entregar textos 911 através da transportadora móvel subjacente.

## <a name="are-there-any-limits-on-sending-messages"></a>Há limites para o envio de mensagens?

Para garantir que continuamos a oferecer a elevada qualidade de serviço consistente com os nossos SLAs, os Serviços de Comunicação Azure aplicam limites de tarifas (diferentes para cada primitivo). Os desenvolvedores que ligarem para as nossas APIs para além do limite receberão uma resposta de código de estado de 429 HTTP. Se a sua empresa tiver requisitos que excedam os limites de taxa, por favor envie-nos um e-mail para phone@microsoft.com .

Limites de tarifas para SMS:

|Operação|Âmbito|Prazo (s)| Limite (pedido #) | Unidades de mensagem por minuto|
|---------|-----|-------------|-------------------|-------------------------|
|Enviar mensagem|Por Número|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Como é que os Serviços de Comunicação Azure lidam com opt-outs para números gratuitos?

Os opt-outs para números gratuitos dos EUA são mandatados e aplicados pelas transportadoras americanas.
- STOP - Se um destinatário de mensagens de texto quiser optar por não optar, pode enviar 'STOP' para o número gratuito. A transportadora envia a seguinte resposta por defeito para STOP: "NETWORK MSG: Respondeu com a palavra "stop" que bloqueia todos os textos enviados a partir deste número. Envie um sms para "untop" para receber mensagens novamente."
- START/UNSTOP - Se o destinatário pretender reenviar mensagens de texto a partir de um número gratuito, pode enviar 'START' ou 'UNSTOP para o número gratuito. A transportadora envia a seguinte resposta por defeito para START/UNSTOP: "NETWORK MSG: Respondeu "untop" e começará a receber mensagens novamente a partir deste número."
- Os Serviços de Comunicação Azure detetarão a mensagem STOP e bloquearão todas as mensagens adicionais ao destinatário. O relatório de entrega indicará uma entrega falhada com mensagem de estado como "Remetente bloqueado para destinatário dado."
- As mensagens STOP, UNSTOP e START serão retransmitidas de volta para si. A Azure Communication Services encoraja-o a monitorizar e implementar estes opt-outs para garantir que não são feitas mais tentativas de envio de mensagens aos destinatários que tenham optado por não fazer as suas comunicações.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Como posso receber mensagens usando os Serviços de Comunicação Azure?

Os clientes dos Serviços de Comunicação Azure podem utilizar a Azure Event Grid para receber mensagens recebidas. Siga este [arranque rápido](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) para configurar a sua grelha de eventos para receber mensagens.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Posso enviar/receber mensagens longas (>2048)?

Os Serviços de Comunicação Azure suportam o envio e receção de mensagens longas através de SMS. No entanto, alguns operadores ou dispositivos sem fios podem agir de forma diferente quando recebem mensagens longas.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Como são enviadas mensagens para os números fixos tratados?

Nos Estados Unidos, a Azure Communication Services não verifica os números dos telefones fixos e tentará enviá-lo às transportadoras para entrega. Os clientes serão cobrados para mensagens enviadas para números fixos. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Posso enviar mensagens a vários destinatários?


Sim, pode fazer um pedido com vários destinatários. Siga este [arranque rápido](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) para enviar mensagens a vários destinatários.
