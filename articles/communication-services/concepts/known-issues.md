---
title: Serviços de Comunicação Azure - Questões conhecidas
description: Conheça os problemas conhecidos com os Serviços de Comunicação da Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628160"
---
# <a name="known-issues-azure-communication-services"></a>Questões conhecidas: Serviços de Comunicação Azure

Este artigo fornece informações sobre questões conhecidas associadas aos Serviços de Comunicação Azure.

## <a name="video-streaming-quality-on-chromeandroid"></a>Qualidade de streaming de vídeo no Chrome/Android 

O desempenho do streaming de vídeo pode ser degradado no Chrome Android.

### <a name="possible-causes"></a>Possíveis causas
A qualidade dos fluxos remotos depende da resolução do renderizador do lado do cliente que foi usado para iniciar esse fluxo. Ao subscrever um fluxo remoto, um recetor determinará a sua própria resolução com base nas dimensões do transformador do lado do cliente do remetente.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Os microfones dos auscultadores Bluetooth não são detetados

Pode sentir problemas de ligação dos seus auscultadores bluetooth a uma chamada dos Serviços de Comunicação.

### <a name="possible-causes"></a>Possíveis causas
Não existe uma opção para selecionar o microfone Bluetooth no iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Comutação repetida de dispositivos de vídeo pode fazer com que o streaming de vídeo pare temporariamente

A troca entre dispositivos de vídeo pode fazer com que o seu fluxo de vídeo pare enquanto o fluxo é adquirido a partir do dispositivo selecionado.

### <a name="possible-causes"></a>Possíveis causas
O streaming e a troca entre dispositivos de mídia é computacionalmente intensivo. A comutação frequente pode causar uma degradação do desempenho. Os desenvolvedores são encorajados a parar um fluxo de dispositivo antes de iniciar outro.
