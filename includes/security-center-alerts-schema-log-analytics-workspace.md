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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79538834"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Descrição|
|----|----|
|**Nome de alerta**|Nome do visor de alerta|
|**Tipo de alerta**|identificador de alerta único|
|**Nível de confiança**|(Opcional) O nível de confiança deste alerta (Alto/Baixo)|
|**Pontuação de Confiança**|(Opcional) Indicador de confiança numérica do alerta de segurança|
|**Descrição**|Texto de descrição para o alerta|
|**Nome do ecrã**|O nome da exibição do alerta|
|**Tempo final**|O tempo final do alerta (a hora do último evento contribuindo para o alerta)|
|**Entidades**|Uma lista de entidades relacionadas com o alerta. Esta lista pode conter uma mistura de entidades de diversos tipos|
|**Links Estendidos**|(Opcional) Um saco para todas as ligações relacionadas com o alerta. Este saco pode conter uma mistura de links para diferentes tipos|
|**Propriedades Estendidas**|Um saco de campos adicionais que são relevantes para o alerta|
|**IsIncident**|Determina se o alerta é um incidente ou um alerta regular. Um incidente é um alerta de segurança que agrega vários alertas num incidente de segurança.|
|**ProcessamentoEndTime**|Carimbo de tempo UTC em que o alerta foi criado|
|**Nome componente de produto**|(Opcional) O nome de um componente dentro do produto que gerou o alerta.|
|**Nome do produto**|constante ('Centro de Segurança Azure')|
|**ProviderName**|não utilizado|
|**RemediaçãoPassos**|Itens de ação manual a tomar para remediar a ameaça à segurança|
|**ResourceId**|Identificador completo do recurso afetado|
|**Gravidade**|A gravidade do alerta (Alta/Média/Baixa/Informação)|
|**SourceComputerId**|um GUID único para o servidor afetado (se o alerta for gerado no servidor)|
|**SourceSystem**|não utilizado|
|**Hora de Início**|O tempo de início do alerta (a hora do primeiro evento contribuindo para o alerta)|
|**Systemalertid**|Identificador único desta instância de alerta de segurança|
|**Tenantid**|o identificador do administrador-mãe Azure Ative inquilino da subscrição ao abrigo da qual reside o recurso digitalizado|
|**TimeGenerated**|Carimbo de tempo UTC em que a avaliação teve lugar (tempo de verificação do Centro de Segurança) (idêntico ao DiscoveredTimeUTC)|
|**Tipo**|constante ('SecurityAlert')|
|**Nome do fornecedor**|O nome do fornecedor que forneceu o alerta (por exemplo, 'Microsoft')|
|**FornecedorOriginalid**|não utilizado|
|**WorkspaceResourceGroup**|caso o alerta seja gerado numa instância vm, servidor, conjunto de escala de máquina virtual ou caso do Serviço de Aplicações que reporta a um espaço de trabalho, contém o nome do grupo de recursos do espaço de trabalho|
|**SpacespaceSubscriptionId**|caso o alerta seja gerado numa instância vm, servidor, conjunto de escala de máquina virtual ou caso do Serviço de Aplicações que reporta a um espaço de trabalho, contém essa subscrição do espaço de trabalhoId|
|||
