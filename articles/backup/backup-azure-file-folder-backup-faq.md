---
title: Agente dos Serviços de Recuperação da Microsoft Azure (MARS) – FAQ
description: Aborda questões comuns sobre o backup de ficheiros e pastas com Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: e3a5b6d07982c3261b457d4999025c44489c0a8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182518"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>Perguntas frequentes - Agente da Microsoft Azure Recovery Services (MARS)

Este artigo responde a perguntas comuns sobre o backup de dados com o Agente microsoft Azure Recovery Services (MARS) no serviço [Azure Backup.](backup-overview.md)

## <a name="configure-backups"></a>Configurar backups

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Onde posso descarregar a versão mais recente do agente MARS?

O mais recente agente MARS utilizado ao fazer o backup de máquinas do Windows Server, do System Center DPM e do servidor Backup Microsoft Azure está disponível para [download](https://aka.ms/azurebackup_agent).

### <a name="where-can-i-download-the-vault-credentials-file"></a>Onde posso descarregar o ficheiro de credenciais do cofre?

No portal Azure, navegue para **propriedades** para o seu cofre. Em **Credenciais de Reserva**, selecione a caixa de verificação para já utilizar o mais recente Agente de **Serviços de Recuperação**. Selecione **Transferir**.

![Baixar credenciais](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>Quanto tempo as credenciais do cofre são válidas?

As credenciais do cofre expiram após 10 dias. Se o ficheiro de credenciais expirar, descarregue novamente o ficheiro a partir do portal Azure.

### <a name="what-characters-are-allowed-for-the-passphrase"></a>Que caracteres são permitidos para a palavra-passe?

A palavra-passe deve utilizar caracteres do conjunto de caracteres ASCII, com [valores ASCII inferiores ou iguais a 127](https://docs.microsoft.com/office/vba/language/reference/user-interface-help/character-set-0127).

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>De que unidades posso fazer o back-up de ficheiros e pastas?

Não é possível fazer recuar os seguintes tipos de unidades e volumes:

* Meios de comunicação amovíveis: Todas as fontes de artigos de reserva devem reportar conforme fixado.
* Volumes apenas de leitura: O volume deve ser legível para que o serviço de cópia sombra de volume (VSS) funcione.
* Volumes offline: O volume deve estar online para que o VSS funcione.
* Partilhas de rede: O volume deve ser local para o servidor para ser apoiado através de cópia de segurança online.
* Volumes protegidos pelo BitLocker: O volume deve ser desbloqueado antes de a cópia de segurança poder ocorrer.
* Identificação do Sistema de Ficheiros: o NTFS é o único sistema de ficheiros suportado.

### <a name="what-file-and-folder-types-are-supported"></a>Que tipos de ficheiros e pastas são suportados?

[Saiba mais](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sobre os tipos de ficheiros e pastas suportados para cópia de segurança.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Posso usar o agente MARS para fazer o back-up ficheiros e pastas num VM Azure?  

Sim. O Azure Backup fornece uma cópia de segurança ao nível VM para VMs Azure utilizando a extensão VM para o agente Azure VM. Se pretender fazer o back up ficheiros e pastas no sistema operativo Windows do hóspede no VM, pode instalar o agente MARS para o fazer.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Posso usar o agente MARS para fazer o back-up ficheiros e pastas no armazenamento temporário para o Azure VM?

Sim. Instale o agente MARS e ressalte ficheiros e pastas no sistema operativo Windows do hóspede para armazenamento temporário.

* Os trabalhos de reserva falham quando os dados de armazenamento temporários são eliminados.
* Se os dados de armazenamento temporário forem eliminados, só poderá restaurar o armazenamento não volátil.

### <a name="how-do-i-register-a-server-to-another-region"></a>Como posso registar um servidor noutra região?

Os dados de cópia de segurança são enviados para o centro de dados do cofre no qual o servidor está registado. A forma mais fácil de alterar o datacenter é desinstalar e reinstalar o agente e, em seguida, registar a máquina num novo cofre na região de que necessita.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>O agente MARS suporta a deduplica do Windows Server 2012?

Sim. O agente MARS converte os dados desduplicados em dados normais quando prepara a operação de backup. Em seguida, otimiza os dados para a cópia de segurança, encripta os dados e, em seguida, envia os dados encriptados para o cofre.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Preciso de permissões de administrador para instalar e configurar o agente MARS?

Sim, a instalação do Agente MARS e a configuração de cópias de segurança utilizando a consola MARS precisam que o utilizador seja um administrador local no servidor protegido.

## <a name="manage-backups"></a>Gerir cópias de segurança

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>O que acontece se eu mudar o nome de uma máquina do Windows configurada para cópia de segurança?

Quando se muda o nome de uma máquina Windows, todas as cópias de segurança atualmente configuradas são interrompidas.

* Tens de registar o novo nome da máquina com o cofre reserva.
* Quando registas o novo nome com o cofre, a primeira operação é uma cópia de segurança *completa.*
* Se precisar de recuperar dados com o nome do servidor antigo, utilize a opção para restaurar para um local alternativo no Assistente de Dados de Recuperação. [Saiba mais](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qual é o comprimento máximo do caminho do ficheiro para a cópia de segurança?

O agente MARS baseia-se no NTFS e utiliza a especificação de comprimento do filepath limitada pela [API](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths)do Windows . Se os ficheiros que pretende proteger forem mais longos do que o valor permitido, faça uma erússio na pasta dos pais ou na unidade do disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Que caracteres são permitidos em caminhos de arquivo?

O agente MARS conta com NTFS e permite [caracteres suportados](/windows/win32/FileIO/naming-a-file#naming-conventions) em nomes/caminhos de ficheiros.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>O aviso "Backups Azure não foram configurados para este servidor" aparece

Este aviso pode aparecer mesmo tendo configurado uma política de backup, quando as definições de agenda de cópias de segurança armazenadas no servidor local não são as mesmas que as definições armazenadas no cofre de reserva.

* Quando o servidor ou as definições tiverem sido recuperadas para um bom estado conhecido, os horários de backup podem tornar-se insincronizados.
* Se receber este aviso, [configuure](backup-azure-manage-windows-server.md) novamente a política de backup e, em seguida, execute uma cópia de segurança a pedido para resincronizar o servidor local com a Azure.

## <a name="manage-the-backup-cache-folder"></a>Gerir a pasta de cache de reserva

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual é o requisito de tamanho mínimo para a pasta da cache?

O tamanho da pasta de cache determina a quantidade de dados que está a fazer.

* Os volumes da pasta cache devem ter espaço livre que equivale a pelo menos 5-10% do tamanho total dos dados de backup.
* Se o volume tiver menos de 5% de espaço livre, aumente o tamanho do volume ou mova a pasta cache para um volume com espaço suficiente seguindo [estes passos](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Se fizer uma reserva do Windows System State, precisará de mais 30-35 GB de espaço livre no volume que contém a pasta cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Como verificar se a pasta de risco é válida e acessível?

1. Por defeito, a pasta de risco está localizada em `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Certifique-se de que o caminho da sua localização da pasta de risco coincide com os valores das entradas das chaves de registo apresentadas abaixo:

    | Caminho do registo | Chave do Registo | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Como mudo a cache para o agente MARS?

1. Executar este comando num pedido de comando elevado para parar o motor de reserva:

    ```Net stop obengine```
2. Se configurar a cópia de segurança do Estado do Sistema, abra a Gestão do Disco e desmonte o disco(s) com nomes no formato `"CBSSBVol_<ID>"` .
3. Por predefinição, a pasta de risco está localizada em `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Copie toda a `\Scratch` pasta para uma unidade diferente que tenha espaço suficiente. Certifique-se de que o conteúdo é copiado, não movido.
5. Atualize as seguintes entradas de registo com o caminho da pasta de risco recém-movida.

    | Caminho do registo | Chave do Registo | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta de risco* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta de risco* |

6. Reinicie o motor de reserva com uma solicitação de comando elevada:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Executar uma cópia de segurança a pedido. Depois de a cópia de segurança terminar com sucesso utilizando a nova localização, pode remover a pasta de cache original.

### <a name="where-should-the-cache-folder-be-located"></a>Onde deve ser localizada a pasta de cache?

Não são recomendados os seguintes locais para a pasta cache:

* Partilhar a rede/suportes amovíveis: A pasta cache deve ser local para o servidor que necessita de ser cópia de segurança através da cópia de segurança online. As localizações da rede ou os meios de comunicação amovíveis como as unidades USB não são suportados.
* Volumes offline: A pasta cache deve estar on-line para cópia de segurança esperada usando o Agente de Backup Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Existem atributos da pasta cache que não sejam suportados?

Os seguintes atributos ou as suas combinações não são suportados para a pasta cache:

* Encriptados
* Duplicados eliminados
* Comprimidos
* Disperso
* Ponto de Reanálise

A pasta cache e os metadados VHD não têm os atributos necessários para o agente de backup Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existe uma maneira de ajustar a quantidade de largura de banda usada para cópia de segurança?

Sim, pode utilizar a opção **Change Properties** no agente MARS para ajustar a largura de banda e o tempo. [Saiba mais](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Restauro

### <a name="manage"></a>Gerir

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>Posso recuperar se me esqueci da minha frase?

O agente Azure Backup requer uma palavra-passe (que forneceu durante o registo) para desencriptar os dados com cópia de segurança durante a restauração. Reveja os cenários abaixo para entender as suas opções para lidar com uma frase de passe perdida:

| Máquina Original <br> *(máquina de origem onde foram tomadas cópias de segurança)* | Frase-passe | Opções disponíveis |
| --- | --- | --- |
| Disponível |Perdido |Se a sua máquina original (onde foram feitas cópias de segurança) estiver disponível e ainda estiver registada com o mesmo cofre dos Serviços de Recuperação, então pode regenerar a palavra-passe seguindo estes [passos.](./backup-azure-manage-mars.md#re-generate-passphrase)  |
| Perdido |Perdido |Não é possível recuperar os dados ou os dados não estão disponíveis |

Considere as seguintes condições:

* Se desinstalar e voltar a registar o agente na mesma máquina original com a
  * *A mesma palavra-passe,* então pode restaurar os seus dados de retenção.
  * *Frases-passe diferentes,* então não pode restaurar os seus dados retroativos.
* Se instalar o agente numa *máquina diferente* com a
  * *A mesma frase-passe* (utilizada na máquina original), pode então restaurar os seus dados de retenção.
  * *Frases de passe diferentes,* não é possível restaurar os seus dados de back-up.
* Se a sua máquina original estiver corrompida (impedindo-o de regenerar a palavra-passe através da consola MARS), mas pode restaurar ou aceder à pasta de risco original utilizada pelo agente MARS, então poderá ser capaz de restaurar (se esqueceu da palavra-passe). Para mais assistência, contacte o Apoio ao Cliente.

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>Como é que me recupero se perdi a minha máquina original (para onde foram levados reforços)?

Se tiver a mesma palavra-passe (que forneceu durante o registo) da máquina original, então pode restaurar os dados de retenção para uma máquina alternativa. Reveja os cenários abaixo para entender as suas opções de restauro.

| Máquina Original | Frase-passe | Opções disponíveis |
| --- | --- | --- |
| Perdido |Disponível |Pode instalar e registar o agente MARS noutra máquina com a mesma frase de passe que forneceu durante o registo da máquina original. Escolha **A Opção de Recuperação**  >  **Outro local** para realizar a sua restauração. Para mais informações, consulte este [artigo.](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
| Perdido |Perdido |Não é possível recuperar os dados ou os dados não estão disponíveis |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>Os meus trabalhos de apoio têm falhado ou não estão a funcionar há muito tempo. Já passei do período de retenção. Ainda posso restaurar?

Como medida de segurança, o Azure Backup preservará o mais recente ponto de recuperação, mesmo que já tenha passado do período de retenção. Uma vez retomados os backups e os novos pontos de recuperação ficam disponíveis, o ponto de recuperação mais antigo será removido de acordo com a retenção especificada.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontece se eu cancelar um trabalho de restauro?

Se um trabalho de restauro em curso for cancelado, o processo de restauro para. Todos os ficheiros restaurados antes do cancelamento permanecem no destino configurado (localização original ou alternativa), sem qualquer reversão.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>O agente MARS faz o back up e restaura os ACLs definidos em ficheiros, pastas e volumes?

* O agente MARS apoia acLs definidos em ficheiros, pastas e volumes
* Para a opção de recuperação do Volume Restaurar, o agente MARS oferece uma opção para não restaurar permissões ACL no ficheiro ou pasta que está a ser recuperada
* Para a opção de recuperação de ficheiros e pastas individuais, o agente MARS irá restaurar com permissões ACL (não há opção para ignorar a restauração da ACL).

## <a name="next-steps"></a>Passos seguintes

[Aprenda](tutorial-backup-windows-server-to-azure.md) a fazer o back up de uma máquina Windows.
