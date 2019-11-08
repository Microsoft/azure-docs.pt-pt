---
title: 'Backup do Azure: recuperar arquivos e pastas do backup de VM do Azure'
description: Neste artigo, saiba como recuperar arquivos e pastas de um ponto de recuperação de máquina virtual do Azure.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: recuperação em nível de item; recuperação de arquivo do backup de VM do Azure; restaurar arquivos da VM do Azure
ms.service: backup
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: dacurwin
ms.openlocfilehash: c6b49e794011d915f8cd7b29e6317e80391f2675
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747380"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar arquivos do backup de máquina virtual do Azure

O backup do Azure fornece a capacidade de restaurar as [VMS (máquinas virtuais) do Azure e os discos](./backup-azure-arm-restore-vms.md) de backups de VM do Azure, também conhecidos como pontos de recuperação. Este artigo explica como recuperar arquivos e pastas de um backup de VM do Azure. A restauração de arquivos e pastas está disponível somente para VMs do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre dos serviços de recuperação.

> [!Note]
> Esse recurso está disponível para VMs do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre dos serviços de recuperação.
> Não há suporte para a recuperação de arquivo de um backup de VM criptografado.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar o volume e copiar os arquivos

Para restaurar arquivos ou pastas do ponto de recuperação, acesse a máquina virtual e escolha o ponto de recuperação desejado.

