---
title: Permitir o encerramento de VMs na disconnect Azure Lab Services / Microsoft Docs
description: Saiba como ativar ou desativar o encerramento automático de VMs quando uma ligação remota do ambiente de trabalho estiver desligada.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: a617bdc8e6fcb4588b26f898f437dc7bba3c2f59
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895893"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Permitir o encerramento automático de VMs na desconexão
Este artigo mostra-lhe como pode ativar ou desativar o encerramento automático de VMs de laboratório do **Windows 10** (modelo ou aluno) depois de uma ligação remota de ambiente de trabalho ser desligada. Também pode especificar quanto tempo os VMs devem esperar que o utilizador se reconecte antes de desligar automaticamente.

Um administrador de conta de laboratório pode configurar esta definição para a conta de laboratório na qual cria laboratórios. Para obter mais informações, consulte [configurar o encerramento automático de VMs na desconexão para uma conta de laboratório](how-to-configure-lab-accounts.md). Como dono de laboratório, pode anular a definição ao criar um laboratório ou depois de o laboratório ser criado. 

## <a name="configure-when-creating-a-lab"></a>Configurar ao criar um laboratório
No passo 3 da página do assistente de criação de laboratório, pode ativar ou desativar esta funcionalidade e especificar quanto tempo o VM deve esperar que o utilizador se reconecte antes de desligar automaticamente. 

![Configurar no momento da criação de laboratório](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configurar depois que o laboratório é criado
Pode configurar esta definição na página **Definições,** tal como mostrado na seguinte imagem: 

![Configurar depois que o laboratório é criado](./media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Se desligar o sistema operativo Windows (OS) num VM antes de desligar uma sessão DE PDR para o VM, a função de autoshutdown não funcionará corretamente.  

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Painel para laboratórios de sala de aula](use-dashboard.md)