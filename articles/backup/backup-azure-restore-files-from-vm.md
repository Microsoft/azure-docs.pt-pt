---
title: Recuperar ficheiros e pastas da cópia de segurança Azure VM
description: Neste artigo, aprenda a recuperar ficheiros e pastas de um ponto de recuperação de máquinas virtuais Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4565929b5475e2348685fbec77b596b65ed73fd6
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114335"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar ficheiros da cópia de segurança da máquina virtual Azure

O Azure Backup fornece a capacidade de restaurar [as máquinas virtuais Azure (VMs) e os discos](./backup-azure-arm-restore-vms.md) de backups Azure VM, também conhecidos como pontos de recuperação. Este artigo explica como recuperar ficheiros e pastas de uma cópia de segurança Azure VM. Restaurar ficheiros e pastas está disponível apenas para VMs Azure implantados usando o modelo Gestor de Recursos e protegidos para um cofre de serviços de recuperação.

> [!NOTE]
> Esta funcionalidade está disponível para VMs Azure implantados utilizando o modelo De Gestor de Recursos e protegido para um cofre de Serviços de Recuperação.
> A recuperação do ficheiro de uma cópia de segurança VM encriptada não é suportada.
>

## <a name="mount-the-volume-and-copy-files"></a>Monte o volume e copie ficheiros

Para restaurar ficheiros ou pastas a partir do ponto de recuperação, vá à máquina virtual e escolha o ponto de recuperação desejado.

