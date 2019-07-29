---
title: Criar alertas personalizados para a central de segurança do Azure para IoT | Microsoft Docs
description: Crie e atribua alertas de dispositivo personalizados para a central de segurança do Azure para IoT.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: ed10cbf89f878f8d27b43476d26ac93dd373ed66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597006"
---
# <a name="quickstart-create-custom-alerts"></a>Início rápido: Criar alertas personalizados


O uso de grupos de segurança e alertas personalizados aproveita totalmente as informações de segurança de ponta a ponta e o conhecimento de dispositivo categórico para garantir uma melhor segurança em sua solução de IoT. 

## <a name="why-use-custom-alerts"></a>Por que usar alertas personalizados? 

Você conhece melhor seus dispositivos IoT.

Para os clientes que entendem totalmente seu comportamento de dispositivo esperado, a central de segurança do Azure para IoT permite que você traduza essa compreensão em uma política de comportamento de dispositivo e alerte qualquer desvio do comportamento esperado e normal.

## <a name="security-groups"></a>Grupos de segurança

Os grupos de segurança permitem que você defina grupos lógicos de dispositivos e gerencie seu estado de segurança de forma centralizada.

Esses grupos podem representar dispositivos com hardware específico, dispositivos implantados em um determinado local ou qualquer outro grupo adequado às suas necessidades específicas.

Os grupos de segurança são definidos por uma propriedade de marca de dispositivo de entrelaçamento chamada grupo de **segurança**. Por padrão, cada solução de IoT no Hub IoT tem um grupo de segurança chamado **padrão**. Altere o valor da propriedade The **Security** Group para alterar o grupo de segurança de um dispositivo.
 
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

Use grupos de segurança para agrupar seus dispositivos em categorias lógicas. Depois de criar os grupos, atribua-os aos alertas personalizados de sua escolha, para a solução de segurança de IoT de ponta a ponta mais eficaz. 

## <a name="customize-an-alert"></a>Personalizar um alerta

1. Abra o Hub IoT. 
2. Clique em **alertas personalizados** na seção **segurança** . 
3. Escolha um grupo de segurança ao qual você deseja aplicar a personalização. 
4. Clique em **Adicionar um alerta personalizado**.
5. Selecione um alerta personalizado na lista suspensa. 
6. Edite as propriedades necessárias e clique em **OK**.
7. Certifique-se de clicar em **salvar**. Sem salvar o novo alerta, o alerta será excluído na próxima vez que você fechar o Hub IoT.

 
## <a name="alerts-available-for-customization"></a>Alertas disponíveis para personalização

A tabela a seguir fornece um resumo dos alertas disponíveis para personalização.


| Severity | Nome | Origem de Dados | Descrição | Correção sugerida|
|---|---|---|---|---|
| Baixa      | Alerta personalizado – número de mensagens de nuvem para dispositivo no protocolo AMQP está fora do intervalo permitido          | IoT Hub     | O número de mensagens de nuvem para dispositivo (protocolo AMQP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixa      | Alerta personalizado-o número de mensagens de nuvem rejeitadas para o dispositivo no protocolo AMQP está fora do intervalo permitido | IoT Hub     | Número de mensagens de nuvem para dispositivo (protocolo AMQP) rejeitadas pelo dispositivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixa      | Alerta personalizado-o número de mensagens do dispositivo para a nuvem no protocolo AMQP está fora do intervalo permitido      | IoT Hub     | A quantidade de dispositivo para mensagens na nuvem (protocolo AMQP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|   |
| Baixa      | Alerta personalizado-o número de invocações de método direto está fora do intervalo permitido | IoT Hub     | A quantidade de invocações de método direto em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixa      | Alerta personalizado-o número de carregamentos de arquivo está fora do intervalo permitido | IoT Hub     | A quantidade de carregamentos de arquivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.| |
| Baixa      | Alerta personalizado-o número de mensagens de nuvem para o dispositivo no protocolo HTTP está fora do intervalo permitido | IoT Hub     | A quantidade de mensagens da nuvem para o dispositivo (protocolo HTTP) em uma janela de tempo não está no intervalo permitido configurado                                  |
| Baixa      | Alerta personalizado-o número de mensagens de nuvem rejeitadas para o dispositivo no protocolo HTTP não está no intervalo permitido | IoT Hub     | A quantidade de mensagens de nuvem para dispositivo (protocolo HTTP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixa      | Alerta personalizado-o número de mensagens do dispositivo para a nuvem no protocolo HTTP está fora do intervalo permitido | IoT Hub| A quantidade de dispositivo para mensagens de nuvem (protocolo HTTP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|    |
| Baixa      | Alerta personalizado – número de mensagens de nuvem para dispositivo no protocolo MQTT está fora do intervalo permitido | IoT Hub     | A quantidade de mensagens de nuvem para dispositivo (protocolo MQTT) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|   |
| Baixa      | Alerta personalizado-o número de mensagens de nuvem rejeitadas para o dispositivo no protocolo MQTT está fora do intervalo permitido | IoT Hub     | A quantidade de mensagens de nuvem para dispositivo (protocolo MQTT) rejeitada pelo dispositivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixa      | Alerta personalizado-o número de mensagens do dispositivo para a nuvem no protocolo MQTT está fora do intervalo permitido          | IoT Hub     | A quantidade de dispositivo para mensagens na nuvem (protocolo MQTT) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|
| Baixa      | Alerta personalizado-o número de limpezas de fila de comando está fora do intervalo permitido                               | IoT Hub     | A quantidade de limpezas de fila de comando em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.||
| Baixa      | Alerta personalizado-o número de atualizações do módulo de atualização está fora do intervalo permitido                                       | IoT Hub     | A quantidade de atualizações de conjunto de módulos em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|
| Baixa      | Alerta personalizado-o número de operações não autorizadas está fora do intervalo permitido  | IoT Hub     | A quantidade de operações não autorizadas em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|
| Baixa      | Alerta personalizado-o número de conexões ativas está fora do intervalo permitido  | Agente       | O número de conexões ativas em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido.|  Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigno ou mal-intencionada. Se for mal-intencionado, remova o possível malware e entenda a origem. Se for benigno, adicione a origem à lista de conexões permitidas.  |
| Baixa      | Alerta personalizado-conexão de saída criada para um IP que não é permitido                             | Agente       | Uma conexão de saída foi criada para um IP que está fora da sua lista de IPs permitidos. |Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigno ou mal-intencionada. Se for mal-intencionado, remova o possível malware e entenda a origem. Se for benigno, adicione a origem à lista de IPs permitidos.                        |
| Baixa      | Alerta personalizado-o número de logons locais com falha está fora do intervalo permitido                               | Agente       | A quantidade de logons locais com falha em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |   |
| Baixa      | Alerta personalizado-logon de um usuário que não está na lista de usuários permitidos | Agente       | Um usuário local fora da lista de usuários permitidos, conectado ao dispositivo.|  Se você estiver salvando dados brutos, navegue até sua conta do log Analytics e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações.|
| Baixa      | Alerta personalizado-um processo foi executado e não é permitido | Agente       | Um processo que não é permitido foi executado no dispositivo. |Se você estiver salvando dados brutos, navegue até sua conta do log Analytics e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações.  |
|


## <a name="next-steps"></a>Passos Seguintes

Avance para o próximo artigo para saber como implantar um agente de segurança...

> [!div class="nextstepaction"]
> [Implantar um agente de segurança](how-to-deploy-agent.md)
