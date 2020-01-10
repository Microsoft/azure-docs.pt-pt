---
title: Habilitar desligamento de VMs ao desconectar Azure Lab Services | Microsoft Docs
description: Saiba como habilitar ou desabilitar o desligamento automático de VMs quando uma conexão de área de trabalho remota é desconectada.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: e615170952ea2987639a0bfc269ad5a1692e1e59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480804"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Habilitar o desligamento automático de VMs ao desconectar
Este artigo mostra como você pode habilitar ou desabilitar o desligamento automático de VMs do **Windows 10** Lab (modelo ou aluno) depois que uma conexão de área de trabalho remota é desconectada. Você também pode especificar quanto tempo as VMs devem aguardar até que o usuário se reconecte antes de desligar automaticamente.

Um administrador de conta de laboratório pode definir essa configuração para a conta de laboratório na qual você cria laboratórios. Para obter mais informações, consulte [Configurar o desligamento automático de VMs ao desconectar para uma conta de laboratório](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Como proprietário de um laboratório, você pode substituir a configuração ao criar um laboratório ou depois que o laboratório for criado. 

## <a name="configure-when-creating-a-lab"></a>Configurar ao criar um laboratório
Na página Etapa 3 do assistente de criação de laboratório, você pode habilitar ou desabilitar esse recurso e também especificar quanto tempo a VM deve aguardar até que o usuário se reconecte antes de desligar automaticamente. 

![Configurar no momento da criação do laboratório](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configurar após a criação do laboratório
Você pode definir essa configuração na página **configurações** , conforme mostrado na imagem a seguir: 

![Configurar após a criação do laboratório](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Painel para laboratórios de sala de aula](use-dashboard.md)