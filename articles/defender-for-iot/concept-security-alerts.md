---
title: Lista de alertas personalizados & incorporados
description: Saiba mais sobre alertas de segurança e remediação recomendada utilizando o Defender para as funcionalidades e serviços do IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784565"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Alertas de segurança do Defender para ioT Hub

O Defender for IoT analisa continuamente a sua solução IoT utilizando análises avançadas e inteligência de ameaça para alertá-lo para atividades maliciosas.
Além disso, pode criar alertas personalizados com base no seu conhecimento do comportamento esperado do dispositivo.
Um alerta funciona como um indicador de potencial compromisso, e deve ser investigado e remediado.

Neste artigo, você encontrará uma lista de alertas incorporados, que podem ser desencadeados no seu Hub IoT.
Além dos alertas incorporados, o Defender for IoT permite definir alertas personalizados baseados no comportamento esperado do IoT Hub e/ou do dispositivo.
Para mais informações, consulte [alertas personalizáveis.](concept-customizable-security-alerts.md)

## <a name="built-in-alerts-for-iot-hub"></a>Alertas incorporados para IoT Hub

| Gravidade | Nome | Descrição | Remediação sugerida |
|--|--|--|--|
| **Severidade média** |  |  |  |
| Novo certificado adicionado a um Hub IoT | Médio | Um certificado chamado \' %{DescCertificateName} \' foi adicionado ao IoT Hub \' %{DescIoTHubName} \' . Se esta ação foi feita por uma parte não autorizada, pode indicar atividade maliciosa. | 1. Certifique-se de que o certificado foi adicionado por uma parte autorizada. <br> 2. Se não foi adicionado por uma parte autorizada, retire o certificado e aumente o alerta para a equipa de segurança organizacional. |
| Certificado eliminado de um Hub IoT | Médio | Um certificado chamado \' %{DescCertificateName} \' foi eliminado do IoT Hub \' %{DescIoTHubName} \' . Se esta ação foi feita por uma parte não autorizada, pode indicar uma atividade maliciosa. | 1. Certifique-se de que o certificado foi removido por uma parte autorizada. <br> 2. Se o certificado não tiver sido removido por uma parte autorizada, adicione o certificado de volta e aumente o alerta para a equipa de segurança organizacional. |
| Tentativa falhada detetada para adicionar um certificado a um hub IoT | Médio | Houve uma tentativa falhada de adicionar o certificado \' %{DescCertificateName} \' ao IoT Hub \' %{DescIoTHubName} \' . Se esta ação foi feita por uma parte não autorizada, pode indicar atividade maliciosa. | Certifique-se de que as permissões para alterar certificados só são concedidas a partes autorizadas. |
| Tentativa falhada detetada para apagar um certificado de um Hub IoT | Médio | Houve uma tentativa fracassada de apagar o certificado \' %{DescCertificateName} \' do IoT Hub \' %{DescIoTHubName} \' . Se esta ação foi feita por uma parte não autorizada, pode indicar atividade maliciosa. | Certifique-se de que as permissões para alterar certificados só são concedidas a uma parte autorizada. |
| x.509 dispositivo certificado impressão digital de impressão digital desfasamento | Médio | x.509 dispositivo certificado impressão digital não correspondeu configuração. | Reveja os alertas nos dispositivos. Não são necessárias mais medidas. |
| certificado x.509 expirado | Médio | O certificado do dispositivo X.509 expirou. | Este pode ser um dispositivo legítimo com um certificado expirado ou uma tentativa de personificar um dispositivo legítimo. Se o dispositivo legítimo estiver atualmente a comunicar corretamente, esta é provavelmente uma tentativa de imitação. |
| **Baixa** gravidade |  |  |  |
| Tente adicionar ou editar uma definição de diagnóstico de um Hub IoT detetado | Baixo | Foi detetada uma tentativa de adicionar ou editar as definições de diagnóstico de um Hub IoT. As definições de diagnóstico permitem-lhe recriar pistas de atividade para fins de investigação quando ocorre um incidente de segurança ou a sua rede está comprometida. Se esta ação não for feita por uma parte autorizada, pode indicar atividade maliciosa. | 1. Certifique-se de que o certificado foi removido por uma parte autorizada.<br> 2. Se o certificado não tiver sido removido por uma parte autorizada, adicione o certificado de volta e aumente o alerta para a sua equipa de segurança de informação. |
| Tentativa de apagar uma definição de diagnóstico de um Hub IoT detetado | Baixo | Houve uma tentativa de \' adicionar ou editar a definição de diagnóstico \' %{DescDiagnosticSettingName} \' do IoT Hub \' %{DescIoTHubName} \' . A definição de diagnóstico permite-lhe recriar pistas de atividade para fins de investigação quando ocorre um incidente de segurança ou a sua rede está comprometida. Se esta ação não for feita por uma parte autorizada, pode indicar uma atividade maliciosa. | Certifique-se de que as permissões para alterar as definições de diagnóstico são concedidas apenas a uma parte autorizada. |
| Ficha SAS expirada | Baixo | Ficha SAS expirada usada por um dispositivo | Pode ser um dispositivo legítimo com um token expirado, ou uma tentativa de personificar um dispositivo legítimo. Se o dispositivo legítimo estiver atualmente a comunicar corretamente, esta é provavelmente uma tentativa de imitação. |
| Assinatura simbólica INVálida SAS | Baixo | Um símbolo SAS utilizado por um dispositivo tem uma assinatura inválida. A assinatura não corresponde nem à chave primária ou secundária. | Reveja os alertas nos dispositivos. Não são necessárias mais medidas. |

## <a name="next-steps"></a>Passos seguintes

- Visão [geral](overview.md) do serviço defender para ioT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
