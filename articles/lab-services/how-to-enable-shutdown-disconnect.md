---
title: Permitir o encerramento de VMs na disconnect Azure Lab Services / Microsoft Docs
description: Saiba como ativar ou desativar o encerramento automático de VMs quando uma ligação remota do ambiente de trabalho estiver desligada.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0c5c22d5e4a9d66413e37cce095f5497915bd122
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445717"
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