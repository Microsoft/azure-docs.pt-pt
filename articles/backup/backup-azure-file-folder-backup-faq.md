---
title: Fazendo backup de arquivos e pastas com o backup do Azure-perguntas comuns
description: Aborda perguntas comuns sobre como fazer backup de arquivos e pastas com o backup do Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: c65c04a67b92642d3664293dd666236919142f12
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074197"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Perguntas comuns sobre como fazer backup de arquivos e pastas

Este artigo tem respostas para perguntas comuns abound fazer backup de arquivos e pastas com o agente de Serviços de Recuperação do Microsoft Azure (MARS) no serviço de [backup do Azure](backup-overview.md) .

## <a name="configure-backups"></a>Configurar backups

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Onde posso baixar a versão mais recente do agente MARS?

O agente MARS mais recente usado ao fazer backup de computadores com Windows Server, System Center DPM e Backup do Microsoft Azure Server está disponível para [Download](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Por quanto tempo as credenciais do cofre são válidas?

As credenciais do cofre expiram após 48 horas. Se o arquivo de credenciais expirar, baixe o arquivo novamente no portal do Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>De quais unidades posso fazer backup de arquivos e pastas?

Não é possível fazer backup dos seguintes tipos de unidades e volumes:

* Mídia removível: todas as fontes de itens de backup devem ser relatadas como fixas.
* Volumes somente leitura: o volume deve ser gravável para que o VSS (serviço de cópias de sombra de volume) funcione.
* Volumes offline: o volume deve estar online para que o VSS funcione.
* Compartilhamentos de rede: o volume deve ser local para o backup do servidor usando o backup online.
* Volumes protegidos pelo BitLocker: o volume deve ser desbloqueado antes que o backup possa ocorrer.
* Identificação do Sistema de Ficheiros: o NTFS é o único sistema de ficheiros suportado.

### <a name="what-file-and-folder-types-are-supported"></a>Quais tipos de arquivos e pastas têm suporte?

[Saiba mais](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sobre os tipos de arquivos e pastas com suporte para backup.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Posso usar o agente MARS para fazer backup de arquivos e pastas em uma VM do Azure?  

Sim. O backup do Azure fornece backup em nível de VM para VMs do Azure usando a extensão de VM para o agente de VM do Azure. Se desejar fazer backup de arquivos e pastas no sistema operacional Windows convidado na VM, você poderá instalar o agente MARS para fazer isso.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Posso usar o agente MARS para fazer backup de arquivos e pastas no armazenamento temporário da VM do Azure?

Sim. Instale o agente MARS e faça backup de arquivos e pastas no sistema operacional Windows convidado para o armazenamento temporário.

* Os trabalhos de backup falham quando dados de armazenamento temporários são apagados.
* Se os dados de armazenamento temporários forem excluídos, você só poderá restaurar para o armazenamento não volátil.

### <a name="how-do-i-register-a-server-to-another-region"></a>Como fazer registrar um servidor em outra região?

Os dados de backup são enviados para o datacenter do cofre no qual o servidor está registrado. A maneira mais fácil de alterar o datacenter é desinstalar e reinstalar o agente e, em seguida, registrar o computador em um novo cofre na região de que você precisa.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>O agente MARS dá suporte à eliminação de duplicação do Windows Server 2012?

Sim. O agente MARS converte os dados com eliminação de duplicação em dados normais quando prepara a operação de backup. Em seguida, ele otimiza os dados para backup, criptografa os dados e, em seguida, envia os dados criptografados para o cofre.

## <a name="manage-backups"></a>Gerir cópias de segurança

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>O que acontece se eu renomear um computador Windows configurado para backup?

Quando você renomeia um computador Windows, todos os backups atualmente configurados são interrompidos.

* Você precisa registrar o novo nome do computador com o cofre de backup.
* Quando você registra o novo nome com o cofre, a primeira operação é um backup *completo* .
* Se você precisar recuperar dados do backup para o cofre com o nome do servidor antigo, use a opção para restaurar em um local alternativo no assistente de recuperação de dados. [Saiba mais](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qual é o comprimento máximo do caminho de arquivo para backup?

O agente MARS depende do NTFS e usa a especificação de comprimento FilePath limitada pela API do [Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Se os arquivos que você deseja proteger forem maiores do que o valor permitido, faça backup da pasta pai ou da unidade de disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quais caracteres são permitidos em caminhos de arquivo?

O agente MARS depende do NTFS e permite [caracteres com suporte](/windows/desktop/FileIO/naming-a-file#naming-conventions) em nomes/caminhos de arquivo.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>O aviso "backups do Azure não foram configurados para este servidor" é exibido.

Esse aviso pode aparecer mesmo que você tenha configurado uma política de backup, quando as configurações de agendamento de backup armazenadas no servidor local não são as mesmas que as configurações armazenadas no cofre de backup.

* Quando o servidor ou as configurações forem recuperados para um estado válido conhecido, os agendamentos de backup poderão se tornar não sincronizados.
* Se você receber esse aviso, [Configure](backup-azure-manage-windows-server.md) a política de backup novamente e, em seguida, execute um backup sob demanda para ressincronizar o servidor local com o Azure.

## <a name="manage-the-backup-cache-folder"></a>Gerenciar a pasta de cache de backup

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual é o requisito de tamanho mínimo para a pasta da cache?

O tamanho da pasta cache determina a quantidade de dados para a cópia de segurança.

* Os volumes da pasta de cache devem ter espaço livre igual a pelo menos 5-10% do tamanho total dos dados de backup.
* Se o volume tiver menos de 5% de espaço livre, aumente o tamanho do volume ou mova a pasta de cache para um volume com espaço suficiente.
* Se você fazer backup do estado do sistema do Windows, será necessário mais 30-35 GB de espaço livre no volume que contém a pasta de cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Como verificar se a pasta de rascunho é válida e acessível?

1. Por padrão, a pasta de rascunho está localizada em `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Verifique se o caminho do local da pasta de rascunho corresponde aos valores das entradas da chave do registro mostradas abaixo:

  | Caminho do registo | Chave do Registo | Valor |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta cache* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Como fazer alterar o local do cache para o agente MARS?

1. Execute este comando em um prompt de comandos com privilégios elevados para interromper o mecanismo de backup:

    ```Net stop obengine```

2. Se você tiver configurado o backup do estado do sistema, abra o gerenciamento de disco e desmonte os discos com nomes no formato `"CBSSBVol_<ID>"`.
3. Não mova os arquivos. Em vez disso, copie a pasta de espaço em cache para uma unidade diferente que tenha espaço suficiente.
4. Atualize as entradas de registro a seguir com o caminho da nova pasta de cache.

    | Caminho do registo | Chave do Registo | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta cache* |

5. Reinicie o mecanismo de backup em um prompt de comandos com privilégios elevados:

  ```command
  Net stop obengine

  Net start obengine
  ```

6. Execute um backup sob demanda. Depois que o backup for concluído com êxito usando o novo local, você poderá remover a pasta de cache original.

### <a name="where-should-the-cache-folder-be-located"></a>Onde a pasta de cache deve ser localizada?

As seguintes localizações para a pasta da cache não são recomendadas:

* Compartilhamento de rede/mídia removível: a pasta de cache deve ser local para o servidor que precisa de backup usando o backup online. Locais de rede ou mídias removíveis, como unidades USB, não são suportadas
* Volumes offline: a pasta de cache deve estar online para o backup esperado usando o agente de backup do Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Há algum atributo da pasta de cache que não tem suporte?

Os atributos ou respetivas combinações seguintes não são suportadas para a pasta de cache:

* Encriptados
* Duplicados eliminados
* Comprimidos
* Disperso
* Ponto de Reanálise

A pasta de cache e o VHD de metadados não têm os atributos necessários para o agente do Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Há uma maneira de ajustar a quantidade de largura de banda usada para backup?

Sim, você pode usar a opção **alterar propriedades** no agente Mars para ajustar a largura de banda e o tempo. [Saiba mais](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Restauro

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontecerá se eu cancelar um trabalho de restauração em andamento?

Se um trabalho de restauração em andamento for cancelado, o processo de restauração será interrompido. Todos os arquivos restaurados antes do cancelamento permanecem no destino configurado (local original ou alternativo), sem nenhuma reversão.

## <a name="next-steps"></a>Passos seguintes

[Saiba](tutorial-backup-windows-server-to-azure.md) como fazer backup de um computador Windows.
