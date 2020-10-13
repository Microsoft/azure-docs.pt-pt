---
title: Recuperar ficheiros e pastas a partir de backup Azure VM
description: Neste artigo, aprenda a recuperar ficheiros e pastas a partir de um ponto de recuperação de máquina virtual Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: 3f26f761b3d683be71f7f6d900d91dd432ceefc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292970"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar ficheiros da cópia de segurança da máquina virtual Azure

O Azure Backup fornece a capacidade de restaurar [máquinas virtuais Azure (VMs) e discos](./backup-azure-arm-restore-vms.md) de backups Azure VM, também conhecidos como pontos de recuperação. Este artigo explica como recuperar ficheiros e pastas a partir de uma cópia de segurança do Azure VM. Restaurar ficheiros e pastas só está disponível para VMs Azure implantados utilizando o modelo Gestor de Recursos e protegidos para um cofre dos Serviços de Recuperação.

> [!NOTE]
> Esta funcionalidade está disponível para VMs Azure implantados usando o modelo gestor de recursos e protegidos para um cofre de Serviços de Recuperação.
> A recuperação de ficheiros de uma cópia de segurança encriptada do VM não é suportada.
>

## <a name="mount-the-volume-and-copy-files"></a>Monte os ficheiros de volume e cópia

Para restaurar ficheiros ou pastas do ponto de recuperação, vá à máquina virtual e escolha o ponto de recuperação pretendido.

