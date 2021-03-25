---
title: Notas de lançamento para aplicação Azure Ferramenta Snapshot consistente para ficheiros Azure NetApp | Microsoft Docs
description: Fornece notas de lançamento para a ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111449"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Notas de lançamento para a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Esta página lista as principais alterações feitas ao AzAcSnap para fornecer novas funcionalidades ou resolver defeitos.

## <a name="march-2021"></a>Março-2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>Pré-visualização AzAcSnap v5.0 (Build:20210318.30771)

AzAcSnap v5.0 Preview (Build:20210318.30771) foi lançado com as seguintes correções e melhorias:

- Removido a necessidade de adicionar o utilizador AZACSNAP aos DBs do Inquilino SAP HANA, consulte a comunicação Enable com a secção [SAP HANA.](azacsnap-installation.md#enable-communication-with-sap-hana)
- Fixe para permitir uma [restauração](azacsnap-cmd-ref-restore.md) com volumes configurados com QOS Manual.
- Adicionou controlo mutex às ligações SSH de aceleração para Azure Large Instance.
- Fixar instalador para manusear nomes de caminhos com espaços e outras questões relacionadas.
- Na preparação para o suporte de outros servidores de base de dados, alterou o parâmetro opcional '--hanasid' para '--dbsid'.

Descarregue a [versão mais recente](https://aka.ms/azacsnapdownload) do instalador e reveja como [começar](azacsnap-get-started.md).

## <a name="next-steps"></a>Passos seguintes

- [Começar com a ferramenta Azure Application Consistent Snapshot](azacsnap-get-started.md)
