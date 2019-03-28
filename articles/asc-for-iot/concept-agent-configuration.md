---
title: Configurar um ASC para pré-visualização do IoT agente | Documentos da Microsoft
description: Saiba como configurar os agentes para utilização com o ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3a3806c73ef254abab91bd28cd8761917371235f
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541921"
---
# <a name="configure-security-agents"></a>Configurar os agentes de segurança

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como configurar um agente para utilização com o ASC para IoT.

## <a name="agents"></a>Agentes

ASC para os agentes de segurança de IoT recolher dados de dispositivos de IoT e realizar ações de segurança para atenuar as vulnerabilidades detetadas. Configuração do agente de segurança é controlável usando um conjunto de propriedades de duplos de módulo que pode personalizar. Em geral, as atualizações secundárias para estas propriedades são pouco frequentes.  

ASC para o objeto de configuração de duplo de agente de segurança da IoT é um objeto de formato. JSON. O objeto de configuração é um conjunto de propriedades controláveis que pode definir para controlar o comportamento do agente. 

Estas configurações ajudam a personalizar o agente para cada cenário exigido. Por exemplo, automaticamente excluindo alguns eventos ou manter o consumo de energia para um nível mínimo são possíveis através da configuração dessas propriedades.  

Utilizar o ASC para configuração de agente de segurança de IoT [esquema](https://github.com/azure/asc-for-iot-schemas/security/module/twin) para fazer alterações.  

## <a name="configuration-objects"></a>Objetos de configuração 

Cada ASC para o agente de segurança de IoT relacionados com a propriedade está localizada dentro do objeto de configuração do agente, dentro da seção de propriedades pretendidas, do módulo azureiotsecurity. 

Para modificar a configuração, criar e modificar este objeto dentro da identidade de duplo do módulo de azureiotsecurity. Se o objeto de configuração do agente não existir no duplo do módulo de azureiotsecurity, todos os valores de propriedade de agente de segurança estiverem definidos como predefinido. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //Agent configuration object 
  … 
} 
}
```

Certifique-se validar as alterações de configuração do agente em relação a isso [esquema](https://aka.ms/iot-security-github-module-schema).
O agente não serão iniciadas se o objeto de configuração não coincide com o esquema.


## <a name="editing-a-property"></a>Uma propriedade de edição 

Todas as propriedades personalizadas tem de ser definidas dentro do objeto de configuração de agente dentro do azureiotsecurity módulo duplo. 

Definir uma propriedade substitui o valor predefinido. Para definir uma propriedade, adicione a chave de propriedade para o objeto de configuração com o valor desejado. 

Para utilizar um valor de propriedade predefinido, remova a propriedade do objeto de configuração. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "High" 
  } 
}, 
```

## <a name="default-properties"></a>Propriedades predefinidas 
Conjunto de propriedades controláveis que controlam o ASC para os agentes de segurança de IoT.

Valores predefinidos estão disponíveis no esquema apropriado no [Github](https://aka.ms/iot-security-module-default).

| Name| Estado | Valores válidos| Valores predefinidos| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Necessário: Falso |Valores válidos: Duração em formato ISO 8601 |Valor predefinido: PT7M |Tempo máx. antes das mensagens de prioridade alta são enviados.|
|lowPriorityMessageFrequency |Necessário: Falso|Valores válidos: Duração em formato ISO 8601 |Valor predefinido: PT5H |Tempo máximo antes das mensagens de baixa prioridade são enviados.| 
|snapshotFrequency |Exigir: Falso|Valores: duração válida no formato ISO 8601 |Valor predefinido PT13H |Intervalo de tempo para a criação de instantâneos de estado do dispositivo.| 
|maxLocalCacheSizeInBytes |Necessário: Falso |Valores válidos: |Valor predefinido: 2560000, maior do que 8192 | Armazenamento máximo (em bytes) permitido para a cache de mensagem de um agente. Quantidade máxima de espaço permitido para armazenar as mensagens no dispositivo, antes das mensagens são enviadas.| 
|maxMessageSizeInBytes |Necessário: Falso |Valores válidos: Um número positivo, maior do que 8192, menos de 262144 |Valor predefinido: 204800 |Máximo permitido de tamanho de um agente para a mensagem da cloud. Esta definição controla a quantidade de dados máximos enviados em cada mensagem. |
|eventPriority${EventName} |Necessário: Falso |Valores válidos: Alto, baixa, desativado |Valores predefinidos: |Prioridade de cada agente gerou o evento | 

### <a name="events"></a>Eventos

|Nome do evento| propertyName | Valor Predefinido| Evento de instantâneo| Detalhes de estado  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Eventos de diagnóstico|eventPriorityDiagnostic| Desativado| Falso| Agente relacionada com eventos de diagnóstico. Utilize este evento para o registo verboso.| 
|Erro de configuração |eventPriorityConfigurationError |Baixa |Falso |O agente falhou ao analisar a configuração. Verifique a configuração no esquema.| 
|Estatísticas de eventos ignorados |eventPriorityDroppedEventsStatistics |Baixa |Verdadeiro|Estatísticas de eventos relacionados com o agente. |
|Estatísticas de mensagens|eventPriorityMessageStatistics |Baixa |Verdadeiro |Agente relacionadas com as estatísticas de mensagens. |
|Hardware ligado|eventPriorityConnectedHardware |Baixa |Verdadeiro |Instantâneo de todo o hardware ligados ao dispositivo.|
|Porta de escuta|eventPriorityListeningPorts |Elevado |Verdadeiro |Instantâneo de portas de escuta abertos no dispositivo.|
|Criar processo |eventPriorityProcessCreate |Baixa |Falso |Criação no dispositivo do processo de auditorias.|
|Terminar processo|eventPriorityProcessTerminate |Baixa |Falso |Finalização no dispositivo do processo de auditorias.| 
|Informações do sistema |eventPrioritySystemInformation |Baixa |Verdadeiro |Um instantâneo de informações do sistema (por exemplo: SO ou CPU).| 
|Usuários locais| eventPriorityLocalUsers |Elevado |Verdadeiro|Um instantâneo dos usuários registrados locais no sistema. |
|Iniciar sessão|  eventPriorityLogin |Elevado|Falso|Auditoria os eventos de início de sessão no dispositivo (locais e remotos inícios de sessão).|
|Criar ligação |eventPriorityConnectionCreate|Baixa|Falso|Audita conexões TCP criados para e do dispositivo. |
|Configuração da firewall| eventPriorityFirewallConfiguration|Baixa|Verdadeiro|Instantâneo da configuração de firewall do dispositivo (regras de firewall). |
|Linha de base do SO| eventPriorityOSBaseline| Baixa|Verdadeiro|Verifique o instantâneo de linha de base de SO de dispositivo.|
 

## <a name="see-also"></a>Consultar Também

- [Compreender o ASC para recomendações de IoT](concept-recommendations.md)
- [Explorar o ASC para alertas de IoT](concept-security-alerts.md)
- [Aceder a dados não processados de segurança](how-to-security-data-access.md)