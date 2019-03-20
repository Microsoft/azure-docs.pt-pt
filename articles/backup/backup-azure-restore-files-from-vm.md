---
title: 'Cópia de segurança do Azure: Recuperar ficheiros e pastas a partir de uma cópia de segurança de VM do Azure'
description: Recuperar ficheiros a partir de um ponto de recuperação da máquina virtual do Azure
services: backup
author: pvrk
manager: shivamg
keywords: recuperação ao nível do item; recuperação de ficheiros de cópia de segurança de VM do Azure; restaurar ficheiros a partir da VM do Azure
ms.service: backup
ms.topic: conceptual
ms.date: 3/01/2019
ms.author: pullabhk
ms.openlocfilehash: 25c78e4050f1f5fdb02500fa1f206f6cf05e7134
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111961"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar ficheiros a partir de cópia de segurança da máquina virtual do Azure

Cópia de segurança do Azure fornece a capacidade de restaurar [máquinas de virtuais (VMs) do Azure e discos](./backup-azure-arm-restore-vms.md) de cópias de segurança de VM do Azure, também conhecido como pontos de recuperação. Este artigo explica como recuperar ficheiros e pastas a partir de uma cópia de segurança de VM do Azure. Restaurar ficheiros e pastas só está disponível para VMs do Azure implementadas com o modelo do Resource Manager e protegidos para um cofre dos serviços de recuperação.

> [!Note]
> Esta funcionalidade está disponível para VMs do Azure implementadas com o modelo do Resource Manager e protegidos para um cofre dos serviços de recuperação.
> Recuperação de ficheiros a partir de uma cópia de segurança VM encriptada não é suportada.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar os volume e copia os ficheiros

Para restaurar ficheiros ou pastas do ponto de recuperação, vá para a máquina virtual e escolha o ponto de recuperação pretendido.

