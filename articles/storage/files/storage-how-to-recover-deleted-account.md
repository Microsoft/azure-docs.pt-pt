---
title: Usar um compartilhamento de arquivos do Azure com o armazenamento do Azure | Microsoft Docs
description: Saiba como utilizar uma partilha de ficheiros do Azure com o Windows e o Windows Server.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233795"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Como recuperar uma conta de armazenamento excluída

O armazenamento do Azure fornece resiliência de dados por meio de réplicas automatizadas, mas não impede que os usuários ou o código do aplicativo corrompam dados, seja acidentalmente ou intencionalmente. Manter a fidelidade de dados durante instâncias de erro de aplicativo ou de usuário requer técnicas mais avançadas, como copiar os dados para um local de armazenamento secundário com um log de auditoria.

A tabela a seguir fornece uma visão geral do escopo da recuperação da conta de armazenamento, dependendo da estratégia de replicação.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Azure Resource Manager da conta de armazenamento|Sim|Sim|Sim|Sim|
|Conta de armazenamento clássica|Sim|Sim|Sim|Sim|

Reúna as informações a seguir e arquivo uma solicitação de suporte com Suporte da Microsoft:

* Nome da conta de armazenamento
* Data de exclusão
* Região da conta de armazenamento
* Como a conta de armazenamento foi excluída?
* Qual método excluiu a conta de armazenamento? (Portal, PowerShell, etc.)

Pontos importantes

* Em geral, pode levar até 15 dias a partir do momento da exclusão do serviço de armazenamento para executar a coleta de lixo, portanto, a recuperação das contas de armazenamento pode não ser recuperada com um SLA.
* Suporte da Microsoft tentará recuperar o contêiner/conta em uma base de melhor esforço e não poderá garantir a recuperação.

> [!NOTE]
> A recuperação poderá não ser bem-sucedida se a conta tiver sido recriada. Se você já criou a conta novamente, deverá excluí-la primeiro antes que a recuperação possa ser tentada.
