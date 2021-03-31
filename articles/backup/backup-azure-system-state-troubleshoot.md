---
title: Backup do Estado do Sistema de Resolução de Problemas
description: Neste artigo, aprenda a resolver problemas na Cópia de Segurança do Estado do Sistema para servidores Windows no local.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 7c8e68da1c5da7b25d1385a82bf7dcc2f876306d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376286"
---
# <a name="troubleshoot-system-state-backup"></a>Backup do Estado do Sistema de Resolução de Problemas

Este artigo descreve soluções para problemas que poderá encontrar durante a utilização de Backup do Estado do Sistema.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que execute os seguintes passos de validação, antes de começar a resolver problemas na cópia de segurança do Estado do Sistema:

- [Certifique-se de que o Agente dos Serviços de Recuperação do Microsoft Azure (MARS) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que há conectividade de rede entre o agente MARS e o Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (na consola do Serviço). Se necessário, reinicie e relemque a operação
- [Certifique-se de que o espaço de volume gratuito de 5 a 10% está disponível na localização da pasta de risco](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Verifique se outro processo ou software antivírus está a interferir com o Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [A cópia de segurança agendada falha, mas a cópia de segurança manual funciona](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Certifique-se de que o seu SO tem as atualizações mais recentes
- [Certifique-se de que as unidades e ficheiros não suportados com atributos não suportados estão excluídos da cópia de segurança](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Certifique-se de que o **Relógio do Sistema** no sistema protegido está configurado para corrigir o fuso horário <br>
- [Certifique-se de que o servidor tem, pelo menos, a versão 4.5.2 ou superior do .Net Framework](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se está a tentar **reregistar o servidor** para um cofre, então: <br>
  - Certifique-se de que o agente está desinstalado no servidor e que é eliminado do portal <br>
  - Utilize a mesma frase de acesso que foi inicialmente utilizada para registar o servidor <br>
- Se isto for uma cópia de segurança offline, certifique-se de que a versão 3.7.0 do Azure PowerShell está instalada tanto no computador de origem como em cópia antes de iniciar a operação de backup offline
- [Consideração quando o agente de backup está a funcionar numa máquina virtual Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Limitação

- Recuperar para diferentes hardware usando a recuperação do Estado do Sistema não é recomendado pela Microsoft
- A cópia de segurança do Sistema State suporta atualmente servidores Windows "no local". Esta funcionalidade não está disponível para VMs Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de resolvermos a cópia de segurança do Estado do Sistema com a Azure Backup, execute a seguinte verificação de pré-requisitos.  

### <a name="verify-windows-server-backup-is-installed"></a>Verifique se a cópia de segurança do servidor do Windows está instalada

Certifique-se de que a cópia de segurança do Servidor do Windows está instalada e ativada no servidor. Para verificar o estado de instalação, execute este comando PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Se a saída apresentar o **Estado de Instalação** como **disponível,** significa que a funcionalidade de cópia de segurança do Windows Server está disponível para a instalação, mas não instalada no servidor. No entanto, se o Windows Server Backup não estiver instalado, utilize um dos métodos abaixo para o instalar.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Método 1: Instalar backup do Servidor do Windows utilizando o PowerShell

Para instalar a Cópia de Segurança do Servidor do Windows utilizando o PowerShell, executar o seguinte comando:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Método 2: Instalar backup do Servidor do Windows utilizando o Gestor do Servidor

Para instalar a Cópia de Segurança do Servidor do Windows utilizando o Gestor do Servidor, execute os seguintes passos:

1. Na **Manjedoura de Servidor**, selecione Adicionar **funções e funcionalidades**. Aparece **o assistente de adicionar funções e funcionalidades.**

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecione **o Tipo de Instalação** e selecione **Seguinte**.

    ![Tipo de Instalação](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecione um servidor a partir da piscina do servidor e selecione **Seguinte**. Na Função Servidor, deixe a seleção predefinitiva e selecione **Seguinte**.
4. Selecione **cópia de segurança do servidor do Windows** no separador **Funcionalidades** e selecione **Seguinte**.

    ![Selecione a janela de funcionalidades](./media/backup-azure-system-state-troubleshoot/features.png)

5. No **separador Confirmação,** **selecione Instalar** para iniciar o processo de instalação.
6. No separador **Resultados,** apresentará a função de Backup do Servidor do Windows instalada com sucesso no seu Servidor Windows.

    ![Resultados da instalação](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Permissão de informação do Volume do Sistema

Certifique-se de que o SISTEMA Local tem controlo total na pasta **informação sobre volume do sistema** localizada no volume onde o Windows está instalado. Normalmente, isto é **C:\Informação de volume do sistema.** A cópia de segurança do Windows Server pode falhar se as permissões acima não estiverem corretamente definidas.

### <a name="dependent-services"></a>Serviços dependentes

Certifique-se de que os serviços abaixo estão no estado de funcionamento:

**Nome de serviço** | **Tipo de Arranque**
--- | ---
Chamada de Procedimento Remoto (RPC) | Automático
Sistema de Eventos COM+(Sistema de Eventos) | Automático
Serviço de Notificação de Eventos do Sistema (SENS) | Automático
Cópia sombra de volume (VSS) | Manual
Microsoft Software Shadow Copy Provider (SWPRV) | Manual

### <a name="validate-windows-server-backup-status"></a>Validar o estado de backup do Servidor do Windows

Para validar o estado de backup do Servidor do Windows, execute os seguintes passos:

- Certifique-se de que o WSB PowerShell está a funcionar

  - Corra `Get-WBJob` de um PowerShell elevado e certifique-se de que não devolve o seguinte erro:

    > [!WARNING]
    > Get-WBJob: O termo 'Get-WBJob' não é reconhecido como o nome de um cmdlet, função, ficheiro de script ou programa operável. Verifique a ortografia do nome, ou se foi incluído um caminho, verifique se o caminho está correto e tente novamente.

    - Se falhar com este erro, reinstale a função de backup do Servidor do Windows na máquina do servidor, tal como mencionado no passo 1 dos pré-requisitos.

  - Certifique-se de que a cópia de segurança do WSB está a funcionar corretamente, executando o seguinte comando a partir de uma solicitação de comando elevada:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Substitua X pela letra de unidade do volume onde pretende armazenar a imagem de back up do estado do sistema.

    - Verifique periodicamente o estado do trabalho executando o `Get-WBJob` comando a partir de PowerShell elevado
    - Após o trabalho de backup completar verificar o estado final do trabalho executando `Get-WBJob -Previous 1` o comando

Se o trabalho falhar, indica um problema WSB que resultaria na falha de backups do sistema de segurança do sistema mars.

## <a name="common-errors"></a>Erros comuns

### <a name="vss-writer-timeout-error"></a>Erro de tempo do escritor VSS

| Sintoma | Causa | Resolução
| -- | -- | --
| - O agente MARS falha com a mensagem de erro: "O trabalho da WSB falhou com erros vss. Verifique os registos de eventos vss para resolver a falha"<br/><br/> - Após registo de erros está presente nos registos de eventos da VSS Application: "Um escritor vss rejeitou um evento com erro 0x800423f2, o tempo limite do escritor expirou entre os eventos DeGelo e Descongelamento."| Escritor da VSS é incapaz de completar a tempo devido à falta de CPU e recursos de memória na máquina <br/><br/> Outro software de backup já está a usar o escritor VSS, como resultado, a operação snapshot não poderia ser completada para esta cópia de segurança | Aguarde que o CPU/memória seja libertado no sistema ou aborte os processos que tomam demasiada memória/CPU e tentem novamente a operação. <br/><br/>  Aguarde que a cópia de segurança em curso esteja concluída e tente a operação mais tarde quando não houver cópias de segurança na máquina.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Espaço de disco insuficiente para cultivar cópias de sombra

| Sintoma | Resolução
| -- | --
| - Agente MARS falha com mensagem de erro: A cópia de segurança falhou porque o volume de cópia sombra não podia crescer devido a um espaço de disco insuficiente em volumes que continham ficheiros do sistema <br/><br/> - Após registo de erro/aviso está presente nos registos de eventos do sistema volsnap: "Não havia espaço suficiente em disco no volume C: para aumentar o armazenamento de cópias-sombra para cópias-sombra de C: devido a esta falha, todas as cópias sombra do volume C: estão em risco de ser apagadas" | - Liberte espaço no volume realçado no registo do evento para que haja espaço suficiente para que as cópias sombra cresçam enquanto a cópia de segurança está em andamento <br/><br/> - Enquanto configuramos o espaço de cópia de sombras, podemos restringir a quantidade de espaço usado para a cópia de sombra. Para mais informações, consulte este [artigo](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Divisórias EFI bloqueadas

| Sintoma | Resolução
| -- | --
| O agente MARS falha com a mensagem de erro: "A cópia de segurança do estado do sistema falhou à medida que a partição do sistema EFI está bloqueada. Isto pode ser devido ao acesso à partição do sistema por uma segurança de terceiros ou a um software de back-up" | - Se o problema for devido a um software de segurança de terceiros, então tem de contactar o fornecedor Anti Virus para que eles possam permitir o agente MARS <br/><br/> - Se um software de backup de terceiros estiver em execução, então espere que termine e, em seguida, volte a tentar

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o estado do sistema Windows na implementação do Gestor de Recursos, consulte [o Estado do Sistema do Servidor do Windows](backup-azure-system-state.md)
