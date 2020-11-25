---
title: incluir ficheiro
description: incluir ficheiro
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999195"
---
Nome | URL comercial | URL do governo | Descrição
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Utilizado para controlo de acessos e gestão de identidade utilizando o Azure Ative Directory. 
Cópia de segurança | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Utilizado para transferência de dados de replicação e orquestração.
Replicação | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Utilizado para operações e coordenação de gestão de replicações.
Armazenamento | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Utilizado para acesso à conta de armazenamento que armazena os dados replicados.
Telemetria (opcional) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Usado para telemetria.
Sincronização de hora | ``time.windows.com`` | ``time.nist.gov`` | Usado para verificar a sincronização do tempo entre o sistema e o tempo global em todas as implementações.


