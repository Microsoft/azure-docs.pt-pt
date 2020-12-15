---
title: Resolução de problemas Recuperação de ficheiros Azure VM
description: Problemas de resolução de problemas específicos para recuperar ficheiros e pastas de uma cópia de segurança do Azure VM.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513410"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Problemas de resolução de problemas na recuperação de ficheiros do backup Azure VM

Este artigo fornece medidas de resolução de problemas que podem ajudá-lo a resolver erros de Backup Azure relacionados com problemas específicos para recuperar ficheiros e pastas a partir de uma cópia de segurança do Azure VM. 

## <a name="exception-caught-while-connecting-to-target"></a>Exceção apanhada ao ligar-se ao alvo

Causa possível: O script não consegue aceder ao ponto de recuperação Ação recomendada: Verifique se a máquina preenche todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>O alvo já foi registado através de uma sessão iSCSI

Causa possível: O script já foi executado na mesma máquina e as unidades foram anexadas.
Ação recomendada: Os volumes do ponto de recuperação já foram ligados. Não podem ser montadas com as mesmas letras de acionamento do VM original. Navegue por todos os volumes disponíveis no explorador de ficheiros.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Este script é inválido porque os discos foram desmontados via portal/excederam o limite de 12 horas. Faça o download de um novo script a partir do portal

Causa possível: Os discos foram desmontados do portal ou o limite de 12 horas foi ultrapassado.
Ação recomendada: O script é inválido após 12 horas a partir do momento em que foi descarregado e não pode ser executado. Visite o portal e descarregue um novo script para prosseguir com a recuperação de ficheiros.

## <a name="troubleshooting-common-issues"></a>Resolução de problemas comuns

Esta secção fornece passos para resolver problemas encontrados durante o download e execução do script para recuperação de ficheiros.

## <a name="cannot-download-the-script"></a>Não é possível descarregar o script

Ação recomendada:

