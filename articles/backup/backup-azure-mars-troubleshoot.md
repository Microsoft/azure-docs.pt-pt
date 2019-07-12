---
title: Resolver problemas relacionados com o agente de cópia de segurança do Azure
description: Resolver problemas de instalação e registo do agente do Backup do Azure
services: backup
author: saurabhsensharma
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 437b175efad081b8382d80be8427aa074920fd3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705046"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Resolver problemas relacionados com o agente dos serviços de recuperação do Azure (MARS) da Microsoft

Este artigo descreve como resolver erros que poderá ver durante a configuração, registo, cópia de segurança e restaurar.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que verifique o seguinte antes de iniciar a resolução de problemas do Microsoft o agente dos serviços de recuperação do Azure (MARS):

- [Certifique-se de que o agente MARS está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Certifique-se de que tem conectividade de rede entre o agente MARS e Azure](https://aka.ms/AB-A4dp50).
- Certifique-se de que está a executar o MARS (na consola de serviço). Se for necessário, reinicie e repita a operação.
- [Certifique-se de que 5% a 10% livre de volume espaço está disponível na localização de pasta de rascunho](https://aka.ms/AB-AA4dwtt).
- [Verifique se a outro processo ou antivírus estiver interferindo com o Azure Backup](https://aka.ms/AB-AA4dwtk).
- Se agendado falha de cópia de segurança, mas funciona a cópia de segurança manual, consulte [cópias de segurança não são executados de acordo com a agenda](https://aka.ms/ScheduledBackupFailManualWorks).
- Certifique-se de que seu sistema operacional tem as atualizações mais recentes.
- [Certifique-se de que as unidades não suportadas e os ficheiros com atributos não suportados são excluídos da cópia de segurança](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Certifique-se de que o relógio do sistema protegido está configurado para o fuso horário correto.
- [Certifique-se o .NET Framework 4.5.2 ou posterior está instalado no servidor](https://www.microsoft.com/download/details.aspx?id=30653).
- Se estiver a tentar voltar a registar o servidor para um cofre:
  - Certifique-se de que o agente seja desinstalado no servidor e que é eliminado do portal.
  - Utilize a mesma frase de acesso que foi primeiramente usada para registar o servidor.
- Para cópias de segurança offline, certifique-se de que 3.7.0 do Azure PowerShell está instalado de origem e o computador de cópia antes de iniciar a cópia de segurança.
- Se o agente de cópia de segurança está em execução numa máquina virtual do Azure, veja [este artigo](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

**Mensagem de erro**: Foram fornecidas credenciais de cofre inválidas. O ficheiro está danificado ou não ter as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)

| Causa | Ações recomendadas |
| ---     | ---    |
| **As credenciais do Cofre não são válidas** <br/> <br/> Ficheiros de credenciais de cofre podem estar danificados ou poderão ter expirado. (Por exemplo, eles talvez tenham sido transferidos mais de 48 horas antes da hora de registo.)| Transferir novas credenciais do Cofre de serviços de recuperação no portal do Azure. (Consulte a etapa 6 na [transferir o agente de MARS](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) seção.) Em seguida, siga estes passos, conforme apropriado: <ul><li> Se já tiver instalado e registado MARS, abra a consola do MMC do agente de cópia de segurança do Microsoft Azure e, em seguida, selecione **registar o servidor** no **ações** painel para concluir o registo com o novo credenciais. <br/> <li> Se a nova instalação falhar, tente voltar a instalar com as novas credenciais.</ul> **Nota**: Se tiverem sido transferidos vários ficheiros de credenciais do cofre, apenas o ficheiro mais recente é válido durante as próximas 48 horas. Recomendamos que transfira um novo ficheiro de credenciais do cofre.
| **Firewall e servidor proxy está a bloquear o registo** <br/>ou <br/>**Sem conectividade internet** <br/><br/> Se o servidor proxy ou máquina limitou a conectividade à internet e não a garantir o acesso para os URLs necessários, o registo falhará.| Siga estes passos:<br/> <ul><li> Trabalhar com a sua equipa de TI para garantir que o sistema tem conectividade à internet.<li> Se não tiver um servidor proxy, certifique-se de que a opção de proxy não está selecionada quando registar o agente. [Verifique as definições de proxy](#verifying-proxy-settings-for-windows).<li> Se tiver um servidor de firewall/proxy, trabalhar com a sua equipa de rede para garantir que estes URLs e endereços IP têm acesso:<br/> <br> **URLs**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Endereços IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Tente registar-se novamente depois de concluir os passos de resolução de problemas anteriores.
| **Software antivírus está a bloquear o registo** | Se tiver software antivírus instalado no servidor, adicione regras de exclusão necessários para a análise de software antivírus para estes ficheiros e pastas: <br/><ui> <li> CBengine.exe <li> CSC.exe<li> A pasta de rascunho. Localização predefinida é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A pasta bin em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Recomendações adicionais
- Vá para c: / Windows/Temp e verificar se existem mais de 60 000 ou 65.000 ficheiros com a extensão. tmp. Se existirem, elimine estes ficheiros.
- Certifique-se a data da máquina e correspondência de tempo o fuso horário local.
- Certifique-se [esses sites](backup-configure-vault.md#verify-internet-access) são adicionados aos seus sites fidedignos no Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>A verificar as definições de proxy para Windows

1. Baixe o PsExec do [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) página.
1. Executar `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` num prompt de comando elevado.

   Este comando irá abrir o Internet Explorer.
1. Aceda a **ferramentas** > **opções da Internet** > **ligações** > **definições de LAN**.
1. Verifique as definições de proxy para a conta de sistema.
1. Se nenhum proxy está configurado e são fornecidos detalhes do proxy, remova os detalhes.
1. Se um proxy está configurado e os detalhes do proxy estão incorretos, certifique-se a **IP de Proxy** e **porta** detalhes estão corretos.
1. Feche o Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Não é possível transferir o ficheiro de credenciais do Cofre

| Erro   | Ações recomendadas |
| ---     | ---    |
|Falha ao transferir o ficheiro de credenciais do cofre. (ID: 403) | <ul><li> Tente baixar as credenciais do cofre com um browser diferente ou, siga estes passos: <ul><li> Inicie o Internet Explorer. Selecione a tecla F12. </li><li> Vá para o **rede** separador e limpar o cache e os cookies. </li> <li> Atualize a página.<br></li></ul> <li> Verifique se a subscrição está desativada/expirado.<br></li> <li> Verifique se qualquer regra de firewall está a bloquear o download. <br></li> <li> Certifique-se de que ainda não tiver esgotado o limite do cofre (50 máquinas por cofre).<br></li>  <li> Certifique-se de que o utilizador tem as permissões de cópia de segurança do Azure que são necessárias para transferir as credenciais do cofre e registar um servidor com o cofre. Ver [Use Role-Based o controlo de acesso para gerir pontos de recuperação de cópia de segurança do Azure](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente do Serviço de Recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup

| Erro  | Causa possível | Ações recomendadas |
| ---     | ---     | ---    |
| <br /><ul><li>O agente de serviço de recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup. (ID: 100050) Verifique as definições de rede e certifique-se de que é possível estabelecer ligação à internet.<li>Proxy (407) autenticação necessária. |Um proxy está a bloquear a ligação. |  <ul><li>No Internet Explorer, aceda a **ferramentas** > **opções da Internet** > **segurança** > **Internet**. Selecione **nível personalizado** e desloque para baixo até o **download de arquivo** secção. Selecione **ativar**.<p>Também poderá ter de adicionar [URLs e endereços IP](backup-configure-vault.md#verify-internet-access) aos seus sites fidedignos no Internet Explorer.<li>Altere as definições para utilizar um servidor proxy. Em seguida, forneça o proxy de detalhes do servidor.<li> Se a sua máquina limitou o acesso à internet, certifique-se de que as definições da firewall na máquina ou proxy permitem estes [URLs e endereços IP](backup-configure-vault.md#verify-internet-access). <li>Se tiver software antivírus instalado no servidor, exclua esses arquivos de análise de software antivírus: <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionados com o .NET Framework). Há um CSC.exe para todas as versões do .NET Framework instalada no servidor. Exclua ficheiros de CSC.exe para todas as versões do .NET Framework no servidor afetado. <li>A localização de pasta ou cache de rascunho. <br>A localização predefinida para a pasta de rascunho ou o caminho de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A pasta bin em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao definir a chave de encriptação de cópias de segurança seguras

| Erro | Causas possíveis | Ações recomendadas |
| ---     | ---     | ---    |
| <br />Falha ao definir a chave de encriptação de cópias de segurança seguras. Ativação não foi possível ativar completamente, mas a frase de acesso de encriptação foi guardado para o arquivo a seguir. |<li>O servidor já está registado com outro cofre.<li>Durante a configuração, a frase de acesso foi danificado.| Anular o registo do servidor do cofre e registe-a novamente com uma nova frase de acesso.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não foi concluída com êxito

| Erro  | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
|<br />A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contacte o suporte da Microsoft.     | <li> A pasta de rascunho se encontra num volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente. <li> O ficheiro de OnlineBackup.KEK está em falta.         | <li>Atualizar para o [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a localização de pasta ou cache de rascunho para um volume com espaço livre entre 5% e de 10% do tamanho total dos dados de cópia de segurança. Ao mover corretamente a localização da cache, consulte os passos em [perguntas comuns sobre o backup de arquivos e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o ficheiro de OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de rascunho ou o caminho de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Frase de acesso de encriptação não corretamente configurado

| Erro  | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
| <br />Erro 34506. A frase de acesso de encriptação armazenado neste computador não está corretamente configurado.    | <li> A pasta de rascunho se encontra num volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente. <li> O ficheiro de OnlineBackup.KEK está em falta.        | <li>Atualizar para o [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a localização de pasta ou cache de rascunho para um volume com espaço livre entre 5% e de 10% do tamanho total dos dados de cópia de segurança. Ao mover corretamente a localização da cache, consulte os passos em [perguntas comuns sobre o backup de arquivos e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o ficheiro de OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de rascunho ou o caminho de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>As cópias de segurança não são executados de acordo com a agenda
Se as cópias de segurança agendadas não obter acionadas automaticamente, mas as cópias de segurança manuais funcionem corretamente, experimente as seguintes ações:

- Certifique-se de que a agenda de cópia de segurança do Windows Server não entra em conflito com a agenda cópias de segurança do Azure ficheiros e pastas.

- Certifique-se de que o estado de cópia de segurança online está definido como **ativar**. Para verificar o estado, siga estes passos:

  1. No agendador de tarefas, expanda **Microsoft** e selecione **cópia de segurança Online**.
  1. Faça duplo clique em **Microsoft OnlineBackup** e vá para o **Acionadores** separador.
  1. Verifique se o estado estiver definido como **ativado**. Se não estiver, selecione **edite**, selecione **ativado**e, em seguida, selecione **OK**.

- Certifique-se de que a conta de utilizador selecionada para executar a tarefa está **SYSTEM** ou **Grupo Local de administradores** no servidor. Para verificar a conta de utilizador, vá para o **gerais** separador e verificar o **segurança** opções.

- Certifique-se de que o PowerShell 3.0 ou posterior está instalado no servidor. Para verificar a versão do PowerShell, execute este comando e certifique-se de que o `Major` número de versão é 3 ou posterior:

  `$PSVersionTable.PSVersion`

- Certifique-se de que este caminho é parte do `PSMODULEPATH` variável de ambiente:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a diretiva de execução do PowerShell para `LocalMachine` é definido para restrito, o cmdlet do PowerShell que aciona a tarefa de cópia de segurança poderá falhar. Execute estes comandos no modo elevado para verificar e defina a política de execução para qualquer um `Unrestricted` ou `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Certifique-se de que não há nenhum arquivo de MSOnlineBackup do módulo do PowerShell em falta ou danificado. Se existirem quaisquer ficheiros em falta ou danificados, siga estes passos:

  1. Em qualquer máquina que tenha um agente de MARS que está a funcionar corretamente, copie a pasta de MSOnlineBackup do C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. No computador problemático, cole os arquivos copiados na mesma localização de pasta (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se já existe uma pasta de MSOnlineBackup na máquina, cole os ficheiros na mesma ou substituir quaisquer ficheiros existentes.


> [!TIP]
> Para garantir que as alterações são aplicadas de forma consistente, reinicie o servidor depois de efetuar os passos anteriores.


## <a name="troubleshoot-restore-problems"></a>Resolução de problemas de restauro

Cópia de segurança do Azure com êxito não poderá montar o volume de recuperação, mesmo após alguns minutos. E pode receber mensagens de erro durante o processo. Para começar a recuperar, normalmente, siga estes passos:

1.  Cancele o processo de montagem, se ele estiver a ser executado durante vários minutos.

2.  Verifique se tem a versão mais recente do agente do Backup. Para verificar a versão, diante da **ações** painel de consola do MARS, selecione **sobre o Microsoft Azure Recovery Services Agent**. Confirme que o **versão** número é igual ou superior à versão mencionada [este artigo](https://go.microsoft.com/fwlink/?linkid=229525). Selecione esta ligação para [transferir a versão mais recente](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Aceda a **Gestor de dispositivos** > **controladores de armazenamento** e localize **Iniciador do Microsoft iSCSI**. Se localizá-lo, vá diretamente para o passo 7.

4.  Se não conseguir localizar o serviço Iniciador do iSCSI da Microsoft, tentar localizar uma entrada sob **Gestor de dispositivos** > **controladores de armazenamento** com o nome **dispositivo desconhecido** com o ID de Hardware **ROOT\ISCSIPRT**.

5.  Com o botão direito **dispositivo desconhecido** e selecione **atualizar Driver**.

6.  Atualizar o driver, selecionando a opção para **pesquisar automaticamente software de driver atualizado**. Esta atualização deve ser alterado **dispositivo desconhecido** ao **Iniciador do Microsoft iSCSI**:

    ![Captura de ecrã do Azure Backup Device Manager, com controladores de armazenamento realçados](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Aceda a **Gerenciador de tarefas** > **serviços (Local)**  > **serviço iniciador iSCSI da Microsoft**:

    ![Captura de ecrã do Azure Backup Gerenciador de tarefas, com os serviços (Local) realçados](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Reinicie o serviço Iniciador do iSCSI da Microsoft. Para fazer isso, o serviço com o botão direito e selecione **parar**. Em seguida, faça duplo clique nele novamente e selecione **iniciar**.

9.  Repita a recuperação utilizando [restaurar instantâneas](backup-instant-restore-capability.md).

Se a recuperação continuar a falhar, reinicie o seu servidor ou cliente. Se não pretender reiniciar, ou se a recuperação continuar a falhar, mesmo depois de reiniciar o servidor, tente [recuperar a partir de outra máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte
Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para seu problema resolvido rapidamente.

## <a name="next-steps"></a>Passos seguintes
* Obter mais detalhes sobre [como criar cópias de segurança do Windows Server com o Azure Backup agent](tutorial-backup-windows-server-to-azure.md).
* Se precisar de restaurar uma cópia de segurança, consulte [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
