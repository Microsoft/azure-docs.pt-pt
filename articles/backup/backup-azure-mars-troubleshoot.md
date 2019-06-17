---
title: Resolução de problemas do Azure Backup Agent
description: Resolver problemas de instalação e registo do agente de cópia de segurança do Azure
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 2c2ed46ed6e4a5d6663387777d3425d18b50500e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060212"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Resolver problemas de agente do Microsoft Azure Recovery Services (MARS)

Eis como resolver erros que poderá ver durante a configuração, registo, cópia de segurança e restaurar.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que efetue a abaixo validação, antes de começar a resolução de problemas de agente dos serviços de recuperação do Azure (MARS) da Microsoft:

- [Certifique-se de que o agente de serviços de recuperação do Azure (MARS) da Microsoft é atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que existe conectividade de rede entre o agente MARS e o Azure](https://aka.ms/AB-A4dp50)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (na consola do Serviço). Se necessário, reinicie e volte a tentar a operação
- [Certifique-se de que 5 a 10% de espaço livre do volume está disponível na localização da pasta de rascunho](https://aka.ms/AB-AA4dwtt)
- [Verifique se outro processo ou software antivírus está a interferir com o Azure Backup](https://aka.ms/AB-AA4dwtk)
- [A cópia de segurança agendada falha, mas a cópia de segurança manual funciona](https://aka.ms/ScheduledBackupFailManualWorks)
- Certifique-se de que o seu SO tem as atualizações mais recentes
- [Certifique-se de que as unidades não suportadas e os ficheiros com atributos não suportados são excluídos da cópia de segurança](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Certifique-se de que o **Relógio do Sistema** do sistema protegido está configurado para o fuso horário correto <br>
- [Certifique-se de que o servidor tem, pelo menos, a versão 4.5.2 ou superior do .Net Framework](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se estiver a tentar **registar novamente o servidor** para um cofre, então: <br>
  - Certifique-se de que o agente foi desinstalado no servidor e que foi eliminado do portal <br>
  - Utilize a mesma frase de acesso que foi inicialmente utilizada para registar o servidor <br>
- Em caso de cópia de segurança offline Certifique-se de que o Azure PowerShell versão 3.7.0 está instalado no computador de origem e de cópia, antes de iniciar a operação de cópia de segurança offline
- [Consideração ao agente de cópia de segurança está em execução numa máquina virtual do Azure](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

**Mensagem de erro**: Foram fornecidas credenciais de cofre inválidas. O ficheiro está danificado ou não ter as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)

| Causa | Ação Recomendada |
| ---     | ---    |
| **As credenciais do cofre são inválidas** <br/> <br/> Ficheiros de credenciais de cofre podem estar danificados ou podem ter expirado (ou seja, transferido mais de 48 horas antes da hora de registo)| Transferir uma nova credencial do Cofre de serviços de recuperação a partir do portal do Azure (veja *passo 6* sob [ **transferir o agente de MARS** ](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) secção) e executar o abaixo: <ul><li> Se já tiver instalado e registado o agente de cópia de segurança do Microsoft Azure, em seguida, abra a consola do MMC do agente de cópia de segurança do Microsoft Azure e escolha **registar servidor** partir do painel de ações para concluir o registo com o recém-baixado credenciais <br/> <li> Se a nova instalação do falha em seguida, tente novamente instalar utilizando as credenciais novas</ul> **Nota**: Se vários ficheiros de credenciais do cofre são transferidos anteriormente, apenas o último ficheiro transferido é válido no prazo de 48 horas. Por conseguinte, recomenda-se para baixar o novo ficheiro de credenciais do cofre novo.
| **Está a bloquear o firewall e servidor proxy <br/>ou <br/>conectividade com a Internet não** <br/><br/> Se seu computador ou servidor Proxy limitou o acesso à Internet, em seguida, listando os URLs necessários o registo falhará.| Para resolver este problema, execute o abaixo:<br/> <ul><li> Trabalhar com a sua equipa de TI para garantir que o sistema tem conectividade à Internet<li> Se não tiver o servidor de Proxy, em seguida, certifique-se a opção de proxy é não selecionada ao registar o agente, verifique se os passos de definições de proxy apresentados [aqui](#verifying-proxy-settings-for-windows)<li> Se tiver um servidor de firewall/proxy, em seguida, trabalho com a equipa de rede para assegurar que os abaixo IP e URLs têm acesso<br/> <br> **URLs**<br> - *www.msftncsi.com* <br>-  *.Microsoft.com* <br> -  *.WindowsAzure.com* <br>-  *.microsoftonline.com* <br>-  *.windows.net* <br>**Endereço IP**<br> - *20.190.128.0/18* <br> - *40.126.0.0/18* <br/></ul></ul>Tente registar novamente depois de concluir os passos de resolução de problemas acima
| **Está a bloquear o software antivírus** | Se tiver software antivírus instalado no servidor, em seguida, adicione regras de exclusão necessários para os seguintes ficheiros da análise de software antivírus: <br/><ui> <li> *CBengine.exe* <li> *CSC.exe*<li> A localização predefinida da pasta de rascunho, é *C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* <li> Pasta bin em *C:\Program Files\Microsoft Azure Recovery Services Agent\Bin*

### <a name="additional-recommendations"></a>Recomendações adicionais
- Aceda a *c: / Windows/Temp* e verificar se existem mais de 60 000 ou 65.000 ficheiros com a extensão. tmp. Se existirem, eliminar estes ficheiros
- Certifique-se de que a máquina data e hora correspondência com o fuso horário local
- Certifique-se a [seguintes](backup-configure-vault.md#verify-internet-access) sites são adicionados ao IE sites fidedignos

### <a name="verifying-proxy-settings-for-windows"></a>A verificar as definições de proxy para Windows

- Baixe **psexec** partir [aqui](https://docs.microsoft.com/sysinternals/downloads/psexec)
- Executar isso `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` comando da linha de comandos elevada:
- Isso iniciará *Internet Explorer* janela
- Aceda a *ferramentas* -> *opções da Internet* -> *ligações* -> *definições de LAN*
- Verifique se as definições de proxy *sistema* conta
- Se nenhum proxy está configurado e são fornecidos detalhes do proxy, em seguida, remova os detalhes
-   Se o proxy está configurado e detalhes do proxy estão incorretas, em seguida, certifique-se *IP de Proxy* e *porta* detalhes são precisos
- Fechar *do Internet Explorer*

## <a name="unable-to-download-vault-credential-file"></a>Não é possível transferir o ficheiro de credenciais do Cofre

| Detalhes do erro | Ações recomendadas |
| ---     | ---    |
|Falha ao transferir o ficheiro de credenciais do cofre. (ID: 403) | <ul><li> Tente baixar as credenciais do cofre com o browser diferente ou executar os passos abaixo: <ul><li> Inicie o IE, premir a tecla F12. </li><li> Aceda a **rede** separador para limpar a cache do IE e os cookies </li> <li> Atualize a página<br>(OR)</li></ul> <li> Verifique se a subscrição está desativada/expirado<br>(OR)</li> <li> Verifique se qualquer regra de firewall está a bloquear o download de arquivo de credenciais do Cofre <br>(OR)</li> <li> Certifique-se de que não ter esgotado o limite do cofre (50 máquinas por cofre)<br>(OR)</li>  <li> Certifique-se de que foi pedido ao utilizador permissão de cópia de segurança do Azure para transferir as credenciais do cofre e registar o servidor com o cofre, consulte [artigo](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente do Serviço de Recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup

| Detalhes do erro | Causas possíveis | Ações recomendadas |
| ---     | ---     | ---    |
| **Error** <br /><ol><li>*O agente de serviço de recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup. (ID: 100050) Verifique as definições de rede e certifique-se de que conseguir estabelecer ligação à internet*<li>*(407) Proxy Authentication Required* ((407) Autenticação de Proxy Necessária) |Bloquear a ligação de proxy. |  <ul><li>Inicie **IE** > **definição** > **opções da Internet** > **segurança**  >  **Internet**. Em seguida, selecione **nível personalizado** e desloque-se até ver o ficheiro transferir secção. Selecione **ativar**.<li>Também poderá ter de adicionar esses sites no IE [sites fidedignos](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Altere as definições para utilizar um servidor proxy. Em seguida, forneça o proxy de detalhes do servidor.<li> Se a sua máquina limitou o acesso à internet, certifique-se de que as definições da firewall na máquina ou proxy permitem estes [URLs](backup-configure-vault.md#verify-internet-access) e [endereço IP](backup-configure-vault.md#verify-internet-access). <li>Se tiver software antivírus instalado no servidor, exclua os seguintes ficheiros da análise de software antivírus. <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionados com o .NET Framework). Há um CSC.exe para todas as versões do .NET que está instalada no servidor. Exclua ficheiros de CSC.exe que estão associados a todas as versões do .NET framework no servidor afetado. <li>Localização de pasta ou cache de rascunho. <br>*A localização predefinida para a pasta de rascunho ou o caminho de localização de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>A pasta bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao definir a chave de encriptação de cópias de segurança seguras

| Detalhes do erro | Causas possíveis | Ações recomendadas |
| ---     | ---     | ---    |
| **Error** <br />*Falha ao definir a chave de encriptação de cópias de segurança seguras. Ativação não foi possível ativar completamente, mas a frase de acesso de encriptação foi guardado o ficheiro seguinte*. |<li>O servidor já está registado com outro cofre.<li>Durante a configuração, a frase de acesso foi danificado.| Anular o registo do servidor do cofre e registe novamente com uma nova frase de acesso.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não foi concluída com êxito

| Detalhes do erro | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
|**Error** <br />*A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contacte o suporte da Microsoft*     | <li> A pasta de rascunho se encontra num volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente para outra localização. <li> O ficheiro de OnlineBackup.KEK está em falta.         | <li>Atualizar para o [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a localização de pasta ou cache de rascunho para um volume com espaço livre igual a 5 a 10% do tamanho total dos dados de cópia de segurança. Ao mover corretamente a localização da cache, consulte os passos em [perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o ficheiro de OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de rascunho ou o caminho de localização de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Frase de acesso de encriptação não corretamente configurado

| Detalhes do erro | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
|**Error** <br />*Erro 34506. A frase de acesso de encriptação armazenado neste computador não está configurada corretamente*.    | <li> A pasta de rascunho se encontra num volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente para outra localização. <li> O ficheiro de OnlineBackup.KEK está em falta.        | <li>Atualizar para o [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a pasta de rascunho ou a localização da cache para um volume com espaço livre equivalente aos 5 a 10% do tamanho total dos dados de cópia de segurança. Ao mover corretamente a localização da cache, consulte os passos em [perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o ficheiro de OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de rascunho ou o caminho de localização de cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Não executam cópias de segurança, de acordo com a agenda
Se as cópias de segurança agendadas não obter acionadas automaticamente, enquanto as cópias de segurança manuais funcionam sem problemas, tente as seguintes ações:

- Certifique-se a agenda de cópia de segurança do Windows Server não entram em conflito com a agenda de cópia de segurança do Azure de ficheiros e pastas.

- Certifique-se de que o estado de cópia de segurança Online está definido como **ativar**. Para verificar o estado de efetuar o abaixo:

  - Open **agendador de tarefas** e expanda **Microsoft**e selecione **cópia de segurança Online**.
  - Faça duplo clique em **Microsoft OnlineBackup**e vá para o **Acionadores** separador.
  - Verificar se o estado estiver definido como **ativado**. Se não for, em seguida, selecione **edite** > **ativado** caixa de verificação e clique em **OK**.

- Certifique-se de que a conta de utilizador selecionada para executar a tarefa está **SYSTEM** ou **Grupo Local de administradores** no servidor. Para verificar a conta de utilizador, vá para o **gerais** separador e verificar o **segurança** opções.

- Certifique-se de que o PowerShell 3.0 ou posterior está instalado no servidor. Para verificar a versão do PowerShell, execute o seguinte comando e certifique-se de que o *principais* número de versão é igual ou superior a 3.

  `$PSVersionTable.PSVersion`

- Certifique-se de que o caminho seguinte faz parte do *PSMODULEPATH* variável de ambiente

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a diretiva de execução do PowerShell para *LocalMachine* é definido para restrito, o cmdlet do PowerShell que aciona a tarefa de cópia de segurança poderá falhar. Execute os seguintes comandos no modo elevado, para verificar e defina a política de execução para qualquer um *Unrestricted* ou *RemoteSigned*

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Certifique-se de que existem não existem em falta ou danificado **PowerShell** módulo **MSonlineBackup**. Caso haja qualquer ficheiro em falta ou danificado, para resolver este problema, efetue o abaixo:

  - Em qualquer máquina, ter o agente de MARS que está a funcionar corretamente, copie a pasta de MSOnlineBackup partir *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* caminho.
  - Colar isto no computador problemático no mesmo caminho *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* .
  - Se **MSOnlineBackup** pasta já existe na máquina, cole ou substituir os ficheiros de conteúdo no interior do mesmo.


> [!TIP]
> Para garantir que as alterações são aplicadas de forma consistente, reinicie o servidor depois de efetuar os passos acima.


## <a name="troubleshoot-restore-issues"></a>Resolver problemas de restauro

Cópia de segurança do Azure com êxito não poderá montar o volume de recuperação, mesmo após alguns minutos. Também pode receber mensagens de erro durante o processo. Para começar a recuperar, normalmente, siga estes passos:

1.  Cancele o processo de montagem em curso, caso ele esteve em execução durante vários minutos.

2.  Veja se está na versão mais recente do agente do Backup. Para saber a versão no **ações** painel de consola do MARS, selecione **sobre o Microsoft Azure Recovery Services Agent**. Confirme que o **versão** número é igual ou superior à versão mencionada [este artigo](https://go.microsoft.com/fwlink/?linkid=229525). Pode transferir a versão mais recente [aqui](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Aceda a **Gestor de dispositivos** > **controladores de armazenamento**e localize **Iniciador do Microsoft iSCSI**. Se pode localizá-lo, vá diretamente para o passo 7.

4.  Se não conseguir localizar o serviço de Iniciador Microsoft iSCSI, tentar localizar uma entrada sob **Gestor de dispositivos** > **controladores de armazenamento** chamado **dispositivo desconhecido**, com o ID de Hardware **ROOT\ISCSIPRT**.

5.  Com o botão direito no **dispositivo desconhecido**e selecione **atualizar Driver**.

6.  Atualizar o driver, selecionando a opção para **pesquisar automaticamente software de driver atualizado**. Conclusão da atualização deve ser alterado **dispositivo desconhecido** ao **Iniciador do Microsoft iSCSI**, conforme mostrado abaixo.

    ![Captura de ecrã do Azure Backup Device Manager, com controladores de armazenamento realçados](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Aceda a **Gerenciador de tarefas** > **serviços (Local)**  > **serviço iniciador iSCSI da Microsoft**.

    ![Captura de ecrã do Azure Backup Gerenciador de tarefas, com os serviços (Local) realçados](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Reinicie o serviço Iniciador do iSCSI da Microsoft. Para tal, clique com botão direito no serviço, selecione **parar**, clique com botão direito novamente e selecione **iniciar**.

9.  Repita a recuperação utilizando [ **restaurar instantâneas**](backup-instant-restore-capability.md).

Se a recuperação continuar a falhar, reinicie o seu servidor ou cliente. Se não deseja reiniciar ou a recuperação continuar a falhar, mesmo após o reinício do servidor, tente recuperar a partir de uma máquina alternativa. Siga os passos em [este artigo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte
Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes
* Obter mais detalhes sobre [a efetuar cópias de segurança do Windows Server com o Azure Backup Agent](tutorial-backup-windows-server-to-azure.md).
* Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
