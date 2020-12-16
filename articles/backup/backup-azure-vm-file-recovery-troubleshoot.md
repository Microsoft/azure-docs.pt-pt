---
title: Resolução de problemas Recuperação de ficheiros Azure VM
description: Problemas de resolução de problemas ao recuperar ficheiros e pastas de uma cópia de segurança do Azure VM.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605297"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Problemas de resolução de problemas na recuperação de ficheiros do backup Azure VM

Este artigo fornece medidas de resolução de problemas que podem ajudá-lo a resolver erros de Backup Azure relacionados com problemas ao recuperar ficheiros e pastas de uma cópia de segurança do Azure VM.

## <a name="common-error-messages"></a>Mensagens de erro comuns

### <a name="exception-caught-while-connecting-to-target"></a>Exceção apanhada ao ligar-se ao alvo

**Causa possível**: O script não consegue aceder ao ponto de recuperação.

**Ação recomendada**: Verifique se a máquina preenche todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>O alvo já foi registado através de uma sessão iSCSI

**Causa possível**: O script já foi executado na mesma máquina e as unidades foram anexadas.

**Ação recomendada**: Os volumes do ponto de recuperação já foram ligados. Não podem ser montadas com as mesmas letras de acionamento do VM original. Navegue por todos os volumes disponíveis no explorador de ficheiros.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Este script é inválido porque os discos foram desmontados via portal/excederam o limite de 12 horas. Faça o download de um novo script a partir do portal

**Causa possível**: Os discos foram desmontados do portal ou o limite de 12 horas foi ultrapassado.

**Ação recomendada**: O script é inválido após 12 horas a partir do momento em que foi descarregado e não pode ser executado. Visite o portal e descarregue um novo script para continuar com a recuperação de ficheiros.

## <a name="troubleshooting-common-scenarios"></a>Resolução de problemas de cenários comuns

Esta secção fornece passos para resolver problemas que pode encontrar ao descarregar e executar o script para recuperação de ficheiros.

### <a name="cant-download-the-script"></a>Não é possível descarregar o script

Ação recomendada:

