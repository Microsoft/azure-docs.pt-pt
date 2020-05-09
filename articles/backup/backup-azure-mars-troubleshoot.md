---
title: Problemas de resolução do agente de reserva Azure
description: Neste artigo, aprenda a resolver a instalação e o registo do agente de backup Azure.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 1d1397519b39ffbc439cdd0d3e78d9b553ea302e
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598016"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Problemas de resolução do agente da Microsoft Azure Recovery Services (MARS)

Este artigo descreve como resolver erros que pode ver durante a configuração, registo, cópia de segurança e restauro.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que verifique o seguinte antes de começar a resolver problemas na Microsoft, o agente dos Serviços de Recuperação Do Azure (MARS):

- [Certifique-se de que o agente MARS está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Certifique-se de que tem conectividade](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)de rede entre o agente MARS e o Azure .
- Certifique-se de que o MARS está a funcionar (na consola de serviço). Se for preciso, reinicie e tente novamente a operação.
- Certifique-se de que o espaço de volume gratuito de [5% a 10% está disponível na localização da pasta de risco](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Verifique se outro software de processo ou antivírus está a interferir com o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Se o trabalho de reserva concluído com avisos, consulte [Backup Jobs Completed with Warning](#backup-jobs-completed-with-warning)
- Se a cópia de segurança programada falhar, mas o backup manual funciona, veja que [as cópias](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)de segurança não funcionam de acordo com o horário .
- Certifique-se de que o seu SISTEMA tem as últimas atualizações.
- [Certifique-se de que as unidades e ficheiros não suportados com atributos não suportados estão excluídos da cópia de segurança](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Certifique-se de que o relógio do sistema protegido está configurado para o fuso horário correto.
- Certifique-se de que [a estrutura 4.5.2 ou posterior está instalada no servidor](https://www.microsoft.com/download/details.aspx?id=30653).
- Se está a tentar reregistar o seu servidor num cofre:
  - Certifique-se de que o agente está desinstalado no servidor e que é apagado do portal.
  - Utilize a mesma frase-passe que foi inicialmente usada para registar o servidor.
- Para cópias de segurança offline, certifique-se de que o Azure PowerShell 3.7.0 está instalado tanto na fonte como no computador de cópia antes de iniciar a cópia.
- Se o agente de reserva estiver a funcionar numa máquina virtual Azure, consulte [este artigo](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

**Mensagem de erro**: Credenciais de cofre inválidas fornecidas. O ficheiro está danificado ou não tem as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)

| Causa | Ações recomendadas |
| ---     | ---    |
| **Credenciais de cofre não são válidas** <br/> <br/> Ficheiros credenciais do cofre podem ser corruptos ou podem ter expirado. (Por exemplo, podem ter sido descarregados mais de 48 horas antes da hora do registo.)| Descarregue novas credenciais do cofre dos Serviços de Recuperação no portal Azure. (Ver passo 6 na secção [de descarregar a](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) secção de agente MARS.) Em seguida, tome estas medidas, conforme apropriado: <ul><li> Se já instalou e registou o MARS, abra a consola MMC do Agente de Backup Microsoft Azure e, em seguida, selecione **Register Server** no painel **Deações** para completar o registo com as novas credenciais. <br/> <li> Se a nova instalação falhar, tente reinstalar com as novas credenciais.</ul> **Nota:** Se vários ficheiros credenciais de cofre tiverem sido descarregados, apenas o ficheiro mais recente é válido para as próximas 48 horas. Recomendamos que descarregue um novo ficheiro credencial de cofre.
| **Proxy servidor/firewall está bloqueando o registo** <br/>ou <br/>**Sem conectividade de internet** <br/><br/> Se a sua máquina ou servidor proxy tiver uma conectividade limitada à Internet e não garantir o acesso aos URLs necessários, o registo falhará.| Tome estes passos:<br/> <ul><li> Trabalhe com a sua equipa de TI para garantir que o sistema tem conectividade com a Internet.<li> Se não tiver um servidor proxy, certifique-se de que a opção proxy não é selecionada quando registar o agente. [Verifique as definições de procuração](#verifying-proxy-settings-for-windows).<li> Se tiver um servidor de firewall/proxy, trabalhe com a sua equipa de networking para garantir que estes URLs e endereços IP tenham acesso:<br/> <br> **URLs**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Endereços IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Tente registar-se novamente depois de completar os passos anteriores de resolução de problemas.<br></br> Se a sua ligação for via Azure ExpressRoute, certifique-se de que as definições estão configuradas conforme descrito no [suporte Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **O software antivírus está a bloquear o registo** | Se tiver software antivírus instalado no servidor, adicione as regras de exclusão necessárias à varredura antivírus para estes ficheiros e pastas: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> A pasta de risco. A sua localização padrão é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A pasta do caixote do lixo no C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Recomendações adicionais

- Vá a C:/Windows/Temp e verifique se existem mais de 60.000 ou 65.000 ficheiros com a extensão .tmp. Se houver, elimine estes ficheiros.
- Certifique-se de que a data e a hora da máquina correspondem ao fuso horário local.
- Certifique-se de que [estes sites](install-mars-agent.md#verify-internet-access) são adicionados aos seus sites fidedignos no Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verificação de definições de procuração para Windows

1. Baixe o PsExec a partir da página [Sysinternals.](https://docs.microsoft.com/sysinternals/downloads/psexec)
1. Corra `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` de um pedido de comando elevado.

   Este comando abrirá o Internet Explorer.
1. Ir para **ferramentas** > **opções** > de Internet**Configurações** > **LAN**.
1. Verifique as definições de procuração para a conta do sistema.
1. Se não houver procuração configurada e forem fornecidos detalhes de procuração, remova os detalhes.
1. Se um proxy estiver configurado e os detalhes do proxy estiverem incorretos, certifique-se de que os detalhes do **PROXY IP** e **da Porta** estão corretos.
1. Feche o Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Incapaz de descarregar ficheiro credencial de cofre

| Erro   | Ações recomendadas |
| ---     | ---    |
|Falhou em descarregar o ficheiro credencial do cofre. (ID: 403) | <ul><li> Tente descarregar as credenciais do cofre usando um navegador diferente, ou tome estes passos: <ul><li> Inicie o Internet Explorer. Selecione F12. </li><li> Vá ao separador **Rede** e limpe a cache e os cookies. </li> <li> Atualize a página.<br></li></ul> <li> Verifique se a subscrição está desativada/expirada.<br></li> <li> Verifique se alguma regra de firewall está bloqueando o download. <br></li> <li> Certifique-se de que não esgotou o limite do cofre (50 máquinas por cofre).<br></li>  <li> Certifique-se de que o utilizador tem as permissões de Backup Azure que são necessárias para descarregar credenciais de cofre e registar um servidor com o cofre. Consulte [o Controlo de Acesso baseado em funções para gerir pontos](backup-rbac-rs-vault.md)de recuperação de backup azure .</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente do Serviço de Recuperação do Microsoft Azure não conseguiu ligar ao Microsoft Azure Backup

| Erro  | Causa possível | Ações recomendadas |
| ---     | ---     | ---    |
| <br /><ul><li>O Agente de Serviço de Recuperação do Microsoft Azure não conseguiu ligar-se ao Microsoft Azure Backup. (ID: 100050) Verifique as definições da sua rede e certifique-se de que é capaz de se ligar à internet.<li>(407) Autenticação por procuração necessária. |Um representante está a bloquear a ligação. |  <ul><li>No Internet Explorer, vá a **Tools** > **Internet options** > **Internet** > **Internet**. Selecione **Custom Level** e desça até a secção de **descarregamento** de ficheiros. Selecione **Ativar**.<p>Também pode ter de adicionar [URLs e endereços IP](install-mars-agent.md#verify-internet-access) aos seus sites fidedignos no Internet Explorer.<li>Altere as definições para utilizar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<li> Se a sua máquina tiver acesso limitado à Internet, certifique-se de que as definições de firewall na máquina ou proxy permitem estes [URLs e endereços IP](install-mars-agent.md#verify-internet-access). <li>Se tiver software antivírus instalado no servidor, exclua estes ficheiros da varredura antivírus: <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionado com o .NET Framework). Há um CSC.exe para cada versão .NET Framework instalada no servidor. Exclua ficheiros CSC.exe para todas as versões da .NET Framework no servidor afetado. <li>A pasta de risco ou a localização da cache. <br>A localização predefinida para a pasta de risco ou o caminho cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A pasta do caixote do lixo no C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="backup-jobs-completed-with-warning"></a>Trabalhos de backup concluídos com aviso

- Quando o agente MARS iterates sobre ficheiros e pastas durante a cópia de segurança, pode encontrar várias condições que podem fazer com que a cópia de segurança seja marcada como completada com avisos. Nestas condições, um trabalho mostra-se como concluído com avisos. Tudo bem, mas significa que pelo menos um ficheiro não foi capaz de ser apoiado. Então o trabalho ignorou o ficheiro, mas apoiou todos os outros ficheiros em questão na fonte de dados.

  ![Trabalho de backup concluído com avisos](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- As condições que podem fazer com que as cópias de segurança saltem ficheiros incluem:
  - Atributos de ficheiro não suportados (por exemplo: numa pasta OneDrive, fluxo comprimido, pontos de reparse). Para a lista completa, consulte a [matriz](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#supported-file-types-for-backup)de suporte .
  - Um problema no sistema de ficheiros
  - Outro processo de interferência (por exemplo: software antivírus que mantém as pegas nos ficheiros pode impedir o agente MARS de aceder aos ficheiros)
  - Ficheiros bloqueados por uma aplicação  

- O serviço de backup marcará estes ficheiros como falhados no ficheiro de registo, com a seguinte convenção de nomeação: *LastBackupFailedFilesxxxx.txt* sob a pasta *C:\Program Files\Microsoft Azure Recovery Service Agent\temp.*
- Para resolver o problema, reveja o ficheiro de registo para compreender a natureza da questão:

  | Código de erro             | Razões                                             | Recomendações                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | O ficheiro ou diretório é corrompido e ilegível. | Executar **chkdsk** no volume de origem.                             |
  | 0x80070002, 0x80070003 | O sistema não encontra o ficheiro especificado.         | [Certifique-se de que a pasta de risco não está cheia](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)  <br><br>  Verifique se o volume onde o espaço de risco está configurado existe (não eliminado)  <br><br>   [Certifique-se de que o agente MARS está excluído do antivírus instalado na máquina](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | O acesso é negado                                    | [Verifique se o antivírus ou outro software de terceiros está bloqueando o acesso](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | O acesso ao ficheiro da nuvem é negado.                | Ficheiros OneDrive, Ficheiros Git ou quaisquer outros ficheiros que possam estar offline na máquina |

- Pode utilizar [regras de Exclusão adicionais à política existente](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#add-exclusion-rules-to-existing-policy) para excluir ficheiros não suportados, desaparecidos ou eliminados da sua política de backup para garantir cópias de segurança bem sucedidas.

- Evite eliminar e recriar pastas protegidas com os mesmos nomes na pasta de alto nível. Ao fazê-lo, poderia resultar na conclusão do backup com avisos com o erro *Foi detetada uma inconsistência crítica, pelo que as alterações não podem ser replicadas.*  Se precisar de eliminar e recriar pastas, considere fazê-lo em subpastas sob a pasta de nível superior protegida.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao definir a chave de encriptação de cópias de segurança seguras

| Erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| <br />Falhou em definir a chave de encriptação para cópias de segurança seguras. A ativação não conseguiu completamente, mas a frase-passe de encriptação foi guardada no ficheiro seguinte. |<li>O servidor já está registado com outro cofre.<li>Durante a configuração, a frase-passe foi corrompida.| Desmarque o servidor do cofre e registe-o novamente com uma nova frase de passe.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não completou com sucesso

| Erro  | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
|<br />A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contacte o Suporte da Microsoft.     | <li> A pasta de risco está localizada num volume que não tem espaço suficiente. <li> A pasta de risco foi incorretamente movida. <li> O ficheiro OnlineBackup.KEK está desaparecido.         | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a pasta de risco ou a localização cache para um volume com espaço livre que esteja entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente a localização da cache, consulte os passos em [questões comuns sobre o backup de ficheiros e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o ficheiro OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de risco ou o caminho cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Encryption passphrase not correctly configured (A frase de acesso de encriptação não foi configurada corretamente)

| Erro  | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
| <br />Erro 34506. A palavra-passe de encriptação armazenada neste computador não está corretamente configurada.    | <li> A pasta de risco está localizada num volume que não tem espaço suficiente. <li> A pasta de risco foi incorretamente movida. <li> O ficheiro OnlineBackup.KEK está desaparecido.        | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do Agente MARS.<li>Mova a pasta de risco ou a localização cache para um volume com espaço livre que esteja entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente a localização da cache, consulte os passos em [questões comuns sobre o backup de ficheiros e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o ficheiro OnlineBackup.KEK está presente. <br>*A localização predefinida para a pasta de risco ou o caminho cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Os backups não funcionam de acordo com o horário

Se as cópias de segurança programadas não forem acionadas automaticamente, mas as cópias de segurança manuais funcionam corretamente, tente as seguintes ações:

- Certifique-se de que o calendário de cópias de segurança do Windows Server não entra em conflito com o calendário de cópias de segurança dos ficheiros E pastas Do IA.

- Certifique-se de que o estado de backup on-line está definido para **ativar**. Para verificar o estado, tome estas medidas:

  1. No Programador de Tarefas, expanda a **Microsoft** e selecione **Backup Online**.
  1. Clique duas vezes no **Microsoft-OnlineBackup** e vá ao separador **Triggers.**
  1. Verifique se o estado está definido para **Ativado**. Se não for, **selecione Editar**, selecione **Ativado**e, em seguida, selecione **OK**.

- Certifique-se de que a conta de utilizador selecionada para executar a tarefa é **sistema** ou grupo de **administradores locais** no servidor. Para verificar a conta de utilizador, vá ao separador **Geral** e verifique as opções de **Segurança.**

- Certifique-se de que o PowerShell 3.0 ou mais tarde está instalado no servidor. Para verificar a versão PowerShell, execute `Major` este comando e verifique se o número da versão é 3 ou mais tarde:

  `$PSVersionTable.PSVersion`

- Certifique-se de que `PSMODULEPATH` este caminho faz parte da variável ambiental:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a política de `LocalMachine` execução `restricted`powerShell estiver definida para , o cmdlet PowerShell que aciona a tarefa de backup pode falhar. Executar estes comandos em modo elevado para verificar `Unrestricted` e `RemoteSigned`definir a política de execução para:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Certifique-se de que não existem ficheiros MSOnlineBackup do módulo PowerShell em falta ou corruptos. Se houver ficheiros em falta ou corruptos, tome estas medidas:

  1. De qualquer máquina que tenha um agente MARS que esteja a funcionar corretamente, copie a pasta MSOnlineBackup a partir de C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na máquina problemática, colhe os ficheiros copiados no mesmo local da pasta (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se já existir uma pasta MSOnlineBackup na máquina, colhe os ficheiros para a máquina ou substitua quaisquer ficheiros existentes.

> [!TIP]
> Para garantir que as alterações são aplicadas de forma consistente, reinicie o servidor depois de executar os passos anteriores.

## <a name="resource-not-provisioned-in-service-stamp"></a>Recurso não provisionado em carimbo de serviço

Erro | Possíveis causas | Ações recomendadas
--- | --- | ---
A operação em curso falhou devido a um erro de serviço interno "Recurso não provisionado em carimbo de serviço". Por favor, tente novamente a operação depois de algum tempo. (ID: 230006) | O servidor protegido foi renomeado. | <li> Mude o nome do servidor de volta para o nome original como registado no cofre. <br> <li> Reregide o servidor no cofre com o novo nome.

## <a name="troubleshoot-restore-problems"></a>Problemas de resolução de problemas restauram problemas

O Azure Backup pode não conseguir aumentar o volume de recuperação, mesmo depois de vários minutos. E pode receber mensagens de erro durante o processo. Para começar a recuperar normalmente, tome estes passos:

1. Cancele o processo de montagem se estiver em funcionamento por vários minutos.

2. Verifique se tem a versão mais recente do agente Dereserva. Para verificar a versão, no painel **Actions** da consola MARS, selecione Sobre o Agente de Serviços de Recuperação do **Microsoft Azure**. Confirme que o número da **versão** é igual ou superior à versão mencionada [neste artigo](https://go.microsoft.com/fwlink/?linkid=229525). Selecione este link para [descarregar a versão mais recente](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Vá aos**controladores** de armazenamento do Gestor > de **Dispositivos**e localize o **Initiator Microsoft iSCSI**. Se o localizar, vá diretamente para o passo 7.

4. Se não conseguir localizar o serviço iniciante da Microsoft iSCSI, tente encontrar uma entrada em controladores de armazenamento do Gestor > de **Dispositivos**chamado dispositivo **desconhecido** com **ROOT\ISCSIPRT**.**Storage controllers**

5. Clique no **dispositivo desconhecido** e selecione Software de Controlador de **Atualização**.

6. Atualize o controlador selecionando a opção de Procurar automaticamente para o **software de controlador atualizado**. Esta atualização deve alterar **o Dispositivo Desconhecido** para o **Initiator ISCSI**da Microsoft:

    ![Screenshot do Gestor de Dispositivos de Backup Azure, com controladores de armazenamento em destaque](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Ir **ao** > Serviço de Inicitor **(Local)** > **da Microsoft iSCSI**:

    ![Screenshot do Gestor de Tarefas de Backup Azure, com Serviços (Local) em destaque](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Reiniciar o serviço iniciante Microsoft iSCSI. Para isso, clique no serviço e selecione **Parar**. Em seguida, clique novamente na direita e selecione **Iniciar**.

9. Volte a tentar a recuperação utilizando o [Restauro Instantâneo](backup-instant-restore-capability.md).

Se a recuperação ainda falhar, reinicie o seu servidor ou cliente. Se não quiser reiniciar, ou se a recuperação ainda falhar mesmo depois de reiniciar o servidor, tente [recuperar de outra máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Problemas de cache de resolução de problemas

O funcionamento da cópia de segurança pode falhar se a pasta cache (também referida como pasta de risco) estiver incorretamente configurada, faltando pré-requisitos ou tiver acesso restrito.

### <a name="prerequisites"></a>Pré-requisitos

Para que as operações do agente MARS tenham sucesso, a pasta cache tem de aderir aos requisitos abaixo:

- [Certifique-se de que 5% a 10% de espaço de volume gratuito está disponível na localização da pasta scratch](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Certifique-se de que a localização da pasta de risco é válida e acessível](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Certifique-se de que os atributos de ficheiro na pasta cache são suportados](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Certifique-se de que o espaço de armazenamento de cópias sombra atribuídos é suficiente para o processo de backup](#increase-shadow-copy-storage)
- [Certifique-se de que não existem outros processos (ex. software antivírus) que limitem o acesso à pasta cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumentar o armazenamento de cópias de sombra

As operações de backup podem falhar se não houver espaço de armazenamento de cópias de sombra suficiente supor para proteger a fonte de dados. Para resolver este problema, aumente o espaço de armazenamento de cópias-sombra no volume protegido utilizando a vssadmina, como mostrado abaixo:

- Verifique o espaço de armazenamento de sombra atual a partir do pedido de comando elevado:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumente o espaço de armazenamento de sombras utilizando o comando abaixo:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Outro software de processo ou antivírus que bloqueia o acesso à pasta cache

Se tiver software antivírus instalado no servidor, adicione as regras de exclusão necessárias à varredura antivírus para estes ficheiros e pastas:  

- A pasta de risco. A sua localização padrão é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- A pasta do caixote do lixo em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Problemas comuns

Esta secção cobre os erros comuns que encontra ao utilizar o agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Mensagem de erro | Ação recomendada
-- | --
O Agente dos Serviços de Recuperação do Microsoft Azure não conseguiu aceder à soma de verificação da cópia de segurança armazenada no local de rascunho | Para resolver este problema, execute o abaixo e reinicie o servidor <br/> - [Verifique se existe um antivírus ou outros processos que bloqueiem os ficheiros de localização de risco](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se a localização do risco é válida e acessível ao agente de Marte.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Mensagem de erro | Ação recomendada
-- | --
O Agente dos Serviços de Recuperação do Microsoft Azure não conseguiu aceder ao local de rascunho para inicializar o VHD | Para resolver este problema, execute o abaixo e reinicie o servidor <br/> - [Verifique se existe um antivírus ou outros processos que bloqueiem os ficheiros de localização de risco](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se a localização do risco é válida e acessível ao agente de Marte.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Mensagem de erro | Ação recomendada
-- | --
A cópia de segurança falhou devido ao armazenamento insuficiente no volume onde se encontra a pasta de risco | Para resolver este problema, verifique os passos abaixo e tente novamente a operação:<br/>- [Certifique-se de que o agente MARS é o mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verificar e resolver problemas de armazenamento que impactam o espaço de risco de backup](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Mensagem de erro | Ação recomendada
-- | --
Não é possível encontrar alterações num ficheiro. Tal pode dever-se a vários motivos. Repita a operação | Para resolver este problema, verifique os passos abaixo e tente novamente a operação:<br/> - [Certifique-se de que o agente MARS é o mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verificar e resolver problemas de armazenamento que impactam o espaço de risco de backup](#prerequisites)

## <a name="next-steps"></a>Passos seguintes

- Obtenha mais detalhes sobre como fazer backup do [Windows Server com o agente de backup Azure](tutorial-backup-windows-server-to-azure.md).
- Se precisar de restaurar uma cópia de segurança, consulte [a restauração de ficheiros para uma máquina do Windows](backup-azure-restore-windows-server.md).
