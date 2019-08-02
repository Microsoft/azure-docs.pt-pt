---
title: Configurar a central de segurança do Azure para o agente de IoT | Microsoft Docs
description: Saiba como configurar agentes para uso com a central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 8b4764d855663325b2445f7b588b795c15f4edde
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596335"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: Configurar agentes de segurança

Este artigo explica a central de segurança do Azure para agentes de segurança de IoT e detalha como alterá-los e configurá-los. 

> [!div class="checklist"]
> * Configurar agentes de segurança
> * Alterar o comportamento do agente editando propriedades de entrelaçamento
> * Descobrir configuração padrão

## <a name="agents"></a>Agentes

A central de segurança do Azure para agentes de segurança de IoT coleta dados de dispositivos IoT e executa ações de segurança para atenuar as vulnerabilidades detectadas. A configuração do agente de segurança é controlável com o uso de um conjunto de propriedades de módulo de propriedade que você pode personalizar. Em geral, as atualizações secundárias dessas propriedades não são frequentes.  

A central de segurança do Azure para o objeto de configuração do agente de segurança do IoT é um objeto de formato JSON. O objeto de configuração é um conjunto de propriedades controláveis que você pode definir para controlar o comportamento do agente. 

Essas configurações ajudam a personalizar o agente para cada cenário necessário. Por exemplo, excluir automaticamente alguns eventos ou manter o consumo de energia em um nível mínimo é possível Configurando essas propriedades.  

