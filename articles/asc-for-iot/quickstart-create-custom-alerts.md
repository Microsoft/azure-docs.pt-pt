---
title: 'Quickstart: Criar alertas personalizados para o Centro de Segurança Azure para ioT'
description: Compreenda, crie e atribua alertas de dispositivos personalizados para o Azure Security Center para o serviço de segurança IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 063e5c9e7d75fd1c07d148c265b1fe64eee3cbc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303533"
---
# <a name="quickstart-create-custom-alerts"></a>Quickstart: Criar alertas personalizados


Utilizando grupos de segurança personalizados e alertas, tira o máximo partido das informações de segurança e do conhecimento categórico do dispositivo para garantir uma melhor segurança em toda a sua solução IoT. 

## <a name="why-use-custom-alerts"></a>Por que usar alertas personalizados? 

Conhece melhor os seus dispositivos ioT.

Para os clientes que compreendem plenamente o comportamento esperado do dispositivo, o Azure Security Center for IoT permite traduzir este entendimento numa política de comportamento do dispositivo e alertar para qualquer desvio do comportamento normal esperado.

## <a name="security-groups"></a>Grupos de segurança

Os grupos de segurança permitem-lhe definir grupos lógicos de dispositivos e gerir o seu estado de segurança de forma centralizada.

Estes grupos podem representar dispositivos com hardware específico, dispositivos implantados num determinado local, ou qualquer outro grupo adequado às suas necessidades específicas.

Os grupos de segurança são definidos por uma propriedade de twin tag de dispositivo chamada **SecurityGroup**. Por padrão, cada solução IoT no IoT Hub tem um grupo de segurança chamado **padrão**. Altere o valor da propriedade **securityGroup** para alterar o grupo de segurança de um dispositivo.
 
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

1. Abra o seu Hub IoT. 
2. Clique em **alertas Personalizados** na secção **de Segurança.** 
3. Escolha um grupo de segurança ao qual deseja aplicar a personalização. 
4. Clique **em Adicionar um alerta personalizado**.
5. Selecione um alerta personalizado da lista de abandono. 
6. Editar as propriedades necessárias, clique em **OK**.
7. Certifique-se de clicar em **SAVE**. Sem guardar o novo alerta, o alerta é apagado da próxima vez que fechar o IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Alertas disponíveis para personalização

O Azure Security Center for IoT oferece um grande número de alertas que podem ser personalizados de acordo com as suas necessidades específicas. Reveja a tabela de [alerta personalizável](concept-customizable-security-alerts.md) para a gravidade do alerta, fonte de dados, descrição e as nossas etapas de reparação sugeridas se e quando cada alerta for recebido. 


## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para aprender a implantar um agente de segurança...

> [!div class="nextstepaction"]
> [Implante um agente de segurança](how-to-deploy-agent.md)
