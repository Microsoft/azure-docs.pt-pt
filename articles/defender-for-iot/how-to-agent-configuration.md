---
title: Configurar agentes de segurança
description: Saiba como configurar o Defender para agentes de segurança IoT para utilização com o Serviço de Segurança IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: c348b800e9587f13e6ff004317a2aa12efb03394
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939400"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: Configure agentes de segurança

Este artigo explica o Defender para agentes de segurança IoT e detalha como alterá-los e configurá-los.

> [!div class="checklist"]
> * Configurar agentes de segurança
> * Alterar o comportamento do agente editando propriedades gémeas
> * Descubra a configuração padrão

## <a name="agents"></a>Agentes

O Defender para os agentes de segurança IoT recolhe dados de dispositivos IoT e executa ações de segurança para mitigar as vulnerabilidades detetadas. A configuração do agente de segurança é controlável usando um conjunto de propriedades gémeas do módulo que pode personalizar. Em geral, as atualizações secundárias a estas propriedades são pouco frequentes.

O objeto de configuração de configuração de dois agentes de segurança do IoT é um objeto de formato JSON. O objeto de configuração é um conjunto de propriedades controláveis que pode definir para controlar o comportamento do agente.

Estas configurações ajudam-no a personalizar o agente para cada cenário necessário. Por exemplo, excluir automaticamente alguns eventos, ou manter o consumo de energia a um nível mínimo são possíveis configurando estas propriedades.