1. Inscreva-se no [portal Azure](https://portal.Azure.com) e no painel esquerdo, selecione **máquinas Virtuais**. A partir da lista de máquinas virtuais, selecione a máquina virtual para abrir o painel de instrumentos da máquina virtual.

2. No menu da máquina virtual, selecione **Backup** para abrir o painel de cópias de segurança.

    ![Artigo de backup do cofre dos Serviços de Recuperação Aberta](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. No menu do painel de instrumentos de cópia de segurança, selecione **A Recuperação de Ficheiros**.

    ![Selecione recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    O menu **de recuperação de** ficheiros abre.

    ![Menu de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. A partir do menu de ponto de **recuperação Select,** selecione o ponto de recuperação que detém os ficheiros que deseja. Por padrão, o último ponto de recuperação já está selecionado.

5. Para descarregar o software utilizado para copiar ficheiros do ponto de recuperação, selecione **Download Executable** (para Windows Azure VMs) ou **Download Script** (para Linux Azure VMs, é gerado um script python).

    ![Baixar Executável](./media/backup-azure-restore-files-from-vm/download-executable.png)

    O Azure descarrega o executável ou o script para o computador local.

    ![baixar mensagem para o executável ou script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o executável ou script como administrador, sugere-se que guarde o ficheiro descarregado para o seu computador.

6. O executável ou script está protegido por palavra-passe e requer uma senha. No menu **'Recuperação de ficheiros',** selecione o botão de cópia para carregar a palavra-passe na memória.

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. [Certifique-se de que tem a máquina certa](#selecting-the-right-machine-to-run-the-script) para executar o script. Se a máquina certa for a mesma máquina onde descarregou o script, então pode continuar na secção de descarregamento. A partir da localização de descarregamento (normalmente a pasta *Downloads),* clique com o botão direito para executar o executável ou script e execute-o com credenciais de Administrador. Quando solicitado, digite a palavra-passe ou cole a palavra-passe da memória e prima **Enter**. Uma vez que a palavra-passe válida é inserida, o script conecta-se ao ponto de recuperação.

    ![Saída executável](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Para máquinas Linux, um script python é gerado. É necessário descarregar o script e copiá-lo para o servidor Linux relevante/compatível. Poderá ter de modificar as permissões para a executar com ```chmod +x <python file name>``` . Em seguida, executar o arquivo python com ```./<python file name>``` .

Consulte a secção [de requisitos de Acesso](#access-requirements) para se certificar de que o script é executado com sucesso.

### <a name="identifying-volumes"></a>Identificar volumes

#### <a name="for-windows"></a>Para Windows

Quando executa o executável, o sistema operativo monta os novos volumes e atribui letras de acionamento. Pode utilizar o Windows Explorer ou o File Explorer para navegar nessas unidades. As letras de acionamento atribuídas aos volumes podem não ser as mesmas que a máquina virtual original. No entanto, o nome do volume é preservado. Por exemplo, se o volume da máquina virtual original for "Disco de Dados `\` (E:)", esse volume pode ser ligado no computador local como "Disco de Dados ('Qualquer letra': `\` ). Navegue por todos os volumes mencionados na saída do script até encontrar os seus ficheiros ou pasta.  

   ![Volumes de recuperação anexados](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Para Linux

No Linux, os volumes do ponto de recuperação são montados na pasta onde o script é executado. Os discos, volumes e os caminhos de montagem correspondentes são mostrados em conformidade. Estes caminhos de montagem são visíveis para os utilizadores terem acesso ao nível da raiz. Navegue pelos volumes mencionados na saída do script.

  ![Menu de recuperação de ficheiros Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Fechar a ligação

Depois de identificar os ficheiros e copiá-los para um local de armazenamento, remova (ou desmonte) as unidades adicionais. Para desmontar as unidades, no menu **De recuperação de ficheiros** no portal Azure, selecione **Unmount Disks**.

![Desmonte discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Uma vez que os discos tenham sido desmontados, recebes uma mensagem. Pode levar alguns minutos para a ligação ser atualizada para que possa remover os discos.

No Linux, após a ligação ao ponto de recuperação ser cortada, o SO não remove automaticamente os caminhos de montagem correspondentes. Os caminhos de montagem existem como volumes "órfãos" e são visíveis, mas lançam um erro quando acedem/escrevem os ficheiros. Podem ser removidos manualmente. O script, quando executado, identifica quaisquer volumes existentes a partir de quaisquer pontos de recuperação anteriores e limpa-os mediante consentimento.

> [!NOTE]
> Certifique-se de que a ligação está fechada depois de restaurados os ficheiros necessários. Isto é importante, especialmente no cenário em que a máquina em que o script é executado também está configurado para cópia de segurança. Se a ligação ainda estiver aberta, a cópia de segurança subsequente poderá falhar com o erro "UserErrorUnableToOpenMount". Isto acontece porque as unidades/volumes montados são assumidos como disponíveis e quando acedidos podem falhar porque o armazenamento subjacente, ou seja, o servidor alvo iSCSI pode não estar disponível. A limpeza da ligação removerá estas unidades/volumes e para que não estejam disponíveis durante a cópia de segurança.

## <a name="selecting-the-right-machine-to-run-the-script"></a>Selecionando a máquina certa para executar o script

Se o script for descarregado com sucesso, o próximo passo é verificar se a máquina em que planeia executar o script é a máquina certa. Seguem-se os requisitos a preencher na máquina.

### <a name="original-backed-up-machine-versus-another-machine"></a>Máquina original apoiada contra outra máquina

1. Se a máquina de apoio for um VM de disco grande - isto é, o número de discos é superior a 16 discos ou cada disco é superior a 4 TB, então o script **deve ser executado em outra máquina** e estes requisitos têm de ser [cumpridos.](#file-recovery-from-virtual-machine-backups-having-large-disks)
1. Mesmo que a máquina de apoio não seja um VM de disco grande, [nestes cenários](#special-configurations) o script não pode ser executado no mesmo VM apoiado.

### <a name="os-requirements-on-the-machine"></a>Requisitos de SO na máquina

A máquina onde o script precisa de ser executado deve satisfazer [estes requisitos de SO](#system-requirements).

### <a name="access-requirements-for-the-machine"></a>Requisitos de acesso à máquina

A máquina onde o script precisa de ser executado deve satisfazer [estes requisitos de acesso](#access-requirements).

## <a name="special-configurations"></a>Configurações especiais

### <a name="dynamic-disks"></a>Discos dinâmicos

Se o Azure VM protegido tiver volumes com uma ou ambas as seguintes características, não pode executar o script executável no mesmo VM.

- Volumes que abrangem vários discos (volumes de riscas e listras)
- Volumes tolerantes a falhas (volumes espelhados e RAID-5) em discos dinâmicos

Em vez disso, execute o script executável em qualquer outro computador com um sistema operativo compatível.

### <a name="windows-storage-spaces"></a>Espaços de Armazenamento do Windows

Windows Storage Spaces é uma tecnologia Windows que permite virtualizar o armazenamento. Com espaços de armazenamento do Windows, pode agrupar discos padrão da indústria em piscinas de armazenamento. Em seguida, você usa o espaço disponível nessas piscinas de armazenamento para criar discos virtuais, chamados espaços de armazenamento.

Se o Azure VM protegido utilizar espaços de armazenamento do Windows, não pode executar o script executável no mesmo VM. Em vez disso, execute o script executável em qualquer outra máquina com um sistema operativo compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

No Linux, o Gestor de Volume Lógico (LVM) e/ou o software RAID Arrays são utilizados para gerir volumes lógicos em vários discos. Se o Linux VM protegido utilizar matrizes LVM e/ou RAID, não pode executar o script no mesmo VM. Em vez disso, execute o script em qualquer outra máquina com um SO compatível e que suporte o sistema de ficheiros do VM protegido.

A saída seguinte do script exibe os discos LVM e/ou RAID Arrays e os volumes com o tipo de partição.

   ![Menu de saída Linux LVM](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Para colocar estas divisórias on-line, executar os comandos nas seguintes secções.

#### <a name="for-lvm-partitions"></a>Para divisórias LVM

Uma vez executado o script, as divisórias LVM são montadas no volume físico(s)/disco(s) especificado na saída do script. O processo é

1. Obtenha a lista única de nomes de grupos de volume dos volumes físicos ou discos
2. Em seguida, listar os volumes lógicos nesses grupos de volume
3. Em seguida, monte os volumes lógicos para um caminho desejado.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Listando nomes de grupos de volume de volumes físicos

Para listar os nomes do grupo de volume:

```bash
pvs -o +vguuid
```

Este comando listará todos os volumes físicos (incluindo os presentes antes de executar o script), os nomes correspondentes do grupo de volume e os IDs exclusivos do grupo de volume (UUIDs). Uma saída de amostra do comando é mostrada abaixo.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

A primeira coluna (PV) mostra o volume físico, as colunas subsequentes mostram o nome do grupo de volume relevante, formato, atributos, tamanho, espaço livre e o ID único do grupo de volume. A saída do comando mostra todos os volumes físicos. Consulte a saída do script e identifique os volumes relacionados com a cópia de segurança. No exemplo acima, a saída do script teria mostrado /dev/sdf e /dev/sdd. E assim, o grupo de volume *datavg_db* pertence ao script e o grupo de volume *Appvg_new* pertence à máquina. A ideia final é garantir que um nome único de grupo de volume deve ter um ID único.

###### <a name="duplicate-volume-groups"></a>Duplicados grupos de volume

Existem cenários em que os nomes do grupo de volume podem ter 2 UUIDs depois de executar o script. Significa que os nomes do grupo de volume na máquina onde o script é executado e no VM apoiado são os mesmos. Então precisamos renomear os grupos de volume de VMs apoiados. Veja o exemplo abaixo.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

A saída do guião teria mostrado /dev/sdg, /dev/sdh, /dev/sdm2 como anexado. Assim, os nomes VG correspondentes são Appvg_new e rootvg. Mas os mesmos nomes também estão presentes na lista de VG da máquina. Podemos verificar se um nome VG tem dois UUIDs.

Agora precisamos renomear nomes de VG para volumes baseados em scripts, por exemplo: /dev/sdg, /dev/sdh, /dev/sdm2. Para renomear o grupo de volume, use o seguinte comando

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Agora temos todos os nomes de VG com identificações únicas.

###### <a name="active-volume-groups"></a>Grupos de volume ativos

Certifique-se de que os grupos de Volume correspondentes aos volumes do script estão ativos. É utilizado o seguinte comando para exibir grupos de volume ativos. Verifique se os grupos de volume relacionados do script estão presentes nesta lista.

```bash
vgdisplay -a
```  

Caso contrário, ative o grupo de volume utilizando o seguinte comando.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Enumeração de volumes lógicos dentro dos grupos de volume

Assim que tivermos a lista única e ativa de VGs relacionados com o script, então os volumes lógicos presentes nesses grupos de volume podem ser listados usando o seguinte comando.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Este comando mostra o caminho de cada volume lógico como 'Caminho LV'.

##### <a name="mounting-logical-volumes"></a>Montagem de volumes lógicos

Para montar os volumes lógicos no caminho à sua escolha:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Não use "mount-a". Este comando monta todos os dispositivos descritos em '/etc/fstab'. Isto pode significar que os dispositivos duplicados podem ser montados. Os dados podem ser redirecionados para dispositivos criados por um script, que não persistem os dados, pelo que podem resultar na perda de dados.

#### <a name="for-raid-arrays"></a>Para matrizes RAID

O seguinte comando exibe detalhes sobre todos os discos de raid:

```bash
#!/bin/bash
mdadm –detail –scan
```

 O disco RAID relevante é apresentado como `/dev/mdm/<RAID array name in the protected VM>`

Utilize o comando de montagem se o disco RAID tiver volumes físicos:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outro LVM configurado nele, utilize o procedimento anterior para divisórias LVM, mas utilize o nome de volume no lugar do nome DO DISCO RAID.

## <a name="system-requirements"></a>Requisitos de sistema

### <a name="for-windows-os"></a>Para Windows OS

A tabela seguinte mostra a compatibilidade entre o servidor e os sistemas operativos do computador. Ao recuperar ficheiros, não é possível restaurar ficheiros numa versão anterior ou futura do sistema operativo. Por exemplo, não é possível restaurar um ficheiro de um VM do Windows Server 2016 para o Windows Server 2012 ou um computador Windows 8. Pode restaurar ficheiros de um VM para o mesmo sistema operativo do servidor ou para o sistema operativo do cliente compatível.

|Servidor OS | Cliente compatível SO  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Para Linux OS

No Linux, o SISTEMA do computador utilizado para restaurar ficheiros deve suportar o sistema de ficheiros da máquina virtual protegida. Ao selecionar um computador para executar o script, certifique-se de que o computador tem um SISTEMA compatível e utiliza uma das versões identificadas na tabela seguinte:

|Linux OS | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e superior |
| CentOS | 6.5 e superior  |
| RHEL | 6.7 e superior |
| Debian | 7 e acima |
| Oracle Linux | 6.4 e superior |
| SLES | 12 e superior |
| openSUSE | 42.2 e superior |

> [!NOTE]
> Encontrámos alguns problemas na execução do guião de recuperação de ficheiros em máquinas com SLES 12 SP4 OS e estamos a investigar com a equipa SLES.
> Atualmente, a execução do script de recuperação de ficheiros está a funcionar em máquinas com versões SLES 12 SP2 e SP3 OS.
>

O script também requer que os componentes Python e bash executem e conectem-se de forma segura ao ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| bash | 4 e acima |
| python | 2.6.6 e superior  |
| TLS | 1.2 deve ser apoiado  |

## <a name="access-requirements"></a>Requisitos de acesso

Se executar o script num computador com acesso restrito, certifique-se de que há acesso a:

- `download.microsoft.com`
- URLs do Serviço de Recuperação (GEO-NAME refere-se à região onde reside o cofre dos Serviços de Recuperação)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.com` (Para as regiões públicas de Azure)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.cn` (Para Azure China 21Vianet)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.us` (Para o Governo dos EUA)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.de` (Para a Azure Alemanha)
- Portas de saída 53 (DNS), 443, 3260

> [!NOTE]
>
> O ficheiro de script que descarregou no passo 5 [acima](#mount-the-volume-and-copy-files) terá o **nome geo-nome** no nome do ficheiro. Use esse **geo-nome** para preencher o URL. O nome do guião descarregado começará com: \' VMname \' \_ \' geoname \' _ \' GUID \' .<br><br>
> Assim, por exemplo, se o nome de ficheiro do script for *ContosoVM_wcus_12345678,* o **nome geo-nome** é *wcus* e o URL seria:<br> <https://pod01-rec2.wcus.backup.windowsazure.com>
>

Para o Linux, o script requer componentes 'open-iscsi' e 'ishw' para se ligar ao ponto de recuperação. Se os componentes não existirem no computador onde o script é executado, o script pede permissão para instalar os componentes. Fornecer consentimento para instalar os componentes necessários.

O acesso `download.microsoft.com` é necessário para descarregar componentes utilizados para construir um canal seguro entre a máquina onde o script é executado e os dados no ponto de recuperação.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Recuperação de ficheiros a partir de cópias de segurança da máquina virtual com discos grandes

Esta secção explica como realizar a recuperação de ficheiros a partir de cópias de segurança de máquinas Azure Virtual com mais de 16 discos ou cada tamanho do disco é superior a 4 TB.

Uma vez que o processo de recuperação de ficheiros anexa todos os discos da cópia de segurança, quando são utilizados um grande número de discos (>16) ou discos grandes (> 4 TB cada), recomenda-se o seguinte número de pontos de ação:

- Mantenha um servidor de restauro separado (Azure VM D2v3 VMs) para recuperação de ficheiros. Pode usá-lo apenas para recuperação de ficheiros e, em seguida, desligá-lo quando não for necessário. Restaurar a máquina original não é recomendado, uma vez que terá um impacto significativo no próprio VM.
- Em seguida, executar o script uma vez para verificar se a operação de recuperação do ficheiro é bem sucedida.
- Se o processo de recuperação do ficheiro estiver suspenso (os discos nunca são montados ou montados mas os volumes não aparecem), execute os seguintes passos.
  - Se o servidor de restauro for um VM do Windows:
    - Certifique-se de que o SISTEMA é WS 2012 ou superior.
    - Certifique-se de que as teclas de registo estão definidas como sugerido abaixo no servidor de restauro e certifique-se de reiniciar o servidor. O número ao lado do GUID pode variar entre 0001 e 0005. No exemplo seguinte, é 0004. Navegue pelo caminho chave do registo até à secção de parâmetros.

    ![Alterações das chaves do registo](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Se o servidor de restauro for um Linux VM:
  - No ficheiro /etc/iscsi/iscsid.conf, altere a definição de:
    - `node.conn[0].timeo.noop_out_timeout = 5`  Para `node.conn[0].timeo.noop_out_timeout = 30`
- Depois de fazer a mudança acima, volte a executar o guião. Com estas mudanças, é altamente provável que a recuperação do ficheiro tenha sucesso.
- Sempre que o utilizador descarrega um script, o Azure Backup inicia o processo de preparação do ponto de recuperação para download. Com discos grandes, este processo levará um tempo considerável. Se houver sucessivas explosões de pedidos, a preparação do alvo entrará numa espiral de descarregamento. Portanto, é recomendado fazer o download de um script do Portal/PowerShell/CLI, esperar 20-30 minutos (uma heurística) e depois executá-lo. Por esta altura, espera-se que o alvo esteja pronto para a ligação a partir do script.
- Após a recuperação do ficheiro, certifique-se de que volta ao portal e selecione **Discos Desmonte** para pontos de recuperação onde não foi capaz de montar volumes. Essencialmente, este passo irá limpar quaisquer processos/sessões existentes e aumentar a chance de recuperação.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas durante a recuperação de ficheiros das máquinas virtuais, verifique a seguinte tabela para obter informações adicionais.

| Mensagem de erro / Cenário | Causa Provável | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída Exe: *Exceção apanhada durante a ligação ao alvo* | O guião não é capaz de aceder ao ponto de recuperação    | Verifique se a máquina preenche os [requisitos de acesso anteriores](#access-requirements). |  
| Saída exe: *O alvo já foi registado através de uma sessão iSCSI.* | O guião já foi executado na mesma máquina e as unidades foram anexadas | Os volumes do ponto de recuperação já foram anexados. Não podem ser montadas com as mesmas letras de acionamento do VM original. Navegue por todos os volumes disponíveis no explorador de ficheiros para o seu ficheiro. |
| Saída exe: *Este script é inválido porque os discos foram desmontados via portal/excederam o limite de 12 horas. Faça o download de um novo script a partir do portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi ultrapassado | Este exe em particular é agora inválido e não pode ser executado. Se quiser aceder aos ficheiros desse ponto de recuperação no tempo, visite o portal para um novo exe.|
| Na máquina onde o exe é executado: Os novos volumes não são desmontados após o botão de desmontagem ser clicado | O iniciador iSCSI da máquina não está a responder/a refrescar a sua ligação ao alvo e a manter a cache. |  Depois de clicar em **Desmontagem,** aguarde alguns minutos. Se os novos volumes não forem desmontados, navegue por todos os volumes. A navegação de todos os volumes obriga o iniciador a atualizar a ligação, e o volume é desmontado com uma mensagem de erro de que o disco não está disponível.|
| Saída Exe: O script é executado com sucesso mas "Novos volumes anexados" não é exibido na saída do script |    Este é um erro transitório    | Os volumes já terão sido anexados. Abrir o Explorador para navegar. Se estiver sempre a utilizar a mesma máquina para executar scripts, considere reiniciar a máquina e a lista deve ser exibida nas execuções subsequentes do exe. |
| Linux específico: Não é capaz de ver os volumes desejados | O SO da máquina onde o script é executado pode não reconhecer o sistema de ficheiros subjacente do VM protegido | Verifique se o ponto de recuperação é consistente ou consistente com ficheiros. Se for consistente com o ficheiro, execute o script noutra máquina cujo SISTEMA de SEGURANÇA reconheça o sistema de ficheiros protegido do VM. |
| Windows específico: Não é capaz de ver os volumes desejados | Os discos podem ter sido anexados, mas os volumes não foram configurados | A partir do ecrã de gestão do disco, identifique os discos adicionais relacionados com o ponto de recuperação. Se algum destes discos estiver em estado offline, tente ingurá-los on-line clicando à direita no disco e selecione **Online**.|

## <a name="security"></a>Segurança

Esta secção discute as várias medidas de segurança tomadas para a implementação da recuperação de ficheiros a partir de backups da Azure VM.

### <a name="feature-flow"></a>Fluxo de recursos

Esta funcionalidade foi construída para aceder aos dados VM sem a necessidade de restaurar todos os discos VM ou VM e com o número mínimo de passos. O acesso aos dados VM é fornecido por um script (que monta o volume de recuperação quando executado como mostrado abaixo) e constitui a pedra angular de todas as implementações de segurança:

  ![Fluxo de recursos de segurança](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementações de segurança

#### <a name="select-recovery-point-who-can-generate-script"></a>Selecione ponto de recuperação (quem pode gerar script)

O script fornece acesso aos dados VM, por isso é importante regular quem pode gerar em primeiro lugar. Tem de entrar no portal Azure e ser [RBAC autorizado](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a gerar o script.

A recuperação de ficheiros necessita do mesmo nível de autorização necessário para a restauração de VM e restauro dos discos. Por outras palavras, apenas os utilizadores autorizados podem ver os dados VM podem gerar o script.

O script gerado é assinado com o certificado oficial da Microsoft para o serviço Azure Backup. Qualquer adulteração do script significa que a assinatura está quebrada, e qualquer tentativa de executar o script é destacada como um risco potencial pelo SO.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Volume de recuperação de montagem (que pode executar script)

Apenas um Administrador pode executar o script e deve ser executado em modo elevado. O script executa apenas um conjunto de passos pré-gerado e não aceita a entrada de qualquer fonte externa.

Para executar o script, é necessária uma palavra-passe que só é mostrada ao utilizador autorizado no momento da geração do script no portal Azure ou PowerShell/CLI. Isto é para garantir que o utilizador autorizado que descarrega o script também é responsável pela execução do script.

#### <a name="browse-files-and-folders"></a>Navegue em ficheiros e pastas

Para navegar em ficheiros e pastas, o script utiliza o iniciador iSCSI na máquina e conecta-se ao ponto de recuperação configurado como um alvo iSCSI. Aqui pode imaginar cenários em que se está a tentar imitar/falsificar qualquer um dos componentes.

Utilizamos um mecanismo de autenticação CHAP mútuo para que cada componente autenha o outro. Isto significa que é extremamente difícil para um iniciador falso ligar-se ao alvo iSCSI e para que um alvo falso seja ligado à máquina onde o script é executado.

O fluxo de dados entre o serviço de recuperação e a máquina está protegido através da construção de um túnel TLS seguro sobre o TCP[(TLS 1.2 deve ser suportado](#system-requirements) na máquina onde o script é executado).

Qualquer Lista de Controlo de Acesso de Ficheiros (ACL) presente no VM dos pais/apoiados também está preservada no sistema de ficheiros montado.

O script dá acesso apenas à leitura a um ponto de recuperação e é válido por apenas 12 horas. Se desejar remover o acesso mais cedo, inscreva-se no portal Azure/PowerShell/CLI e execute **discos desmontados** para esse ponto de recuperação específico. O guião será invalidado imediatamente.

## <a name="next-steps"></a>Passos seguintes

- Para qualquer problema durante a restauração de ficheiros, consulte a secção [de resolução de problemas](#troubleshooting)
- Saiba como [restaurar ficheiros via PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- Saiba como [restaurar ficheiros via Azure CLI](./tutorial-restore-files.md)
- Depois de o VM ser restaurado, aprenda a [gerir backups](./backup-azure-manage-vms.md)
