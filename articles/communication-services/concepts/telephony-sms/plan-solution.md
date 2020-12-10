---
title: Planeie a sua solução de telefonia e SMS dos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba como planear eficazmente o seu uso de números de telefone e telefonia.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 271d7ce38d50fd21975327eb1f21528aeacc485e
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937241"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Planear a solução de telefonia e SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Os Serviços de Comunicação Azure permitem-lhe utilizar números de telefone para fazer chamadas de voz e enviar mensagens SMS com a rede telefónica comutada pelo público (PSTN). Neste documento, vamos rever os tipos de números de telefone, opções de configuração e disponibilidade da região para o planeamento da sua solução de telefonia e SMS utilizando serviços de comunicação.





## <a name="phone-number-types-in-azure-communication-services"></a>Tipos de números de telefone nos Serviços de Comunicação Azure
 
Os Serviços de Comunicação oferecem dois tipos de números de telefone: **locais** e **gratuitos.** 

### <a name="local-numbers"></a>Números locais
Os números locais (geográficos) são números de telefone de 10 dígitos que consistem nos códigos de área locais nos Estados Unidos. Por exemplo, `+1 (206) XXX-XXXX` é um número local com um código de área de `206` . Este código de área é atribuído à cidade de Seattle. Estes números de telefone são geralmente usados por indivíduos e empresas locais. A Azure Communication Services oferece números locais nos Estados Unidos. Estes números podem ser usados para fazer chamadas telefónicas, mas não para enviar mensagens SMS. 

### <a name="toll-free-numbers"></a>Números gratuitos
Os números gratuitos são números de telefone de 10 dígitos com códigos de área distintos que podem ser chamados de qualquer número de telefone gratuitamente. Por exemplo, `+1 (800) XXX-XXXX` é um número gratuito na região da América do Norte. Estes números de telefone são geralmente utilizados para fins de atendimento ao cliente. A Azure Communication Services oferece números gratuitos nos Estados Unidos. Estes números podem ser usados para fazer chamadas telefónicas e para enviar mensagens SMS. Os números gratuitos não podem ser utilizados por pessoas e só podem ser atribuídos a aplicações.

#### <a name="choosing-a-phone-number-type"></a>Escolher um tipo de número de telefone

Se o seu número de telefone for utilizado por uma aplicação (por exemplo, para fazer chamadas ou enviar mensagens em nome do seu serviço), pode selecionar um número gratuito ou local (geográfico). Pode selecionar um número gratuito se a sua aplicação estiver a enviar mensagens SMS e/ou a fazer chamadas.

Se o seu número de telefone estiver a ser utilizado por uma pessoa (por exemplo, um utilizador da sua aplicação de chamada), o número de telefone local (geográfico) deve ser utilizado. 

A tabela abaixo resume estes tipos de números de telefone: 

| Tipo de número de telefone | Exemplo                              | Disponibilidade do país    | Capacidade de número de telefone |Caso de uso comum                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Local (Geográfico)        | +1 (código de área local) XXX XX XX XX  | EUA                      | Chamando (Saída) | Atribuir números de telefone aos utilizadores nas suas aplicações  |
| Toll-Free         | +1 *(código* de área sem portagem) XXX XX XX | EUA                      | Chamada (Saída), SMS (Entrada/Saída)| Atribuir números de telefone a sistemas/Bots de resposta vocal interativa (IVR), aplicações SMS                                        |


## <a name="phone-number-features-in-azure-communication-services"></a>Características de número de telefone nos Serviços de Comunicação Azure 

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

Para a maioria dos números de telefone, permitimos configurar um conjunto de funcionalidades "à la carte". Estas funcionalidades podem ser selecionadas à medida que aluga os seus números de telefone nos Serviços de Comunicação Azure.

As funcionalidades que lhe estão disponíveis dependem do país em que está a operar, da sua caixa de utilização e do tipo de número de telefone que selecionou. Estas características variam de país para país devido aos requisitos regulamentares. A Azure Communication Services oferece os seguintes recursos de número de telefone:

- **SMS de ida e saída** Esta opção permite-lhe enviar mensagens SMS aos seus utilizadores. Isto pode ser útil em cenários de notificação e autenticação de dois fatores. 
- **SMS de entrada e saída bidirecciona** Esta opção permite-lhe enviar e receber mensagens dos seus utilizadores utilizando números de telefone. Isto pode ser útil em cenários de atendimento ao cliente.
- **Telefone de ida e saída** Esta opção permite-lhe esboçar chamadas para os seus utilizadores e configurar o ID do autor da chamada para chamadas de saída feitas pelo seu serviço. Isto pode ser útil em cenários de atendimento ao cliente e notificação de voz.

## <a name="countryregion-availability"></a>Disponibilidade país/região

A tabela que se segue mostra onde pode adquirir diferentes tipos de números de telefone, juntamente com as chamadas de entrada e saída e as funcionalidades de SMS associadas a estes tipos de números de telefone.

|Tipo de número| Adquirir números em | Fazer chamadas para                                        | Receber chamadas de                                    |Enviar mensagens para       | Receber mensagens de |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Local (Geográfico)  | EUA                 | EUA, Canadá, Reino Unido, Alemanha, França,. +mais*| EUA, Canadá, Reino Unido, Alemanha, França,. +mais* |Não disponível| Não disponível |
| Toll-Free | EUA                 | EUA                                                   | EUA                                                    |EUA                | EUA |

*Para mais detalhes sobre destinos de chamadas e preços, consulte a [página de preços](../pricing.md).

## <a name="azure-subscriptions-eligibility"></a>Elegibilidade das Assinaturas Azure

Para adquirir um número de telefone, você precisa estar em uma subscrição Azure paga. Os números de telefone não podem ser adquiridos em contas de julgamento ou por créditos gratuitos da Azure. 

Atualmente, a disponibilidade de números de telefone está restrita a subscrições do Azure que têm um endereço de faturação nos Estados Unidos.

## <a name="next-steps"></a>Passos seguintes

### <a name="quickstarts"></a>Guias de Início Rápido

- [Obter um número de telefone](../../quickstarts/telephony-sms/get-phone-number.md)
- [Fazer uma chamada](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Enviar um SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Documentação conceptual

- [Conceitos de voz e vídeo](../voice-video-calling/about-call-types.md)
- [Fluxos de chamada](../call-flows.md)
- [Preços](../pricing.md)