1. Inicie sessão para o [portal do Azure](https://portal.Azure.com) e no painel esquerdo, clique em **máquinas virtuais**. Na lista de máquinas virtuais, selecione a máquina virtual para abrir o dashboard de nessa máquina virtual.

2. No menu da máquina virtual, clique em **cópia de segurança** para abrir o dashboard de cópia de segurança.

    ![Item aberto de cópia de segurança do cofre dos serviços de recuperação](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. No menu do dashboard de cópia de segurança, clique em **recuperação de ficheiros**.

    ![Botão de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    O **recuperação de ficheiros** é aberto o menu.

    ![Menu de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Do **selecione o ponto de recuperação** menu pendente, selecione o ponto de recuperação que contém os arquivos desejados. Por predefinição, o ponto de recuperação mais recente já está selecionado.

5. Para baixar o software usado para copiar ficheiros a partir do ponto de recuperação, clique em **transferir executável** (para o Windows Azure VM) ou **transferir Script** (para VM do Linux do Azure, um script de python é gerado).

    ![Palavra-passe gerada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure transfere o ficheiro executável ou script no computador local.

    ![Transferir a mensagem para o ficheiro executável ou script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o executável ou script como um administrador, é aconselhável que guarde a transferência para o seu computador.

6. O ficheiro executável ou script é protegidos por senha e requer uma palavra-passe. Na **recuperação de ficheiros** menu, clique no botão de cópia para carregar a palavra-passe na memória.

    ![Palavra-passe gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. A partir da localização de transferência (normalmente, a pasta de transferências), com o botão direito do executável ou script e executá-lo com as credenciais de administrador. Quando lhe for pedido, escreva a palavra-passe ou cole a palavra-passe da memória e prima Enter. Depois de introduzir a palavra-passe válida, o script liga ao ponto de recuperação.

    ![Menu de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Se executar o script num computador com acesso restrito, certifique-se de que existe o acesso a:

    - download.microsoft.com
    - URLs do serviço de recuperação (nome da área geográfica refere-se para a região onde reside o Cofre de serviço de recuperação)
        - <https://pod01-rec2.geo-name.backup.windowsazure.com> (Para as geos públicas do Azure)
        - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (Para o Azure na China)
        - <https://pod01-rec2.geo-name.backup.windowsazure.us> (Para o Azure Governo dos E.U.A.)
        - <https://pod01-rec2.geo-name.backup.windowsazure.de> (Para o Azure Alemanha)
    - porta de saída 3260

> [!Note]
> 
> * O nome de ficheiro de script transferido terá o **geo-name** a ser preenchido no URL. Para, por exemplo: O nome do script transferido começa com \'VMname\'\_\'geoname\'_\'GUID\', como ContosoVM_wcus_12345678...<br><br>
> * O URL seria "<https://pod01-rec2.wcus.backup.windowsazure.com>"


   Para o Linux, o script requer componentes de 'open-iscsi' e "lshw" para ligar ao ponto de recuperação. Se os componentes não existirem no computador em que o script é executado, o script pede-lhe permissão para instalar os componentes. Forneça o consentimento para instalar os componentes necessários.

   O acesso ao download.microsoft.com é necessário para transferir componentes utilizados para criar um canal seguro entre a máquina em que o script é executado e os dados no ponto de recuperação.

   Pode executar o script em qualquer máquina que tem o sistema de operativo mesmo (ou compatível) da VM de cópia de segurança. Consulte a [tabela de sistema operacional compatível](backup-azure-restore-files-from-vm.md#system-requirements) para sistemas operacionais compatíveis. Se a máquina virtual do Azure protegida utilizar espaços de armazenamento do Windows (para VMs do Windows Azure) ou matrizes LVM/RAID (para VMs do Linux), não é possível executar o executável ou script na mesma máquina virtual. Em vez disso, execute o ficheiro executável ou script em qualquer outra máquina com um sistema operativo compatível.

### <a name="identifying-volumes"></a>Identificação de Volumes

#### <a name="for-windows"></a>Para Windows

Ao executar o executável, o sistema operativo monta os novos volumes e atribui letras de unidade. Pode utilizar o Explorador do Windows ou o Explorador de ficheiros para procurar essas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original, no entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original era "disco de dados (e:`\`)", que o volume pode ser anexado no computador local que o "disco de dados ('Qualquer letra':`\`). Procure por meio de todos os volumes mencionados na saída do script até encontrar as ficheiros/pastas.  

   ![Menu de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Para Linux

No Linux, os volumes de ponto de recuperação são instalados para a pasta onde o script é executado. Os discos anexados, volumes e os caminhos de montagem correspondentes são apresentados em conformidade. Montagem esses caminhos estão visíveis para utilizadores que têm acesso de nível de raiz. Procure os volumes mencionados na saída do script.

  ![Menu de recuperação de ficheiro do Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Fechar a ligação

Depois de identificar os ficheiros e copiá-los para uma localização de armazenamento local, remova (ou desmontar) as unidades adicionais. Desmontar as unidades, sobre o **recuperação de ficheiros** menu no portal do Azure, clique em **desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Assim que os discos foram desmontados, receberá uma mensagem. Pode demorar alguns minutos para que a ligação atualizar para que pode remover os discos.

No Linux, depois da ligação ao ponto de recuperação é severed, o sistema operacional não remove os correspondentes caminhos de montagem automaticamente. Os caminhos de montagem existem como volumes "órfãos" e se encontram visíveis, mas lançar um erro quando acesso/gravação as arquivos. Eles podem ser removidos manualmente. O script, quando executado, identifica quaisquer esses volumes existente a partir de pontos de recuperação anterior e limpa-los com o consentimento.

## <a name="special-configurations"></a>Configurações especiais

### <a name="dynamic-disks"></a>Discos dinâmicos

Se a VM do Azure protegido tiver volumes com uma ou ambas as seguintes características, não é possível executar o script executável na mesma VM.

- Volumes que abrangem vários discos (abrangido e repartidos volumes)
- Tolerante a falhas (volumes espelhados e RAID-5) de volumes em discos dinâmicos

Em vez disso, execute o script de executável em qualquer outro computador com um sistema operativo compatível.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Espaços de armazenamento do Windows é uma tecnologia do Windows que permite-lhe virtualizar o armazenamento. Com espaços de armazenamento do Windows pode agrupar os discos de norma da indústria em agrupamentos de armazenamento. Em seguida, irá utilizar o espaço disponível nesses agrupamentos de armazenamento para criar discos virtuais, chamados espaços de armazenamento.

Se a VM do Azure protegidos utilizar espaços de armazenamento do Windows, não é possível executar o script executável na mesma VM. Em vez disso, execute o script de executável em qualquer outra máquina com um sistema operativo compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

No Linux, o Gestor de volumes lógicos (LVM) e/ou matrizes RAID de software é utilizado para gerir volumes lógicos ao longo de vários discos. Se a VM protegida de Linux utilizar LVM e/ou matrizes RAID, não é possível executar o script na mesma VM. Em vez disso, execute o script em qualquer outra máquina com um sistema operacional compatível e que suporta o sistema de ficheiros da VM protegida.

A seguinte saída de script apresenta os discos LVM e/ou matrizes RAID e os volumes com o tipo de partição.

   ![Menu de saída de LVM do Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Para transformar estas partições online, execute os comandos nas seções a seguir.

#### <a name="for-lvm-partitions"></a>Para partições LVM

Para listar os nomes de grupos de volume num volume físico.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Para listar todos os volumes de lógicos, nomes e os caminhos num grupo de volume.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Para montar os volumes lógicos para o caminho da sua preferência.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Para matrizes RAID

O comando seguinte apresenta detalhes sobre todos os discos de raid.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 O disco RAID relevante é apresentado como `/dev/mdm/<RAID array name in the protected VM>`

Se o disco RAID tem volumes físicos, utilize o comando de montagem.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outro LVM configurado no mesmo, em seguida, utilize o procedimento anterior para partições LVM mas utilizar o nome do volume em vez do nome do disco de RAID

## <a name="system-requirements"></a>Requisitos de sistema

### <a name="for-windows-os"></a>Para o sistema operacional do Windows

A tabela seguinte mostra a compatibilidade entre sistemas de operativos de servidor e computador. Ao recuperar ficheiros, não é possível restaurar os ficheiros para uma versão de sistema operativo anterior ou futura. Por exemplo, não é possível restaurar um ficheiro de uma VM do Windows Server 2016 para o Windows Server 2012 ou um computador com Windows 8. Pode restaurar ficheiros a partir de uma VM para o mesmo sistema operativo de servidor ou para o sistema operacional cliente compatível.

|SO do servidor | SO de cliente compatível  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Para o SO Linux

No Linux, o sistema operativo do computador utilizado para restaurar os ficheiros têm de suportar o sistema de ficheiros da máquina virtual protegida. Ao selecionar um computador para executar o script, certifique-se de que o computador tem um sistema operacional compatível e utiliza uma das versões identificadas na tabela a seguir:

|SO Linux | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e acima |
| CentOS | 6.5 e acima  |
| RHEL | 6.7 e acima |
| Debian | 7 e acima |
| Oracle Linux | 6.4 e acima |
| SLES | 12 e acima |
| openSUSE | 42.2 e acima |

> [!Note]
> Descobrimos alguns problemas da execução do script de recuperação de ficheiros em máquinas com o sistema operacional do SLES 12 SP4. Investigar com a equipa SLES.
> Atualmente, em execução o script de recuperação de ficheiros está a funcionar em máquinas com as versões do SLES 12 SP2 e o sistema operacional do SP3.
>

O script também requer o Python e o bash componentes para executar e ligar com segurança para o ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| Bash | 4 e superior |
| python | 2.6.6 e acima  |
| TLS | 1.2 deve ser suportada  |

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas ao recuperar ficheiros a partir de máquinas virtuais, veja a tabela seguinte para obter informações adicionais.

| Mensagem de erro / cenário | Causa provável | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída do exe: *Ligar para o destino de exceção* |Script não é capaz de aceder ao ponto de recuperação    | Verifique se o computador cumpre os requisitos de acesso anterior. |  
| Saída do exe: *O destino já foi registrado através de uma sessão de iSCSI.* | O script já foi executado no mesmo computador e as unidades que tenham sido vinculadas | Os volumes de ponto de recuperação já tenham sido vinculados. NÃO pode ser montadas com as mesmas letras de unidade da original VM. Procurar por meio de todos os volumes disponíveis no Explorador de ficheiros para o ficheiro |
| Saída do exe: *Este script é inválido porque os discos têm sido desmontados através do portal/12-RH de excedeu o limite. Transfira um novo script a partir do portal.* |    Os discos têm sido desmontados partir do portal ou excedida o limite de 12 horas | Nesse exe particular agora é inválido e não pode ser executado. Se pretender aceder aos ficheiros dessa recuperação ponto anterior no tempo, visite o portal para um novo. exe|
| No computador onde é executado no exe: Os novos volumes não são desmontados depois de clica no botão de desmontagem | O Iniciador do iSCSI na máquina é não estabelecer ligação com o destino de responder/atualizar e manter a cache. |  Depois de clicar em **desmontar**, aguarde alguns minutos. Se a novos volumes não são desmontados, procure os todos os volumes. Todos os volumes de navegação na força o iniciador para atualizar a ligação e o volume é desmontado com uma mensagem de erro que o disco não está disponível.|
| Saída do exe: Script é executado com êxito, mas "Novos volumes anexados" não são apresentados na saída do script |    Este é um erro transitório    | Os volumes seriam tenha sido vinculados já. Abra o Explorador para procurar. Se estiver a utilizar o mesmo computador para executar scripts sempre, considere reiniciar o computador e deve ser apresentada a lista nas execuções subsequentes exe. |
| Linux específico: Não é possível ver os volumes pretendidos | O sistema operacional da máquina em que o script é executado talvez não reconheça o sistema de ficheiros subjacente da VM protegido | Verifique se o ponto de recuperação está consistente com ficheiros ou consistentes de falhas. Se o ficheiro consistente, execute o script em outro computador cujo sistema operacional reconhece o sistema de ficheiros da VM protegido |
| Específico do Windows: Não é possível ver os volumes pretendidos | Os discos tenha sido vinculados, mas os volumes não foram configurados | A partir do ecrã de gestão do disco, identifique os discos adicionais relacionadas com o ponto de recuperação. Se qualquer um destes discos estiverem offline no Estado tente torná-los online clicando no disco e clique em "Online"|

## <a name="security"></a>Segurança

Esta secção descreve as várias medidas de segurança decorridas para a implementação da recuperação de ficheiros de cópias de segurança de VM do Azure, de forma a que os utilizadores estão cientes dos aspectos de segurança do recurso.

### <a name="feature-flow"></a>Fluxo de funcionalidade

Esta funcionalidade foi criada para acessar os dados VM sem a necessidade de restaurar a VM ou VM inteira discos e nos passos mínimos. Acesso a dados da VM é fornecido por um script (que monta o volume de recuperação quando executado, conforme mostrado abaixo) e, por conseguinte, de forma a base de todas as implementações de segurança

  ![Fluxo de funcionalidade de segurança](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementações de segurança

#### <a name="select-recovery-point-who-can-generate-script"></a>Selecione o ponto de recuperação (que pode gerar o script)

O script que fornece acesso a dados da VM, é importante regulam a quem pode gerá-lo em primeiro lugar. Uma tem de iniciar sessão no portal do Azure e deve ser [RBAC autorizado](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) ser capaz de gerar o script.

Recuperação de ficheiros tem o mesmo nível de autorização conforme necessário para o restauro de discos e de restauro de VMS. Em outras palavras, apenas os utilizadores autorizados pode ver os dados da VM pode gerar o script.

O script gerado é assinado com certificado oficial da Microsoft para o serviço de cópia de segurança do Azure. Qualquer falsificação no script significa que a assinatura é quebrada e qualquer tentativa de executar o script é realçada como um risco potencial com o sistema operacional.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Volume de recuperação de montagem (que pode executar o script)

Apenas o administrador pode executar o script e deve executá-lo no modo elevado. O script apenas executa um conjunto gerado de passos e não aceita entrada a partir de qualquer origem externa.

Para executar o script, um requer uma palavra-passe que é mostrada apenas para o utilizador autorizado no momento da geração do script no portal do Azure ou PowerShell/CLI. Trata-se para se certificar de que o utilizador autorizado que transfere o script também é responsável por executar o script.

#### <a name="browse-files-and-folders"></a>Procurar ficheiros e pastas

Para procurar ficheiros e pastas, o script utiliza o iniciador iSCSI na máquina e ligar ao ponto de recuperação que esteja configurado como um destino iSCSI. Aqui uma pode assumir cenários onde um está tentando imitar/spoof seja/todos os componentes.

Podemos usar o mecanismo de autenticação mútuo do CHAP para que cada componente autentica o outro. Isso significa que é extremamente difícil para um iniciador falso para se ligar ao destino iSCSI e um destino falso para ser ligado à máquina em que o script é executado.

O fluxo de dados entre o serviço de recuperação e a máquina está protegido com a criação de um túnel seguro de SSL através de TCP ([TLS 1.2 deve ser suportada](#system-requirements) na máquina em que o script é executado)

Qualquer ficheiro de controlo de acesso à lista (ACL) presente na principal/cópia de segurança VM são mantidas no sistema de ficheiros instalado também.

O script fornece acesso só de leitura para um ponto de recuperação e é válido por apenas 12 horas. Se o usuário deseja remover o acesso anteriormente, em seguida, inicie sessão no Portal/PowerShell/CLI do Azure e executar o **desmontar discos** para esse ponto de recuperação específico. O script irá ser invalidado imediatamente.
