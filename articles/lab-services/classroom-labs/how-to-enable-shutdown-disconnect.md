---
title: Permitir o encerramento de VMs na desconexão dos Serviços de Laboratório Azure  Microsoft Docs
description: Aprenda a ativar ou desativar o encerramento automático de VMs quando uma ligação remota de ambiente de trabalho estiver desligada.
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
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117129"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Ativar o encerramento automático de VMs na desconexão
Este artigo mostra-lhe como pode ativar ou desativar o encerramento automático de VMs de laboratório **do Windows 10** (modelo ou aluno) depois de uma ligação remota de ambiente de trabalho ser desligada. Também pode especificar quanto tempo os VMs devem esperar que o utilizador se reconecte antes de desligar automaticamente.

Um administrador de conta de laboratório pode configurar este cenário para a conta de laboratório em que cria laboratórios. Para obter mais informações, consulte o [encerramento automático de VMs em desconexão para uma conta](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)de laboratório . Como dono de laboratório, pode supor o cenário quando se cria um laboratório ou depois de o laboratório ser criado. 

## <a name="configure-when-creating-a-lab"></a>Configure ao criar um laboratório
Na página 3 do assistente de criação de laboratório, pode ativar ou desativar esta funcionalidade e também especificar quanto tempo o VM deve esperar que o utilizador se reconecte antes de desligar automaticamente. 

![Configure no momento da criação de laboratório](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configurar depois que o laboratório é criado
Pode configurar esta definição na página **Definições,** conforme mostrado na seguinte imagem: 

![Configurar depois que o laboratório é criado](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Se desligar o sistema operativo Windows (OS) num VM antes de desligar uma sessão de RDP para o VM, a função de desligamento automático não funcionará corretamente.  

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Painel de instrumentos para laboratórios de sala de aula](use-dashboard.md)