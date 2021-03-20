---
title: Redefinir palavras-passe para VMs de laboratório em Azure Lab Services | Microsoft Docs
description: Saiba como redefinir palavras-passe para máquinas virtuais (VMs) em laboratórios da Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1b0b13862ca4620da15606138c0a80adeac8056a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96436815"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Definir ou redefinir palavra-passe para máquinas virtuais em laboratórios (estudantes)
Este artigo mostra-lhe como os alunos podem definir/redefinir a palavra-passe para os seus VMs. 

## <a name="enable-resetting-of-passwords"></a>Permitir a reposição de senhas
No momento da criação do laboratório, o proprietário do laboratório pode ativar ou desativar a **mesma palavra-passe para todas as máquinas virtuais.** Se esta opção foi ativada, os alunos não podem redefinir a palavra-passe. Todos os VMs nos laboratórios terão a mesma senha que o instrutor. 

Se esta opção for desativada, os utilizadores terão de definir uma palavra-passe ao tentarem ligar-se pela primeira vez ao VM. Os alunos também podem redefinir a palavra-passe mais tarde, a qualquer momento, como mostrado na última secção deste artigo. 

## <a name="reset-password-for-the-first-time"></a>Redefinir a palavra-passe pela primeira vez
Se a **mesma palavra-passe para todas as máquinas virtuais** tiver sido desativada, quando os utilizadores (estudantes) selecionam o botão **'Ligar'** no azulejo de laboratório na página **'As minhas máquinas virtuais',** o utilizador vê a seguinte caixa de diálogo para definir a palavra-passe para o VM: 

![Redefinir a palavra-passe para o aluno](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Redefinir a palavra-passe mais tarde
O estudante também pode definir a palavra-passe clicando no menu de transbordo **(três pontos verticais)** no azulejo do laboratório e selecionando a **palavra-passe reset**. 

![Redefinir a palavra-passe mais tarde](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre outras opções de utilização do aluno que um proprietário de laboratório pode configurar, consulte o seguinte artigo: [Configurar](how-to-configure-student-usage.md)o uso do aluno .
