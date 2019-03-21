---
title: Resolução de problemas do Azure Backup Agent
description: Resolver problemas de instalação e registo do agente de cópia de segurança do Azure
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: saurse
ms.openlocfilehash: a9a445208c151a537c35fa7afafa48b19486828f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008156"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Resolver problemas de agente do Microsoft Azure Recovery Services (MARS)

Eis como resolver erros que poderá ver durante a configuração, registo, cópia de segurança e restaurar.

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

| Detalhes do erro | Causas possíveis | Ações recomendadas |
| ---     | ---     | ---    |
| **Error** </br> *Foram fornecidas credenciais de cofre inválidas. O ficheiro está danificado ou não ter as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)* | <ul><li> As credenciais do cofre são inválidas (ou seja, eles foram transferidos mais de 48 horas antes da hora de registo).<li>Agente de MARS não consegue transferir os ficheiros para o diretório Temp do Windows. <li>As credenciais do cofre estão numa localização de rede. <li>TLS 1.0 está desativado<li> Um servidor proxy configurado está a bloquear a ligação. <br> |  <ul><li>Transferir as credenciais do cofre novo. (**Nota**: Se vários ficheiros de credenciais do cofre são transferidos anteriormente, apenas o último ficheiro transferido é válido no prazo de 48 horas.) <li>Inicie **IE** > **definição** > **opções da Internet** > **segurança**  >  **Internet**. Em seguida, selecione **nível personalizado**e desloque-se até ver o ficheiro transferir secção. Em seguida, selecione **ativar**.<li>Também poderá ter de adicionar esses sites no IE [sites fidedignos](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Altere as definições para utilizar um servidor proxy. Em seguida, forneça o proxy de detalhes do servidor. <li> Corresponde a data e hora com a sua máquina.<li>Se obtiver um erro a indicar que as transferências de ficheiros não são permitidas, é provável que haja um grande número de ficheiros no diretório c: / Windows/Temp.<li>Vá para c: / Windows/Temp e verificar se existem mais de 60 000 ou 65.000 ficheiros com a extensão. tmp. Se existirem, elimine estes ficheiros.<li>Certifique-se de que tem o .NET framework 4.6.2 instalado. <li>Se tiver desativado o TLS 1.0 devido a conformidade com PCI, consulte este [resolução de problemas de página](https://support.microsoft.com/help/4022913). <li>Se tiver software antivírus instalado no servidor, exclua os seguintes ficheiros da análise de software antivírus: <ul><li>CBengine.exe<li>CSC.exe, que está relacionado com o .NET Framework. Há um CSC.exe para todas as versões do .NET que está instalada no servidor. Exclua ficheiros de CSC.exe que estão associados a todas as versões do .NET Framework no servidor afetado. <li>Localização de pasta ou cache de rascunho. <br>*A localização predefinida para a pasta de rascunho ou o caminho de localização de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>A pasta bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin

## <a name="unable-to-download-vault-credential-file"></a>Não é possível transferir o ficheiro de credenciais do Cofre

| Detalhes do erro | Ações recomendadas |
| ---     | ---    |
|Falha ao transferir o ficheiro de credenciais do cofre. (ID: 403) | <ul><li> Tente baixar as credenciais do cofre com o browser diferente ou executar os passos abaixo: <ul><li> Inicie o IE, premir a tecla F12. </li><li> Aceda a **rede** separador para limpar a cache do IE e os cookies </li> <li> Atualize a página<br>(OR)</li></ul> <li> Verifique se a subscrição está desativada/expirado<br>(OR)</li> <li> Verifique se qualquer regra de firewall está a bloquear o download de arquivo de credenciais do Cofre <br>(OR)</li> <li> Certifique-se de que não ter esgotado o limite do cofre (50 máquinas por cofre)<br>(OR)</li>  <li> Certifique-se de que foi pedido ao utilizador permissão de cópia de segurança do Azure para transferir as credenciais do cofre e registar o servidor com o cofre, consulte [artigo](backup-rbac-rs-vault.md)</li></ul> | 

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente do Serviço de Recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup

| Detalhes do erro | Causas possíveis | Ações recomendadas |
| ---     | ---     | ---    |
| **Error** <br /><ol><li>*O agente de serviço de recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup. (ID: 100050) Verifique as definições de rede e certifique-se de que conseguir estabelecer ligação à internet*<li>*Autenticação do proxy (407) necessária* |Bloquear a ligação de proxy. |  <ul><li>Inicie **IE** > **definição** > **opções da Internet** > **segurança**  >  **Internet**. Em seguida, selecione **nível personalizado** e desloque-se até ver o ficheiro transferir secção. Selecione **Ativar**.<li>Também poderá ter de adicionar esses sites no IE [sites fidedignos](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Altere as definições para utilizar um servidor proxy. Em seguida, forneça o proxy de detalhes do servidor. <li>Se tiver software antivírus instalado no servidor, exclua os seguintes ficheiros da análise de software antivírus. <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionados com o .NET Framework). Há um CSC.exe para todas as versões do .NET que está instalada no servidor. Exclua ficheiros de CSC.exe que estão associados a todas as versões do .NET framework no servidor afetado. <li>Localização de pasta ou cache de rascunho. <br>*A localização predefinida para a pasta de rascunho ou o caminho de localização de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>A pasta bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao definir a chave de encriptação de cópias de segurança seguras

| Detalhes do erro | Causas possíveis | Ações recomendadas |
| ---     | ---     | ---    |
| **Error** <br />*Falha ao definir a chave de encriptação de cópias de segurança seguras. Ativação não foi possível ativar completamente, mas a frase de acesso de encriptação foi guardado o ficheiro seguinte*. |<li>O servidor já está registado com outro cofre.<li>Durante a configuração, a frase de acesso foi danificado.| Anular o registo do servidor do cofre e registe novamente com uma nova frase de acesso.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não foi concluída com êxito

| Detalhes do erro | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
|**Error** <br /><ol>*A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contacte o suporte da Microsoft*     | <li> A pasta de rascunho se encontra num volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente para outra localização. <li> O ficheiro de OnlineBackup.KEK está em falta.         | <li>Atualizar para o [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a localização de pasta ou cache de rascunho para um volume com espaço livre igual a 5 a 10% do tamanho total dos dados de cópia de segurança. Ao mover corretamente a localização da cache, consulte os passos em [perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o ficheiro de OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de rascunho ou o caminho de localização de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Frase de acesso de encriptação não corretamente configurado

| Detalhes do erro | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
|**Error** <br /><ol>*Erro 34506. A frase de acesso de encriptação armazenado neste computador não está configurada corretamente*.    | <li> A pasta de rascunho se encontra num volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente para outra localização. <li> O ficheiro de OnlineBackup.KEK está em falta.        | <li>Atualizar para o [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a pasta de rascunho ou a localização da cache para um volume com espaço livre equivalente aos 5 a 10% do tamanho total dos dados de cópia de segurança. Ao mover corretamente a localização da cache, consulte os passos em [perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o ficheiro de OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de rascunho ou o caminho de localização de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Não executam cópias de segurança, de acordo com a agenda
Se as cópias de segurança agendadas não obter acionadas automaticamente, enquanto as cópias de segurança manuais funcionam sem problemas, tente as seguintes ações:

- Certifique-se a agenda de cópia de segurança do Windows Server não entram em conflito com a agenda de cópia de segurança de ficheiros do Azure e de pastas.
- Aceda a **painel de controlo** > **ferramentas administrativas** > **Programador de tarefas**. Expanda **Microsoft**e selecione **cópia de segurança Online**. Faça duplo clique em **Microsoft OnlineBackup**e vá para o **Acionadores** separador. Certifique-se de que o estado é definido como **ativado**. Se não estiver, selecione **edite**e selecione o **ativado** caixa de verificação e clique em **OK**. No **gerais** separador, aceda à **opções de segurança** e certifique-se de que a conta de utilizador selecionada para executar a tarefa é um **sistema** ou **Local Grupo de administradores** no servidor.

- Veja se o PowerShell 3.0 ou posterior está instalado no servidor. Para verificar a versão do PowerShell, execute o seguinte comando e certifique-se de que o *principais* número de versão é igual ou superior a 3.

  `$PSVersionTable.PSVersion`

- Se o seguinte caminho é parte da *PSMODULEPATH* variável de ambiente.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a diretiva de execução do PowerShell para *LocalMachine* é definido para restrito, o cmdlet do PowerShell que aciona a tarefa de cópia de segurança poderá falhar. Execute os seguintes comandos no modo elevado, para verificar e defina a política de execução para qualquer um *Unrestricted* ou *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

> [!TIP]
> Para garantir que as alterações são aplicadas de forma consistente, reinicie o servidor depois de efetuar os passos acima.


## <a name="troubleshoot-restore-issues"></a>Resolver problemas de restauro

Cópia de segurança do Azure com êxito não poderá montar o volume de recuperação, mesmo após alguns minutos. Também pode receber mensagens de erro durante o processo. Para começar a recuperar, normalmente, siga estes passos:

1.  Cancele o processo de montagem em curso, caso ele esteve em execução durante vários minutos.

2.  Veja se está na versão mais recente do agente do Backup. Para saber a versão no **ações** painel de consola do MARS, selecione **sobre o Microsoft Azure Recovery Services Agent**. Confirme que o **versão** número é igual ou superior à versão mencionada [este artigo](https://go.microsoft.com/fwlink/?linkid=229525). Pode baixar a versão mais recente a partir [aqui](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Aceda a **Gestor de dispositivos** > **controladores de armazenamento**e localize **Iniciador do Microsoft iSCSI**. Se pode localizá-lo, vá diretamente para o passo 7.

4.  Se não conseguir localizar o serviço de Iniciador Microsoft iSCSI, tentar localizar uma entrada sob **Gestor de dispositivos** > **controladores de armazenamento** chamado **dispositivo desconhecido**, com o ID de Hardware **ROOT\ISCSIPRT**.

5.  Com o botão direito no **dispositivo desconhecido**e selecione **atualizar Driver**.

6.  Atualizar o driver, selecionando a opção para **pesquisar automaticamente software de driver atualizado**. Conclusão da atualização deve ser alterado **dispositivo desconhecido** ao **Iniciador do Microsoft iSCSI**, conforme mostrado abaixo.

    ![Captura de ecrã do Azure Backup Device Manager, com controladores de armazenamento realçados](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Aceda a **Gerenciador de tarefas** > **serviços (Local)** > **serviço iniciador iSCSI da Microsoft**.

    ![Captura de ecrã do Azure Backup Gerenciador de tarefas, com os serviços (Local) realçados](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Reinicie o serviço Iniciador do iSCSI da Microsoft. Para tal, clique com botão direito no serviço, selecione **parar**, clique com botão direito novamente e selecione **iniciar**.

9.  Repita a recuperação utilizando [ **restaurar instantâneas**](backup-instant-restore-capability.md).

Se a recuperação continuar a falhar, reinicie o seu servidor ou cliente. Se não deseja reiniciar ou a recuperação continuar a falhar, mesmo após o reinício do servidor, tente recuperar a partir de uma máquina alternativa. Siga os passos em [este artigo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte
Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes
* Obter mais detalhes sobre [a efetuar cópias de segurança do Windows Server com o Azure Backup Agent](tutorial-backup-windows-server-to-azure.md).
* Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
