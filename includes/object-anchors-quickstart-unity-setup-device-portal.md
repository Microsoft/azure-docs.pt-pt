---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044691"
---
### <a name="set-up-the-windows-device-portal"></a>Configurar o Portal do Dispositivo do Windows

Para ligar aos hololens sobre Wi-Fi, siga estes passos:

1. Primeiro [Ligue os HoloLens ao Wi-Fi.](https://docs.microsoft.com/hololens/hololens-network)

2. Em seguida, obtenha o endereço IP no dispositivo em **Definições > Rede & Opções Avançadas de > Wi-Fi > Internet**.

3. A partir de um navegador web no seu PC, vá a `https://<YOUR_HOLOLENS_IP_ADDRESS>` . O navegador apresentará a seguinte mensagem: "Há um problema com o certificado de segurança deste site". Esta mensagem ocorre porque o certificado emitido para o Portal do Dispositivo é um certificado auto-assinado. Pode ignorar o erro do certificado e continuar.

Consulte [aqui](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal) mais informações sobre a configuração do Portal do Dispositivo Do Windows.
