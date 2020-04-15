---
title: Configurar agentes de segurança
description: Saiba como configurar o Azure Security Center para agentes de segurança IoT para uso com o Centro de Segurança Azure para o serviço de segurança IoT.
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
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: e41b7222ca89976674973e1eb700d62765b306f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311333"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: Configure agentes de segurança

Este artigo explica o Azure Security Center para agentes de segurança IoT, e detalha como mudá-los e configurá-los.

> [!div class="checklist"]
> * Configurar agentes de segurança
> * Alterar o comportamento do agente editando propriedades gémeas
> * Descubra a configuração padrão

## <a name="agents"></a>Agentes

O Azure Security Center para agentes de segurança IoT recolhe dados de dispositivos IoT e realiza ações de segurança para mitigar as vulnerabilidades detetadas. A configuração do agente de segurança é controlável usando um conjunto de propriedades gémeas de módulo que pode personalizar. Em geral, as atualizações secundárias a estas propriedades são pouco frequentes.

O Azure Security Center para o objeto de configuração twin do agente de segurança IoT é um objeto de formato JSON. O objeto de configuração é um conjunto de propriedades controláveis que pode definir para controlar o comportamento do agente.

Estas configurações ajudam-no a personalizar o agente para cada cenário necessário. Por exemplo, excluir automaticamente alguns eventos, ou manter o consumo de energia a um nível mínimo são possíveis configurando estas propriedades.

