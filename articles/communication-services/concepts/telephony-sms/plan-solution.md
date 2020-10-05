---
title: Planeie a sua solução de telefonia e SMS dos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba como planear eficazmente o seu uso de números de telefone e telefonia.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947411"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Planear a solução de telefonia e SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Este documento descreve os vários planos de telefonia e tipos de números que os Serviços de Comunicação Azure têm para oferecer. Vamos percorrer os fluxos de decisão para ajudá-lo a selecionar um fornecedor de serviços de voz, tipos de números de telefone, planos e capacidades disponibilizadas através dos Serviços de Comunicação.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Sobre números de telefone nos Serviços de Comunicações Azure

Os Serviços de Comunicação Azure permitem-lhe utilizar números de telefone para colocar e receber chamadas telefónicas e mensagens SMS. Estes números de telefone podem ser usados para configurar o ID do chamador em chamadas de saída feitas pelo seu serviço.
  
Se não tiver um número de telefone existente para importar para a sua solução de Serviços de Comunicação, a forma mais simples de começar é obter um novo número de telefone dos Serviços de Comunicação Azure em questão de minutos.

Se tiver um número de telefone existente que pretenda continuar a utilizar na sua solução (por exemplo, 1-800-EMPRESA), pode apresentar o número de telefone do prestador existente para serviços de comunicação.

O diagrama a seguir ajuda-o a navegar pelas opções disponíveis:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Diagrama mostrando como tomar uma decisão sobre os seus números de telefone.":::

Agora, vamos rever os tipos e capacidades de números de telefone disponíveis através dos Serviços de Comunicação. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Oferta direta da Microsoft de números e capacidades de telefone

A Azure Communication Services proporciona uma excelente flexibilidade para os desenvolvedores. Na maioria dos números de telefone, permitimos que configuure um conjunto de planos "à la carte". Alguns desenvolvedores só precisam de um plano de chamada de entrada; alguns podem optar por planos de SMS de entrada e saída. Estes planos podem ser selecionados à medida que aluga e/ou adouca os seus números de telefone nos Serviços de Comunicação.

Os planos disponíveis dependem do país e do tipo de número de telefone que está a operar. O diagrama abaixo representa o fluxo de decisão: Os planos disponíveis dependem do país e do tipo de número de telefone que você está operando dentro.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Diagrama mostrando como tomar uma decisão sobre os seus números de telefone."::: -->

Antes de selecionar um tipo de número de telefone, vamos rever o plano internacional de numeragem de telefone.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Leitura opcional. Plano internacional de numeragem de telecomunicações públicas (E.164)

> [!NOTE]
> Recomendamos rever esta informação mesmo que esteja familiarizado com o plano de numeração de telefone E.164 para entender melhor os tipos e capacidades de números oferecidos pela oferta direta dos Serviços de Comunicação Azure.

O plano internacional de numeragem de telecomunicações pública está definido na recomendação da União Internacional das Telecomunicações (UTA) E.164. Os números em conformidade estão limitados a um máximo de 15 dígitos.

O número de telefone é composto por

-   Um prefixo "+"
-   Um prefixo de marcação internacional ou código país/região (um, dois ou três dígitos) 
-   *(Opcional)* Um código de destino nacional ou um plano de numeragem, vulgarmente designado por código de área. O comprimento deste código depende do país. Nos Estados Unidos, são três dígitos. Na Austrália e na Nova Zelândia, é um dígito. Alemanha, Japão, México e alguns outros países têm comprimentos variáveis dos códigos de área. Por exemplo, na Alemanha, o código de área pode ser de dois a cinco dígitos, enquanto no Japão pode ser de um a cinco dígitos.
-   Um número de assinante

> [!NOTE]
> A classificação acima não está totalmente em conformidade com a norma ITU E.164 e destina-se a fornecer uma descrição simplificada. Por exemplo, o número de assinante está subdividido dentro da norma. Se está interessado em aprender mais profundamente sobre o plano internacional de numeragem, o [padrão UTA E.164](https://www.itu.int/rec/T-REC-E.164) é um excelente lugar para começar.  

Aqui estão alguns exemplos que o ajudarão a entender melhor o plano de numerações:

Um número de telefone regional nos EUA:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Diagrama mostrando como tomar uma decisão sobre os seus números de telefone.":::

Um número de telefone regional no Canadá:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Diagrama mostrando como tomar uma decisão sobre os seus números de telefone.":::

Um número gratuito na região da América do Norte:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Diagrama mostrando como tomar uma decisão sobre os seus números de telefone.":::

Um número de telemóvel no Reino Unido:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Diagrama mostrando como tomar uma decisão sobre os seus números de telefone.":::

Em seguida, vamos rever os tipos específicos de números de telefone disponíveis nos Serviços de Comunicação Azure.

## <a name="phone-number-types-in-azure-communication-services"></a>Tipos de números de telefone nos Serviços de Comunicação Azure

A Microsoft oferece planos regionais e gratuitos para códigos curtos e números móveis em países onde aplicável.

A tabela abaixo resume estes tipos de números de telefone: 

| Tipo de número de telefone | Exemplo                              | Disponibilidade do país    | Caso de uso comum                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regional          | +1 (código de área geográfica) XXX XX XX  | EUA, Canadá, Porto Rico | Atribuir números de telefone aos utilizadores nas suas aplicações ou atribuir sistemas/Bots de Resposta vocal Interativa (IVR) |
| Gratuito         | +1 *(código*de área sem portagem) XXX XX XX XX XX | EUA, Canadá, Porto Rico | Atribuição de sistemas/bots de resposta vocal interativa (IVR), aplicações SMS                                        |

## <a name="plans"></a>Planos 

Vamos ver as capacidades que pode ativar para os seus números de telefone. Estas capacidades variam de país para país devido aos requisitos regulamentares. A Azure Communication Services oferece as seguintes capacidades:

- **SMS de saída de ida,** útil para a notificação e cenários de autenticação de dois fatores.
- **SMS bidirecionais,** um pacote predefinido que permite o envio e recebido SMS como parte de um plano.
- **Chamada PSTN**, pode selecionar uma chamada de entrada e usar o ID do autor da chamada para escruissar chamadas de saída.

## <a name="next-steps"></a>Passos seguintes

### <a name="quickstarts"></a>Guias de Início Rápido

- [Obter um número de telefone](../../quickstarts/telephony-sms/get-phone-number.md)
- [Fazer uma chamada](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Enviar um SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Documentação conceptual

- [Conceitos de voz e vídeo](../voice-video-calling/about-call-types.md)
- [Fluxos de chamadas e fluxos de SMS](../call-flows.md)
- [Preços](../pricing.md)
