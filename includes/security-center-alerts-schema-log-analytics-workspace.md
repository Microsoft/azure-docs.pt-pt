---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "79538834"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Descrição|
|----|----|
|**Nome de alerta**|Nome do visor de alerta|
|**AlertaType**|identificador de alerta único|
|**ConfiançaLevel**|(Opcional) O nível de confiança deste alerta (Alto/Baixo)|
|**ConfidenceScore**|(Opcional) Indicador de confiança numérica do alerta de segurança|
|**Descrição**|Texto de descrição para o alerta|
|**DisplayName**|O nome de exibição do alerta|
|**Fim do Tempo**|O tempo de fim de impacto do alerta (a hora do último evento contribuindo para o alerta)|
|**Entidades**|Uma lista de entidades relacionadas com o alerta. Esta lista pode conter uma mistura de entidades de diversos tipos|
|**Estendidos**|(Opcional) Um saco para todos os links relacionados com o alerta. Este saco pode conter uma mistura de links para diferentes tipos|
|**Extensões**|Um saco de campos adicionais que são relevantes para o alerta|
|**IsIncident**|Determina se o alerta é um incidente ou um alerta regular. Um incidente é um alerta de segurança que agrega vários alertas num incidente de segurança.|
|**ProcessamentoEndTime**|Calendário utc em que o alerta foi criado|
|**Nome de ProdutoComponentName**|(Opcional) O nome de um componente dentro do produto que gerou o alerta.|
|**Nome do produto**|constante ('Azure Security Center')|
|**ProviderName**|não éused|
|**ReparaçãoSteps**|Itens de ação manual a tomar para remediar a ameaça à segurança|
|**ResourceId**|Identificador completo do recurso afetado|
|**Gravidade**|A gravidade do alerta (Alto/Médio/Baixo/Informativo)|
|**SourceComputerId**|um GUID único para o servidor afetado (se o alerta for gerado no servidor)|
|**SourceSystem**|não éused|
|**Horário de início**|A hora de início de impacto do alerta (a hora do primeiro evento contribuindo para o alerta)|
|**SystemAlertId**|Identificador único desta instância de alerta de segurança|
|**TenantId**|o identificador do inquilino do diretório ativo do pai Azure Ative da subscrição em que reside o recurso digitalizado|
|**TimeGenerated**|Prazo utc em que a avaliação ocorreu (tempo de digitalização do Centro de Segurança) (idêntico ao DescobritimeUTC)|
|**Tipo**|constante ('SecurityAlert')|
|**Nome do fornecedor**|O nome do fornecedor que forneceu o alerta (por exemplo, 'Microsoft')|
|**FornecedorOriginalId**|não éused|
|**WorkspaceResourceGroup**|no caso de o alerta ser gerado numa instância VM, Server, Virtual Machine Scale set ou App Service que reporte a um espaço de trabalho, contém o nome do grupo de recursos do espaço de trabalho|
|**WorkspaceSubscriptionId**|no caso de o alerta ser gerado numa instância VM, Server, Virtual Machine Scale set ou App Service que reporte a um espaço de trabalho, contém essa subscrição do espaço de trabalhoId|
|||
