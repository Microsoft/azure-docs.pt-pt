---
title: Configurar encerramento automático de VMs nos Serviços de Laboratório Azure
description: Este artigo descreve como configurar o encerramento automático de VMs na conta de laboratório.
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
ms.openlocfilehash: afe74e5c3aec2519ec22eee9573ae7b47c1c73f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588212"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Configure o encerramento automático de VMs na definição de desconexão para uma conta de laboratório
Pode ativar ou desativar o encerramento automático de VMs de laboratório do Windows (modelo ou aluno) depois de uma ligação remota de ambiente de trabalho ser desligada. Também pode especificar quanto tempo os Serviços lab devem esperar que o utilizador se reconecte antes de desligar automaticamente.

## <a name="enable-automatic-shutdown"></a>Ativar o encerramento automático

1. Na página **da Conta lab,** selecione **as definições** de Laboratórios no menu esquerdo.
2. Selecione as **máquinas virtuais desativadas automaticamente quando os utilizadores desligarem** a opção.
3. Especifique quanto tempo os Serviços lab devem esperar que o utilizador se reconecte antes de desligar automaticamente os VMs.

    ![Definição automática de paragem na conta de laboratório](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Esta definição aplica-se a todos os laboratórios criados na conta do laboratório. Um criador de laboratório (educador) pode sobrepor-se a este cenário ao nível do laboratório. A alteração desta configuração na conta do laboratório só afetará os laboratórios que são criados após a mudança.

    Para desativar esta definição, desverifique a caixa de verificação das **máquinas virtuais de paragem automática quando os utilizadores desligam** a opção nesta página. 

## <a name="next-steps"></a>Passos seguintes
Para saber como um dono de laboratório pode configurar ou anular este cenário a nível de laboratório, consulte [este artigo](how-to-enable-shutdown-disconnect.md)