Use a central de segurança do Azure para o [esquema](https://aka.ms/iot-security-github-module-schema) de configuração do agente de segurança do IOT para fazer alterações.  

## <a name="configuration-objects"></a>Objetos de configuração 

As propriedades relacionadas a cada central de segurança do Azure para o agente de segurança do IoT estão localizadas no objeto de configuração do agente, na seção propriedades desejadas, do módulo **azureiotsecurity** . 

Para modificar a configuração, crie e modifique esse objeto dentro da identidade de **azureiotsecurity** do módulo. 

Se o objeto de configuração do agente não existir no módulo **azureiotsecurity** , todos os valores de Propriedade do agente de segurança serão definidos como padrão. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Esquema de configuração e validação 

Certifique-se de validar a configuração do agente nesse [esquema](https://aka.ms/iot-security-github-module-schema). Um agente não será iniciado se o objeto de configuração não corresponder ao esquema.

 
Se, enquanto o agente está em execução, o objeto de configuração é alterado para uma configuração não válida (a configuração não corresponde ao esquema), o agente irá ignorar a configuração inválida e continuará usando a configuração atual. 

### <a name="configuration-validation"></a>Validação de configuração

A central de segurança do Azure para o agente de segurança de IoT relata sua configuração atual na seção Propriedades relatadas da identidade de **azureiotsecurity** do módulo.
O agente relata todas as propriedades disponíveis, se uma propriedade não tiver sido definida pelo usuário, o agente relatará a configuração padrão.

Para validar sua configuração, compare os valores definidos na seção desejada com os valores relatados na seção relatada.

Se houver uma incompatibilidade entre as propriedades desejadas e as relatadas, o agente não conseguirá analisar a configuração.

Valide as propriedades desejadas no [esquema](https://aka.ms/iot-security-github-module-schema), corrija os erros e defina as propriedades desejadas novamente!

> [!NOTE]
> Um alerta de erro de configuração será acionado do agente caso o agente não possa analisar a configuração desejada.
> Compare a seção relatada e desejada para entender se o alerta ainda se aplica

## <a name="editing-a-property"></a>Editando uma propriedade 

Todas as propriedades personalizadas devem ser definidas dentro do objeto de configuração do agente no módulo **azureiotsecurity** .
Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

### <a name="setting-a-property"></a>Definindo uma propriedade

1. No Hub IoT, localize e selecione o dispositivo que você deseja alterar.

2. Clique em seu dispositivo e, em seguida, no módulo **azureiotsecurity** .

3. Clique em **identidade do módulo**...

4. Edite as propriedades que você deseja alterar no módulo de segurança.
   
   Por exemplo, para configurar eventos de conexão como prioridade alta e coletar eventos de alta prioridade a cada 7 minutos, use a configuração a seguir.
   
   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "highPriorityMessageFrequency": {
          "value" : "PT7M"
        },    
        "eventPriorityConnectionCreate": {
          "value" : "High" 
        }
      } 
    }, 
    ```

5. Clique em **Guardar**.

### <a name="using-a-default-value"></a>Usando um valor padrão

Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

## <a name="default-properties"></a>Propriedades padrão 

A tabela a seguir contém as propriedades controláveis da central de segurança do Azure para agentes de segurança de IoT.

Os valores padrão estão disponíveis no esquema adequado no [GitHub](https\://aka.ms/iot-security-module-default).

| Nome| Estado | Valores válidos| Valores predefinidos| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Obrigatório: falso |Valores válidos: Duração no formato ISO 8601 |Valor padrão: PT7M |Intervalo de tempo máximo antes que as mensagens de alta prioridade sejam enviadas.|
|lowPriorityMessageFrequency |Obrigatório: falso|Valores válidos: Duração no formato ISO 8601 |Valor padrão: PT5H |Tempo máximo antes que as mensagens de baixa prioridade sejam enviadas.| 
|snapshotFrequency |Exigir: falso|Valores válidos: Duração no formato ISO 8601 |Valor padrão PT13H |Intervalo de tempo para a criação de instantâneos de status do dispositivo.| 
|maxLocalCacheSizeInBytes |Obrigatório: falso |Valores válidos: |Valor padrão: 2560000, maior que 8192 | Armazenamento máximo (em bytes) permitido para o cache de mensagens de um agente. Quantidade máxima de espaço permitido para armazenar mensagens no dispositivo, antes que as mensagens sejam enviadas.| 
|maxMessageSizeInBytes |Obrigatório: falso |Valores válidos: Um número positivo, maior que 8192, menor que 262144 |Valor padrão: 204800 |Tamanho máximo permitido de um agente para uma mensagem de nuvem. Essa configuração controla a quantidade de dados máximos enviados em cada mensagem. |
|eventPriority${EventName} |Obrigatório: falso |Valores válidos: Alta, baixa, desativada |Valores padrão: |Prioridade de cada evento gerado por agente | 

### <a name="supported-security-events"></a>Eventos de segurança com suporte

|Nome do evento| propertyName | Default Value| Evento de instantâneo| Status de detalhes  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento de diagnóstico|eventPriorityDiagnostic| Desativado| False| Eventos de diagnóstico relacionados ao agente. Use esse evento para log detalhado.| 
|Erro de configuração |eventPriorityConfigurationError |Baixa |False |O agente não pôde analisar a configuração. Verifique a configuração em relação ao esquema.| 
|Estatísticas de eventos descartados |eventPriorityDroppedEventsStatistics |Baixa |True|Estatísticas de evento relacionadas ao agente. |
|Estatísticas de mensagem|eventPriorityMessageStatistics |Baixa |True |Estatísticas de mensagens relacionadas ao agente. |
|Hardware conectado|eventPriorityConnectedHardware |Baixa |True |Instantâneo de todo o hardware conectado ao dispositivo.|
|Portas de escuta|eventPriorityListeningPorts |Alta |True |Instantâneo de todas as portas de escuta abertas no dispositivo.|
|Criar processo |eventPriorityProcessCreate |Baixa |False |Audita a criação do processo no dispositivo.|
|Término do processo|eventPriorityProcessTerminate |Baixa |False |Audita o encerramento do processo no dispositivo.| 
|Informações do sistema |eventPrioritySystemInformation |Baixa |True |Um instantâneo das informações do sistema (por exemplo: SO ou CPU).| 
|Usuários locais| eventPriorityLocalUsers |Alta |True|Um instantâneo dos usuários locais registrados no sistema. |
|Iniciar sessão|  eventPriorityLogin |Alta|False|Auditar os eventos de logon para o dispositivo (logons locais e remotos).|
|Criação de conexão |eventPriorityConnectionCreate|Baixa|False|Audita as conexões TCP criadas de e para o dispositivo. |
|Configuração do firewall| eventPriorityFirewallConfiguration|Baixa|True|Instantâneo da configuração de firewall do dispositivo (regras de firewall). |
|Linha de base do so| eventPriorityOSBaseline| Baixa|True|Instantâneo da verificação de linha de base do so do dispositivo.|
|
 

## <a name="next-steps"></a>Passos Seguintes

- [Entender a central de segurança do Azure para obter recomendações de IoT](concept-recommendations.md)
- [Explore a central de segurança do Azure para alertas de IoT](concept-security-alerts.md)
- [Acessar dados brutos de segurança](how-to-security-data-access.md)
