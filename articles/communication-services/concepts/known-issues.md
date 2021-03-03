---
title: Serviços de Comunicação Azure - FAQ / Questões conhecidas
description: Saiba mais sobre os Serviços de Comunicação Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e58335f1e266af651eb5867ca98e9ec979803b94
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655892"
---
# <a name="faq--known-issues"></a>Perguntas frequentes / Questões conhecidas
Este artigo fornece informações sobre questões conhecidas e perguntas frequentes relacionadas com os Serviços de Comunicação Azure.

## <a name="faq"></a>FAQ

### <a name="why-is-the-quality-of-my-video-degraded"></a>Porque é que a qualidade do meu vídeo está degradada?

A qualidade dos streams de vídeo é determinada pelo tamanho do renderizador do lado do cliente que foi usado para iniciar esse fluxo. Ao subscrever um fluxo remoto, um recetor determinará a sua própria resolução com base nas dimensões do transformador do lado do cliente do remetente.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Por que não é possível enumerar/selecionar dispositivos de microfone/altifalante no Safari?

As aplicações não podem enumerar/selecionar dispositivos de microfone/altifalante (como bluetooth) no Safari iOS/iPad. É uma limitação do sistema operativo- há sempre apenas um dispositivo.

Para o Safari no MacOS - a aplicação não pode enumerar/selecionar o altifalante através do Gestor de Dispositivos de Serviços de Comunicação - estes têm de ser selecionados através do SISTEMA. Se utilizar o Chrome no MacOS, a aplicação pode enumerar/selecionar dispositivos através do Gestor de Dispositivos de Serviços de Comunicação.

## <a name="known-issues"></a>Problemas conhecidos

Esta secção fornece informações sobre questões conhecidas associadas aos Serviços de Comunicação Azure.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Comutação repetida de dispositivos de vídeo pode fazer com que o streaming de vídeo pare temporariamente

A troca entre dispositivos de vídeo pode fazer com que o seu fluxo de vídeo pare enquanto o fluxo é adquirido a partir do dispositivo selecionado.

#### <a name="possible-causes"></a>Possíveis causas
O streaming e a troca entre dispositivos de mídia é computacionalmente intensivo. A comutação frequente pode causar uma degradação do desempenho. Os desenvolvedores são encorajados a parar um fluxo de dispositivo antes de iniciar outro.