1. Inscreva-se no [portal Azure](https://portal.Azure.com) e no painel esquerdo, clique em **máquinas virtuais**. A partir da lista de máquinas virtuais, selecione a máquina virtual para abrir o painel de instrumentos da máquina virtual.

2. No menu da máquina virtual, clique em **Backup** para abrir o painel de backup.

    ![Item de backup do cofre do Open Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. No menu do painel de backup, clique em Recuperação de **Ficheiros**.

    ![Botão de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    O menu de recuperação de **ficheiros** abre.

    ![Menu de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. A partir do menu de entrega do ponto de **recuperação Select,** selecione o ponto de recuperação que detém os ficheiros que deseja. Por padrão, o último ponto de recuperação já está selecionado.

5. Para descarregar o software utilizado para copiar ficheiros a partir do ponto de recuperação, clique em **Download Executeable** (para VMs Windows Azure) ou **Download Script** (para VMs Linux Azure, é gerado um script python).

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    O Azure descarrega o executável ou script para o computador local.

    ![mensagem de descarregamento para o executável ou script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o executável ou o script como administrador, é sugerido que você salve o arquivo baixado em seu computador.

6. O executável ou script está protegido por palavra-passe e requer uma senha. No menu **'Recuperação** de Ficheiros', clique no botão de cópia para carregar a palavra-passe na memória.

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. A partir da localização de descarregamento (normalmente a pasta Downloads), clique no direito do executável ou do script e execute-o com credenciais do Administrador. Quando solicitado, digite a palavra-passe ou cola a palavra-passe a partir da memória e prima **Enter**. Uma vez introduzida a palavra-passe válida, o script liga-se ao ponto de recuperação.

    ![Menu de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Para as máquinas Linux, é gerado um guião python. É preciso descarregar o script e copiá-lo para o servidor Linux relevante/compatível. Poderá ter de modificar as permissões para executá-la com ```chmod +x <python file name>```. Em seguida, executar o ficheiro python com ```./<python file name>```.

Consulte a secção [de requisitos](#access-requirements) de Acesso para se certificar de que o script é executado com sucesso.

### <a name="identifying-volumes"></a>Identificar volumes

#### <a name="for-windows"></a>Para Windows

Quando executa o executável, o sistema operativo monta os novos volumes e atribui cartas de acionamento. Pode utilizar o Windows Explorer ou o File Explorer para navegar nessas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original. No entanto, o nome do volume é preservado. Por exemplo, se o volume da máquina virtual original for "Data Disk (E:`\`)", esse volume pode ser fixado no computador local como "Data Disk ('Qualquer letra':`\`). Navegue por todos os volumes mencionados na saída do script até encontrar os arquivos ou a pasta.  

   ![Menu de recuperação de ficheiros](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Para Linux

Em Linux, os volumes do ponto de recuperação são montados na pasta onde o script é executado. Os discos, volumes e os respetivos caminhos de montagem são apresentados em conformidade. Estes caminhos de montagem são visíveis para os utilizadores terem acesso ao nível da raiz. Navegue pelos volumes mencionados na saída do script.

  ![Menu de recuperação de ficheiros Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Fechar a ligação

Depois de identificar os ficheiros e copiá-los para um local de armazenamento local, remova (ou desmonte) as unidades adicionais. Para desmontar as unidades, no menu de recuperação de **ficheiros** no portal Azure, clique **em Discos Desmontar**.

![Discos desmontar](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Uma vez que os discos tenham sido desmontados, recebeuma mensagem. Pode levar alguns minutos para a ligação refrescar-se para que possa remover os discos.

Em Linux, após a ligação ao ponto de recuperação ser cortada, o SO não remove automaticamente os caminhos de montagem correspondentes. Os caminhos de montagem existem como volumes "órfãos" e são visíveis, mas geram um erro quando você acessa/grava os arquivos. Podem ser removidos manualmente. O script, quando executado, identifica quaisquer volumes existentes a partir de quaisquer pontos de recuperação anteriores e limpa-os após consentimento.

## <a name="special-configurations"></a>Configurações especiais

### <a name="dynamic-disks"></a>Discos dinâmicos

Se o Azure VM protegido tiver volumes com uma ou ambas as seguintes características, não pode executar o script executável no mesmo VM.

- Volumes que abrangem vários discos (volumes riscados e listrados)
- Volumes tolerantes a falhas (volumes espelhados e RAID-5) em discos dinâmicos

Em vez disso, execute o script executável em qualquer outro computador com um sistema operativo compatível.

### <a name="windows-storage-spaces"></a>Espaços de Armazenamento de Janelas

O Windows Storage Spaces é uma tecnologia Windows que permite virtualizar o armazenamento. Com espaços de armazenamento windows pode agrupar discos padrão da indústria em piscinas de armazenamento. Depois, utiliza-se o espaço disponível nessas piscinas de armazenamento para criar discos virtuais, chamados espaços de armazenamento.

Se o Azure VM protegido utilizar espaços de armazenamento do Windows, não pode executar o script executável no mesmo VM. Em vez disso, execute o script executável em qualquer outra máquina com um sistema operativo compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

Em Linux, o gestor de volumelógico (LVM) e/ou o software RAID Arrays são usados para gerir volumes lógicos em vários discos. Se o Linux VM protegido utilizar lvm e/ou RAID Arrays, não pode executar o script no mesmo VM. Em vez disso, execute o script em qualquer outra máquina com um SISTEMA compatível e que suporte o sistema de ficheiros do VM protegido.

A saída de script seguinte apresenta os discos LVM e/ou RAID Arrays e os volumes com o tipo de partição.

   ![Menu de saída Linux LVM](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Para colocar estas divisórias on-line, execute os comandos nas seguintes secções.

#### <a name="for-lvm-partitions"></a>Para divisórias LVM

Para listar os nomes de grupos de volumes em um volume físico:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Para listar todos os volumes lógicos, nomes e seus caminhos em um grupo de volumes:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command’s results>
```

Para montar os volumes lógicos no caminho de sua escolha:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Para arrays RAID

O comando a seguir exibe detalhes sobre todos os discos RAID:

```bash
#!/bin/bash
mdadm –detail –scan
```

 O disco RAID relevante é apresentado como `/dev/mdm/<RAID array name in the protected VM>`

Use o comando Mount se o disco RAID tiver volumes físicos:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outro LVM configurado, use o procedimento anterior para partições LVM, mas use o nome do volume no lugar do nome do disco RAID.

## <a name="system-requirements"></a>Requisitos de sistema

### <a name="for-windows-os"></a>Para windows OS

A tabela que se segue mostra a compatibilidade entre os sistemas operativos do servidor e do computador. Ao recuperar ficheiros, não é possível restaurar ficheiros numa versão anterior ou futura do sistema operativo. Por exemplo, não é possível restaurar um ficheiro de um VM Windows Server 2016 para o Windows Server 2012 ou um computador Windows 8. Pode restaurar ficheiros de um VM para o mesmo sistema operativo do servidor, ou para o sistema operativo compatível com o cliente.

|Servidor OS | Cliente compatível OS  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Para Linux OS

No Linux, o SISTEMA do computador utilizado para restaurar ficheiros deve suportar o sistema de ficheiros da máquina virtual protegida. Ao selecionar um computador para executar o script, certifique-se de que o computador tem um SISTEMA compatível e utiliza uma das versões identificadas na tabela seguinte:

|Linux OS | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e superior |
| CentOS | 6.5 e acima  |
| RHEL | 6.7 e acima |
| Debian | 7 e acima |
| Oracle Linux | 6.4 e acima |
| SLES | 12 e acima |
| openSUSE | 42.2 e acima |

> [!NOTE]
> Encontramos alguns problemas na execução do script de recuperação de arquivo em computadores com o sistema operacional SLES 12 SP4 e estamos investigando com a equipe do SLES.
> Atualmente, executar o script de recuperação de ficheiros está a trabalhar em máquinas com versões SLES 12 SP2 e SP3 OS.
>

O script também requer python e componentes de bater para executar e ligar de forma segura ao ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| bash | 4 e acima |
| python | 2.6.6 e superior  |
| TLS | 1.2 deve ser apoiado  |

## <a name="access-requirements"></a>Requisitos de acesso

Se executar o script num computador com acesso restrito, certifique-se de que há acesso a:

- `download.microsoft.com`
- URLs do serviço de recuperação (nome geográfico refere-se à região onde reside o cofre do serviço de recuperação)
  - <https://pod01-rec2.geo-name.backup.windowsazure.com> (Para geos públicos de Azure)
  - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (Para Azure China 21Vianet)
  - <https://pod01-rec2.geo-name.backup.windowsazure.us> (Para o Governo dos EUA de Azure)
  - <https://pod01-rec2.geo-name.backup.windowsazure.de> (Para a Alemanha Azure)
- porta de saída 3260

> [!NOTE]
>
> - O nome do ficheiro de script descarregado terá o **nome geo-nome** para ser preenchido no URL. Para o exame: O nome do script descarregado começa com \'Nome VM\'\_\'geonome\'_\'\'GUID , como *ContosoVM_wcus_12345678*
> - O URL seria <https://pod01-rec2.wcus.backup.windowsazure.com>"
>

Para o Linux, o guião requer componentes 'open-iscsi' e 'lshw' para se ligar ao ponto de recuperação. Se os componentes não existirem no computador em que o script é executado, o script solicitará permissão para instalar os componentes. Dê consentimento para instalar os componentes necessários.

O acesso ao `download.microsoft.com` é necessário para descarregar componentes utilizados para construir um canal seguro entre a máquina onde o script é executado e os dados no ponto de recuperação.

Pode executar o script em qualquer máquina que tenha o mesmo (ou compatível) sistema operativo que o VM de back-up. Consulte a [tabela OS compatível](backup-azure-restore-files-from-vm.md#system-requirements) para sistemas operativos compatíveis. Se a máquina virtual Azure protegida utilizar espaços de armazenamento windows (para VMs Windows Azure) ou LM/RAID Arrays (para VMs Linux), não pode executar o executável ou script na mesma máquina virtual. Em vez disso, execute o executável ou script em qualquer outra máquina com um sistema operativo compatível.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Recuperação de ficheiros de backups de máquinas virtuais com grandes discos

Esta seção explica como executar a recuperação de arquivos de backups de máquinas virtuais do Azure com mais de 16 discos e cada tamanho de disco é maior que 32 TB.

Uma vez que o processo de recuperação de ficheiros anexa todos os discos da cópia de segurança, quando são utilizados grandes discos (>16) ou discos grandes (> 32 TB cada), recomendam-se os seguintes pontos de ação:

- Mantenha um servidor de restauro separado (VMs Azure VM D2v3) para a recuperação do ficheiro. Você pode usar isso apenas para recuperação de arquivos e, em seguida, desligá-lo quando não for necessário. A restauração na máquina original não é recomendada, pois ela terá um impacto significativo na própria VM.
- Em seguida, executar o script uma vez para verificar se a operação de recuperação de ficheiros é bem sucedida.
- Se o processo de recuperação de arquivo for interrompido (os discos nunca são montados ou se estiverem montados, mas os volumes não aparecerem), execute as etapas a seguir.
  - Se o servidor de restauração for uma VM do Windows:
    - Verifique se o sistema operacional é WS 2012 ou superior.
    - Certifique-se de que as teclas de registo estão definidas como sugerido abaixo no servidor de restauro e certifique-se de reiniciar o servidor. O número ao lado do GUID pode variar de 0001-0005. No exemplo a seguir, é 0004. Navegue pelo caminho da chave do registo até à secção de parâmetros.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Se o servidor de restauração for uma VM do Linux:
  - No arquivo/etc/iSCSI/iscsid.conf, altere a configuração de:
    - nó.conn[0].timeo.noop_out_timeout = 5 ao nó.conn[0].timeo.noop_out_timeout = 30
- Depois de fazer a alteração acima, execute o script novamente. Com essas alterações, é altamente provável que a recuperação do arquivo seja bem sucedido.
- Cada vez que o utilizador descarrega um script, o Azure Backup inicia o processo de preparação do ponto de recuperação para download. Com discos grandes, esse processo levará um tempo considerável. Se houver sucessivas explosões de pedidos, a preparação do alvo entrará numa espiral de descarregamento. Portanto, é recomendável baixar um script do portal/PowerShell/CLI, aguardar 20-30 minutos (um heurístico) e, em seguida, executá-lo. Por esta altura, espera-se que o alvo esteja pronto para a ligação a partir do guião.
- Após a recuperação do ficheiro, certifique-se de que volta ao portal e clique em **discos Desmontar** para pontos de recuperação onde não foi capaz de montar volumes. Essencialmente, este passo irá limpar quaisquer processos/sessões existentes e aumentar a possibilidade de recuperação.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas ao recuperar ficheiros das máquinas virtuais, consulte a tabela seguinte para obter informações adicionais.

| Mensagem de erro / Cenário | Causa provável | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída exe: *Exceção apanhada ao ligar-se ao alvo* | O script não é capaz de acessar o ponto de recuperação    | Verifique se a máquina preenche os [requisitos de acesso anteriores](#access-requirements). |  
| Saída exe: O alvo já foi iniciado através de *uma sessão iSCSI.* | O guião já foi executado na mesma máquina e as unidades foram anexadas | Os volumes do ponto de recuperação já foram anexados. Não podem ser montados com as mesmas letras de acionamento do VM original. Navegue por todos os volumes disponíveis no explorador de arquivos para o arquivo. |
| Saída exe: *Este script é inválido porque os discos foram desmontados através do portal/excederam o limite de 12 horas. Descarregue um novo script do portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi excedido | Este exe em particular é agora inválido e não pode ser executado. Se você quiser acessar os arquivos desse ponto de recuperação no tempo, visite o portal para obter um novo exe.|
| No computador em que o exe é executado: os novos volumes não são desmontados depois que o botão de desmontagem é clicado | O iniciador iSCSI no computador não está respondendo/atualizando sua conexão com o destino e mantendo o cache. |  Depois de clicar em **Desmontar,** espere alguns minutos. Se os novos volumes não forem desmontados, procure todos os volumes. Procurar todos os volumes força o iniciador a atualizar a conexão e o volume é desmontado com uma mensagem de erro informando que o disco não está disponível.|
| Saída de exe: o script é executado com êxito, mas "novos volumes anexados" não é exibido na saída do script |    Este é um erro transitório    | Os volumes já estarão anexados. Open Explorer para navegar. Se você estiver usando o mesmo computador para executar scripts todas as vezes, considere reiniciar o computador e a lista deverá ser exibida nas execuções subsequentes do exe. |
| Específico linux: Incapaz de ver os volumes desejados | O Sistema operativo da máquina onde o script é executado pode não reconhecer o sistema de ficheiros subjacente do VM protegido | Verifique se o ponto de recuperação é consistente com falha ou consistente com o arquivo. Se estiver consistente com o arquivo, execute o script em outra máquina cujo sistema operacional reconheça o sistema de arquivos da VM protegida. |
| Windows específico: Não é capaz de ver os volumes desejados | Os discos podem ter sido anexados, mas os volumes não foram configurados | A partir do ecrã de gestão do disco, identifique os discos adicionais relacionados com o ponto de recuperação. Se algum destes discos estiver em estado offline, tente trazê-los on-line clicando no disco e clique em **Online**.|

## <a name="security"></a>Segurança

Esta seção aborda as várias medidas de segurança tomadas para a implementação da recuperação de arquivos de backups de VM do Azure.

### <a name="feature-flow"></a>Fluxo de recurso

Esse recurso foi criado para acessar os dados da VM sem a necessidade de restaurar toda a VM ou os discos de VM e com o número mínimo de etapas. O acesso aos dados da VM é fornecido por um script (que monta o volume de recuperação quando executado conforme mostrado abaixo) e ele forma a base de todas as implementações de segurança:

  ![Fluxo de funcionalidade de segurança](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementações de segurança

#### <a name="select-recovery-point-who-can-generate-script"></a>Selecione ponto de recuperação (que pode gerar script)

O script fornece acesso aos dados da VM, portanto, é importante regular quem pode gerá-lo em primeiro lugar. Você precisa entrar no portal Azure e ser [RBAC autorizado](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a gerar o script.

A recuperação de ficheiros necessita do mesmo nível de autorização necessário para a restauração do VM e restauro dos discos. Por outras palavras, apenas os utilizadores autorizados podem ver os dados vM podem gerar o script.

O script gerado é assinado com o certificado oficial da Microsoft para o serviço de backup do Azure. Qualquer violação no script significa que a assinatura está quebrada e qualquer tentativa de executar o script é realçada como um risco potencial pelo sistema operacional.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Volume de recuperação do monte (que pode executar script)

Somente um administrador pode executar o script e ele deve ser executado no modo elevado. O script executa apenas um conjunto de etapas gerado previamente e não aceita a entrada de qualquer fonte externa.

Para executar o script, é necessária uma senha que é mostrada apenas para o usuário autorizado no momento da geração de script no portal do Azure ou PowerShell/CLI. Isto é para garantir que o utilizador autorizado que descarrega o script também é responsável pela execução do script.

#### <a name="browse-files-and-folders"></a>Navegue em ficheiros e pastas

Para navegar em ficheiros e pastas, o script utiliza o iniciador iSCSI na máquina e liga-se ao ponto de recuperação que é configurado como um alvo iSCSI. Aqui você pode imaginar cenários em que um está tentando imitar/falsificar um ou todos os componentes.

Usamos um mecanismo de autenticação CHAP mútuo para que cada componente autentique o outro. Isso significa que é extremamente difícil para um iniciador falso se conectar ao destino iSCSI e para que um destino falso seja conectado à máquina onde o script é executado.

O fluxo de dados entre o serviço de recuperação e a máquina está protegido construindo um túnel SSL seguro sobre tCP[(TLS 1.2 deve ser suportado](#system-requirements) na máquina onde o script é executado).

Qualquer ACL (lista de controle de acesso) de arquivo presente na VM pai/com backup também é preservada no sistema de arquivos montado.

O script dá acesso apenas a leitura a um ponto de recuperação e é válido por apenas 12 horas. Se desejar remover o acesso mais cedo, assine no Portal Azure/PowerShell/CLI e execute **discos desmontados** para esse ponto de recuperação específico. O guião será invalidado imediatamente.

## <a name="next-steps"></a>Passos seguintes

- Para quaisquer problemas durante a restauração de ficheiros, consulte a secção [de resolução de problemas](#troubleshooting)
- Saiba como [restaurar ficheiros via Powershell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Saiba como [restaurar ficheiros via Azure CLI](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- Depois que a VM é restaurada, aprenda a [gerir backups](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