Utilize o Centro de Segurança Azure para configuração de [um](https://aka.ms/iot-security-github-module-schema) agente de segurança IoT para fazer alterações.

## <a name="configuration-objects"></a>Objetos de configuração

Propriedades relacionadas com todos os agentes de segurança do Azure Security Center para ioT estão localizadas no objeto de configuração do agente, dentro da secção de propriedades desejada, do módulo de **segurança azureiot.**

Para modificar a configuração, crie e modifique este objeto dentro da identidade dupla do módulo de **segurança azul.**

Se o objeto de configuração do agente não existir no módulo de segurança **azul** twin, todos os valores de propriedade do agente de segurança estão definidos como padrão.

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

O Azure Security Center for IoT security agent informa a sua configuração atual dentro da secção de propriedades reportadas da identidade gémea do módulo de **segurança azureiot.**
O agente informa todas as propriedades disponíveis, se uma propriedade não foi definida pelo utilizador, o agente relata a configuração padrão.

Para validar a sua configuração, compare os valores definidos na secção desejada com os valores relatados na secção reportada.

Se houver um desfasamento entre as propriedades desejadas e as propriedades reportadas, então o agente não foi capaz de analisar a configuração.

Valide as suas propriedades desejadas contra o [esquema,](https://aka.ms/iot-security-github-module-schema)corrija os erros e volte a definir as propriedades desejadas!

> [!NOTE]
> Será disparado um alerta de erro de configuração do agente no caso de o agente não ter sido capaz de analisar a configuração desejada.
> Compare a secção reportada e desejada para entender se o alerta ainda se aplica

## <a name="editing-a-property"></a>Editar uma propriedade

Todas as propriedades personalizadas devem ser definidas dentro do objeto de configuração do agente dentro do módulo de **segurança azul** twin.
Para utilizar um valor de propriedade predefinido, retire a propriedade do objeto de configuração.

### <a name="setting-a-property"></a>Definição de uma propriedade

1. No seu Hub IoT, localize e selecione o dispositivo que pretende alterar.

1. Clique no seu dispositivo e, em seguida, no módulo **de segurança azureiot.**

1. Clique no **Módulo Identidade Twin**.

1. Editar as propriedades que pretende alterar no módulo de segurança.

   Por exemplo, para configurar eventos de conexão como alta prioridade e recolher eventos de alta prioridade a cada 7 minutos, use a seguinte configuração.

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

### <a name="using-a-default-value"></a>Usando um valor predefinido

Para utilizar um valor de propriedade predefinido, retire a propriedade do objeto de configuração.

## <a name="default-properties"></a>Propriedades padrão

A tabela seguinte contém as propriedades controláveis do Azure Security Center para agentes de segurança IoT.

Os valores predefinidos estão disponíveis no esquema adequado no [GitHub](https\://aka.ms/iot-security-module-default).

| Nome| Estado | Valores válidos| Valores predefinidos| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|altaPrioridadeMessageFrequência|Obrigatório: falso |Valores válidos: Duração no formato ISO 8601 |Valor predefinido: PT7M |Intervalo de tempo máximo antes de enviar mensagens de alta prioridade.|
|baixaPrioridadeMessageFrequência |Obrigatório: falso|Valores válidos: Duração no formato ISO 8601 |Valor predefinido: PT5H |Tempo máximo antes de enviarem mensagens de baixa prioridade.|
|snapshotFrequência |Exigir: falso|Valores válidos: Duração no formato ISO 8601 |Valor predefinido PT13H |Intervalo de tempo para a criação de instantâneos do estado do dispositivo.|
|maxLocalCacheSizeInBytes |Obrigatório: falso |Valores válidos: |Valor predefinido: 2560000, superior a 8192 | Armazenamento máximo (in bytes) permitido para o cache de mensagem de um agente. Quantidade máxima de espaço permitida para armazenar mensagens no dispositivo, antes de enviar mensagens.|
|maxMessageSizeInBytes |Obrigatório: falso |Valores válidos: Um número positivo, superior a 8192, inferior a 262144 |Valor predefinido: 204800 |O tamanho máximo permitido de um agente para cloud message. Esta definição controla a quantidade de dados máximos enviados em cada mensagem. |
|eventoPriority${EventName} |Obrigatório: falso |Valores válidos: Alto, Baixo, Off |Valores predefinidos: |Prioridade de todos os eventos gerados por agentes |

### <a name="supported-security-events"></a>Eventos de segurança apoiados

|Nome do evento| PropertyName | Valor Predefinido| Evento Snapshot| Estado dos Detalhes  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento de diagnóstico|eventoPriorityDiagnostic| Desativado| Falso| Eventos de diagnóstico relacionados com o agente. Utilize este evento para a exploração madeireira verbosa.|
|Erro de configuração |eventoPriorityConfigurationError |Baixa |Falso |O agente não analisou a configuração. Verifique a configuração contra o esquema.|
|Estatísticas de eventos derrubados |eventoPriorityDroppedEventsStatistics |Baixa |Verdadeiro|Estatísticas de eventos relacionadas com o agente. |
|Hardware conectado|eventoPriorityConnectedHardware |Baixa |Verdadeiro |Foto de todo o hardware ligado ao dispositivo.|
|Portas de escuta|eventoPriorityListeningPorts |Alta |Verdadeiro |Instantâneo de todas as portas de escuta abertas no dispositivo.|
|Processo criar |eventoPriorityProcessCreate |Baixa |Falso |Auditorias processam criação no dispositivo.|
|Processo encerrado|eventoPriorityProcessTerminate |Baixa |Falso |Auditorias processam rescisão no dispositivo.|
|Informação do sistema |eventoPrioritySystemInformation |Baixa |Verdadeiro |Uma imagem instantânea da informação do sistema (por exemplo: OS ou CPU).|
|Utilizadores locais| eventoPrioritárioLocalUsers |Alta |Verdadeiro|Uma foto dos utilizadores locais registados dentro do sistema. |
|Iniciar sessão|  eventoPriorityLogin |Alta|Falso|Audite os eventos de login no dispositivo (logins locais e remotos).|
|Criação de conexão |eventoPriorityConnectionCreate|Baixa|Falso|Audita as ligações TCP criadas de e para o dispositivo. |
|Configuração da firewall| eventoPriorityFirewallConfiguration|Baixa|Verdadeiro|Instantâneo da configuração da firewall do dispositivo (regras de firewall). |
|Linha de base do OS| eventoPriorityOSBaseline| Baixa|Verdadeiro|Foto instantânea da verificação da linha de base do dispositivo OS.|
|

## <a name="next-steps"></a>Passos seguintes

- [Compreender o Centro de Segurança Azure para recomendações do IoT](concept-recommendations.md)
- [Explore o Centro de Segurança Azure para alertas IoT](concept-security-alerts.md)
- [Aceder a dados de segurança não processados](how-to-security-data-access.md)
