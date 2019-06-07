---
title: Resolver problemas de cópia de segurança do Estado do sistema com o Azure Backup
description: Resolva problemas no Backup de estado do sistema.
services: backup
author: srinathvasireddy
manager: sivan
keywords: como a cópia de segurança; Estado do sistema de cópia de segurança
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathvasireddy
ms.openlocfilehash: 53b9f8fb58a6e70a4bd2cd02adb9ce824466d7de
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481598"
---
# <a name="troubleshoot-system-state-backup"></a>Resolver problemas de cópia de segurança do Estado do sistema

Este artigo descreve as soluções para problemas que poderá encontrar ao utilizar o Backup de estado do sistema.

## <a name="pre-requisite"></a>Pré-requisito

Antes, resolver problemas de cópia de segurança de estado de sistema com o Azure Backup, certifique-se de realizar a o abaixo pré-requisitos de verificação.  

### <a name="verify-windows-server-backup-is-installed"></a>Verifique se a cópia de segurança do Windows Server está instalada

Certifique-se de que a cópia de segurança do Windows Server é instalada e ativada no servidor. Para verificar o estado da instalação, execute o comando do PowerShell abaixo:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Se a saída apresenta o **estado de instalação** como **disponível**, significa que funcionalidade de cópia de segurança do Windows Server está disponível para a instalação, mas não instalados no servidor. No entanto se a cópia de segurança do Windows Server não estiver instalada, em seguida, utilize uma dos métodos para instalá-lo abaixo.

**Método 1: Instalar o Backup do Windows Server com o PowerShell**

Para instalar o Backup do Windows Server com o PowerShell, execute o comando abaixo:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Método 2: Instalar o Backup do Windows Server com o Gestor de servidor**

Para instalar a cópia de segurança do servidor Windows utilizando o Gestor de servidor, execute o abaixo:

