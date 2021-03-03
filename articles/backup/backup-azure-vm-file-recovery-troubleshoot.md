---
title: Resolução de problemas Recuperação de ficheiros Azure VM
description: Problemas de resolução de problemas ao recuperar ficheiros e pastas de uma cópia de segurança do Azure VM.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700308"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Problemas de resolução de problemas na recuperação de ficheiros de uma cópia de segurança do Azure VM

Este artigo fornece etapas de resolução de problemas que podem ajudá-lo a resolver problemas na recuperação de ficheiros e pastas a partir de uma cópia de segurança da máquina virtual Azure (VM).

## <a name="common-error-messages"></a>Mensagens de erro comuns

Esta secção fornece passos para resolver mensagens de erro que possa ver.

### <a name="exception-caught-while-connecting-to-target"></a>"Exceção apanhada durante a ligação ao alvo"

**Causa possível**: O script não consegue aceder ao ponto de recuperação.

**Ação recomendada**: Para resolver este problema, siga os passos listados no [script, mas a ligação falhou](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"O alvo já foi registado através de uma sessão iSCSI"

**Causa possível**: O script já estava executado na mesma máquina e as unidades foram anexadas.

**Ação recomendada**: Os volumes do ponto de recuperação já foram ligados. Não podem ser montados com as mesmas letras de unidade do VM original. Navegue pelos volumes disponíveis no Explorador de Ficheiros.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Este script é inválido porque os discos foram desmontados via portal/excederam o limite de 12 horas. Faça o download de um novo script a partir do portal"

**Causa possível**: Os discos foram desmontados do portal ou o prazo de 12 horas foi ultrapassado.

**Ação recomendada:** 12 horas após o download do script, torna-se inválido e não pode ser executado. Vá ao portal e, em seguida, baixe um novo script para continuar com a recuperação de ficheiros.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>Não é possível carregar o módulo iscsi_tcp (ou) módulo iscsi_tcp não encontrado

**Ação recomendada**: Para resolver este problema, siga os passos nas [transferências do script com sucesso, mas não é executado](#the-script-downloads-successfully-but-fails-to-run).

## <a name="common-problems"></a>Problemas comuns

Esta secção fornece passos para resolver problemas comuns que pode experimentar ao descarregar e executar o script para recuperação de ficheiros.

### <a name="you-cant-download-the-script"></a>Não pode descarregar o script

1. Certifique-se de que tem as [permissões necessárias para descarregar o script](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script).
1. Verifique a ligação com os IPs do alvo Azure. Executar um dos seguintes comandos a partir de um pedido de comando elevado:

   `nslookup download.microsoft.com`

    ou

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>O script descarrega com sucesso, mas não funciona

Quando executar o script Python para a recuperação do nível de item (ILR) no SUSE Linux Enterprise Server 12 SP4, falha com o erro "iscsi_tcp módulo não pode ser carregado" ou "não iscsi_tcp_module encontrado".

**Causa possível**: O módulo ILR utiliza **iscsi_tcp** para estabelecer uma ligação TCP ao serviço de cópia de segurança. Como parte do desbloqueio SLES 12 SP4, a SUSE removeu **iscsi_tcp** do pacote open-iscsi, pelo que a operação ILR falha.

**Ação recomendada**: A execução do script de recuperação de ficheiros não é suportada em SUSE 12 SP4 VMs. Experimente a operação de restauro numa versão mais antiga do SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>O script é executado, mas a ligação ao alvo iSCSI falhou

Pode ver uma mensagem de erro "Exceção apanhada durante a ligação ao alvo".

1. Certifique-se de que a máquina onde o script é executado satisfaz os [requisitos de acesso](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script).
1. Verifique a ligação com os IPs do alvo Azure. Executar um dos seguintes comandos a partir de um pedido de comando elevado:

   `nslookup download.microsoft.com`

   ou

   `ping download.microsoft.com`
1. Garantir o acesso à porta de saída iSCSI 3260.
1. Verifique se há uma firewall ou NSG bloqueando o tráfego para IPs alvo Azure ou URLs de serviço de recuperação.
1. Certifique-se de que o seu software antivírus não bloqueia a execução do script.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Está ligado ao ponto de recuperação, mas os discos não estavam ligados.

Resolva este problema seguindo os passos para o seu sistema operativo.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Recuperação do Ficheiro Windows falha no servidor com piscinas de armazenamento

Quando executar o script pela primeira vez no Windows Server 2012 R2 e Windows Server 2016 (com piscinas de armazenamento), o pool de armazenamento pode ser anexado ao VM apenas para leitura.

>[!Tip]
> Certifique-se de que tem a [máquina certa para executar o script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Para resolver este problema, atribua manualmente o acesso de leitura-escrita à piscina de armazenamento e anexe os discos virtuais:

1. Aceda a **relatórios de**  >  **ficheiros e serviços de armazenamento** de  >    >  **serviços de armazenamento.**

   ![Screenshot mostrando opções de Storage Pools.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Na janela **da Piscina de Armazenamento,** clique com o botão direito na piscina de armazenamento disponível e selecione **set Read-Write Access**.

   ![Screenshot mostrando opções de clique direito para uma bobina de armazenamento.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Depois de atribuída a piscina de armazenamento, o acesso à leitura-escrita, clique à direita na secção **Discos Virtuais** e, em seguida, selecione Attach **Virtual Disk**.

   ![Screenshot mostrando opções de clique direito para um disco virtual.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>A recuperação do Ficheiro Linux não consegue montar automaticamente porque o disco não contém volumes

Durante a recuperação de ficheiros, o serviço de cópia de segurança deteta volumes e montagem automática. No entanto, se os discos com suporte têm divisórias cruas, esses discos não estão montados automaticamente e não se pode ver o disco de dados para recuperação.

Para resolver este problema, vá a [recuperar ficheiros da cópia de segurança da máquina virtual Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>A recuperação do ficheiro Linux falha porque o SISTEMA não conseguiu identificar o sistema de ficheiros

Quando executar o script de recuperação de ficheiros, o disco de dados não se anexa. Vê-se um erro de "As seguintes divisões não foram montadas uma vez que o SISTEMA não conseguiu identificar o sistema de ficheiros".

Para resolver este problema, verifique se o volume está encriptado com uma aplicação de terceiros. Se estiver encriptado, o disco ou vM não aparecerão como encriptados no portal.

1. Inscreva-se no VM apoiado e execute este comando:

   `lsblk -f`

   ![Screenshot mostrando os resultados do comando para listar dispositivos de bloqueio.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Verifique o sistema de ficheiros e a encriptação. Se o volume estiver encriptado, a recuperação do ficheiro não é suportada. Saiba mais na [matriz de suporte para a cópia de segurança Azure VM](./backup-support-matrix-iaas.md#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Os discos estão ligados, mas os volumes não estão montados

Resolva este problema seguindo os passos para o seu sistema operativo.

#### <a name="windows"></a>Windows

Quando executar o script de recuperação de ficheiros para o Windows, vê uma mensagem de "0 volumes de recuperação anexados". No entanto, os discos são descobertos na consola de gestão de discos.

**Causa possível**: Quando anexou volumes através do iSCSI, alguns volumes que foram detetados ficaram offline. Quando o canal iSCSI comunica entre o VM e o serviço, deteta estes volumes e os coloca on-line, mas não estão montados.

   ![Screenshot mostrando os volumes de recuperação 0 ligados.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Para identificar e resolver esta questão, execute os seguintes passos:

>[!Tip]
>Certifique-se de que tem a [máquina certa para executar o script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. Na janela **cmd,** executar **o diskmgmt** para abrir a **Gestão do Disco**.
1. Procure por mais discos. No exemplo seguinte, **o Disco 2** é um disco adicional.

   ![Screenshot da janela de Gestão de Discos com disco adicional.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Clique com o botão direito **Novo Volume** e, em seguida, selecione Change Drive Letter **and Paths**.

   ![Screenshot mostrando as opções de clique direito no disco adicional.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Na janela **Change Drive Letter ou Path,** selecione **Atribua a seguinte letra de unidade,** atribua uma unidade disponível e, em seguida, selecione **OK**.

   ![Screenshot da janela Change Drive Letter ou Path.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Abra o File Explorer para ver a unidade que escolheu e explore os ficheiros.

#### <a name="linux"></a>Linux

>[!Tip]
>Certifique-se de que tem a [máquina certa para executar o script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Se o Linux VM protegido utilizar matrizes LVM ou RAID, siga os passos em [recuperar ficheiros da cópia de segurança da máquina virtual Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Não é possível copiar os ficheiros de volumes montados

A cópia pode falhar com o erro "0x80070780: O ficheiro não pode ser acedido pelo sistema." 

Verifique se o servidor de origem tem deduplicação de disco ativada. Se o fizer, certifique-se de que o servidor de restauro também tem deduplica ativação ativada nas unidades. Pode deixar a desduplicação não configurada para não desduplicar as unidades do servidor de restauro.

## <a name="next-steps"></a>Passos seguintes

- [Recuperar ficheiros e pastas da cópia de segurança da máquina virtual Azure](backup-azure-restore-files-from-vm.md)