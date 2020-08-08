---
title: Configurar diagnósticos
titleSuffix: Azure Digital Twins
description: Veja como ativar o registo com as definições de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: d47bb7cc868c5733c6e36290f097fec783764cd3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003577"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Resolução de problemas Azure Digital Twins: Registo de diagnósticos

A Azure Digital Twins recolhe [métricas](troubleshoot-metrics.md) para a sua instância de serviço que dão informações sobre o estado dos seus recursos. Você pode usar estas métricas para avaliar a saúde geral do serviço Azure Digital Twins e os recursos ligados a ele. Estas estatísticas viradas para o utilizador ajudam-no a ver o que se passa com as suas Gémeas Digitais Azure e ajudam a realizar análises de causa-raiz em problemas sem necessidade de contactar o suporte do Azure.

Este artigo mostra-lhe como ligar **o registo de diagnósticos** para os seus dados de métricas a partir da sua instância Azure Digital Twins. Pode utilizar estes registos para ajudá-lo a resolver problemas de serviço e configurar definições de diagnóstico para enviar métricas Azure Digital Twins para diferentes destinos. Pode ler mais sobre estas definições na [*Criar definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.*](../azure-monitor/platform/diagnostic-settings.md)

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Ligue as definições de diagnóstico com o portal Azure

Eis como ativar as definições de diagnóstico para a sua instância Azure Digital Twins:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **as definições** de diagnóstico do menu e, em seguida, **adicione a definição de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

3. Na página que se segue, preencha os seguintes valores:
     * **Nome de definição de**diagnóstico : Dê um nome às definições de diagnóstico.
     * **Detalhes da categoria**: Escolha quais as operações que pretende monitorizar e verifique as caixas para permitir diagnósticos para essas operações. As operações que as definições de diagnóstico podem reportar são:
        - DigitalTwinsOperação
        - EventRoutesOperação
        - ModelosOperação
        - Consulta
        - AllMetrics
        
        Para mais detalhes sobre estas opções, consulte a secção [*de detalhes*](#category-details) da categoria abaixo.
     * **Detalhes do destino**: Escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação das três opções:
        - Enviar para o Log Analytics
        - Arquivar numa conta de armazenamento
        - Transmitir em fluxo para um hub de eventos

        Pode ser-lhe pedido que preencha detalhes adicionais se forem necessários para a sua seleção de destino.  
    
4. Guarde as novas definições. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo configurado de volta na página **de definições de Diagnóstico,** para o seu exemplo. 

## <a name="category-details"></a>Detalhes da categoria

Aqui estão mais detalhes sobre as categorias de registo que podem ser selecionadas em **detalhes de categoria** ao configurar configurações de diagnóstico.

| Categoria do registo | Descrição |
| --- | --- |
| ADTModelsOperação | Registar todas as chamadas da API relativas a Modelos |
| ADTQueryOperação | Registar todas as chamadas da API relativas a consultas |
| ADTEventRoutesOperação | Regisque todas as chamadas da API relativas às Rotas de Eventos, bem como a saída de eventos da Azure Digital Twins para um serviço de ponto final como Event Grid, Event Hubs e Service Bus |
| ADTDigitalTwinsOperação | Registar todas as chamadas da API relativas às Gémeas Digitais Azure |

Cada categoria de registo consiste em operações de escrita, leitura, exclusão e ação.  Estes mapas para rest API chamadas da seguinte forma:

| Tipo de evento | Operações REST API |
| --- | --- |
| Escrita | PUT E PATCH |
| Ler | GET |
| Eliminar | DELETE |
| Ação | POST |

Aqui está uma lista completa das operações e [correspondentes chamadas API de Azure Digital Twins](https://docs.microsoft.com/rest/api/azure-digitaltwins/) REST que são registadas em cada categoria. 

>[!NOTE]
> Cada categoria de registo contém várias chamadas de API de operações/REST. Na tabela abaixo, cada categoria de registo mapeia todas as chamadas de API de operações/REST por baixo até à lista da próxima categoria de registo. 

| Categoria do registo | Operação | REST Chamadas API e outros eventos |
| --- | --- | --- |
| ADTModelsOperação | Microsoft.DigitalTwins/models/write | Atualização de modelos digitais twin api |
|  | Microsoft.DigitalTwins/modelos/ler | Modelos twin digital obter por Id e APIs listar |
|  | Microsoft.DigitalTwins/models/delete | Modelos Gémeos Digitais Delete API |
|  | Microsoft.DigitalTwins/modelos/ação | Modelos Twin Digitais Adicionam API |
| ADTQueryOperação | Microsoft.DigitalTwins/consulta/ação | Consulta Gémeas API |
| ADTEventRoutesOperação | Microsoft.DigitalTwins/eventroutes/write | Rotas do Evento Adicionar API |
|  | Microsoft.DigitalTwins/eventroutes/read | Rotas de eventos obter por Id e APIs listar |
|  | Microsoft.DigitalTwins/eventroutes/delete | Rotas do evento Eliminar API |
|  | Microsoft.DigitalTwins/eventroutes/action | Egress de um evento para um serviço de Ponto Final (não uma chamada da API) |
| ADTDigitalTwinsOperação | Microsoft.DigitalTwins/digitaltwins/write | Gémeos Digitais Adicionar, Adicionar Relacionamento, Atualização, Componente de Atualização |
|  | Microsoft.DigitalTwins/digitaltwins/read | Gémeos Digitais Obter Por Id, Obter Componente, Obter Relacionamento por ID, Lista de Relacionamentos De Entrada, Relacionamentos de Lista |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Gémeos Digitais Delete, Excluir Relacionamento |
|  | Microsoft.DigitalTwins/digitaltwins/action | Gémeos Digitais Enviam Telemetria Componente, Enviar Telemetria |

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a configuração de diagnósticos, consulte [*recolher e consumir dados de registo dos seus recursos Azure.*](../azure-monitor/platform/platform-logs-overview.md)
* Para obter informações sobre as métricas Azure Digital Twins, consulte [*resolução de problemas: Ver métricas com O Monitor Azure*](troubleshoot-metrics.md).
* Para ver como ativar alertas para as suas métricas, consulte [*Resolução de Problemas: Configurar alertas*](troubleshoot-alerts.md).
