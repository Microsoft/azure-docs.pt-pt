---
title: Compreenda o suporte para a atualização de dispositivos desligados utilizando o Microsoft Connected Cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Compreenda o suporte para a atualização de dispositivos desligados utilizando a Cache Conectada do Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680060"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Compreender o suporte para atualizações de dispositivos desligados

Num cenário transparente de gateway, um ou mais dispositivos podem passar as suas mensagens através de um único dispositivo de gateway que mantém a ligação ao Azure IoT Hub. Nestes casos, os dispositivos para crianças podem não ter conectividade com a Internet ou não podem ser autorizados a descarregar conteúdos a partir da internet. O módulo IoT Edge de pré-visualização de cache conectado da Microsoft fornecerá a Atualização do Dispositivo para os clientes do Azure IoT Hub com a capacidade de uma cache inteligente na rede, que permite atualizações baseadas em imagens e pacotes de dispositivos baseados em Linux OS por trás e gateway IoT Edge (dispositivos IoT a jusante), e também ajudará a guardar a largura de banda para os clientes do Hub Azure IoT.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Como funciona a pré-visualização da Cache conectada pela Microsoft para a Atualização do Dispositivo para O Azure IoT Hub?

O Microsoft Connected Cache é uma cache inteligente e transparente para conteúdos publicados para o conteúdo do Device Update para conteúdo Azure IoT Hub e pode ser personalizado para cache de conteúdos de outras fontes, como repositórios de pacotes também. O Microsoft Connected Cache é uma cache fria que é aquecida pelos pedidos do cliente para as gamas exatas de ficheiros solicitadas pelo cliente de Otimização de Entrega e não faz conteúdo pré-seed. A descrição passo a passo do diagrama e passo abaixo explica como a Cache Conectada da Microsoft funciona dentro da atualização do dispositivo para a infraestrutura Azure IoT Hub.

>[!Note]
>Ao definir este fluxo, assumiu-se que o gateway IoT Edge tem conectividade com a Internet. Para o cenário a jusante do gateway IoT Edge (Nested Edge), a "Rede de Entrega de Conteúdos" (CDN) pode ser considerada a MCC hospedada no porta de entrada IoT Edge.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Atualização de dispositivos desligados" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. O Microsoft Connected Cache é implantado como um módulo IoT Edge para o servidor on-prem.
2. A atualização do dispositivo para os clientes Azure IoT Hub está configurada para descarregar conteúdo da Cache Conectada do Microsoft em virtude do atributo GatewayHostName da cadeia de ligação do dispositivo para dispositivos de folha IoT **OU** parent_hostname definido no config.toml para dispositivos infantis IoT Edge.
3. A atualização do dispositivo para os clientes do Azure IoT Hub em ambos os casos recebe comandos de descarregamento de conteúdos de atualização a partir da Atualização do Dispositivo para o serviço Azure IoT Hub e solicita conteúdo de atualização para a Cache Conectada microsoft em vez do CDN. O Microsoft Connected Cache por padrão está configurado para ouvir na porta 80 de http, e o cliente de Otimização de Entrega faz o pedido de conteúdo na porta 80, pelo que o progenitor deve estar configurado para ouvir nesta porta.  Apenas o protocolo http é suportado neste momento.
4. O servidor Cache Conectado da Microsoft descarrega o conteúdo do CDN, semeia a sua cache local armazenada em disco e entrega o conteúdo ao cliente Azure IoT Hub.
   
>[!Note]
>Ao utilizar atualizações baseadas em pacotes, o servidor Cache ligado ao Microsoft será configurado pelo administrador com o nome de anfitrião do pacote necessário.

5. Os pedidos subsequentes de outros clientes do Azure IoT Hub para o mesmo conteúdo de atualização virão agora da cache e o Microsoft Connected Cache não fará pedidos ao CDN para o mesmo conteúdo.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Apoio ao IoT Industrial (IIoT) com cenários de hospedagem de pais/filhos

Quando um gateway IoT Edge a jusante ou infantil estiver hospedado no servidor Cache Ligado ao Microsoft, será configurado para solicitar o conteúdo de atualização a partir do gateway IoT Edge do pai, hospedando o servidor Cache ligado à Microsoft. Isto é necessário para o máximo de níveis necessários antes de chegar ao gateway IoT Edge pai que hospeda um servidor Microsoft Connected Cache que tenha acesso à Internet. A partir do servidor ligado à Internet, o conteúdo é solicitado ao CDN, altura em que o conteúdo é entregue de volta ao gateway IoT Edge da criança que originalmente solicitou o conteúdo. O conteúdo será armazenado em disco em todos os níveis.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Acesso à pré-visualização da Cache Conectada à Microsoft para atualização do dispositivo para O Azure IoT Hub

O módulo Microsoft Connected Cache IoT Edge é lançado como pré-visualização para clientes que estão a implementar soluções utilizando a Atualização do Dispositivo para O Hub IoT Azure. O acesso à pré-visualização é por convite. [Solicite acesso](https://aka.ms/MCCForDeviceUpdateForIoT) à pré-visualização da Cache conectada da Microsoft para atualização do dispositivo para ioT Hut e forneça as informações solicitadas se quiser aceder ao módulo.
