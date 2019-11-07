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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583660"
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


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre outras opções de uso de alunos que um proprietário de laboratório pode configurar, consulte o seguinte artigo: [Configurar o uso de alunos](how-to-configure-student-usage.md).
