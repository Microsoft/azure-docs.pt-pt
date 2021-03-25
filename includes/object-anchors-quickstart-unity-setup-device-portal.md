---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105103948"
---
### <a name="set-up-the-windows-device-portal"></a>Configurar o Portal do Dispositivo do Windows

Para ligar aos hololens sobre Wi-Fi, siga estes passos:

1. Primeiro [Ligue os HoloLens ao Wi-Fi.](/hololens/hololens-network)

2. Em seguida, obtenha o endereço IP no dispositivo em **Definições > Rede & Opções Avançadas de > Wi-Fi > Internet**.

3. A partir de um navegador web no seu PC, vá a `https://<YOUR_HOLOLENS_IP_ADDRESS>` . O navegador apresentará a seguinte mensagem: "Há um problema com o certificado de segurança deste site". Esta mensagem ocorre porque o certificado emitido para o Portal do Dispositivo é um certificado auto-assinado. Pode ignorar o erro do certificado e continuar.

Consulte [aqui](/windows/mixed-reality/using-the-windows-device-portal) mais informações sobre a configuração do Portal do Dispositivo Do Windows.