1. Certifique-se de que tem todas as [permissões necessárias para descarregar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Garantir que há conectividade com os IPs-alvo do Azure

Para verificar a ligação, executar um dos seguintes comandos a partir de uma solicitação de comando elevada.

`nslookup download.microsoft.com`

ou

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>O script descarrega com sucesso, mas não funciona

#### <a name="for-sles-12-sp4-os-linux"></a>Para SLES 12 SP4 OS (Linux)

**Erro**: iscsi_tcp_module não encontrado

**Causa possível**: Ao executar o script Python para a recuperação do nível de item (ILR) na versão 12sp4 do SUSE Linux OS, falha com o erro **_iscsi_tcp módulo não pode ser carregado_* _.

O módulo ILR utiliza _ *iscsi_tcp** para estabelecer uma ligação TCP ao serviço de cópia de segurança. Como parte do lançamento 12SP4, a SUSE removeu **iscsi_tcp** do pacote open-iscsi, pelo que a operação ILR falha.

**Ação recomendada**: A execução do script de recuperação de ficheiros não é suportada em VMs SUSE 12SP4. Experimente a operação de restauro numa versão mais antiga do SUSE 12SP4.

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>O script é executado, mas a ligação ao alvo iSCSI falhou

**Erro**: Exceção apanhada durante a ligação ao alvo

1. Certifique-se de que a máquina onde o script é executado satisfaz todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Verifique se há conectividade com os IPs-alvo do Azure.
Para verificar a ligação, executar um dos seguintes comandos a partir de uma solicitação de comando elevada.

   `nslookup download.microsoft.com`

   ou

   `ping download.microsoft.com`
1. Certifique-se de que há acesso à porta de saída iSCSI 3260.
1. Certifique-se de que não existe firewall ou NSG que esteja a bloquear o tráfego para IPs alvo Azure ou URLs de serviço de recuperação.
1. Verifique se o antivírus está bloqueando a execução do script.

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>Ligado ao ponto de recuperação, mas os discos não ficaram ligados

Certifique-se de que tem a [máquina certa para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

#### <a name="on-a-windows-vm"></a>Em um VM windows

**A piscina de armazenamento em VM fica anexada no modo apenas de leitura**: No Windows 2012 R2 e Windows 2016 (com piscinas de armazenamento), quando executa o script pela primeira vez, a piscina de armazenamento anexada ao VM pode entrar num estado apenas de leitura.

Para resolver este problema, precisamos definir manualmente o Read-Write Acesso à piscina de armazenamento pela primeira vez e anexar os discos virtuais. Siga os passos abaixo:

1. Desema estada Read-Write acesso.

   Navegue para **o servidor Gestor** de  >  **arquivos e serviços de armazenamento**  >  **volumes** de armazenamento  >  **piscinas** de armazenamento .

   ![Armazenamento do Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Na janela **Storage Pool,** clique com o botão direito na piscina de armazenamento disponível e selecione a opção **'set Read-Write Access'.**

   ![Escrita de leitura de armazenamento de windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Depois de atribuída a piscina de armazenamento com acesso de leitura/escrita, anexe o disco virtual.

   Navegue para **o Servidor Manager UI**. Na secção **Discos Virtuais** > clique à direita para selecionar a opção **'Anexar o disco virtual'.**

   ![Gestor de servidores Virtual Disk](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>Em um Linux VM

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>A recuperação de ficheiros falha na montagem automática porque o disco não contém volumes

Durante a recuperação de ficheiros, o serviço de backup deteta volumes e peças automáticas. No entanto, se os discos com suporte têm divisórias cruas, então esses discos não são montados automaticamente e não se pode ver o disco de dados para recuperação.

Para resolver este problema, siga os passos documentados neste [artigo.](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>O SISTEMA não conseguiu identificar o sistema de ficheiros que fez com que a recuperação do Ficheiro Linux falhasse enquanto montava discos

Durante a execução do script de recuperação de ficheiros, o disco de dados não se anexou com o seguinte erro:

"As seguintes divisórias não foram montadas uma vez que o SISTEMA não conseguiu identificar o sistema de ficheiros"

Para resolver este problema, verifique se o volume está encriptado com uma aplicação de terceiros. Se estiver encriptado, o disco ou vM não aparecerão como encriptados no portal.

1. Inscreva-se no VM de apoio e execute o comando:

   `*lsblk -f*`

   ![Disco sem volume](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Verifique o sistema de ficheiros e a encriptação.
3. Se o volume for encriptado, a recuperação do ficheiro não é suportada. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Os discos estão ligados, mas incapazes de montar volumes

- Certifique-se de que tem a [máquina certa para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

#### <a name="on-windows-vms"></a>Em VMs windows

Enquanto executa o script de recuperação de ficheiros para o Windows, há uma mensagem de que ***0 volumes estão ligados** _. No entanto, os discos são descobertos na consola de gestão de discos. Ao anexar volumes através do iSCSI, alguns volumes que foram detetados vão para um estado offline. Quando o canal iSCSI comunica entre o VM e o serviço, deteta estes volumes e os coloca on-line, mas não estão montados.

   ![Disco não ligado](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Para identificar e resolver esta questão, execute os seguintes passos:

1. Vá a _ *Gestão de Discos** executando o comando **diskmgmt** na janela CMD.
1. Verifique se são apresentados discos adicionais. No exemplo seguinte, o Disco 2 é um disco adicional.

   ![Gestão de discos0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Clique com o botão direito no **novo volume** e selecione Change Drive Letter **and Paths**.

   ![Gestão de discos1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Na janela **'Adicionar letra de unidade ou caminho',** selecione **Atribua a seguinte letra de unidade** e atribua uma unidade disponível e selecione **OK**.

   ![Gestão de discos2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. A partir do explorador de ficheiros, veja a letra de unidade que escolheu e explore os ficheiros.

#### <a name="on-linux-vms"></a>Em Linux VMs

- Certifique-se de que tem a [máquina certa para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Se o Linux VM protegido utilizar matrizes LVM ou RAID, siga os passos indicados neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="cant-copy-the-files-from-mounted-volumes"></a>Não é possível copiar os ficheiros de volumes montados

A cópia pode falhar com o erro **0x80070780: O ficheiro não pode ser acedido pelo sistema.** Neste caso, verifique se o servidor de origem tem deduplicação de disco ativada. Em seguida, certifique-se de que o servidor de restauro também tem deduplica ativação ativada nas unidades. Pode deixar o papel de desduplica não configurado para não desduplicar as unidades do servidor de restauro.

## <a name="next-steps"></a>Passos seguintes

- [Recuperar ficheiros e pastas da cópia de segurança da máquina virtual Azure](backup-azure-restore-files-from-vm.md)
