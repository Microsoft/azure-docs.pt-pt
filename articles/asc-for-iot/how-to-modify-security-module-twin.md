---
title: Como as instruções para modificar um ASC para duplo do módulo de IoT no ASC para pré-visualização do IoT | Documentos da Microsoft
description: Saiba como modificar uma ASC para duplo de módulo de segurança de IoT para o ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541951"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Modificar um ASC para duplo do módulo de IoT

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como modificar a configuração de um existente **AzureIoTSecurity módulo duplo** para um dispositivo existente. 

Ver [criar um ASC para o módulo de IoT](quickstart-create-security-twin.md) para saber como tornar um novo módulo de segurança para um novo dispositivo.  

## <a name="modification-considerations"></a>Considerações de modificação

> [!IMPORTANT]
> Cada configuração na configuração de duplo substitui a configuração predefinida. Se uma configuração específica já não está presente na configuração de duplo, é utilizada a configuração predefinida. 

## <a name="configuration-schema-and-validation"></a>Esquema de configuração e validação 

Lembre-se de que valide a configuração de agente em relação a isso [esquema](https://github.com/Azure/asc-for-iot/schema/security_module_twin). Um agente não serão iniciadas se o objeto de configuração não coincide com o esquema.

 
Se, enquanto o agente está em execução, o objeto de configuração for alterado para uma configuração não válidos (a configuração não coincide com o esquema), o agente irá ignorar a configuração inválida e irá continuar a utilizar a configuração atual. 

## <a name="edit-a-property"></a>Editar uma propriedade  

Defina todas as propriedades personalizadas dentro do objeto de configuração de agente dentro do AzureIoTSecurity módulo duplo. 

Para definir uma propriedade, adicione a chave de propriedade para o objeto de configuração com o valor desejado. Para utilizar um valor de propriedade predefinido, remova a propriedade do objeto de configuração:

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>Propriedades 
A tabela seguinte contém todas as propriedades configuráveis que controlam o ASC para os agentes de IoT. 
          

| Name| Estado | Valores válidos| Valores predefinidos| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Necessário: Falso |Valores válidos: Duração em formato ISO 8601 |Valor predefinido: PT7M |Tempo máx. antes das mensagens de prioridade alta são enviados.|
|lowPriorityMessageFrequency |Necessário: Falso|Valores válidos: Duração em formato ISO 8601 |Valor predefinido: PT5H |Tempo máximo antes das mensagens de baixa prioridade são enviados.| 
|snapshotFrequency |Exigir: Falso|Valores: duração válida no formato ISO 8601 |Valor predefinido PT13H |Intervalo de tempo para a criação de instantâneos de estado do dispositivo.| 
|maxLocalCacheSizeInBytes |Necessário: Falso |Valores válidos: |Valor predefinido: 2560000, maior do que 8192 | Armazenamento máximo (em bytes) permitido para a cache de mensagem de um agente. Quantidade máxima de espaço permitido para armazenar as mensagens no dispositivo, antes das mensagens são enviadas.| 
|maxMessageSizeInBytes |Necessário: Falso |Valores válidos: Um número positivo, maior do que 8192, menos de 262144 |Valor predefinido: 204800 |Máximo permitido de tamanho de um agente para a mensagem da cloud. Esta definição controla a quantidade de dados máximos enviados em cada mensagem. |
|eventPriority${EventName} |Necessário: Falso |Valores válidos: Alto, baixa, desativado |Valores predefinidos: |Prioridade de cada agente gerou o evento. | 
|

### <a name="events"></a>Eventos

A lista seguinte de eventos são todos os eventos ASC para o agente de IoT podem recolher dos seus dispositivos. Utilize o módulo duplo de AzureIotSecurity para configurar que esses eventos são recolhidos e decidir a prioridade na sua solução. 
 
|Nome do evento| propertyName | Valor predefinido| Evento de instantâneo| Estado de detalhe  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Eventos de diagnóstico|eventPriorityDiagnostic| Desativado| Falso| Agente relacionada com eventos de diagnóstico. Utilize este evento para o registo verboso.| 
Erro de configuração |eventPriorityConfigurationError |Baixa |Falso |O agente falhou ao analisar a configuração. Verifique a configuração no esquema.| 
|Estatísticas de eventos ignorados |eventPriorityDroppedEventsStatistics |Baixa |Verdadeiro|Estatísticas de eventos relacionados com o agente. |
|Estatísticas de mensagens|eventPriorityMessageStatistics |Baixa |Verdadeiro |Agente relacionadas com as estatísticas de mensagens. |
|Hardware ligado|eventPriorityConnectedHardware |Baixa |Verdadeiro |Instantâneo de todo o hardware ligados ao dispositivo.|
|Porta de escuta|eventPriorityListeningPorts |Elevado |Verdadeiro |Instantâneo de portas de escuta abertos no dispositivo.|
| Criar processo |eventPriorityProcessCreate |Baixa |Falso |Criação no dispositivo do processo de auditorias.|
| Terminar processo|eventPriorityProcessTerminate |Baixa |Falso |Finalização no dispositivo do processo de auditorias.| 
 Informações do sistema |eventPrioritySystemInformation |Baixa |Verdadeiro |Um instantâneo de informações do sistema, como o SO ou da CPU.|
|Usuários locais| eventPriorityLocalUsers |Elevado |Verdadeiro|Um instantâneo dos usuários registrados locais no sistema. |
|Iniciar sessão|  eventPriorityLogin |Elevado|Falso|Audita os eventos de início de sessão no dispositivo (locais e remotos inícios de sessão).|
|Criar ligação |eventPriorityConnectionCreate|Baixa|Falso|Audita conexões TCP criados para e do dispositivo. |
|Configuração da firewall| eventPriorityFirewallConfiguration|Baixa|Verdadeiro|Instantâneo da configuração de firewall do dispositivo (regras de firewall). |
|Linha de base do SO| eventPriorityOSBaseline| Baixa|Verdadeiro|Verifique o instantâneo de linha de base de SO de dispositivo.| 
|


## <a name="next-steps"></a>Passos Seguintes

- Leia o ASC para IoT [descrição geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Compreender e explorar [ASC para alertas de IoT](concept-security-alerts.md)
- Descubra como aceder ao seu [dados de segurança](how-to-security-data-access.md)
