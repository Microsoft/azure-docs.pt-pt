---
title: Como recuperar uma conta de armazenamento eliminada
description: Saiba como recuperar uma conta de armazenamento eliminada
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252640"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Como recuperar uma conta de armazenamento eliminada

O Azure Storage fornece resiliência de dados através de réplicas automatizadas, mas não impede que utilizadores ou código de aplicação corrompam dados, acidentalmente ou maliciosamente. Manter a fidelidade dos dados durante os casos de aplicação ou erro do utilizador requer técnicas mais avançadas, tais como copiar os dados para um local de armazenamento secundário com um registo de auditoria.

O quadro seguinte fornece uma visão geral do âmbito de recuperação da conta de armazenamento, dependendo da estratégia de replicação.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Gestor de recursos da conta de armazenamento Azure|Sim|Sim|Sim|Sim|
|Conta de armazenamento Clássica|Sim|Sim|Sim|Sim|

Recolha as seguintes informações e preencha um pedido de suporte com o Suporte da Microsoft:

* Nome da conta de armazenamento
* Data de supressão
* Região da conta de armazenamento
* Como foi apagada a conta de armazenamento?
* Que método apagou a conta de armazenamento? (Portal, PowerShell, etc.)

Pontos Importantes

* Geralmente pode demorar até 15 dias a partir do momento da eliminação para o serviço de armazenamento realizar a recolha de lixo, pelo que a recuperação das contas de armazenamento pode não ser recuperada com um SLA.
* O Microsoft Support tentará recuperar o Recipiente/Conta numa base de melhor esforço e não pode garantir a recuperação.

> [!NOTE]
> A recuperação pode não ser bem sucedida se a conta tiver sido recriada. Se já recriou a conta, tem de a eliminar primeiro antes de se tentar a recuperação.
