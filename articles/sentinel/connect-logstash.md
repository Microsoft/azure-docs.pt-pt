---
title: Ligue fontes de dados através de Logstash a Azure Sentinel | Microsoft Docs
description: Saiba como utilizar o Logstash para encaminhar registos de fontes de dados externas para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: d388478fb3bc9b4e355d8c3cd3f16c0a785b8b27
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578924"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Utilize o Logstash para ligar fontes de dados ao Azure Sentinel

> [!IMPORTANT]
> A ingestão de dados utilizando o plugin de saída Logstash está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Utilizando o novo plugin de saída do Azure Sentinel para o motor de recolha de **dados logstash,** pode agora enviar qualquer tipo de registo que pretenda através do Logstash diretamente para o seu espaço de trabalho Log Analytics em Azure Sentinel. Os seus registos serão enviados para uma tabela personalizada que definirá utilizando o plugin de saída.

Para saber mais sobre como trabalhar com o motor de recolha de dados Logstash, consulte [Começar com Logstash.](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)

## <a name="overview"></a>Descrição Geral

### <a name="architecture-and-background"></a>Arquitetura e fundo

![Diagrama da arquitetura log stash.](./media/connect-logstash/logstash-architecture.png)

O motor Logstash é composto por três componentes:

- Plugins de entrada: Recolha personalizada de dados de várias fontes.
- Plugins de filtro: Manipulação e normalização de dados de acordo com critérios especificados.
- Plugins de saída: Envio personalizado de dados recolhidos e tratados para vários destinos.

> [!NOTE]
> O Azure Sentinel suporta apenas o plugin de saída fornecido. Não suporta plugins de saída de terceiros para Azure Sentinel, ou qualquer outro plugin logstash de qualquer tipo.

O plugin de saída Azure Sentinel para Logstash envia dados formatados por JSON para o seu espaço de trabalho Log Analytics, utilizando a API do Colecionador de Dados HTTP Log Analytics. Os dados são ingeridos em registos personalizados.

- Saiba mais sobre a [API Log Analytics REST](/rest/api/loganalytics/create-request).
- Saiba mais sobre [registos personalizados.](../azure-monitor/agents/data-sources-custom-logs.md)

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Implementar o plugin de saída Azure Sentinel em Logstash

### <a name="step-1-installation"></a>Passo 1: Instalação

O plugin de saída Azure Sentinel está disponível na coleção Logstash.

- Siga as instruções no documento Logstash [Working with plugins](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) para instalar o ***[plugin microsoft-logstash-output-azure-loganalytics.](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)***
   
- Se o seu sistema de Logstash não tiver acesso à Internet, siga as instruções do documento de [Gestão de Plugin Offline](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) de Logstash para preparar e utilizar um pacote de plugin offline. (Isto exigirá que você construa outro sistema logstash com acesso à Internet.)

### <a name="step-2-configuration"></a>Passo 2: Configuração

Utilize as informações na Estrutura de Logstash de um documento [de ficheiro config](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) e adicione o plugin de saída Azure Sentinel à configuração com as seguintes teclas e valores. (A sintaxe de ficheiro config adequada é mostrada após a tabela.)

| Nome do campo | Tipo de dados | Description |
|----------------|---------------|-----------------|
| `workspace_id` | cadeia (de carateres) | Insira o seu espaço de trabalho ID GUID. * |
| `workspace_key` | string | Insira a sua chave primária do espaço de trabalho GUID. * |
| `custom_log_table_name` | string | Desa ajuste o nome da tabela na qual os troncos serão ingeridos. Apenas um nome de mesa por plugin de saída pode ser configurado. A tabela de registos aparecerá em Azure Sentinel em **Logs,** em **Tabelas** na categoria **Registos Personalizados,** com um `_CL` sufixo. |
| `endpoint` | string | Campo opcional. Por predefinição, este é o ponto final do Log Analytics. Utilize este campo para definir um ponto final alternativo. |
| `time_generated_field` | string | Campo opcional. Esta propriedade substitui o campo **timegenerated** padrão em Log Analytics. Introduza o nome do campo de marcação de tempo na fonte de dados. Os dados no campo devem estar em conformidade com o formato ISO 8601 ( `YYYY-MM-DDThh:mm:ssZ` ) |
| `key_names` | matriz | Insira uma lista de campos de esquemas de saída do Log Analytics. Cada item da lista deve ser incluído em cotações únicas e os itens separados por vírgulas, e toda a lista incluída em parênteses quadrados. Veja o exemplo abaixo. |
| `plugin_flush_interval` | número | Campo opcional. Definir para definir o intervalo máximo (em segundos) entre as transmissões de mensagens para Log Analytics. O padrão é 5. |
    | `amount_resizing` | boolean | True ou false. Ativar ou desativar o mecanismo automático de escala, que ajusta o tamanho do tampão de mensagem de acordo com o volume de dados de registo recebidos. |
| `max_items` | número | Campo opcional. Só se aplica se `amount_resizing` for definido como "falso". Utilize para definir uma tampa no tamanho do tampão de mensagem (em registos). A predefinição é 2 000.  |

\*Pode encontrar o ID do espaço de trabalho e a chave primária no recurso do espaço de trabalho, sob **gestão de Agentes.**

#### <a name="sample-configurations"></a>Configurações de amostras

Aqui estão algumas configurações de amostra que usam algumas opções diferentes.

- Uma configuração básica que utiliza um tubo de entrada de filebeat:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Uma configuração básica que utiliza um tubo de entrada TCP:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Uma configuração avançada:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Visite o [repositório gitHub](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) do plugin de saída para saber mais sobre o seu funcionamento interno, configuração e definições de desempenho.

### <a name="step-3-restart-logstash"></a>Passo 3: Reiniciar logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>Passo 4: Ver registos de entrada em Azure Sentinel

1. Verifique se as mensagens estão a ser enviadas para o plugin de saída.

1. A partir do menu de navegação Azure Sentinel, clique em **Registos**. No título **tabelas,** expanda a categoria **Registos Personalizados.** Encontre e clique no nome da tabela especificada (com um `_CL` sufixo) na configuração.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Screenshot de registos personalizados de registos.":::

1. Para ver os registos na tabela, consulte a tabela utilizando o nome da mesa como esquema.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Screenshot de uma consulta de registos personalizados de registos.":::

## <a name="monitor-output-plugin-audit-logs"></a>Monitor de saída plugin registos de auditoria

Para monitorizar a conectividade e a atividade do plugin de saída Azure Sentinel, ative o ficheiro de registo logstash apropriado. Consulte o documento ['Layout do Diretório de Registo'](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) para a localização do ficheiro de registo.

Se não estiver a ver nenhum dado neste ficheiro de registo, gere e envie alguns eventos localmente (através dos plugins de entrada e filtro) para se certificar de que o plugin de saída está a receber dados. O Azure Sentinel irá suportar apenas questões relacionadas com o plugin de saída.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar o Logstash para ligar fontes de dados externas ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa a detetar ameaças com o Azure Sentinel, utilizando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