1. Entre no [portal do Azure](https://portal.Azure.com) e no painel esquerdo, clique em **máquinas virtuais**. Na lista de máquinas virtuais, selecione a máquina virtual para abrir o painel da máquina virtual.

2. No menu da máquina virtual, clique em **backup** para abrir o painel de backup.

    ![Abrir item de backup do cofre de serviços de recuperação](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. No menu painel de backup, clique em **recuperação de arquivo**.

    ![Botão recuperação de arquivo](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    O menu **recuperação de arquivo** é aberto.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. No menu suspenso **selecionar ponto de recuperação** , selecione o ponto de recuperação que contém os arquivos desejados. Por padrão, o ponto de recuperação mais recente já está selecionado.

5. Para baixar o software usado para copiar arquivos do ponto de recuperação, clique em **baixar executável** (para VM do Windows Azure) ou **baixar script** (para VM do Linux Azure, um script Python é gerado).

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    O Azure baixa o executável ou o script no computador local.

    ![baixar mensagem para o executável ou script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o executável ou o script como administrador, é recomendável que você salve o download em seu computador.

6. O executável ou o script é protegido por senha e requer uma senha. No menu **recuperação de arquivo** , clique no botão Copiar para carregar a senha na memória.

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. No local de download (geralmente a pasta downloads), clique com o botão direito do mouse no executável ou script e execute-o com as credenciais de administrador. Quando solicitado, digite a senha ou cole a senha da memória e pressione Enter. Depois que a senha válida for inserida, o script se conectará ao ponto de recuperação.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Se você executar o script em um computador com acesso restrito, verifique se há acesso a:

    - download.microsoft.com
    - URLs do serviço de recuperação (nome geográfico refere-se à região onde reside o cofre do serviço de recuperação)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.com (para áreas geográficas públicos do Azure)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.cn (para o Azure China 21Vianet)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.us (para o governo dos EUA do Azure)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.de (para Azure Alemanha)
    - porta de saída 3260

> [!Note]
>
> - O nome do arquivo de script baixado terá o **nome geográfico** a ser preenchido na URL. Por exemplo: o nome do script baixado começa com \'VMname\'\_\'geoname\'_\'GUID\', como ContosoVM_wcus_12345678....<br><br>
> - A URL seria "https:\//pod01-rec2.wcus.backup.windowsazure.com"

   Para o Linux, o script requer os componentes ' Open-iSCSI ' e ' lshw ' para se conectar ao ponto de recuperação. Se os componentes não existirem no computador em que o script é executado, o script solicitará permissão para instalar os componentes. Forneça consentimento para instalar os componentes necessários.

   O acesso ao download.microsoft.com é necessário para baixar os componentes usados para criar um canal seguro entre o computador em que o script é executado e os dados no ponto de recuperação.

   Você pode executar o script em qualquer computador que tenha o mesmo sistema operacional (ou compatível) que a VM de backup. Consulte a [tabela de so compatível](backup-azure-restore-files-from-vm.md#system-requirements) para sistemas operacionais compatíveis. Se a máquina virtual do Azure protegida usar espaços de armazenamento do Windows (para VMs do Windows Azure) ou matrizes LVM/RAID (para VMs do Linux), você não poderá executar o executável ou o script na mesma máquina virtual. Em vez disso, execute o executável ou o script em qualquer outra máquina com um sistema operacional compatível.

### <a name="identifying-volumes"></a>Identificando volumes

#### <a name="for-windows"></a>Para Windows

Quando você executa o executável, o sistema operacional monta os novos volumes e atribui letras de unidade. Você pode usar o Windows Explorer ou o explorador de arquivos para procurar essas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original, no entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original era "disco de dados (E:`\`)", esse volume pode ser anexado ao computador local como "disco de dados (' qualquer letra ':`\`). Navegue por todos os volumes mencionados na saída do script até encontrar seus arquivos/pasta.  

   ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Para Linux

No Linux, os volumes do ponto de recuperação são montados na pasta em que o script é executado. Os discos anexados, os volumes e os caminhos de montagem correspondentes são mostrados adequadamente. Esses caminhos de montagem são visíveis para os usuários que têm acesso no nível raiz. Navegue pelos volumes mencionados na saída do script.

  ![Menu de recuperação de arquivos do Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Fechando a conexão

Depois de identificar os arquivos e copiá-los para um local de armazenamento local, remova (ou desmonte) as unidades adicionais. Para desmontar as unidades, no menu **recuperação de arquivos** na portal do Azure, clique em **desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Depois que os discos forem desmontados, você receberá uma mensagem. Pode levar alguns minutos para que a conexão seja atualizada para que você possa remover os discos.

No Linux, depois que a conexão com o ponto de recuperação é severa, o sistema operacional não remove os caminhos de montagem correspondentes automaticamente. Os caminhos de montagem existem como volumes "órfãos" e são visíveis, mas geram um erro quando você acessa/grava os arquivos. Eles podem ser removidos manualmente. O script, quando executado, identifica esses volumes existentes a partir de quaisquer pontos de recuperação anteriores e os limpa após o consentimento.

## <a name="special-configurations"></a>Configurações especiais

### <a name="dynamic-disks"></a>Discos dinâmicos

Se a VM do Azure protegida tiver volumes com uma ou ambas as características a seguir, você não poderá executar o script executável na mesma VM.

- Volumes que abrangem vários discos (volumes estendidos e distribuídos)
- Volumes tolerantes a falhas (volumes espelhados e RAID-5) em discos dinâmicos

Em vez disso, execute o script executável em qualquer outro computador com um sistema operacional compatível.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Os espaços de armazenamento do Windows são uma tecnologia do Windows que permite virtualizar o armazenamento. Com os espaços de armazenamento do Windows, você pode agrupar discos padrão do setor em pools de armazenamento. Em seguida, use o espaço disponível nesses pools de armazenamento para criar discos virtuais, chamados de espaços de armazenamento.

Se a VM do Azure protegida usar espaços de armazenamento do Windows, você não poderá executar o script executável na mesma VM. Em vez disso, execute o script executável em qualquer outra máquina com um sistema operacional compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

No Linux, o LVM (Gerenciador de volume lógico) e/ou matrizes de RAID de software são usados para gerenciar volumes lógicos em vários discos. Se a VM do Linux protegida usar o LVM e/ou matrizes RAID, você não poderá executar o script na mesma VM. Em vez disso, execute o script em qualquer outro computador com um sistema operacional compatível e que ofereça suporte ao sistema de arquivos da VM protegida.

A saída de script a seguir exibe os discos LVM e/ou matrizes RAID e os volumes com o tipo de partição.

   ![Menu de saída do LVM do Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Para colocar essas partições online, execute os comandos nas seções a seguir.

#### <a name="for-lvm-partitions"></a>Para partições LVM

Para listar os nomes de grupos de volumes em um volume físico.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Para listar todos os volumes lógicos, nomes e seus caminhos em um grupo de volumes.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Para montar os volumes lógicos no caminho de sua escolha.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Para matrizes RAID

O comando a seguir exibe detalhes sobre todos os discos RAID.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 O disco RAID relevante é exibido como `/dev/mdm/<RAID array name in the protected VM>`

Use o comando Mount se o disco RAID tiver volumes físicos.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outro LVM configurado, use o procedimento anterior para partições LVM, mas use o nome do volume no lugar do nome do disco RAID

## <a name="system-requirements"></a>Requisitos de sistema

### <a name="for-windows-os"></a>Para o sistema operacional Windows

A tabela a seguir mostra a compatibilidade entre os sistemas operacionais de servidor e computador. Ao recuperar arquivos, você não pode restaurar arquivos para uma versão anterior ou futura do sistema operacional. Por exemplo, você não pode restaurar um arquivo de uma VM do Windows Server 2016 para o Windows Server 2012 ou um computador com Windows 8. Você pode restaurar arquivos de uma VM para o mesmo sistema operacional do servidor ou para o sistema operacional do cliente compatível.

|Sistema operacional do servidor | Sistema operacional cliente compatível  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Para o SO Linux

No Linux, o sistema operacional do computador usado para restaurar arquivos deve dar suporte ao sistema de arquivos da máquina virtual protegida. Ao selecionar um computador para executar o script, verifique se o computador tem um so compatível e usa uma das versões identificadas na tabela a seguir:

|SO Linux | Versões  |
| --------------- | ---- |
| Ubuntu | 12, 4 e acima |
| CentOS | 6,5 e acima  |
| RHEL | 6,7 e acima |
| Debian | 7 e acima |
| Oracle Linux | 6,4 e acima |
| SLES | 12 e acima |
| openSUSE | 42,2 e acima |

> [!Note]
> Encontramos alguns problemas na execução do script de recuperação de arquivo em computadores com o sistema operacional SLES 12 SP4. Investigando com a equipe do SLES.
> Atualmente, a execução do script de recuperação de arquivo está funcionando em computadores com versões do SLES 12 SP2 e do sistema operacional SP3.
>

O script também requer componentes Python e bash para executar e conectar-se com segurança ao ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| raso | 4 e acima |
| python | 2.6.6 e acima  |
| TLS | 1,2 deve ter suporte  |

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Recuperação de arquivos de backups de máquina virtual com discos grandes

Esta seção explica como executar a recuperação de arquivos de backups de máquinas virtuais do Azure cujo número de discos está > 16 e cada tamanho de disco é > 4 TB.

Como o processo de recuperação de arquivo anexa todos os discos do backup, quando um grande número de discos (> 16) ou discos grandes (> 4 TB cada) são usados, os seguintes pontos de ação são recomendados:

- Mantenha um servidor de restauração separado (VMs de D2v3 de VM do Azure) para recuperação de arquivos. Você pode usar essa recuperação de arquivo somente e desligar quando não for necessário. Não é recomendável restaurar no computador original, pois ele terá um impacto significativo na própria VM.
- Em seguida, execute o script uma vez para verificar se a operação de recuperação de arquivo foi concluída com sucesso.
- Se o processo de recuperação de arquivo for interrompido (os discos nunca são montados ou se estiverem montados, mas os volumes não forem exibidos), execute as etapas a seguir.
  - Se o servidor de restauração for uma VM do Windows
    - Verifique se o sistema operacional é WS 2012 +.
    - Verifique se as chaves do registro estão definidas conforme sugerido abaixo no servidor de restauração e certifique-se de reinicializar o servidor. O número ao lado do GUID pode variar de 0001-0005. No exemplo a seguir, é 0004. Navegue pelo caminho da chave do registro até a seção de parâmetros.

    ![iSCSI-reg-Key-Changes. png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Se o servidor de restauração for uma VM do Linux
  - No arquivo/etc/iSCSI/iscsid.conf, altere a configuração de
    - node. conn [0]. time. noop_out_timeout = 5 para node. conn [0]. time. noop_out_timeout = 30
- Depois de executar o seguinte, agora execute o script novamente. Com essas alterações, é altamente provável que a recuperação do arquivo seja realizada com sucesso.
- Cada vez que o usuário baixa um script, o backup do Azure inicia o processo de preparação do ponto de recuperação para download. Com discos grandes, isso levará um tempo considerável. Se houver picos de solicitações sucessivas, a preparação de destino entrará em uma espiral de download. Portanto, é recomendável baixar um script do portal/PowerShell/CLI, aguardar 20-30 minutos (um heurístico) e, em seguida, executá-lo. Neste momento, espera-se que o destino esteja pronto para a conexão do script.
- Após a recuperação de arquivos, certifique-se de voltar ao portal para clicar em "desmontar discos" para pontos de recuperação em que você não conseguiu montar volumes. Essencialmente, essa etapa limpará todos os processos/sessões existentes e aumentará a chance de recuperação.

## <a name="troubleshooting"></a>Resolução de problemas

Se você tiver problemas ao recuperar arquivos das máquinas virtuais, verifique a tabela a seguir para obter informações adicionais.

| Mensagem de erro/cenário | Causa provável | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *exceção ao conectar-se ao destino* |O script não é capaz de acessar o ponto de recuperação    | Verifique se o computador atende aos requisitos de acesso anteriores. |  
| Saída de exe: *o destino já foi conectado por meio de uma sessão iSCSI.* | O script já foi executado no mesmo computador e as unidades foram anexadas | Os volumes do ponto de recuperação já foram anexados. Eles não podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no explorador de arquivos para seu arquivo |
| Saída de exe: *esse script é inválido porque os discos foram desmontados por meio do portal/excederam o limite de 12 horas. Baixe um novo script do Portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi excedido | Este exe específico agora é inválido e não pode ser executado. Se você quiser acessar os arquivos desse ponto de recuperação no tempo, visite o portal para obter um novo exe|
| No computador em que o exe é executado: os novos volumes não são desmontados depois que o botão de desmontagem é clicado | O iniciador iSCSI no computador não está respondendo/atualizando sua conexão com o destino e mantendo o cache. |  Depois de clicar em **desmontar**, aguarde alguns minutos. Se os novos volumes não forem desmontados, procure todos os volumes. Procurar todos os volumes força o iniciador a atualizar a conexão e o volume é desmontado com uma mensagem de erro informando que o disco não está disponível.|
| Saída exe: o script é executado com êxito, mas "novos volumes anexados" não é exibido na saída do script |    Este é um erro transitório    | Os volumes já foram anexados. Abra o Explorer para procurar. Se você estiver usando o mesmo computador para executar scripts toda vez, considere reiniciar o computador e a lista deverá ser exibida nas execuções subsequentes do exe. |
| Específico do Linux: não é possível exibir os volumes desejados | O sistema operacional do computador em que o script é executado pode não reconhecer o sistema de arquivos subjacente da VM protegida | Verifique se o ponto de recuperação é consistente com falha ou consistente com o arquivo. Se o arquivo for consistente, execute o script em outro computador cujo sistema operacional reconheça o sistema de arquivos da VM protegida |
| Específico do Windows: não é possível exibir os volumes desejados | Os discos podem ter sido anexados, mas os volumes não foram configurados | Na tela de gerenciamento de disco, identifique os discos adicionais relacionados ao ponto de recuperação. Se qualquer um desses discos estiver no estado offline, tente torná-los online clicando com o botão direito do mouse no disco e clique em ' online '|

## <a name="security"></a>Segurança

Esta seção aborda as várias medidas de segurança tomadas para a implementação da recuperação de arquivos de backups de VM do Azure, de modo que os usuários estejam cientes dos aspectos de segurança do recurso.

### <a name="feature-flow"></a>Fluxo de recursos

Esse recurso foi criado para acessar os dados da VM sem a necessidade de restaurar toda a VM ou os discos de VM e, em etapas mínimas. O acesso aos dados da VM é fornecido por um script (que monta o volume de recuperação quando executado conforme mostrado abaixo) e, portanto, forma a base de todas as implementações de segurança

  ![Fluxo de recursos de segurança](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementações de segurança

#### <a name="select-recovery-point-who-can-generate-script"></a>Selecionar ponto de recuperação (que pode gerar script)

O script fornece acesso aos dados da VM, é importante regular quem pode gerá-lo em primeiro lugar. É necessário entrar no portal do Azure e o RBAC deve ser [autorizado](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a ser capaz de gerar o script.

A recuperação de arquivo precisa do mesmo nível de autorização necessário para restauração de VM e restauração de discos. Em outras palavras, somente usuários autorizados podem exibir os dados da VM que podem gerar o script.

O script gerado é assinado com o certificado oficial da Microsoft para o serviço de backup do Azure. Qualquer violação no script significa que a assinatura está quebrada e qualquer tentativa de executar o script é realçada como um risco potencial pelo sistema operacional.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montar o volume de recuperação (que pode executar o script)

Somente o administrador pode executar o script e executá-lo no modo elevado. O script executa apenas um conjunto de etapas gerado previamente e não aceita a entrada de qualquer fonte externa.

Para executar o script, um requer uma senha que é mostrada apenas para o usuário autorizado no momento da geração de script no portal do Azure ou PowerShell/CLI. Isso é para garantir que o usuário autorizado que baixa o script também seja responsável por executar o script.

#### <a name="browse-files-and-folders"></a>Procurar arquivos e pastas

Para procurar arquivos e pastas, o script usa o iniciador iSCSI no computador e conecta-se ao ponto de recuperação configurado como um destino iSCSI. Aqui, é possível supor cenários em que um está tentando imitar/falsificar ou todos os componentes.

Usamos o mecanismo de autenticação CHAP mútuo para que cada componente autentique o outro. Isso significa que é extremamente difícil para um iniciador falso se conectar ao destino iSCSI e um destino falso a ser conectado à máquina onde o script é executado.

O fluxo de dados entre o serviço de recuperação e o computador é protegido pela criação de um túnel SSL seguro sobre TCP (o[TLS 1,2 deve ter suporte](#system-requirements) no computador onde o script é executado)

Qualquer ACL (lista de controle de acesso) de arquivo presente na VM pai/com backup também é preservada no sistema de arquivos montado.

O script fornece acesso somente leitura a um ponto de recuperação e é válido por apenas 12 horas. Se o usuário quiser remover o acesso anteriormente, entre no portal do Azure/PowerShell/CLI e execute os **discos desmontados** para esse ponto de recuperação específico. O script será invalidado imediatamente.