1. Na **Manager Sever** e clique em **para adicionar funções e funcionalidades**. O **Assistente para adicionar funções e funcionalidades** aparece.

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecione **tipo de instalação** e clique em **próxima**.

    ![Tipo de instalação](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecione um servidor no agrupamento de servidores e clique em **seguinte**. Na função de servidor, deixe a seleção predefinida e clique em **seguinte**.
4. Selecione **cópia de segurança do Windows Server** na **funcionalidades** separador e clique em **seguinte**.

    ![elástica](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na **confirmação** separador, clique em **instalar** para iniciar o processo de instalação.
6. Na **resultados** separador, apresentará o Backup do Windows Server funcionalidade é instalada com êxito no seu servidor do Windows.

    ![Resultado](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Permissão de informações de Volume do sistema

Certifique-se de que o sistema Local tem controlo total **informações do Volume de sistema** pasta localizada no volume onde está instalado o windows. Normalmente, isto é **C:\System Volume informações**. Cópia de segurança do Windows Server pode falhar se as permissões acima não estão definidas corretamente

### <a name="dependent-services"></a>Serviços dependentes

Certifique-se a abaixo serviços estão no estado de execução:

**Nome do serviço** | **Tipo de arranque**
--- | ---
Call(RPC) de procedimento remoto | Automático
Eventos do COM+ System(EventSystem) | Automático
Service(SENS) de notificação de eventos do sistema | Automático
Copy(VSS) de cópias de sombra de volume | Manual
Provider(SWPRV) de cópia de sombra de Software da Microsoft | Manual

### <a name="validate-windows-server-backup-status"></a>Validar o estado da cópia de segurança do Windows Server

Para validar o estado da cópia de segurança do Windows Server, execute o abaixo:

  * Certifique-se de que está a executar o WSB PowerShell

    -   Executar `Get-WBJob` de uma elevada do PowerShell e certifique-se de que ela não retorna o seguinte erro:

    > [!WARNING]
    > Get-WBJob: O termo 'Get-WBJob' não é reconhecido como o nome de um cmdlet, a função, o ficheiro de script ou o programa operável. Verifique a ortografia do nome, ou se um caminho foi incluído, certifique-se de que o caminho está correto e tente novamente.

    -   Se falhar com este erro, em seguida, reinstale a funcionalidade de cópia de segurança do Windows Server na máquina do servidor, conforme mencionado nos pré-requisitos passo 1.

  * Certifique-se de que a cópia de segurança do WSB está a funcionar corretamente, ao executar o comando da linha de comandos elevada abaixo:

      ` wbadmin start systemstatebackup -backuptarget:X: -quiet `

      > [!NOTE]
      >Substituir o X com a letra da unidade do volume onde pretende armazenar o estado do sistema de fazer backup de imagem.

    - Verifique periodicamente o estado da tarefa, executando `Get-WBJob` comando do PowerShell elevado        
    - Após a conclusão da tarefa de cópia de segurança Verifique o estado final da tarefa executando `Get-WBJob -Previous 1` comando

Se a tarefa falhar, ele indica um problema WSB que poderia resultar numa falha de Backups de estado do sistema de agente de MARS.

## <a name="common-errors"></a>Erros comuns

### <a name="vss-writer-timeout-error"></a>Erro de tempo limite de escritor VSS

| Sintoma | Causa | Resolução
| -- | -- | --
| -Agente de MARS pode falhar com mensagem de erro: "Tarefa WSB falhou com erros VSS. Verifique os registos de eventos VSS para resolver a falha"<br/><br/> -Erro seguinte registo está presente nos logs de eventos da aplicação do VSS: "Um escritor VSS rejeitou um evento com erro 0x800423f2, tempo limite o escritor excedido entre os eventos congelamento e Thaw."| Escritor VSS não consegue concluir no tempo devido à falta de recursos de CPU e memória na máquina <br/><br/> Outro software de backup já está a utilizar o escritor VSS, como resultado não foi possível concluir a operação de instantâneo para esta cópia de segurança | Aguarde pela CPU/memória libertar no sistema ou abortar processos tendo muita memória/CPU e repita a operação <br/><br/>  Aguarde que a cópia de segurança em curso seja concluída e tente a operação mais tarde, quando não existem cópias de segurança estão em execução na máquina


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Espaço em disco suficiente para crescer cópias de sombra

| Sintoma | Resolução
| -- | --
| -Agente de MARS pode falhar com mensagem de erro: Cópia de segurança falhou porque o volume de cópia de sombra não pode crescer devido a espaço em disco insuficiente nos volumes que contém ficheiros de sistema <br/><br/> -Após o registo de erro/aviso está presente nos logs de eventos do sistema de volsnap: "Ocorreu espaço em disco suficiente no volume c: para aumentar o armazenamento de cópia sombra para cópias de sombra de c: devido a esta falha de todas as cópias de sombra de volume c: estão em risco de que está a ser eliminado" | -Liberte espaço no volume realçado no registo de eventos para que haja espaço suficiente para cópias de sombra aumente enquanto a cópia de segurança está em curso <br/><br/> -Ao configurar o espaço de cópia de sombra a Microsoft pode limitar a quantidade de espaço utilizada para cópia de sombra, para obter mais informações consulte esta [artigo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>Partição EFI bloqueada

| Sintoma | Resolução
| -- | --
| Agente de MARS pode falhar com mensagem de erro: "Estado do sistema novamente até falhou uma vez que a partição do sistema EFI está bloqueada. Isso pode ser devido a acesso de partição de sistema por uma segurança de terceiros ou fazer cópia de segurança de software" | – Se o problema é devido a um software de segurança de terceiros, em seguida, terá de contactar o fornecedor de vírus de antimalware, para que eles podem permitir que o agente MARS <br/><br/> – Se estiver a executar um software de cópia de segurança de terceiros, em seguida, aguarde que seja concluído e, em seguida, repita back cópia de segurança


## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre o estado do sistema Windows na implementação do Resource Manager, consulte [cópia de segurança do sistema de estado do Windows Server](backup-azure-system-state.md)
