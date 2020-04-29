---
title: Backup de ficheiros e pastas - questões comuns
description: Aborda questões comuns sobre o backup de ficheiros e pastas com o Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 6e9f265672ff15e40444a46a3e440e73a0051a5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254755"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Perguntas comuns sobre o backup de ficheiros e pastas

Este artigo responde a questões comuns que abundam no backup de ficheiros e pastas com o Agente de Serviços de Recuperação do Microsoft Azure (MARS) no serviço [de backup Azure.](backup-overview.md)

## <a name="configure-backups"></a>Configurar cópias de segurança

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Onde posso descarregar a versão mais recente do agente MARS?

O mais recente agente MARS utilizado ao fazer backup das máquinas Do Windows Server, DoM do System Center e do servidor de backup do Microsoft Azure está disponível para [download](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Quanto tempo as credenciais do cofre são válidas?

As credenciais do cofre expiram após 10 dias. Se o ficheiro de credenciais expirar, faça o download do ficheiro novamente a partir do portal Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>A partir de que unidades posso fazer o back-up de ficheiros e pastas?

Não é possível apoiar os seguintes tipos de unidades e volumes:

* Meios amovíveis: Todas as fontes de artigo de reserva devem apresentar-se como fixas.
* Volumes de leitura apenas: O volume deve ser repreensível para que o serviço de cópia de sombra de volume (VSS) funcione.
* Volumes offline: O volume deve estar online para que o VSS funcione.
* Partilhas de rede: O volume deve ser local para o servidor ser apoiado usando cópia de segurança on-line.
* Volumes protegidos bitLocker: O volume deve ser desbloqueado antes da cópia de segurança poder ocorrer.
* Identificação do Sistema de Ficheiros: o NTFS é o único sistema de ficheiros suportado.

### <a name="what-file-and-folder-types-are-supported"></a>Que tipos de ficheiros e pastas são suportados?

[Saiba mais](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sobre os tipos de ficheiros e pastas suportados para cópia de segurança.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Posso usar o agente MARS para fazer o back-up de ficheiros e pastas num VM Azure?  

Sim. A Azure Backup fornece cópia de segurança ao nível de VM para VMs utilizando a extensão VM para o agente Azure VM. Se pretender fazer o back-up de ficheiros e pastas no sistema operativo Windows de hóspedes no VM, pode instalar o agente MARS para o fazer.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Posso usar o agente MARS para fazer o back-up de ficheiros e pastas em armazenamento temporário para o Azure VM?

Sim. Instale o agente MARS e recue ficheiros e pastas no sistema operativo Windows de hóspedes para armazenamento temporário.

* Os trabalhos de reserva falham quando os dados temporários de armazenamento são eliminados.
* Se os dados de armazenamento temporário forem eliminados, só pode restaurar o armazenamento não volátil.

### <a name="how-do-i-register-a-server-to-another-region"></a>Como posso registar um servidor para outra região?

Os dados de cópia de segurança são enviados para o datacenter do cofre no qual o servidor está registado. A forma mais fácil de alterar o datacenter é desinstalar e reinstalar o agente e, em seguida, registar a máquina para um novo cofre na região de que necessita.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>O agente MARS suporta a duplicação do Windows Server 2012?

Sim. O agente MARS converte os dados duplicados em dados normais quando prepara a operação de backup. Em seguida, otimiza os dados para backup, encripta os dados e, em seguida, envia os dados encriptados para o cofre.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Preciso de permissões de administrador para instalar e configurar o agente MARS?

Sim, a instalação do Agente MARS e a configuração de backups utilizando a consola MARS precisam que o utilizador seja um administrador local no servidor protegido.

## <a name="manage-backups"></a>Gerir cópias de segurança

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>O que acontece se eu mudar o nome de uma máquina do Windows configurada para cópia de segurança?

Quando renomear uma máquina windows, todas as cópias de segurança configuradas são paradas.

* Tens de registar o novo nome da máquina com o cofre de reserva.
* Quando registas o novo nome com o cofre, a primeira operação é uma cópia de segurança *completa.*
* Se precisar de recuperar os dados apoiados no cofre com o antigo nome do servidor, utilize a opção para restaurar um local alternativo no Assistente de Dados de Recuperação. [Saiba mais](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qual é o comprimento máximo do caminho de arquivo para cópia de segurança?

O agente MARS baseia-se no NTFS e utiliza a especificação de comprimento do processo de arquivo limitada pela [API do Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Se os ficheiros que pretende proteger forem mais longos do que o valor permitido, faça o backup da pasta dos pais ou a unidade do disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Que caracteres são permitidos em caminhos de arquivo?

O agente MARS baseia-se no NTFS e permite [caracteres suportados](/windows/desktop/FileIO/naming-a-file#naming-conventions) em nomes/caminhos de ficheiros.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>O aviso "Backups Azure não foram configurados para este servidor" aparece

Este aviso pode aparecer mesmo que tenha configurado uma política de backup, quando as definições de horário de reserva armazenadas no servidor local não são as mesmas que as definições armazenadas no cofre de reserva.

* Quando o servidor ou as definições tiverem sido recuperadas para um bom estado conhecido, os horários de backup podem tornar-se insincronizados.
* Se receber este aviso, [configure](backup-azure-manage-windows-server.md) novamente a política de backup e, em seguida, faça uma cópia de segurança a pedido para resincronizar o servidor local com o Azure.

## <a name="manage-the-backup-cache-folder"></a>Gerir a pasta de cache de reserva

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual é o requisito de tamanho mínimo para a pasta da cache?

O tamanho da pasta cache determina a quantidade de dados para a cópia de segurança.

* Os volumes de pasta cache devem ter espaço livre que equivale a pelo menos 5-10% do tamanho total dos dados de backup.
* Se o volume tiver menos de 5% de espaço livre, aumente o tamanho do volume ou mova a pasta cache para um volume com espaço suficiente seguindo [estes passos](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Se fizer backup do Windows System State, necessitará de mais 30-35 GB de espaço livre no volume que contém a pasta cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Como verificar se a pasta de risco é válida e acessível?

1. Por padrão, a pasta de risco está localizada em`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Certifique-se de que o caminho da localização da pasta de risco corresponde aos valores das entradas da chave de registo abaixo indicados:

    | Caminho do registo | Chave do Registo | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Como posso mudar a localização do cache para o agente MARS?

1. Executar este comando num pedido de comando elevado para parar o motor de reserva:

    ```Net stop obengine```
2. Se tiver configurado a cópia de segurança do Estado do Sistema, abra `"CBSSBVol_<ID>"`a Gestão do Disco e desmonte o disco com nomes no formato .
3. Por padrão, a pasta de risco está localizada em`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Copie `\Scratch` toda a pasta para uma unidade diferente que tenha espaço suficiente. Certifique-se de que o conteúdo é copiado, não movido.
5. Atualize as seguintes entradas de registo com o caminho da pasta de risco recém-movida.

    | Caminho do registo | Chave do Registo | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta de risco* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta de risco* |

6. Reiniciar o motor de reserva num pedido de comando elevado:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Faça um reforço a pedido. Depois de a cópia de segurança terminar com sucesso utilizando a nova localização, pode remover a pasta de cache original.

### <a name="where-should-the-cache-folder-be-located"></a>Onde deve ser localizada a pasta cache?

Não são recomendados os seguintes locais para a pasta cache:

* Partilha de rede/suporte suovível: A pasta cache deve ser local para o servidor que precisa de ser apoiada através de cópias de segurança online. As localizações da rede ou os meios amovíveis como as unidades USB não são suportados.
* Volumes offline: A pasta cache deve estar online para cópia de segurança esperada usando o Agente de Backup Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Há algum atributo da pasta cache que não seja suportada?

Os atributos ou respetivas combinações seguintes não são suportadas para a pasta de cache:

* Encriptados
* Duplicados eliminados
* Comprimidos
* Disperso
* Ponto de Reanálise

A pasta cache e os metadados VHD não têm os atributos necessários para o agente de backup Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existe uma maneira de ajustar a largura de banda utilizada para a cópia de segurança?

Sim, pode utilizar a opção **Change Properties** no agente MARS para ajustar a largura de banda e o tempo. [Saiba mais](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Restauro

### <a name="manage"></a>Gerir

**Posso recuperar se me esqueci da minha frase de passe?**
O agente de backup Azure requer uma palavra-passe (que forneceu durante o registo) para desencriptar os dados de backup durante a restauração. Reveja os cenários abaixo para compreender as suas opções para lidar com uma frase de passe perdida:

| Máquina Original <br> *(máquina de origem onde foram tomadas cópias de segurança)* | Frase-passe | Opções Disponíveis |
| --- | --- | --- |
| Disponível |Perdido |Se a sua máquina original (onde foram tomadas as cópias de segurança) estiver disponível e ainda estiver registada com o mesmo cofre dos Serviços de Recuperação, então pode regenerar a frase-passe seguindo estes [passos](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase).  |
| Perdido |Perdido |Não é possível recuperar os dados ou dados não disponíveis |

Considere as seguintes condições:

* Se desinstalar e voltar a registar o agente na mesma máquina original com
  * Mesma frase de *passe,* então poderá restaurar os seus dados de back-up.
  * *Uma frase-passe diferente,* então não será capaz de restaurar os seus dados de back-up.
* Se instalar o agente numa *máquina diferente* com
  * *A mesma frase -passe* (usada na máquina original), então poderá restaurar os seus dados de back-up.
  * *Palavra-passe diferente,* não será capaz de restaurar os seus dados de back-up.
* Se a sua máquina original for corrompida (impedindo-o de regenerar a palavra-passe através da consola MARS), mas pode restaurar ou aceder à pasta original de risco utilizada pelo agente MARS, então poderá restaurar (se se esqueceu da palavra-passe). Para obter mais assistência, contacte o Apoio ao Cliente.

**Como me recupero se perdi a minha máquina original (para onde foram tiradas cópias de segurança)?**

Se tiver a mesma frase de passe (que forneceu durante o registo) da máquina original, então pode restaurar os dados de back-up a uma máquina alternativa. Reveja os cenários abaixo para entender as suas opções de restauro.

| Máquina Original | Frase-passe | Opções Disponíveis |
| --- | --- | --- |
| Perdido |Disponível |Pode instalar e registar o agente MARS noutra máquina com a mesma frase que forneceu durante o registo da máquina original. Escolha **Opção** > de Recuperação**Outro local** para realizar o seu restauro. Para mais informações, consulte este [artigo.](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine)
| Perdido |Perdido |Não é possível recuperar os dados ou dados não disponíveis |

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontece se eu cancelar um trabalho de restauro em curso?

Se um trabalho de restauro em curso for cancelado, o processo de restauro para. Todos os ficheiros restaurados antes da estadia de cancelamento em destino configurado (localização original ou alternativa), sem quaisquer recuos.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>O agente MARS faz cópia de segurança e restaura os ACLs definidos em ficheiros, pastas e volumes?

* O agente MARS apoia os ACLs definidos em ficheiros, pastas e volumes
* Para a opção de recuperação de volume, o agente MARS oferece uma opção para não restaurar permissões ACL para o ficheiro ou pasta que está a ser recuperado
* Para a opção individual de recuperação de ficheiros e pastas, o agente MARS irá restaurar com permissões ACL (não há opção de não restaurar a ACL).

## <a name="next-steps"></a>Passos seguintes

[Aprenda](tutorial-backup-windows-server-to-azure.md) a fazer o apoio a uma máquina do Windows.
