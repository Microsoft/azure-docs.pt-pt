---
title: Serviços de Comunicação Azure - questões conhecidas
description: Saiba mais sobre os Serviços de Comunicação Azure
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: aa5530dd279e8b45382fe6841b6f193a652c0ba3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566800"
---
# <a name="known-issues-azure-communication-services-client-libraries"></a>Questões conhecidas: Bibliotecas de clientes da Azure Communication Services
Este artigo fornece informações sobre limitações e questões conhecidas relacionadas com as bibliotecas de clientes dos Serviços de Comunicação Azure.

> [!IMPORTANT]
> Existem vários fatores que podem afetar a qualidade da sua experiência de chamada. Consulte a documentação dos requisitos de rede para saber mais sobre a configuração da rede de **[serviços](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** de comunicação e testar as melhores práticas.


## <a name="javascript-client-library"></a>Biblioteca de cliente do JavaScript

Esta secção fornece informações sobre questões conhecidas associadas a bibliotecas de clientes javaScript e chamadas de vídeo nos Serviços de Comunicação Azure.

### <a name="after-refreshing-the-page-user-is-not-removed-from-the-call-immediately"></a>Depois de refrescar a página, o utilizador não é removido da chamada imediatamente 
Se o utilizador estiver numa chamada e decidir atualizar a página, a biblioteca de clientes dos Serviços de Comunicação poderá não ser capaz de informar o serviço de comunicação que está prestes a desligar. O serviço de comunicação dos Serviços de Comunicação não removerá este utilizador imediatamente da chamada, mas aguardará que um utilizador volte a juntar-se assumindo problemas com a conectividade da rede. O utilizador será removido da chamada após o serviço de mídia ter um tempo limite.

Encorajamos os desenvolvedores a criarem experiências que não exijam que os utilizadores finais atualizem a página da sua aplicação enquanto participam numa chamada. Se o utilizador atualizar a página, a melhor forma de lidar com a mesma para a aplicação é reutilizar o mesmo ID do utilizador dos Serviços de Comunicação para o utilizador depois de voltar à aplicação após a atualização.

Para a perspetiva de outros participantes na chamada, este utilizador permanecerá na chamada por tempo predefinido (1-2 minutos). Se o utilizador voltar a juntar-se ao mesmo ID do utilizador dos Serviços de Comunicação, será representado como o mesmo objeto existente na `remoteParticipants` coleção.
Se o utilizador anteriormente enviasse vídeo, `videoStreams` a recolha manterá as informações de fluxo anteriores até que o serviço o limite e remova, neste cenário a aplicação pode decidir observar quaisquer novos fluxos adicionados à coleção e renderizar um com o mais alto `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Não é possível fazer várias pré-visualizações de vários dispositivos na web
Esta é uma limitação conhecida. Consulte a [visão geral](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) da biblioteca do cliente de chamada para obter mais informações.

### <a name="enumeration-of-the-microphone-and-speaker-devices-is-not-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>A enumeração dos dispositivos de microfone e altifalante não é possível no Safari quando a aplicação é executado no iOS ou iPadOS 
As aplicações não podem enumerar/selecionar dispositivos de microfone/altifalante (como Bluetooth) no Safari iOS/iPad. Esta é uma limitação conhecida do sistema operativo.

Se estiver a utilizar o Safari no macOS, a sua aplicação não será capaz de enumerar/selecionar altifalantes através do Gestor de Dispositivos de Serviços de Comunicação. Neste cenário, os dispositivos devem ser selecionados através do SISTEMA. Se utilizar o Chrome no macOS, a aplicação pode enumerar/selecionar dispositivos através do Gestor de Dispositivos de Serviços de Comunicação.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Perde-se a conectividade áudio ao receber mensagens SMS ou chamadas durante uma chamada VoIP em curso
Os navegadores móveis não mantêm a conectividade enquanto estão no estado de fundo. Isto pode levar a uma experiência de chamada degradada se a chamada VoIP foi interrompida por mensagem de texto ou chamada PSTN que empurra a sua aplicação para segundo plano.

<br/>Biblioteca do cliente: Chamada (JavaScript)
<br/>Navegadores: Safari, Chrome
<br/>Sistema Operativo: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Comutação repetida de dispositivos de vídeo pode fazer com que o streaming de vídeo pare temporariamente

A troca entre dispositivos de vídeo pode fazer com que o seu fluxo de vídeo pare enquanto o fluxo é adquirido a partir do dispositivo selecionado.

#### <a name="possible-causes"></a>Possíveis causas
O streaming e a troca entre dispositivos de mídia é computacionalmente intensivo. A comutação frequente pode causar uma degradação do desempenho. Os desenvolvedores são encorajados a parar um fluxo de dispositivo antes de iniciar outro.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>O microfone do auricular Bluetooth não é detetado, portanto, não é audível durante a chamada no Safari no iOS
Os auscultadores Bluetooth não são suportados pelo Safari no iOS. O seu dispositivo Bluetooth não estará listado nas opções de microfone disponíveis e outros participantes não poderão ouvi-lo se tentar utilizar Bluetooth sobre Safari.

#### <a name="possible-causes"></a>Possíveis causas
Esta é uma limitação conhecida do sistema operativo macOS/iOS/iPadOS. 

Com o Safari no **macOS** e **iOS/iPadOS,** não é possível enumerar/selecionar dispositivos de coluna através do Gestor de Dispositivos de Serviços de Comunicação, uma vez que a enumeração/seleção dos altifalantes não é suportada pelo Safari. Neste cenário, a seleção do seu dispositivo deve ser atualizada através do sistema operativo.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>A rotação de um dispositivo pode criar má qualidade de vídeo
Os utilizadores podem experimentar a qualidade de vídeo degradada quando os dispositivos são rodados.

<br/>Dispositivos afetados: Google Pixel 5, Google Pixel 3a, Apple iPad 8 e Apple iPad X
<br/>Biblioteca do cliente: Chamada (JavaScript)
<br/>Navegadores: Safari, Chrome
<br/>Sistema Operativo: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>A comutação da câmara faz o ecrã congelar 
Quando um utilizador dos Serviços de Comunicação se junta a uma chamada utilizando a biblioteca do cliente de chamada JavaScript e, em seguida, premir o botão de comutação da câmara, o UI pode ficar completamente sem resposta até que a aplicação seja atualizada ou o navegador seja pressionado para o fundo pelo utilizador.

<br/>Dispositivos afetados: Google Pixel 4a
<br/>Biblioteca do cliente: Chamada (JavaScript)
<br/>Navegadores: Chrome
<br/>Sistema Operativo: iOS, Android


#### <a name="possible-causes"></a>Possíveis causas
Sob investigação.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Se o sinal de vídeo foi interrompido enquanto a chamada está em estado de "ligação", o vídeo não será enviado após o início da chamada. 
Se os utilizadores decidirem ligar/desligar rapidamente o vídeo enquanto a chamada está no `Connecting` estado - isso pode levar a problemas com o fluxo adquirido para a chamada. Encorajamos os desenvolvedores a construir as suas apps de uma forma que não exija que o vídeo seja ligado/desligado enquanto a chamada está no `Connecting` estado. Esta questão pode causar desempenho de vídeo degradado nos seguintes cenários:

 - Se o Utilizador começar com áudio e, em seguida, iniciar e parar o vídeo enquanto a chamada está no `Connecting` estado.
 - Se o Utilizador começar com áudio e, em seguida, iniciar e parar o vídeo enquanto a chamada está no `Lobby` estado.


#### <a name="possible-causes"></a>Possíveis causas
Sob investigação.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Às vezes demora muito tempo a fazer vídeos remotos dos participantes
Durante uma chamada de grupo em curso, _o Utilizador A_ envia vídeo e, em seguida, o Utilizador _B_ junta-se à chamada. Por vezes, o Utilizador B não vê vídeos do Utilizador A, ou o vídeo do Utilizador A começa a renderizar após um longo atraso. Isto pode ser causado por um ambiente de rede que requer uma configuração adicional. Consulte a documentação dos [requisitos](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) de rede para orientação de configuração da rede.
