---
title: Criar alertas personalizados
description: Compreenda, crie e atribua alertas de dispositivos personalizados para o Azure Defender para o serviço de segurança IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: e59bcca9561c33610170f4bc3674eef307cf0de9
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809051"
---
# <a name="create-custom-alerts"></a>Criar alertas personalizados

Utilizando grupos de segurança personalizados e alertas, aproveita ao máximo as informações de segurança de ponta a ponta e o conhecimento categórico do dispositivo para garantir uma melhor segurança em toda a sua solução IoT.

## <a name="why-use-custom-alerts"></a>Por que usar alertas personalizados?

Conhece melhor os seus dispositivos IoT.

Para os clientes que compreendem perfeitamente o comportamento esperado do dispositivo, o Defender para IoT permite traduzir este entendimento numa política de comportamento do dispositivo e alertar para qualquer desvio do comportamento normal esperado.

## <a name="security-groups"></a>Grupos de segurança

Os grupos de segurança permitem-lhe definir grupos lógicos de dispositivos e gerir o seu estado de segurança de forma centralizada.

Estes grupos podem representar dispositivos com hardware específico, dispositivos implantados num determinado local ou qualquer outro grupo adequado às suas necessidades específicas.

Os grupos de segurança são definidos por uma propriedade de etiquetas gémeas do dispositivo chamada **SecurityGroup**. Por predefinição, cada solução IoT no IoT Hub tem um grupo de segurança chamado **padrão**. Altere o valor da propriedade **Do Grupo de Segurança** para alterar o grupo de segurança de um dispositivo.

Por exemplo:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Utilize grupos de segurança para agrupar os seus dispositivos em categorias lógicas. Depois de criar os grupos, atribua-os aos alertas personalizados da sua escolha, para a solução de segurança IoT mais eficaz.

## <a name="customize-an-alert"></a>Personalize um alerta

1. Abra o seu Hub IoT e selecione **Definições** do menu **Segurança.**

1. Selecione em **alertas personalizados.**

1. Escolha um grupo de segurança a que deseje aplicar a personalização.

1. **Selecione Adicione um alerta personalizado.**

1. Selecione um alerta personalizado da lista de dropdown.

1. Editar as propriedades necessárias, selecione **OK**.

1. Certifique-se de selecionar **SAVE**. Sem guardar o novo alerta, o alerta é apagado da próxima vez que fechar o IoT Hub.

## <a name="alerts-available-for-customization"></a>Alertas disponíveis para personalização

O Defender for IoT oferece um grande número de alertas, que podem ser personalizados de acordo com as suas necessidades específicas. Reveja a [tabela de alerta personalizável](concept-customizable-security-alerts.md) para a gravidade do alerta, fonte de dados, descrição e as nossas medidas de reparação sugeridas se e quando cada alerta for recebido.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a implantar um agente de segurança...

> [!div class="nextstepaction"]
> [Implementar um agente de segurança](how-to-deploy-agent.md)
