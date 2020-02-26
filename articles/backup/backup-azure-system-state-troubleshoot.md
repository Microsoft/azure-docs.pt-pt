---
title: Backup do Estado do Sistema de Resolução de Problemas
description: Neste artigo, aprenda a resolver problemas no System State Backup para servidores Windows no local.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: f311de435d813cb0e6f8a2c3d932e05d695603f3
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583304"
---
# <a name="troubleshoot-system-state-backup"></a>Backup do Estado do Sistema de Resolução de Problemas

Este artigo descreve soluções para problemas que pode encontrar ao usar backup do Estado do Sistema.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que execute a validação abaixo, antes de iniciar a resolução de problemas de backup do System State:

- [Certifique-se de que o Agente dos Serviços de Recuperação do Microsoft Azure (MARS) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que existe conectividade de rede entre o agente MARS e o Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (na consola do Serviço). Se necessário, reinicie e tente novamente a operação
- [Certifique-se de que 5 a 10% de espaço livre do volume está disponível na localização da pasta de rascunho](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Verifique se outro processo ou software antivírus está a interferir com o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [A cópia de segurança agendada falha, mas a cópia de segurança manual funciona](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Certifique-se de que o seu SO tem as atualizações mais recentes
- [Certifique-se de que unidades e ficheiros não suportados com atributos não suportados são excluídos da cópia de segurança](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Certifique-se de que o **Relógio do Sistema** do sistema protegido está configurado para o fuso horário correto <br>
- [Certifique-se de que o servidor tem, pelo menos, a versão 4.5.2 ou superior do .Net Framework](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se está a tentar **reregistar o seu servidor** num cofre, então: <br>
  - Certifique-se de que o agente está desinstalado no servidor e é eliminado do portal <br>
  - Utilize a mesma frase de acesso que foi inicialmente utilizada para registar o servidor <br>
- Se se trata de uma cópia de segurança offline, certifique-se de que a versão 3.7.0 do Azure PowerShell está instalada tanto no computador de origem como no computador de cópia antes de iniciar a operação de backup offline
- [Consideração quando o agente de backup está a funcionar numa máquina virtual Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Limitação

- A Microsoft não recomenda recuperar para um hardware diferente com a recuperação do Estado do Sistema.
- A cópia de segurança do System State suporta atualmente servidores windows "no local". Esta funcionalidade não está disponível para VMs Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de resolvermos o System State Backup com o Azure Backup, execute a verificação de pré-requisitos abaixo.  

### <a name="verify-windows-server-backup-is-installed"></a>Verifique se a cópia de segurança do Windows Server está instalada

Certifique-se de que a cópia de segurança do Servidor do Windows está instalada e ativada no servidor. Para verificar o estado de instalação, execute este comando PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Se a saída apresentar o Estado de **Instalação** como **disponível,** significa que a funcionalidade de reserva do Windows Server está disponível para a instalação, mas não instalada no servidor. No entanto, se o Windows Server Backup não estiver instalado, utilize um dos métodos abaixo para o instalar.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Método 1: Instale a cópia de segurança do Servidor windows utilizando a PowerShell

Para instalar o Windows Server Backup utilizando o PowerShell, execute o comando abaixo:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Método 2: Instalar a cópia de segurança do Servidor do Windows utilizando o Gestor do Servidor

Para instalar o Windows Server Backup utilizando o Server Manager, execute os passos abaixo:

1. No **Server Manger,** clique em **Adicionar funções e funcionalidades**. Aparece o **assistente de adicionar e funcionalidades.**

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecione **Tipo de Instalação** e clique **em Seguinte**.

    ![Tipo de instalação](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecione um servidor a partir da piscina do servidor e clique em **Next**. Na Função Servidor, deixe a seleção predefinida e clique **em Next**.
4. Selecione **a cópia de segurança do Servidor do Windows** no separador **'Funcionalidades'** e clique em **Next**.

    ![características](./media/backup-azure-system-state-troubleshoot/features.png)

5. No separador **Confirmação,** clique **em Instalar** para iniciar o processo de instalação.
6. No separador **Resultados,** irá exibir a funcionalidade de backup do Windows Server, instalada com sucesso no seu Servidor Windows.

    ![Resultado](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Permissão de informação do Volume do Sistema

Certifique-se de que o SISTEMA Local tem controlo total sobre a pasta **informação** sobre volumes do sistema localizada no volume onde o Windows está instalado. Normalmente isto é **C:\Informação**de Volume do Sistema . A cópia de segurança do Windows Server pode falhar se as permissões acima não forem definidas corretamente

### <a name="dependent-services"></a>Serviços dependentes

Certifique-se de que os serviços abaixo estão em estado de funcionamento:

**Nome de serviço** | **Tipo de arranque**
--- | ---
Chamada de procedimento remoto (RPC) | Automático
Sistema de Eventos COM+ (Sistema de Eventos) | Automático
Serviço de Notificação de Eventos do Sistema (SENS) | Automático
Cópia sombra de volume (VSS) | Manual
Microsoft Software Shadow Copy Provider (SWPRV) | Manual

### <a name="validate-windows-server-backup-status"></a>Validar o estado de backup do Servidor do Windows

Para validar o estado de backup do Servidor do Windows, execute os seguintes passos:

- Certifique-se de que a WSB PowerShell está a funcionar

  - Executar `Get-WBJob` a partir de um PowerShell elevado e certifique-se de que não devolve o seguinte erro:

    > [!WARNING]
    > Get-WBJob: O termo 'Get-WBJob' não é reconhecido como o nome de um programa cmdlet, função, script ou operável. Verifique a ortografia do nome, ou se um caminho foi incluído, verifique se o caminho está correto e tente novamente.

    - Se falhar com este erro, reinstale a função de backup do Servidor do Windows na máquina do servidor, tal como mencionado no passo 1 dos pré-requisitos.

  - Certifique-se de que a cópia de segurança wSB está a funcionar corretamente, executando o comando abaixo a partir de um pedido de comando elevado:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Substitua X pela letra de unidade do volume onde pretende armazenar a imagem de back up do estado do sistema.

    - Verifique periodicamente o estado do trabalho executando `Get-WBJob` comando da PowerShell elevada
    - Após o trabalho de backup concluir, verifique o estado final do trabalho executando `Get-WBJob -Previous 1` comando

Se o trabalho falhar, indica um problema wsb que resultaria na falha dos backups do estado do agente MARS.

## <a name="common-errors"></a>Erros comuns

### <a name="vss-writer-timeout-error"></a>Erro de timeout do escritor VSS

| Sintoma | Causa | Resolução
| -- | -- | --
| - Agente mars falha com mensagem de erro: "O trabalho da WSB falhou com erros VSS. Verifique os registos de eventos VSS para resolver a falha"<br/><br/> - Na sequência do registo de erros presente sessão de eventos vss application: "Um escritor vsS rejeitou um evento com erro 0x800423f2, o tempo de validade do escritor expirou entre os eventos Freeze e Thaw."| O escritor do VSS não consegue completar a tempo devido à falta de CPU e recursos de memória na máquina <br/><br/> Outro software de backup já está a usar o escritor VSS, como resultado a operação snapshot não poderia ser completada para esta cópia de segurança | Aguarde que o CPU/memória seja libertado no sistema ou aborte os processos que retirem demasiada memória/CPU e tente novamente a operação. <br/><br/>  Aguarde que a cópia de segurança em curso esteja concluída e tente a operação mais tarde quando não estiverem a funcionar cópias de segurança na máquina.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Espaço de disco insuficiente para cultivar cópias de sombra

| Sintoma | Resolução
| -- | --
| - O agente MARS falha com a mensagem de erro: A cópia de segurança falhou, uma vez que o volume de cópia de sombra não podia crescer devido a um espaço de disco insuficiente em volumes que continham ficheiros do sistema <br/><br/> - Na sequência do registo de erros/advertências presentes nos registos de eventos do sistema volsnap: "Não havia espaço suficiente no volume C: para aumentar o armazenamento de cópias-sombra para cópias-sombra de C: devido a esta falha todas as cópias-sombra do volume C: correm o risco de ser eliminadas" | - Liberte espaço no volume realçado no registo do evento para que haja espaço suficiente para que as cópias sombra cresçam enquanto a cópia de reserva está em andamento <br/><br/> - Ao configurar o espaço de cópia de sombra, podemos restringir a quantidade de espaço utilizado para a cópia da sombra. Para mais informações, consulte este [artigo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)

### <a name="efi-partition-locked"></a>Partição EFI bloqueada

| Sintoma | Resolução
| -- | --
| O agente MARS falha com a mensagem de erro: "A cópia de segurança do estado do sistema falhou à medida que a partição do sistema EFI está bloqueada. Isto pode ser devido ao acesso de divisórias do sistema por um software de segurança de terceiros ou de back up" | - Se o problema se deve a um software de segurança de terceiros, então precisa contactar o fornecedor Anti Virus para que possam permitir o agente MARS <br/><br/> - Se um software de backup de terceiros estiver em execução, então espere que termine e, em seguida, volte a tentar voltar a funcionar

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o estado do sistema Windows na implementação do Gestor de Recursos, consulte [o Estado do Sistema de Servidores do Windows](backup-azure-system-state.md)
