---
title: Guia para controlar o comportamento de encerramento do Windows nos Serviços de Laboratório Do Azure [ Microsoft Docs
description: Passos para desligar automaticamente uma máquina virtual do Windows e remover o comando de paragem do Windows.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522241"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guia para controlar o comportamento de encerramento do Windows

A Azure Lab Services fornece vários controlos de custos para garantir que as máquinas virtuais do Windows (VMs) não estão a funcionar inesperadamente:
 - [Definir um horário](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Definir quotas para utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Ativar encerramento automático ao desligar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Mesmo com estes controlos de custos, existem situações em que um VM do Windows pode inesperadamente continuar a funcionar; e, consequentemente, deduzir da quota do aluno:

- **Janela RDP é deixada aberta**
  
    Quando um aluno se liga ao seu VM utilizando RDP, pode inadvertidamente deixar a janela RDP aberta.  Enquanto a janela RDP permanecer aberta, o desligamento automático na definição de **desconexão** nunca produzirá efeito, uma vez que só é acionado após a desconexão da sessão de RDP.

- **O comando de paragem do Windows é usado para desligar o VM**
  
    Um estudante pode utilizar o comando de paragem do Windows, ou outros mecanismos de paragem fornecidos no Windows, para desligar o VM em vez de utilizar o botão de [paragem do Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Quando isso acontece, do ponto de vista dos Serviços de Laboratório Azure, o VM continua a ser utilizado.
    
Para o ajudar a evitar que estas situações aconteçam, este guia fornece passos para desligar automaticamente um VM do Windows inativo e remover o comando de paragem do Windows do menu **Iniciar.**  

> [!NOTE]
> Um VM também pode deduzir inesperadamente da quota quando o aluno inicia o seu VM, mas nunca realmente se liga a ela usando RDP.  Este guia *não* aborda atualmente este cenário.  Em vez disso, os alunos devem ser imediatamente lembrados para se ligarem imediatamente ao seu VM usando RDP após o seu início; ou devem parar o VM.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Desconexão e paragem automática de RDP para VM inativo

O Windows fornece definições de Política de **Grupo Local** que pode utilizar para definir um limite de tempo para desligar automaticamente uma sessão de RDP quando fica inativa.  Uma sessão é determinada a ficar inativa quando *não* há qualquer entrada de rato\teclado.  Quaisquer atividades de longa duração que não envolvam entrada de rato\teclado fazem com que o VM esteja em estado de ocioso.  Isto inclui executar uma longa consulta, streaming de vídeo, compilação, etc.  Dependendo das necessidades da sua classe, pode optar por definir o limite de tempo inativo para que seja tempo suficiente para lidar com este tipo de atividades.  Por exemplo, pode fixar o limite de tempo inativo para 1 ou mais horas, se necessário.

Aqui está a experiência do aluno quando configura o limite de **sessão inativa** em combinação com o encerramento automático na definição [**de desconexão:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. O aluno liga-se ao seu VM Windows utilizando RDP.
 2. Quando o aluno deixar a janela rdp aberta e o VM ficar inativo para o limite de **sessão inativa** que especificou (como 5 minutos), o aluno verá o seguinte diálogo:

    ![Diálogo expirado do prazo inativo](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Se o aluno *não* clicar em **OK,** a sua sessão de RDP desligar-se-á automaticamente após 2 minutos.
2. Após a desconexão da sessão de RDP, uma vez atingido o prazo especificado para o desligamento automático na definição de **desconexão,** o VM é automaticamente desligado pelos Serviços de Laboratório Azure.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Definir o limite de tempo de sessão de rdp idle no modelo VM

Para definir o limite de tempo inativo da sessão rdp, pode ligar-se ao modelo VM e executar o script abaixo powerShell.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Ou, pode optar por seguir estes passos manuais utilizando o modelo VM:

1. Prima a tecla Windows, **escreva gpedit**e, em seguida, selecione a política do **grupo Editar (Painel de Controlo)**.

1. Vá para a **configuração do computador > modelos administrativos > componentes do Windows > serviços de ambiente**de trabalho remoto > anfitrião de sessão de ambiente de trabalho remoto > limites de tempo de sessão .  

    ![Editor de Política de Grupo Local](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Clique à direita Definir o limite de **tempo para sessões de serviços de ambiente de trabalho remoto sinuosos ativos mas ociosos,** e clique em **Editar**.

1. Introduza as seguintes definições e, em seguida, clique **em OK:**
   1. Selecione **Ativado**.
   1. Em **Opções,** especifique o limite de **sessão idle**.

    ![Limite de sessão inativo](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Finalmente, para combinar este comportamento com a paragem automática na definição de **desconexão,** deve seguir os passos no artigo como fazer: Ativar o [encerramento automático de VMs na desconexão](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Remova o comando de paragem do Windows do menu Iniciar

As definições da Política de **Grupo Local** do Windows também permitem remover o comando de paragem do menu **Iniciar.**

Para remover o comando de paragem, pode ligar-se ao modelo VM e executar o script abaixo do PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Ou, pode optar por seguir estes passos manuais utilizando o modelo VM:

1. Prima a tecla Windows, **escreva gpedit**e, em seguida, selecione a política do **grupo Editar (Painel de Controlo)**.

1. Vá à **configuração do computador > modelos administrativos > menu inicial e barra**de tarefas.  

    ![Editor de Política de Grupo Local](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Clique à direita **Remova e impeça o acesso aos comandos Desativados, Reiniciar, Dormir e Hibernar,** e clique em **Editar**.

1. Selecione a definição **ativada** e, em seguida, clique **em OK:**
 
   ![Definição de encerramento](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Note que o comando de paragem já não aparece no menu Windows **Start;** apenas aparece o comando **Desligar.**

    ![Comando de encerramento](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Passos seguintes
Veja o artigo sobre como preparar um Modelo Windows VM: Guia para configurar uma máquina de [modelo seletiva do Windows nos Serviços do Laboratório Azure](how-to-prepare-windows-template.md)