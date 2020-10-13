---
title: Guia para controlar o comportamento de paragem do Windows nos Serviços Azure Lab Microsoft Docs
description: Passos para desligar automaticamente uma máquina virtual do Windows e remover o comando de paragem do Windows.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: c6021131787dde4fe23ec4caad107bda2e20158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541565"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guia para controlar o comportamento de paragem do Windows

A Azure Lab Services fornece vários controlos de custos para garantir que as máquinas virtuais do Windows (VMs) não estão a funcionar inesperadamente:
 - [Definir um horário](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Definir quotas para utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Ativar encerramento automático ao desligar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Mesmo com estes controlos de custos, existem situações em que um VM do Windows pode inesperadamente continuar a funcionar; e, consequentemente, deduzir da quota do aluno:

- **Janela RDP é deixada aberta**
  
    Quando um aluno se conecta ao seu VM utilizando RDP, pode inadvertidamente deixar a janela RDP aberta.  Enquanto a janela RDP permanecer aberta, a **paragem automática da definição de desconexão** nunca entrará em vigor, uma vez que só é acionada após a sessão de PDR ser desligada.

- **O comando de paragem do Windows é utilizado para desligar o VM**
  
    Um estudante pode utilizar o comando de paragem do Windows, ou outros mecanismos de paragem fornecidos dentro do Windows, para desligar o VM em vez de utilizar o botão de [paragem dos Serviços Azure Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Quando isto acontece, do ponto de vista dos Serviços Azure Lab, o VM ainda está a ser usado.
    
Para o ajudar a evitar que estas situações aconteçam, este guia fornece medidas para desligar automaticamente um VM do Windows e remover o comando de paragem do Windows do menu **Iniciar.**  

> [!NOTE]
> Um VM também pode deduzir inesperadamente da quota quando o aluno inicia o seu VM, mas nunca realmente se conecta a ele usando RDP.  Este guia *não* aborda atualmente este cenário.  Em vez disso, os alunos devem ser lembrados para se conectarem imediatamente ao seu VM usando RDP depois de o iniciarem; ou devem parar o VM.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Remover o comando de paragem do Windows do menu Iniciar

As definições **de Política de Grupo local** do Windows também permitem remover o comando de paragem do menu **Iniciar.**

Para remover o comando de paragem, pode ligar-se ao modelo VM e executar o script abaixo do PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Ou, pode optar por seguir estes passos manuais utilizando o modelo VM:

1. Pressione a tecla Windows, **escreva gpedit**e, em seguida, **selecione Editar a política do grupo (Painel de controlo)**.

1. Aceda à **configuração do computador > modelos administrativos > menu inicial e barra de tarefas.**  

    ![Editor de Política de Grupo Local](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Clique com o botão direito **Remover e impedir o acesso aos comandos Shut Down, Restart, Sleep e Hibernate**, e clique em **Editar**.

1. Selecione a definição **ativada** e, em seguida, clique **em OK**:
 
   ![Definição de encerramento](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Note que o comando de paragem já não aparece no menu Windows **Start;** apenas aparece o comando **Desligar.**

    ![Comando de encerramento](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo sobre como preparar um VM modelo do Windows: [Guia para configurar uma máquina de modelos Windows nos Serviços de Laboratório Azure](how-to-prepare-windows-template.md)
