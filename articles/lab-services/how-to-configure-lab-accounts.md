---
title: Configure o encerramento automático de VMs nos Serviços de Laboratório Azure
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
ms.openlocfilehash: 787b9aa3738335ee0261ab94253cf828c232bd44
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896019"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Configure o encerramento automático de VMs na definição de desconexão para uma conta de laboratório
Pode ativar ou desativar o encerramento automático dos VMs do laboratório do Windows (modelo ou estudante) depois de uma ligação remota de ambiente de trabalho ser desligada. Também pode especificar quanto tempo os Serviços de Laboratório devem esperar que o utilizador se reconecte antes de desligar automaticamente.

## <a name="enable-automatic-shutdown"></a>Ativar o encerramento automático

1. Na página **'Contas de Laboratório',** selecione **as definições de Laboratório** no menu esquerdo.
2. Selecione as **máquinas virtuais desligar automaticamente quando os utilizadores desligarem** a opção.
3. Especifique quanto tempo os Serviços de Laboratório devem esperar que o utilizador se reconecte antes de desligar automaticamente os VMs.

    ![Definição automática de paragem na conta do laboratório](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Esta definição aplica-se a todos os laboratórios criados na conta do laboratório. Um criador de laboratório (educador) pode anular esta definição ao nível do laboratório. A alteração desta definição na conta do laboratório só afetará os laboratórios que são criados após a alteração.

    Para desativar esta definição, desmarque a caixa de verificação para as **máquinas virtuais de encerramento automática quando os utilizadores desligarem** a opção nesta página. 

## <a name="next-steps"></a>Passos seguintes
Para saber como um dono de laboratório pode configurar ou sobrepor esta definição ao nível do laboratório, consulte [este artigo](how-to-enable-shutdown-disconnect.md)