Utilize o esquema de configuração do agente de segurança IoT para [estois.](https://aka.ms/iot-security-github-module-schema)

## <a name="configuration-objects"></a>Objetos de configuração

As propriedades relacionadas com cada agente de segurança Defender for IoT estão localizadas no objeto de configuração do agente, dentro da secção de propriedades desejadas, do módulo **de insegurança azureiot.**

Para modificar a configuração, crie e modifique este objeto dentro da identidade dupla do módulo **de azureiotsecurity.**

Se o objeto de configuração do agente não existir no módulo **de azureiotsecurity** twin, todos os valores de propriedade do agente de segurança estão definidos para predefinido.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Esquema de configuração e validação

Certifique-se de validar a configuração do seu agente contra este [esquema](https://aka.ms/iot-security-github-module-schema). Um agente não será lançado se o objeto de configuração não corresponder ao esquema.

Se, enquanto o agente estiver em execução, o objeto de configuração for alterado para uma configuração não válida (a configuração não corresponde ao esquema), o agente ignorará a configuração inválida e continuará a utilizar a configuração atual.

### <a name="configuration-validation"></a>Validação de configuração

O Defensor do agente de segurança IoT informa a sua configuração atual dentro da secção de propriedades reportadas do módulo **de azureiotsecurity** identidade dupla.
O agente informa todas as propriedades disponíveis, se uma propriedade não foi definida pelo utilizador, o agente reporta a configuração padrão.

Para validar a sua configuração, compare os valores definidos na secção desejada com os valores relatados na secção relatada.

Se houver um desfasamento entre as propriedades desejadas e as propriedades relatadas, então o agente não foi capaz de analisar a configuração.

Valide as propriedades desejadas contra o [esquema,](https://aka.ms/iot-security-github-module-schema)corrija os erros e volte a definir as propriedades desejadas!

> [!NOTE]
> Um alerta de erro de configuração será disparado do agente caso o agente não tenha sido capaz de analisar a configuração desejada.
> Compare a secção relatada e desejada para entender se o alerta ainda se aplica

## <a name="editing-a-property"></a>Edição de um imóvel

Todas as propriedades personalizadas devem ser definidas dentro do objeto de configuração do agente dentro do módulo **de insegurança azureiot.**
Para utilizar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

### <a name="setting-a-property"></a>Definição de um imóvel

1. No seu Hub IoT, localize e selecione o dispositivo que pretende alterar.

1. Clique no seu dispositivo e, em seguida, no módulo **de insegurança azureiot.**

1. Clique em **Módulo Identidade Twin**.

1. Edite as propriedades que pretende alterar no módulo de segurança.

   Por exemplo, para configurar eventos de ligação como alta prioridade e recolher eventos de alta prioridade a cada 7 minutos, utilize a seguinte configuração.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Clique em **Guardar**.

### <a name="using-a-default-value"></a>Usando um valor padrão

Para utilizar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

## <a name="default-properties"></a>Propriedades predefinidos

A tabela seguinte contém as propriedades controláveis do Defender para agentes de segurança IoT.

Os valores predefinidos estão disponíveis no esquema adequado no [GitHub](https\://aka.ms/iot-security-module-default).

| Nome| Estado | Valores válidos| Valores predefinidos| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|altaPriorityMessageFrequency|Obrigatório: falso |Valores válidos: Duração no Formato ISO 8601 |Valor predefinido: PT7M |Intervalo de tempo máximo antes de serem enviadas mensagens de alta prioridade.|
|baixaPriorityMessageFrequency |Obrigatório: falso|Valores válidos: Duração no Formato ISO 8601 |Valor predefinido: PT5H |Tempo máximo antes de serem enviadas mensagens de baixa prioridade.|
|snapshotFrequency |Exigir: falso|Valores válidos: Duração no Formato ISO 8601 |Valor predefinido PT13H |Intervalo de tempo para a criação de instantâneos de estado do dispositivo.|
|maxLocalCacheSizeInBytes |Obrigatório: falso |Valores válidos: |Valor padrão: 2560000, maior que 8192 | Armazenamento máximo (em bytes) permitido para o cache de mensagem de um agente. Quantidade máxima de espaço permitido para armazenar mensagens no dispositivo, antes de as mensagens serem enviadas.|
|maxMessageSizeInBytes |Obrigatório: falso |Valores válidos: Um número positivo, maior que 8192, menos de 262144 |Valor padrão: 204800 |O tamanho máximo permitido de um agente para a mensagem na nuvem. Esta definição controla a quantidade de dados máximos enviados em cada mensagem. |
|eventPriority${EventName} |Obrigatório: falso |Valores válidos: Alto, Baixo, Desligado |Valores predefinidos: |Prioridade de todos os eventos gerados por agentes |

### <a name="supported-security-events"></a>Eventos de segurança apoiados

|Nome do evento| PropertyName | Valor Predefinido| Evento Snapshot| Estado dos detalhes  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento de diagnóstico|eventoPriorityDiagnostic| Desativado| Falso| Eventos de diagnóstico relacionados com agente. Utilize este evento para a extração verbose.|
|Erro de configuração |eventPriorityConfigurationError |Baixo |Falso |O agente não analisou a configuração. Verifique a configuração contra o esquema.|
|Estatísticas de eventos abandonadas |eventPriorityDroppedEventsStatistics |Baixo |Verdadeiro|Estatísticas de eventos relacionadas com o agente. |
|Hardware conectado|eventPriorityConnectedHardware |Baixo |Verdadeiro |Imagem de todo o hardware ligado ao dispositivo.|
|Portas de escuta|eventoPriorityListeningPorts |Alto |Verdadeiro |Imagem de todas as portas de escuta abertas no dispositivo.|
|Criar processo |eventPriorityProcessCreate |Baixo |Falso |Auditorias processam criação no dispositivo.|
|Fim do processo|eventPriorityProcessTerminar |Baixo |Falso |Auditorias processam a rescisão do dispositivo.|
|Informações do sistema |eventPrioritySystemInformation |Baixo |Verdadeiro |Uma imagem instantânea das informações do sistema (por exemplo: SO ou CPU).|
|Utilizadores locais| eventoPriorityLocalUsers |Alto |Verdadeiro|Uma foto dos utilizadores locais registados dentro do sistema. |
|Iniciar sessão|  eventPriorityLogin |Alto|Falso|Audite os eventos de login no dispositivo (logins locais e remotos).|
|Criar conexão |eventPriorityConnectionCreate|Baixo|Falso|Auditorias ligações TCP criadas de e para o dispositivo. |
|Configuração da firewall| eventPriorityFirewallConfiguration|Baixo|Verdadeiro|Imagem de imagem da configuração da firewall do dispositivo (regras de firewall). |
|Linha de base do SO| eventPriorityOSBaseline| Baixo|Verdadeiro|Imagem de imagem do dispositivo VERIFICAção da linha de base DO.|
|

## <a name="next-steps"></a>Passos seguintes

- [Compreenda as recomendações do Defender para ioT](concept-recommendations.md)
- [Explore o Defender para alertas IoT](concept-security-alerts.md)
- [Aceder a dados de segurança não processados](how-to-security-data-access.md)
