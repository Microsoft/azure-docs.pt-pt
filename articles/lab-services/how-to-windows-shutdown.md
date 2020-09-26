---
title: Guia para controlar o comportamento de paragem do Windows nos Serviços Azure Lab Microsoft Docs
description: Passos para desligar automaticamente uma máquina virtual do Windows e remover o comando de paragem do Windows.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3c20bc2bb79faf53c4f3fbd113c18c5c6d923e59
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334026"
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

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Desconexão e encerramento automáticos de RDP para VM inativo

O Windows fornece definições **de Política de Grupo Local** que pode utilizar para definir um prazo para desligar automaticamente uma sessão DE PDR quando ficar inativa.  Uma sessão é determinada a ficar inativa quando *não* há nenhuma entrada de teclado\teclado.  Quaisquer atividades de longa duração que não envolvam entrada de teclado de rato fazem com que o VM esteja em estado de ocioso.  Isto inclui a execução de uma longa consulta, streaming de vídeo, compilação, etc.  Dependendo das necessidades da sua turma, pode optar por definir o prazo de tempo inativo para que seja tempo suficiente para lidar com este tipo de atividades.  Por exemplo, pode definir o tempo de marcha lenta para 1 ou mais horas, se necessário.

Aqui está a experiência do aluno ao configurar o limite de **sessão ocioso** em combinação com a paragem automática na definição [**de desconexão:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. O aluno conecta-se ao seu VM windows usando RDP.
 2. Quando o aluno deixar a janela RDP aberta e o VM ficar inativo para o **limite de sessão ociosa** que especificou (como 5 minutos), o aluno verá o seguinte diálogo:

    ![Diálogo expirado do prazo de marcha lenta](./media/how-to-windows-shutdown/idle-time-expired.png)

1. Se o aluno *não* clicar **em OK,** a sessão de PDR desligar-se-á automaticamente após 2 minutos.
2. Após a sessão RDP desligar, uma vez atingido o prazo especificado para a **paragem automática na definição de desconexão,** o VM é automaticamente desligado pelos Serviços Azure Lab.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Definir o tempo limite de sessão de RDP inativo no modelo VM

Para definir o prazo de tempo inativo da sessão RDP, pode ligar-se ao VM do modelo e executar o script abaixo do PowerShell.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Ou, pode optar por seguir estes passos manuais utilizando o modelo VM:

1. Pressione a tecla Windows, **escreva gpedit**e, em seguida, **selecione Editar a política do grupo (Painel de controlo)**.

1. Aceda à **configuração do computador > modelos administrativos > componentes do Windows > serviços de ambiente de trabalho remoto > o anfitrião de sessão de ambiente de trabalho remoto > limites de tempo**de sessão .  

    ![Screenshot que mostra o "Editor de política de grupo local" com "Prazos de Sessão" selecionados.](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Clique com o botão direito **Definir o prazo de tempo para sessões de Serviços de Ambiente de Trabalho remoto ativo mas inativo**, e clique em **Editar**.

1. Introduza as seguintes definições e, em seguida, clique **em OK**:
   1. Selecione **Ativado**.
   1. Em **Opções**, especifique o **limite de sessão de marcha lenta**.

    ![Limite de sessão ociosa](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Por fim, para combinar este comportamento com a **paragem automática na definição de desconexão,** deve seguir os passos no artigo de como fazer: Ativar o [encerramento automático dos VMs na desconexão](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> Depois de configurar esta definição utilizando o PowerShell para modificar a definição de registo direta ou manualmente utilizando o editor de Política de Grupo, tem primeiro de reiniciar o VM para que as definições produzam efeitos.  Além disso, se configurar a definição utilizando o registo, o editor de Política de Grupo nem sempre atualiza para refletir alterações na definição de registo; no entanto, a definição de registo ainda entra em vigor como esperado e verá a sessão rdp desligada quando estiver inativa durante o período de tempo especificado.

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