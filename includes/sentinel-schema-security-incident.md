---
title: incluir ficheiro
description: incluir ficheiro
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761729"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

| Campo | Tipo de dados | Descrição |
| ---- | ---- | ---- |
| **Dados Adicionais** | dynamic | Alertas contam, contagem de marcadores, contagem de comentários, alerta nomes de produtos e táticas |
| **Alertas** | dynamic | Alertas a partir do qual incidente foi criado |
| **BookmarkIds** | dynamic | Entidades com marcadores |
| **Classificação** | string | Classificação de encerramento de incidentes |
| **ClassificaçãoComoção** | string | Comentário de classificação de encerramento de incidentes |
| **ClassificaçãoReason** | string | Motivo de classificação de encerramento de incidentes |
| **Tempo fechado** | datetime | Timetamp (UTC) de quando o incidente foi encerrado pela última vez |
| **Comentários** | dynamic | Comentários de incidentes |
| **Tempo criado** | datetime | Timetamp (UTC) de quando o incidente foi criado |
| **Descrição** | string | Descrição do incidente |
| **Primeira Hora de Atividade** | datetime | Primeira vez no evento |
| **FirstModifiedTime** | datetime | Timetamp (UTC) de quando o incidente foi modificado pela primeira vez |
| **Nome incidente** | string | GUID Interno |
| **Número de incidentes** | int |  |
| **IncidenteUrl** | string | Ligação ao incidente |
| **Etiquetas** | dynamic | Etiquetas |
| **Última Hora de Atividade** | datetime | Última hora do evento |
| **Última Hora Da Modificação** | datetime | Timetamp (UTC) de quando o incidente foi modificado pela última vez <br>(a modificação descrita pelo registo atual) |
| **ModificadoBy** | string | Utilizador ou sistema que modificou o incidente |
| **Proprietário** | dynamic |  |
| **RelacionadosAnlyticRuleIds** | dynamic | Regras a partir das quais os alertas do incidente foram desencadeados |
| **Gravidade** | string | Gravidade do incidente (Alto/Médio/Baixo/Informativo) |
| **SourceSystem** | string | Constante ('Azure') |
| **Estado** | string |  |
| **TenantId** | string |  |
| **TimeGenerated** | datetime | Timetamp (UTC) de quando o registo atual foi criado <br>(após modificação do incidente) |
| **Título** | string | 
| **Tipo** | string | Constante ('SecurityIncident') |
|
