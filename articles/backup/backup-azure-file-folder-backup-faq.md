---
title: Perguntas comuns ao fazer backup de arquivos e pastas com o Azure Backup
description: Aborda perguntas comuns sobre o backup de arquivos e pastas com o Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: dacurwin
ms.openlocfilehash: 5dbd4fefd5c5e1acd7e12ace547ddb8866b7f081
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148593"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Perguntas comuns sobre o backup de arquivos e pastas 

Este artigo tem respostas a perguntas comuns de cópia de segurança ficheiros e pastas com o agente dos serviços de recuperação do Azure (MARS) da Microsoft no há muitos a [Azure Backup](backup-overview.md) serviço.

## <a name="general"></a>Geral

### <a name="why-does-the-mars-agent-need-net-framework-452-or-higher"></a>O agente de MARS é por que preciso o .NET framework 4.5.2 ou superior?

Nova funcionalidade disponível em [restauro imediato](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) precisa do .NET Framework 4.5.2 ou superior.

## <a name="configure-backups"></a>Configurar cópias de segurança

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Onde posso transferir a versão mais recente do agente MARS? 
O agente de MARS mais recente utilizado ao fazer backup de máquinas do Windows Server, o System Center DPM e o servidor de cópia de segurança do Microsoft Azure está disponível para [transferir](https://aka.ms/azurebackup_agent). 

### <a name="how-long-are-vault-credentials-valid"></a>O intervalo de tempo são as credenciais do cofre válido?
As credenciais do cofre expiram após 48 horas. Se o ficheiro de credenciais expirar, transfira novamente o ficheiro do portal do Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>De que unidades posso criar segurança de ficheiros e pastas? 

Não pode criar cópias de segurança os seguintes tipos de unidades e volumes:

* Suporte de dados amovível: Todas as origens de item de cópia de segurança tem de ser comunicadas como fixas.
* Volumes de só de leitura: O volume tem de ser gravável para o serviço de cópia de sombra de volumes (VSS) para funcionar.
* Offline volumes: O volume tem de estar online para que o VSS funcione.
* Partilhas de rede: O volume tem de ser local no servidor para ser feita com a cópia de segurança online.
* Volumes protegidos de BitLocker: O volume tem de ser desbloqueado antes da cópia de segurança ocorrer.
* Identificação do sistema de ficheiros: O NTFS é o único sistema de ficheiros suportado.

### <a name="what-file-and-folder-types-are-supported"></a>Que tipos de arquivo e pasta são suportados?

São suportados os seguintes tipos:

* Encriptados
* Comprimidos
* Dispersos
* Comprimidos + dispersos
* Hiperligações: Não suportado, ignorado
* Ponto de reanálise: Não suportado, ignorado
* Encriptados + dispersos: Não suportado, ignorado
* Stream comprimido: Não suportado, ignorado
* Pontos de reanálise, incluindo ligações DFS e pontos de junção


### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Pode utilizar o agente MARS para efetuar cópias de segurança de ficheiros e pastas numa VM do Azure?  
Sim. O Azure Backup fornece cópias de segurança ao nível da VM para VMs do Azure com a extensão da VM para o agente de VM do Azure. Se quiser fazer uma cópia de segurança de ficheiros e pastas no sistema operacional Windows convidado na VM, pode instalar o agente de MARS para fazer isso. 

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Pode utilizar o agente MARS para efetuar cópias de segurança de ficheiros e pastas no armazenamento temporário para a VM do Azure? 
Sim. Instale o agente MARS e fazer uma cópia de segurança de ficheiros e pastas no sistema operativo Windows para armazenamento temporário. -As tarefas cópia de segurança falhar sobre o armazenamento temporário de dados serem eliminados.
- Se os dados de armazenamento temporário serem eliminados, só pode restaurar para o armazenamento não volátil.

### <a name="how-do-i-register-a-server-to-another-region"></a>Como me Registro um servidor para outra região?

Dados de cópia de segurança são enviados para o datacenter do cofre no qual o servidor está registado. A maneira mais fácil de alterar o datacenter é desinstalar e reinstalar o agente e, em seguida, registe-se a máquina para um novo cofre na região que precisa

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>O suporte de agente de MARS Windows Server 2012 duplicados é?
Sim. O agente de MARS converte os dados com duplicados eliminados para dados normais quando prepara a operação de cópia de segurança. Ele, em seguida, otimiza os dados para cópia de segurança, encripta os dados e, em seguida, envia os dados encriptados no cofre.

## <a name="manage-backups"></a>Gerir cópias de segurança

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>O que acontece se eu mudar o nome de uma máquina Windows configurada para cópia de segurança?

Quando mudar o nome de uma máquina Windows, todas as cópias de segurança atualmente configuradas são paradas.

- Tem de registar o novo nome do computador com o Cofre de cópia de segurança.
- Quando registra o novo nome com o cofre, a primeira operação é uma *completo* cópia de segurança.
- Se precisar de recuperar dados de cópia de segurança no cofre com o nome do servidor antigo, utilize a opção para restaurar para uma localização alternativa no Assistente para recuperar dados. [Saiba mais](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine). 

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>O que é o comprimento do caminho de ficheiro máximo para cópia de segurança?
O agente de MARS depende do NTFS e utiliza a especificação do comprimento de caminho do ficheiro limitada pela [API do Windows](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths). Se os ficheiros que pretende proteger são maiores do que o valor permitido, cópia de segurança da pasta principal ou a unidade de disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Que carateres são permitidos em caminhos de ficheiros?

O agente de MARS depende do NTFS e permite [carateres suportados pelo](/windows/desktop/FileIO/naming-a-file#naming_conventions) em nomes/caminhos de ficheiros.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>É apresentado o aviso "As cópias de segurança do Azure não foram configuradas para este servidor".
Este aviso pode aparecer mesmo que configurou uma política de cópia de segurança, quando as definições do agendamento de cópia de segurança armazenadas no servidor local não são as mesmas que as definições armazenadas no Cofre de cópia de segurança.
- Quando o servidor ou as definições tenham sido recuperadas para um bom estado conhecido, agendas de cópia de segurança podem tornar-se não sincronizadas.
- Se receber este aviso [configurar](backup-azure-manage-windows-server.md) novamente a política de cópia de segurança e, em seguida, cópia de segurança de execução de uma demanda para ressincronizar o servidor local com o Azure.


## <a name="manage-the-backup-cache-folder"></a>Gerir a pasta da cache de cópia de segurança

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual é o requisito de tamanho mínimo para a pasta da cache?
O tamanho da pasta cache determina a quantidade de dados para a cópia de segurança.
- Os volumes de pasta de cache devem ter espaço livre que é igual a, pelo menos, 5 a 10% do tamanho total dos dados de cópia de segurança.
- Se o volume tiver menos de 5% de espaço livre, aumentar o tamanho do volume ou mova a pasta de cache para um volume com espaço suficiente.
- 
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Como posso alterar a localização da cache para o agente de MARS?


1. Execute este comando numa linha de comandos elevada para parar o mecanismo de cópia de segurança:

    ```PS C:\> Net stop obengine```

2. Não mova os ficheiros. Em vez disso, copie a pasta de espaço da cache para uma unidade diferente que tenha espaço suficiente.
3. Atualize as seguintes entradas de registo com o caminho da pasta de cache nova.<br/>

    | Caminho do registo | Chave do Registo | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta cache* |

4. Reinicie o motor de cópia de segurança numa linha de comandos elevada:

    ```PS C:\> Net start obengine```

5. Depois da cópia de segurança concluída com êxito utilizando a nova localização, pode remover a pasta cache original.


### <a name="where-should-the-cache-folder-be-located"></a>Onde a pasta da cache será localizada?

As seguintes localizações para a pasta da cache não são recomendadas:

* Suporte de dados amovível/partilha de rede: A pasta da cache tem de ser local no servidor que necessita de cópia de segurança utilizando a cópia de segurança online. Localizações de rede ou mídia removível como unidades USB não é suportada
* Offline volumes: A pasta da cache tem de estar online para a cópia de segurança esperada com o agente de cópia de segurança do Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Existem todos os atributos da pasta cache que não são suportados?
Os atributos ou respetivas combinações seguintes não são suportadas para a pasta de cache:

* Encriptados
* Duplicados eliminados
* Comprimidos
* Dispersos
* Ponto de Reanálise

A pasta de cache e o VHD de metadados não têm os atributos necessários para o agente do Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existe uma forma de ajustar a quantidade de largura de banda utilizada para cópia de segurança?
 
Sim, pode utilizar o **alterar propriedades** opção no agente de MARS para ajustar a largura de banda e temporização das atualizações. [Saiba mais](backup-configure-vault.md#enable-network-throttling)* *.

## <a name="restore"></a>Restauro

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontece se cancelar uma tarefa de restauro em curso?

Se uma tarefa de restauro em curso é cancelada, interrompe o processo de restauração. Todos os ficheiros restaurados antes do cancelamento mantenha-se no destino configurado (localização original ou alternativa), sem qualquer reversões.


## <a name="next-steps"></a>Passos Seguintes

[Saiba](tutorial-backup-windows-server-to-azure.md) como fazer uma cópia de segurança de uma máquina Windows.
