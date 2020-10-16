---
title: Resolução de problemas do agente reserva da Azure
description: Neste artigo, aprenda a resolver problemas na instalação e registo do agente Azure Backup.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 2e2e807a8b849af435fe82d54bbfdd96b729fa38
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091462"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Resolução de problemas do agente Microsoft Azure Recovery Services (MARS)

Este artigo descreve como resolver erros que pode ver durante a configuração, registo, cópia de segurança e restauro.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que verifique o seguinte antes de começar a resolver problemas à Microsoft o agente Azure Recovery Services (MARS):

- [Certifique-se de que o agente MARS está atualizado.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que tem conectividade de rede entre o agente MARS e o Azure.](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que o MARS está a funcionar (na consola de serviço). Se precisar, reinicie e relemque a operação.
- [Certifique-se de que o espaço de volume gratuito de 5% a 10% está disponível no local da pasta de risco](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Verifique se outro processo ou software antivírus está a interferir com o Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Se o trabalho de backup concluído com avisos, consulte [trabalhos de backup concluídos com aviso](#backup-jobs-completed-with-warning)
- Se a cópia de segurança programada falhar, mas a cópia de segurança manual funcionar, ver [cópias de segurança não funcionam de acordo com o horário](#backups-dont-run-according-to-schedule).
- Certifique-se de que o seu SO tem as últimas atualizações.
- [Certifique-se de que as unidades e ficheiros não suportados com atributos não suportados estão excluídos da cópia de segurança](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Certifique-se de que o relógio do sistema protegido está configurado para o fuso horário correto.
- [Certifique-se de que o quadro .NET 4.5.2 ou posteriormente é instalado no servidor](https://www.microsoft.com/download/details.aspx?id=30653).
- Se estiver a tentar reregistar o servidor para um cofre:
  - Certifique-se de que o agente está desinstalado no servidor e que é eliminado do portal.
  - Use a mesma frase que foi inicialmente usada para registar o servidor.
- Para cópias de segurança offline, certifique-se de que o Azure PowerShell 3.7.0 está instalado tanto na fonte como no computador de cópia antes de iniciar a cópia de segurança.
- Se o agente de reserva estiver a funcionar numa máquina virtual Azure, consulte [este artigo](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

**Mensagem de erro**: Credenciais de cofre inválidas fornecidas. O ficheiro está danificado ou não tem as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)

| Causa | Ações recomendadas |
| ---     | ---    |
| **As credenciais do cofre não são válidas.** <br/> <br/> Os ficheiros de credencial do cofre podem ser corruptos ou podem ter expirado. (Por exemplo, podem ter sido descarregados mais de 48 horas antes da hora de inscrição.)| [Descarregue novas credenciais](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) do cofre dos Serviços de Recuperação no portal Azure. Em seguida, tome estas medidas, conforme apropriado: <ul><li> Se já instalou e registou o MARS, abra a consola MMC do Agente de Backup do Microsoft Azure. Em seguida, **selecione 'Registar Servidor'** no painel **de Ações** para completar o registo com as novas credenciais. <br/> <li> Se a nova instalação falhar, tente reinstalar-se com as novas credenciais.</ul> **Nota:** Se vários ficheiros de credenciais de abóbada tiverem sido descarregados, apenas o ficheiro mais recente é válido durante as próximas 48 horas. Recomendamos que descarregue um novo ficheiro de credencial de cofre.
| **O servidor/firewall proxy está a bloquear o registo** <br/>ou <br/>**Sem conectividade na Internet** <br/><br/> Se a sua máquina ou servidor proxy tiver uma conectividade limitada na Internet e não garantir o acesso aos URLs necessários, o registo falhará.| Tome estes passos:<br/> <ul><li> Trabalhe com a sua equipa de TI para garantir que o sistema tem conectividade com a Internet.<li> Se não tiver um servidor proxy, certifique-se de que a opção de procuração não é selecionada quando regista o agente. [Verifique as definições de procuração](#verifying-proxy-settings-for-windows).<li> Se tiver um servidor de firewall/proxy, trabalhe com a sua equipa de networking para garantir que estes URLs e endereços IP tenham acesso:<br/> <br> **URLs**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Endereços IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Tente registar-se novamente depois de completar os passos anteriores de resolução de problemas.<br></br> Se a sua ligação for via Azure ExpressRoute, certifique-se de que as definições estão configuradas como descrito no [suporte Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **O software antivírus está a bloquear o registo** | Se tiver software antivírus instalado no servidor, adicione as regras de exclusão necessárias à verificação antivírus para estes ficheiros e pastas: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> A pasta de risco. A sua localização predefinida é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A pasta do caixote do lixo em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Recomendações adicionais

- Vá a C:/Windows/Temp e verifique se existem mais de 60.000 ou 65.000 ficheiros com a extensão .tmp. Se houver, elimine estes ficheiros.
- Certifique-se de que a data e a hora da máquina coincidem com o fuso horário local.
- Certifique-se de que [estes sites](install-mars-agent.md#verify-internet-access) são adicionados aos seus sites fidedignos no Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verificação das definições de procuração para windows

1. Baixe o PsExec a partir da página [Sysinternals.](/sysinternals/downloads/psexec)
1. Fugir `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` de um pedido de comando elevado.

   Este comando abrirá o Internet Explorer.
1. Ir **Tools**a  >  **ferramentas opções**  >  **internet Configurações**LAN  >  **.**
1. Verifique as definições de procuração para a conta do sistema.
1. Se não houver procuração configurada e forem fornecidos detalhes de procuração, remova os detalhes.
1. Se um representante estiver configurado e os detalhes do proxy estiverem incorretos, certifique-se de que os detalhes do **Proxy IP** e **do Porto** estão corretos.
1. Feche o Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Incapaz de descarregar ficheiro credencial de abóbada

| Erro   | Ações recomendadas |
| ---     | ---    |
|Falhou em descarregar o ficheiro de credencial do cofre. (ID: 403) | <ul><li> Tente descarregar as credenciais do cofre usando um navegador diferente, ou tome estes passos: <ul><li> Inicie o Internet Explorer. Selecione F12. </li><li> Vá ao separador **'Rede'** e limpe a cache e os cookies. </li> <li> Atualize a página.<br></li></ul> <li> Verifique se a subscrição está desativada/expirada.<br></li> <li> Verifique se alguma regra de firewall está a bloquear o download. <br></li> <li> Certifique-se de que não esgotou o limite do cofre (50 máquinas por abóbada).<br></li>  <li> Certifique-se de que o utilizador tem as permissões de Backup Azure que são necessárias para descarregar credenciais de cofre e registar um servidor com o cofre. Consulte [o controlo de acesso baseado em funções do Azure para gerir os pontos de recuperação do Azure Backup](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente do Serviço de Recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup

| Erro  | Causa possível | Ações recomendadas |
| ---     | ---     | ---    |
| <br /><ul><li>O Agente de Serviço de Recuperação do Microsoft Azure não conseguiu ligar-se ao Microsoft Azure Backup. (ID: 100050) Verifique as definições da sua rede e certifique-se de que é capaz de se ligar à internet.<li>(407) Autenticação por procuração necessária. |Um representante está a bloquear a ligação. |  <ul><li>No Internet Explorer, aceda às opções **de Internet tools**  >  **Internet options**  >  **Security**  >  **Internet.** Selecione **Custom Level** e desloque-se para baixo para a secção **de descarregamento de ficheiros.** Selecione **Ativar**.<p>Também poderá ter de adicionar [URLs e endereços IP](install-mars-agent.md#verify-internet-access) aos seus sites fidedignos no Internet Explorer.<li>Altere as definições para utilizar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<li> Se a sua máquina tiver acesso limitado à Internet, certifique-se de que as definições de firewall na máquina ou procuração permitem estes [urls e endereços IP](install-mars-agent.md#verify-internet-access). <li>Se tiver software antivírus instalado no servidor, exclua estes ficheiros da varredura antivírus: <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionado com o Quadro .NET). Há um CSC.exe para cada versão .NET Framework instalada no servidor. Exclua CSC.exe ficheiros para todas as versões do Quadro .NET no servidor afetado. <li>A pasta de risco ou a localização do cache. <br>A localização predefinida para a pasta de risco ou para o caminho da cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A pasta do caixote do lixo em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>O ficheiro de credencial de abóbada especificado não pode ser utilizado, uma vez que não é descarregado do cofre associado a este servidor

| Erro  | Causa possível | Ações recomendadas |
| ---     | ---     | ---    |
| O ficheiro de credencial de abóbada especificado não pode ser utilizado, uma vez que não é descarregado do cofre associado a este servidor. (ID: 100110) Por favor, forneça credenciais de cofre apropriadas. | O ficheiro de credencial do cofre é de um cofre diferente daquele em que este servidor já está registado. | Certifique-se de que a máquina-alvo e a máquina de origem estão registadas no mesmo cofre dos Serviços de Recuperação. Se o servidor alvo já estiver registado num cofre diferente, utilize a opção **'Registo Servidor'** para registar-se no cofre correto.  

## <a name="backup-jobs-completed-with-warning"></a>Trabalhos de backup concluídos com aviso

- Quando o agente MARS iteração sobre ficheiros e pastas durante a cópia de segurança, pode encontrar várias condições que podem fazer com que a cópia de segurança seja marcada como completada com avisos. Nestas condições, um trabalho mostra como preenchido com avisos. Tudo bem, mas significa que pelo menos um ficheiro não foi capaz de ser apoiado. Então o trabalho ignorou o ficheiro, mas ressalveu todos os outros ficheiros em questão na fonte de dados.

  ![Trabalho de reserva concluído com avisos](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- As condições que podem fazer com que as cópias de segurança ignorem ficheiros incluem:
  - Atributos de ficheiro não suportados (por exemplo: numa pasta OneDrive, fluxo comprimido, pontos de reparase). Para a lista completa, consulte a [matriz de suporte.](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)
  - Um problema do sistema de ficheiros
  - Outro processo que interfere (por exemplo: o software antivírus que segura as alças em ficheiros pode impedir o agente MARS de aceder aos ficheiros)
  - Ficheiros bloqueados por uma aplicação  

- O serviço de cópia de segurança marcará estes ficheiros como falhados no ficheiro de registo, com a seguinte convenção de nomeação: *LastBackupFailedFilesxxxx.txt* ao abrigo da pasta *C:\Program Files\Microsoft Azure Recovery Service Agent\temp.*
- Para resolver o problema, reveja o ficheiro de registo para entender a natureza da questão:

  | Código de erro             | Razões                                             | Recomendações                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | O ficheiro ou diretório é corrompido e ilegível. | Executar **chkdsk** no volume de origem.                             |
  | 0x80070002, 0x80070003 | O sistema não consegue encontrar o ficheiro especificado.         | [Certifique-se de que a pasta de risco não está cheia](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  Verifique se existe o volume onde o espaço de risco está configurado (não eliminado)  <br><br>   [Certifique-se de que o agente MARS está excluído do antivírus instalado na máquina](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Acesso é negado                                    | [Verifique se o antivírus ou outro software de terceiros está a bloquear o acesso](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | O acesso ao ficheiro em nuvem é negado.                | Ficheiros OneDrive, Ficheiros Git ou quaisquer outros ficheiros que possam estar em estado offline na máquina |

- Pode utilizar [as regras de Exclusão adicionais à política existente](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) para excluir ficheiros não suportados, em falta ou eliminados da sua política de backup para garantir cópias de segurança bem sucedidas.

- Evite eliminar e recriar pastas protegidas com os mesmos nomes na pasta de nível superior. Ao fazê-lo, pode resultar na conclusão da cópia de segurança com avisos com o erro *Uma inconsistência crítica foi detetada, pelo que as alterações não podem ser replicadas.*  Se precisar de eliminar e recriar pastas, considere fazê-lo em sub-dobradeiras sob a pasta de nível superior protegida.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao definir a chave de encriptação de cópias de segurança seguras

| Erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| <br />Não conseguiu definir a chave de encriptação para cópias de segurança seguras. A ativação não foi completamente bem sucedida, mas a palavra-passe de encriptação foi guardada para o seguinte ficheiro. |<li>O servidor já está registado com outro cofre.<li>Durante a configuração, a palavra-passe foi corrompida.| Desagregar o servidor do cofre e registá-lo novamente com uma nova frase-passe.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não completou com sucesso

| Erro  | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
|<br />A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contacte o Suporte da Microsoft.     | <li> A pasta de risco está localizada num volume que não tem espaço suficiente. <li> A pasta de risco foi movida incorretamente. <li> O ficheiro OnlineBackup.KEK desapareceu.         | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a pasta de risco ou a localização do cache para um volume com espaço livre que seja entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente a localização da cache, consulte os passos em [Questões Comuns sobre o backup de ficheiros e pastas](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder).<li> Certifique-se de que o ficheiro OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de risco ou para o caminho da cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Encryption passphrase not correctly configured (A frase de acesso de encriptação não foi configurada corretamente)

| Erro  | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
| <br />Erro 34506. A palavra-passe de encriptação armazenada neste computador não está corretamente configurada.    | <li> A pasta de risco está localizada num volume que não tem espaço suficiente. <li> A pasta de risco foi movida incorretamente. <li> O ficheiro OnlineBackup.KEK desapareceu.        | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do Agente MARS.<li>Mova a pasta de risco ou a localização do cache para um volume com espaço livre que seja entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente a localização da cache, consulte os passos em [Questões Comuns sobre o backup de ficheiros e pastas](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder).<li> Certifique-se de que o ficheiro OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de risco ou para o caminho da cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Backups não funcionam de acordo com o horário

Se as cópias de segurança programadas não forem acionadas automaticamente, mas as cópias de segurança manuais funcionarem corretamente, experimente as seguintes ações:

- Certifique-se de que a agenda de backup do Windows Server não entra em conflito com o horário de backup dos ficheiros E das pastas Azure.

- Certifique-se de que o estado de backup on-line está definido para **Ativar**. Para verificar o estado, tome estas medidas:

  1. No Task Scheduler, expanda a **Microsoft** e selecione **Backup Online**.
  1. Clique duas vezes no **Microsoft-OnlineBackup** e vá para o **separador Triggers.**
  1. Verifique se o estado está definido para **Ativado**. Se não for, **selecione Editar**, selecione **Ativado**e, em seguida, selecione **OK**.

- Certifique-se de que a conta de utilizador selecionada para executar a tarefa é o grupo **de administradores** **locais** ou SYSTEM no servidor. Para verificar a conta de utilizador, vá ao separador **Geral** e verifique as opções **de Segurança.**

- Certifique-se de que o PowerShell 3.0 ou posteriormente é instalado no servidor. Para verificar a versão PowerShell, execute este comando e verifique se o número da `Major` versão é 3 ou mais tarde:

  `$PSVersionTable.PSVersion`

- Certifique-se de que este caminho faz parte da `PSMODULEPATH` variável ambiental:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a política de execução powerShell `LocalMachine` for definida para , o `restricted` cmdlet PowerShell que desencadeia a tarefa de backup pode falhar. Executar estes comandos em modo elevado para verificar e definir a política de execução para qualquer um `Unrestricted` ou `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Certifique-se de que não faltam ou corrompem ficheiros DO módulo POWERShell MSOnlineBackup. Se houver algum ficheiro em falta ou corrupto, tome estes passos:

  1. De qualquer máquina que tenha um agente MARS que esteja a funcionar corretamente, copie a pasta MSOnlineBackup de C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na máquina problemática, cole os ficheiros copiados no mesmo local da pasta (C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se já existir uma pasta MSOnlineBackup na máquina, cole os ficheiros na máquina ou substitua quaisquer ficheiros existentes.

> [!TIP]
> Para garantir que as alterações são aplicadas de forma consistente, reinicie o servidor após a realização dos passos anteriores.

## <a name="resource-not-provisioned-in-service-stamp"></a>Recurso não aprovisionado em carimbo de serviço

Erro | Possíveis causas | Ações recomendadas
--- | --- | ---
A operação em curso falhou devido a um erro de serviço interno "Recurso não aprovisionado em carimbo de serviço". Por favor, recaia a operação depois de algum tempo. (ID: 230006) | O servidor protegido foi renomeado. | <li> Mude o nome do servidor para o nome original registado no cofre. <br> <li> Re-registrar o servidor para o cofre com o novo nome.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>O trabalho não podia ser iniciado como outro trabalho estava em andamento

Se notar uma mensagem de aviso na **consola MARS**História do  >  **trabalho**, dizendo que "Job não poderia ser iniciado como outro trabalho estava em andamento", então isso pode ser devido a uma instância duplicada do trabalho desencadeado pelo Agendador de Tarefas.

![O trabalho não podia ser iniciado como outro trabalho estava em andamento](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Para resolver este problema:

1. Lançar o snap-in do Agendador de Tarefas digitando *taskschd.msc* na janela Run
1. No painel esquerdo, navegue para a **Biblioteca de Agendadores de**  ->  **Tarefas Microsoft**  ->  **OnlineBackup**.
1. Para cada tarefa nesta biblioteca, clique duas vezes na tarefa de abrir propriedades e executar os seguintes passos:
    1. Mudar para o separador **Definições**.

         ![Separador de definições](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Altere a opção para **Se a tarefa já estiver em execução, então aplica-se a seguinte regra**. Escolha **Não iniciar uma nova instância**.

         ![Alterar a regra para não iniciar uma nova instância](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Resolução de problemas restabelece problemas

A Azure Backup pode não montar com sucesso o volume de recuperação, mesmo após vários minutos. E pode receber mensagens de erro durante o processo. Para começar a recuperar normalmente, tome estes passos:

1. Cancele o processo de montagem se estiver em funcionamento há vários minutos.

2. Verifique se tem a versão mais recente do agente de reserva. Para verificar a versão, no painel **de Ações** da consola MARS, selecione Sobre o **Agente de Serviços de Recuperação do Microsoft Azure**. Confirme que o número **da versão** é igual ou superior à versão mencionada neste [artigo](https://go.microsoft.com/fwlink/?linkid=229525). Selecione este link para [descarregar a versão mais recente.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. Aceda aos controladores de armazenamento **do Gestor de**  >  **Dispositivos** e localize **o Iniciador Microsoft iSCSI**. Se o localizarem, vão diretamente para o passo 7.

4. Se não conseguir localizar o serviço de iniciador Microsoft iSCSI, tente encontrar uma entrada sob controladores de armazenamento **do Gestor de**  >  **Dispositivos denominados** **Dispositivo Desconhecido** com Hardware ID **ROOT\ISCSIPRT**.

5. Clique no dispositivo **desconhecido** com direito e selecione **'Atualizar o Software do Controlador'**( Update Driver Software).

6. Atualize o controlador selecionando automaticamente a opção de **Procurar para obter software de controlador atualizado.** Esta atualização deve alterar **o Dispositivo Desconhecido** para o **Iniciador microsoft iSCSI**:

    ![Screenshot do Azure Backup Device Manager, com controladores de armazenamento em destaque](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Vá aos Serviços **de Gestor de**  >  **Tarefas (Local)**  >  **Microsoft iSCSI Initiator Service**:

    ![Screenshot do Gestor de Tarefas de Backup Azure, com serviços (Locais) em destaque](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Reinicie o serviço de iniciador Microsoft iSCSI. Para isso, clique com o botão direito no serviço e **selecione Stop**. Em seguida, clique com o botão direito novamente e **selecione Iniciar**.

9. Relemcando a recuperação utilizando [o Instant Restore](backup-instant-restore-capability.md).

Se a recuperação ainda falhar, reinicie o seu servidor ou cliente. Se não quiser reiniciar, ou se a recuperação ainda falhar mesmo depois de reiniciar o servidor, tente [recuperar de outra máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Problemas de Cache resolução de problemas

A operação de backup pode falhar se a pasta de cache (também designada como pasta de risco) estiver incorretamente configurada, em falta de pré-requisitos ou tiver acesso restrito.

### <a name="prerequisites"></a>Pré-requisitos

Para que as operações do agente MARS tenham sucesso, a pasta cache tem de cumprir os seguintes requisitos:

- [Certifique-se de que o espaço de volume gratuito de 5% a 10% está disponível na localização da pasta de risco](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Certifique-se de que a localização da pasta de risco é válida e acessível](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Certifique-se de que os atributos de ficheiro na pasta cache são suportados](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Certifique-se de que o espaço de armazenamento de cópias de sombra atribuído é suficiente para o processo de backup](#increase-shadow-copy-storage)
- [Certifique-se de que não existem outros processos (ex. software antivírus) que restringem o acesso à pasta cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumentar o armazenamento de cópias de sombra

As operações de backup podem falhar se não houver espaço suficiente de armazenamento de cópias de sombra que seja necessário para proteger a fonte de dados. Para resolver este problema, aumente o espaço de armazenamento de cópias de sombra no volume protegido utilizando **vssadmin** como mostrado abaixo:

- Verifique o espaço de armazenamento de sombras atual a partir da solicitação de comando elevada:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumente o espaço de armazenamento de sombras utilizando o seguinte comando:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Outro processo ou software antivírus que bloqueia o acesso à pasta cache

Se tiver software antivírus instalado no servidor, adicione as regras de exclusão necessárias à verificação antivírus para estes ficheiros e pastas:  

- A pasta de risco. A sua localização padrão é `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- A pasta do caixote do lixo `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Problemas comuns

Esta secção cobre os erros comuns que encontra durante a utilização do agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Mensagem de erro | Ação recomendada
--|--
O Agente dos Serviços de Recuperação do Microsoft Azure não conseguiu aceder à soma de verificação da cópia de segurança armazenada no local de rascunho | Para resolver este problema, execute os seguintes passos e reinicie o servidor <br/> - [Verifique se existe um antivírus ou outros processos que bloqueiem os ficheiros de localização do risco](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se a localização do risco é válida e acessível ao agente MARS.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Mensagem de erro | Ação recomendada
--|--
O Agente dos Serviços de Recuperação do Microsoft Azure não conseguiu aceder ao local de rascunho para inicializar o VHD | Para resolver este problema, execute os seguintes passos e reinicie o servidor <br/> - [Verifique se antivírus ou outros processos estão bloqueando os ficheiros de localização do risco](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se a localização do risco é válida e acessível ao agente MARS.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Mensagem de erro | Ação recomendada
--|--
A cópia de segurança falhou devido a um armazenamento insuficiente no volume onde está localizada a pasta de risco | Para resolver este problema, verifique os seguintes passos e retentou a operação:<br/>- [Certifique-se de que o agente MARS é o mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verifique e resolva problemas de armazenamento que impactem o espaço de risco de backup](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Mensagem de erro | Ação recomendada
--|--
Não é possível encontrar alterações num ficheiro. Tal pode dever-se a vários motivos. Repita a operação | Para resolver este problema, verifique os seguintes passos e retentou a operação:<br/> - [Certifique-se de que o agente MARS é o mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verifique e resolva problemas de armazenamento que impactem o espaço de risco de backup](#prerequisites)

## <a name="next-steps"></a>Passos seguintes

- Obtenha mais detalhes sobre como fazer backup do [Windows Server com o agente Azure Backup](tutorial-backup-windows-server-to-azure.md).
- Se precisar de restaurar uma cópia de segurança, consulte [restaurar os ficheiros numa máquina Do Windows](backup-azure-restore-windows-server.md).
