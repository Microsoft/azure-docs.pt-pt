---
title: Solucionar problemas de backup do estado do sistema
description: Neste artigo, saiba como solucionar problemas no backup de estado do sistema para servidores Windows locais.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: fde5fd9f2464c2aff9a7a34ffa440ab9a6a1ca51
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665045"
---
# <a name="troubleshoot-system-state-backup"></a>Solucionar problemas de backup do estado do sistema

Este artigo descreve soluções para problemas que você pode chegar ao usar o backup de estado do sistema.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que você execute a validação abaixo antes de iniciar a solução de problemas de backup do estado do sistema:

- [Verifique se o agente de Serviços de Recuperação do Microsoft Azure (MARS) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que existe conectividade de rede entre o agente MARS e o Azure](https://aka.ms/AB-A4dp50)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (na consola do Serviço). Se necessário, reinicie e repita a operação
- [Certifique-se de que 5 a 10% de espaço livre do volume está disponível na localização da pasta de rascunho](https://aka.ms/AB-AA4dwtt)
- [Verifique se outro processo ou software antivírus está a interferir com o Azure Backup](https://aka.ms/AB-AA4dwtk)
- [A cópia de segurança agendada falha, mas a cópia de segurança manual funciona](https://aka.ms/ScheduledBackupFailManualWorks)
- Certifique-se de que o seu SO tem as atualizações mais recentes
- [Garantir que unidades e arquivos sem suporte com atributos sem suporte sejam excluídos do backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Certifique-se de que o **Relógio do Sistema** do sistema protegido está configurado para o fuso horário correto <br>
- [Certifique-se de que o servidor tem, pelo menos, a versão 4.5.2 ou superior do .Net Framework](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se você estiver tentando **registrar novamente o servidor** em um cofre, então: <br>
  - Verifique se o agente está desinstalado no servidor e se ele foi excluído do portal <br>
  - Utilize a mesma frase de acesso que foi inicialmente utilizada para registar o servidor <br>
- Se esse for um backup offline, verifique se Azure PowerShell versão 3.7.0 está instalada no computador de origem e de cópia antes de começar a operação de backup offline
- [Consideração quando o agente de backup estiver em execução em uma máquina virtual do Azure](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Limitação

- A Microsoft não recomenda recuperar para um hardware diferente com a recuperação do Estado do Sistema.
- O backup de estado do sistema atualmente dá suporte a servidores Windows "locais". Essa funcionalidade não está disponível para VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de solucionarmos problemas de backup de estado do sistema com o backup do Azure, execute a verificação de pré-requisitos abaixo.  

### <a name="verify-windows-server-backup-is-installed"></a>Verificar se Backup do Windows Server está instalado

Verifique se Backup do Windows Server está instalado e habilitado no servidor. Para verificar o status da instalação, execute este comando do PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Se a saída exibir o **estado de instalação** como **disponível**, significa que o recurso de backup do Windows Server está disponível para a instalação, mas não instalado no servidor. No entanto, se Backup do Windows Server não estiver instalado, use um dos métodos a seguir para instalá-lo.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Método 1: instalar Backup do Windows Server usando o PowerShell

Para instalar Backup do Windows Server usando o PowerShell, execute o comando abaixo:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Método 2: instalar Backup do Windows Server usando Gerenciador do Servidor

Para instalar Backup do Windows Server usando Gerenciador do Servidor, execute as etapas abaixo:

1. Em **Gerenciador de servidores**, clique em **adicionar funções e recursos**. O **Assistente Adicionar funções e recursos** é exibido.

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecione o **tipo de instalação** e clique em **Avançar**.

    ![Tipo de Instalação](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecione um servidor no pool de servidores e clique em **Avançar**. Na função de servidor, deixe a seleção padrão e clique em **Avançar**.
4. Selecione **backup do Windows Server** na guia **recursos** e clique em **Avançar**.

    ![elástica](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na guia **confirmação** , clique em **instalar** para iniciar o processo de instalação.
6. Na guia **resultados** , ele exibirá a backup do Windows Server recurso foi instalado com êxito no Windows Server.

    ![Resultado](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Permissão de informações de volume do sistema

Verifique se o sistema local tem controle total sobre a pasta **informações de volume do sistema** localizada no volume em que o Windows está instalado. Geralmente, isso é **C:\System informações de volume**. O backup do Windows Server poderá falhar se as permissões acima não estiverem definidas corretamente

### <a name="dependent-services"></a>Serviços dependentes

Verifique se os serviços a seguir estão em estado de execução:

**Nome do serviço** | **Tipo de inicialização**
--- | ---
RPC (chamada de procedimento remoto) | Automático
Sistema de eventos COM+ (EventSystem) | Automático
Serviço de notificação de eventos do sistema (SENS) | Automático
VSS (cópia de sombra de volume) | Manual
Provedor de cópia de sombra de software da Microsoft (SWPRV) | Manual

### <a name="validate-windows-server-backup-status"></a>Validar Backup do Windows Server status

Para validar Backup do Windows Server status, execute as seguintes etapas:

- Verificar se o PowerShell do WSB está em execução

  - Execute `Get-WBJob` de um PowerShell elevado e verifique se ele não retorna o seguinte erro:

    > [!WARNING]
    > Get-WBJob: o termo ' Get-WBJob ' não é reconhecido como o nome de um cmdlet, função, arquivo de script ou programa operável. Verifique a ortografia do nome ou, se um caminho foi incluído, verifique se o caminho está correto e tente novamente.

    - Se ele falhar com esse erro, reinstale o recurso Backup do Windows Server no computador servidor, conforme mencionado na etapa 1 dos pré-requisitos.

  - Verifique se o backup do WSB está funcionando corretamente, executando o comando abaixo no prompt de comando elevado:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Substitua X pela letra da unidade do volume em que você deseja armazenar a imagem de backup do estado do sistema.

    - Verificar periodicamente o status do trabalho executando `Get-WBJob` comando do PowerShell elevado
    - Após a conclusão do trabalho de backup, verifique o status final do trabalho executando `Get-WBJob -Previous 1` comando

Se o trabalho falhar, ele indica um problema WSB que resultaria em falha nos backups de estado do sistema do agente MARS.

## <a name="common-errors"></a>Erros comuns

### <a name="vss-writer-timeout-error"></a>Erro de tempo limite do gravador VSS

| Sintoma | Causa | Resolução
| -- | -- | --
| -O agente MARS falha com a mensagem de erro: "falha no trabalho WSB com erros VSS. Verificar os logs de eventos do VSS para resolver a falha "<br/><br/> -O seguinte log de erros está presente nos logs de eventos do aplicativo VSS: "um gravador VSS rejeitou um evento com o erro 0x800423f2, o tempo limite do gravador expirou entre os eventos Freeze e descongelar."| O gravador VSS não pode ser concluído no tempo devido à falta de recursos de CPU e memória no computador <br/><br/> Outro software de backup já está usando o gravador VSS, pois uma operação de instantâneo de resultado não pôde ser concluída para este backup | Aguarde a liberação da CPU/memória no sistema ou anule os processos usando muita memória/CPU e repita a operação. <br/><br/>  Aguarde a conclusão do backup em andamento e tente executar a operação posteriormente quando não houver backups em execução no computador.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Espaço em disco insuficiente para aumentar as cópias de sombra

| Sintoma | Resolução
| -- | --
| -O agente MARS falha com a mensagem de erro: falha no backup porque o volume da cópia de sombra não pôde crescer devido a espaço em disco insuficiente nos volumes que contêm arquivos do sistema <br/><br/> -O seguinte log de erros/avisos está presente nos logs de eventos do sistema VolSnap: "não havia espaço em disco suficiente no volume C: para aumentar o armazenamento de cópia de sombra para cópias de sombra de C: devido a essa falha, todas as cópias de sombra do volume C: estão em risco de serem excluídas" | -Libere espaço no volume realçado no log de eventos para que haja espaço suficiente para que as cópias de sombra cresçam enquanto o backup está em andamento <br/><br/> -Ao configurar o espaço de cópia de sombra, podemos restringir a quantidade de espaço usado para cópia de sombra. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)

### <a name="efi-partition-locked"></a>Partição EFI bloqueada

| Sintoma | Resolução
| -- | --
| O agente MARS falha com a mensagem de erro: "falha no backup do estado do sistema porque a partição do sistema EFI está bloqueada. Isso pode ser devido ao acesso à partição do sistema por uma segurança de terceiros ou backup de software " | -Se o problema for devido a um software de segurança de terceiros, você precisará entrar em contato com o fornecedor de antivírus para que ele possa permitir o agente MARS <br/><br/> -Se um software de backup de terceiros estiver em execução, aguarde sua conclusão e repita o backup

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o estado do sistema do Windows na implantação do Gerenciador de recursos, consulte [fazer backup do estado do sistema do Windows Server](backup-azure-system-state.md)
