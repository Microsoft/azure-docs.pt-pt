---
title: Redefinir senhas para vMs de laboratório em laboratório em Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a redefinir senhas para máquinas virtuais (VMs) em laboratórios de sala de aula da Azure Lab Services.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73583660"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Definir ou redefinir palavra-passe para máquinas virtuais em laboratórios de sala de aula (alunos)
Este artigo mostra-lhe como os alunos podem definir/redefinir a palavra-passe para os seus VMs. 

## <a name="enable-resetting-of-passwords"></a>Ativar a reposição de senhas
No momento da criação do laboratório, o proprietário do laboratório pode ativar ou desativar a **mesma palavra-passe para todas as máquinas virtuais**. Se esta opção foi ativada, os alunos não podem redefinir a palavra-passe. Todos os VMs nos laboratórios terão a mesma senha definida pelo instrutor. 

Se esta opção for desativada, os utilizadores terão de definir uma palavra-passe quando tentarem ligar-se ao VM pela primeira vez. Os alunos também podem redefinir a palavra-passe mais tarde, como mostrado na última secção deste artigo. 

## <a name="reset-password-for-the-first-time"></a>Redefinir a palavra-passe pela primeira vez
Se a **mesma palavra-passe utilizar para todas as máquinas virtuais** foi desativada, quando os utilizadores (estudantes) selecionam o botão **Connect** no azulejo de laboratório na página das **minhas máquinas virtuais,** o utilizador vê a seguinte caixa de diálogo para definir a palavra-passe para o VM: 

![Redefinir a palavra-passe para o aluno](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Redefinir a palavra-passe mais tarde
O aluno também pode definir a palavra-passe clicando no menu de transbordo **(três pontos verticais**) no azulejo do laboratório e selecionando a **palavra-passe Reset**. 

![Redefinir a palavra-passe mais tarde](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre outras opções de utilização do aluno que um proprietário de laboratório pode configurar, consulte o seguinte artigo: [Configurar](how-to-configure-student-usage.md)o uso do aluno .
