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
ms.openlocfilehash: 76020b3c1f28e5b5f6363aef181b76bc93a9613e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294498"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

| Campo | Tipo de dados | Descrição |
| ---- | ---- | ---- |
| **Dados Adicionais** | dynamic | Alertas contam, contagem de marcadores, contagem de comentários, alerta nomes de produtos e táticas |
| **Alertas** | dynamic | Alertas a partir do qual incidente foi criado |
| **BookmarkIds** | dynamic | Entidades com marcadores |
| **Classificação** | cadeia | Classificação de encerramento de incidentes |
| **ClassificaçãoComoção** | cadeia | Comentário de classificação de encerramento de incidentes |
| **ClassificaçãoReason** | cadeia | Motivo de classificação de encerramento de incidentes |
| **Horário de encerramento** | datetime | Timetamp (UTC) de quando o incidente foi encerrado pela última vez |
| **Comentários** | dynamic | Comentários de incidentes |
| **Tempo criado** | datetime | Timetamp (UTC) de quando o incidente foi criado |
| **Descrição** | cadeia | Descrição do incidente |
| **Primeira Hora de Atividade** | datetime | Primeira vez no evento |
| **FirstModifiedTime** | datetime | Timetamp (UTC) de quando o incidente foi modificado pela primeira vez |
| **Nome incidente** | cadeia | GUID Interno |
| **Número de incidentes** | int |  |
| **IncidenteUrl** | cadeia | Ligação ao incidente |
| **Etiquetas** | dynamic | Etiquetas |
| **Última Hora de Atividade** | datetime | Última hora do evento |
| **Última Hora Da Modificação** | datetime | Timetamp (UTC) de quando o incidente foi modificado pela última vez <br>(a modificação descrita pelo registo atual) |
| **ModificadoBy** | cadeia | Utilizador ou sistema que modificou o incidente |
| **Proprietário** | dynamic |  |
| **RelacionadosAnlyticRuleIds** | dynamic | Regras a partir das quais os alertas do incidente foram desencadeados |
| **Gravidade** | cadeia | Gravidade do incidente (Alto/Médio/Baixo/Informativo) |
| **SourceSystem** | cadeia | Constante ('Azure') |
| **Estado** | cadeia |  |
| **TenantId** | cadeia |  |
| **TimeGenerated** | datetime | Timetamp (UTC) de quando o registo atual foi criado <br>(após modificação do incidente) |
| **Título** | cadeia | 
| **Tipo** | cadeia | Constante ('SecurityIncident') |
|
