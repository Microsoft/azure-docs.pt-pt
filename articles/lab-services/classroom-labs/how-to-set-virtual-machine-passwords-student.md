---
title: Redefinir senhas para VMs de laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Saiba como redefinir senhas para VMs (máquinas virtuais) em laboratórios de sala de aula do Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 26b0f710590496875521e0dd8577a35841fbd3dd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657011"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Definir ou redefinir a senha para máquinas virtuais em laboratórios de sala de aula (estudantes)
Este artigo mostra como os alunos podem definir/redefinir a senha para suas VMs. 

## <a name="enable-resetting-of-passwords"></a>Habilitar a redefinição de senhas
No momento da criação do laboratório, o proprietário do laboratório pode habilitar ou desabilitar o **uso da mesma senha para todas as máquinas virtuais**. Se essa opção tiver sido habilitada, os alunos não poderão redefinir a senha. Todas as VMs nos laboratórios terão a mesma senha definida pelo instrutor. 

Se essa opção estiver desabilitada, os usuários precisarão definir uma senha ao tentar se conectar à VM pela primeira vez. Os alunos também podem redefinir a senha posteriormente a qualquer momento, conforme mostrado na última seção deste artigo. 

## <a name="reset-password-for-the-first-time"></a>Redefinir a senha pela primeira vez
Se a opção **usar a mesma senha para todas as máquinas virtuais** estiver desabilitada, quando os usuários (alunos) selecionarem o botão **conectar** no bloco do laboratório na página **minhas máquinas virtuais** , o usuário verá a caixa de diálogo a seguir para definir a senha da VM: 

![Redefinir a senha para o aluno](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Redefinir senha mais tarde
O aluno também pode definir a senha clicando no menu de estouro (**três pontos verticais**) no bloco do laboratório e selecionando **Redefinir senha**. 

![Redefinir senha mais tarde](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre outras opções de uso de aluno que um proprietário de laboratório pode configurar, consulte o seguinte artigo: [Configure o uso do aluno](how-to-configure-student-usage.md).