1. Certifique-se de que tem todas as [permissões necessárias para descarregar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Certifique-se de que existe conectividade com o Azure target IP(s).
Para verificar a ligação, executar o seguinte comando a partir de uma pronta de comando elevada. 
    - *nslookup download.microsoft.com* ou
    - *ping download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>O script descarrega com sucesso, mas não funciona

### <a name="for-sles-12-sp4-os-linux"></a>Para SLES 12 SP4 OS (Linux)

Erro: não iscsi_tcp_module encontrado

Causa possível: Ao executar o script Python para a recuperação do nível de item na versão 12sp4 do SUSE Linux OS, falha com um erro ***iscsi_tcp módulo não pode ser carregado** _. O módulo ILR utiliza o "iscsi_tcp" para estabelecer a ligação TCP ao serviço de backup, sUSE como parte da libertação 12SP4 removida iscsi_tcp do pacote open-iscsi, daí que a operação ILR falhe.

Ação recomendada: A execução do script de recuperação de ficheiros não é suportada em VMs SUSE 12SP4, tente a operação de restauro numa versão mais antiga do SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>O script é executado, mas a ligação ao alvo iSCSI falhou

Erro: Exceção apanhada durante a ligação ao Target

1. Certifique-se de que a máquina onde o script é executado satisfaz todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Verifique se a conectividade com o Azure target IP(s)
Para verificar a ligação, executar o seguinte comando a partir de uma pronta de comando elevada. 
    - _nslookup download.microsoft.com* ou<br>
    - *ping download.microsoft.com*
3. Certifique-se de que existe acesso à porta de saída iSCSI 3260.
4. Certifique-se de que não existe firewall/NSG que esteja a bloquear o tráfego para IP(s) alvo de Azure ou URLs de serviço de recuperação.
5. Verifique se o antivírus está bloqueando a execução do script.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Ligado ao ponto de recuperação, mas os discos não ficaram ligados

Certifique-se de que tem a [máquina certa para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>No Windows VM

**Piscina de armazenamento em VM fica anexada no modo apenas de leitura** Para o Windows 2012 R2 e Windows 2016 (com piscina de armazenamento), enquanto executa o script pela primeira vez, o pool de armazenamento ligado ao VM pode entrar em estado de leitura.

Para resolver este problema, precisamos definir manualmente o Read-Write Acesso à piscina de armazenamento pela primeira vez e anexar os discos virtuais, seguir os passos abaixo:

1. Desema estada Read-Write acesso.
Navegue para o Gestor de > serviços de arquivo e armazenamento > volumes > piscinas de armazenamento.

   ![Armazenamento do Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. Na janela **storage Pool,** clique com o botão direito na piscina de armazenamento disponível e selecione a opção **De Acesso Read-Write.**

   ![Escrita de leitura de armazenamento de windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. Depois de o armazenamento ser atribuído com acesso de leitura/escrita, temos de anexar o disco virtual.
Navegue para **o Servidor Manager UI**, sob a secção Discos Virtuais > clique direito para selecionar a opção **Dedem o Disco Virtual.**

   ![Gestor de servidores Virtual Disk](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Em Linux VM

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>A recuperação do ficheiro não consegue montar automaticamente porque o disco não contém volumes

Durante a recuperação de ficheiros, o serviço de cópia de segurança deteta volumes e suportes automáticos. No entanto, se os discos com suporte têm divisórias cruas, então esses discos não são montados automaticamente e não podemos ver o disco de dados para recuperação.

Para resolver este problema, siga os passos documentados neste [artigo.](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>O SISTEMA não conseguiu identificar o sistema de ficheiros que fez com que a recuperação do Ficheiro Linux falhasse enquanto monta discos

Enquanto executava o disco de dados do script de recuperação de ficheiros não se anexava com o erro abaixo: *As seguintes divisórias não foram montadas uma vez que o SISTEMA de Sistema de Ficheiros não conseguiu identificar o sistema de ficheiros*

- Para resolver este problema, verifique se o Volume está encriptado com uma aplicação de terceiros. Se encriptado, então o disco ou VM não aparecerão como encriptados no portal.
1. Iniciar sessão no VM apoiado e executar o comando: *lsblk -f*<br>

   ![Disco sem volume](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Verifique o sistema de ficheiros e a encriptação.
3. Se o volume estiver encriptado, a recuperação do ficheiro não é suportada. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Os discos estão ligados, mas incapazes de montar volumes

- Certifique-se de que tem a [máquina certa para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>Nas janelas VM

Enquanto executa o script de recuperação de ficheiros para as janelas existe uma mensagem de que ***0 volumes estão ligados** _, mas os discos são descobertos na consola de gestão de discos. Ao anexar volumes através do iSCSI, alguns volumes que são detetados vão para o estado offline. Quando o canal iSCSI comunica entre o VM e o serviço, deteta estes volumes e os coloca online, mas não estão montados.

   ![Disco não ligado](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Para identificar e resolver esta questão, execute os seguintes passos:

1. Vá a _ *Gestão de Discos** executando o comando **diskmgmt** na janela CMD.
2. Verifique se são apresentados discos adicionais. No exemplo abaixo, o Disco 2 é um disco adicional.

   ![Gestão de discos0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Clique no **volume** e selecione **Change Drive Letter and Paths**.

   ![Gestão de discos1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. Na janela **'Adicionar letra de unidade ou caminho'** selecione **Atribua a seguinte letra de unidade** e atribua uma unidade disponível e clique **OK**. 

   ![Gestão de discos2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. A partir do explorador de ficheiros, pode ver a Unidade.

   ![Gestão de discos3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. Os novos volumes devem ser anexados.  

   ![Disco não montado](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. No explorador de ficheiros, os novos volumes serão visíveis após a repetição do script.

   ![Disco não montagem1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Disco não montando2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Em Linux VMs 

- Certifique-se de que tem a [máquina certa para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Se o Linux VM protegido utilizar matrizes LVM e/ou RAID, siga os passos indicados neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Não é possível copiar os ficheiros de Volumes montados

A cópia pode falhar com um erro 0x80070780: O ficheiro não pode ser acedido pelo sistema. Neste caso, verifique se o servidor de origem tem deduplicação de disco ativada. Em seguida, certifique-se de que o servidor de restauro também tem deduplica ativação ativada nas unidades. Pode deixar o papel de desduplica não configurado para que não desduplicar as unidades do servidor de restauro.
