---
title: Automatizar DR do FileShare do StorSimple com o Azure Site Recovery | Microsoft Docs
description: Descreve as etapas e as práticas recomendadas para a criação de uma solução de recuperação de desastre para compartilhamentos de arquivos hospedados no armazenamento Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: 650798fdb884e6494990efb533335a1dd8b4d89f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875400"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução de recuperação de desastre automatizada usando Azure Site Recovery para compartilhamentos de arquivos hospedados no StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida que aborda as complexidades de dados não estruturados normalmente associados a compartilhamentos de arquivos. O StorSimple usa o armazenamento em nuvem como uma extensão da solução local e automaticamente armazena em camadas os dados no armazenamento local e no armazenamento em nuvem. A proteção de dados integrada, com instantâneos locais e na nuvem, elimina a necessidade de uma infraestrutura de armazenamento de proliferação.

O [Azure site Recovery](../site-recovery/site-recovery-overview.md) é um serviço baseado no Azure que fornece recursos de Dr (recuperação de desastre) ao orquestrar a replicação, o failover e a recuperação de máquinas virtuais. O Azure Site Recovery dá suporte a várias tecnologias de replicação para replicação, proteção e failover contínuos de máquinas virtuais e aplicativos para nuvens privadas/públicas ou hospedadas.

Usando os recursos de Azure Site Recovery, replicação de máquina virtual e instantâneo de nuvem do StorSimple, você pode proteger o ambiente completo do servidor de arquivos. No caso de uma interrupção, você pode usar um único clique para colocar seus compartilhamentos de arquivos online no Azure em apenas alguns minutos.

Este documento explica em detalhes como você pode criar uma solução de recuperação de desastre para seus compartilhamentos de arquivos hospedados no armazenamento do StorSimple e executar failovers planejados, não planejados e de teste usando um plano de recuperação com um clique. Em essência, ele mostra como você pode modificar o plano de recuperação em seu cofre de Azure Site Recovery para habilitar failovers do StorSimple durante cenários de desastre. Além disso, ele descreve as configurações e os pré-requisitos com suporte. Este documento pressupõe que você esteja familiarizado com os conceitos básicos das arquiteturas Azure Site Recovery e StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implantação Azure Site Recovery com suporte
Os clientes podem implantar servidores de arquivos como servidores físicos ou VMs (máquinas virtuais) em execução no Hyper-V ou VMware e, em seguida, criar compartilhamentos de arquivos a partir de volumes criados fora do armazenamento do StorSimple. O Azure Site Recovery pode proteger as implantações físicas e virtuais em um site secundário ou no Azure. Este documento aborda os detalhes de uma solução de DR com o Azure como o site de recuperação para uma VM de servidor de arquivos hospedada no Hyper-V e com compartilhamentos de arquivos no armazenamento do StorSimple. Outros cenários nos quais a VM do servidor de arquivos está em uma VM VMware ou em um computador físico podem ser implementados da mesma forma.

## <a name="prerequisites"></a>Pré-requisitos
A implementação de uma solução de recuperação de desastres com um clique que usa Azure Site Recovery para compartilhamentos de arquivos hospedados no armazenamento do StorSimple tem os seguintes pré-requisitos:

   - VM do servidor de arquivos do Windows Server 2012 R2 local hospedada no Hyper-V ou VMware ou em um computador físico
   - Dispositivo de armazenamento StorSimple local registrado com o Azure StorSimple Manager
   - Dispositivo de nuvem StorSimple criado no Azure StorSimple Manager. O dispositivo pode ser mantido em um estado de desligamento.
   - Compartilhamentos de arquivos hospedados nos volumes configurados no dispositivo de armazenamento do StorSimple
   - [Cofre de serviços de Azure site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) criado em uma assinatura Microsoft Azure

Além disso, se o Azure for seu site de recuperação, execute a [ferramenta de avaliação de prontidão de máquina virtual do Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que eles sejam compatíveis com as VMs do Azure e os serviços de Azure site Recovery.

Para evitar problemas de latência (que podem resultar em custos mais altos), certifique-se de criar seu dispositivo de nuvem StorSimple, conta de automação e conta (s) de armazenamento na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Habilitar DR para compartilhamentos de arquivos do StorSimple
Cada componente do ambiente local precisa ser protegido para habilitar a replicação e a recuperação completas. Esta seção descreve como:
    
   - Configurar a replicação de Active Directory e DNS (opcional)
   - Usar Azure Site Recovery para habilitar a proteção da VM do servidor de arquivos
   - Habilitar a proteção de volumes do StorSimple
   - Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar a replicação de Active Directory e DNS (opcional)
Se você quiser proteger os computadores que executam o Active Directory e o DNS para que eles estejam disponíveis no site de recuperação de desastre, você precisará protegê-los explicitamente (para que os servidores de arquivos estejam acessíveis após o failover com autenticação). Há duas opções recomendadas com base na complexidade do ambiente local do cliente.

#### <a name="option-1"></a>Opção 1
Se o cliente tiver um pequeno número de aplicativos, um único controlador de domínio para todo o site local e estiver fazendo o failover de todo o site, é recomendável usar Azure Site Recovery replicação para replicar o computador do controlador de domínio para um secundário site (isso é aplicável para site a site e site a Azure).

#### <a name="option-2"></a>Opção 2
Se o cliente tiver um grande número de aplicativos, estiver executando uma floresta Active Directory e estiver fazendo o failover de alguns aplicativos por vez, é recomendável configurar um controlador de domínio adicional no site de DR (um site secundário ou no Azure).

Consulte [solução de Dr automatizada para Active Directory e DNS usando Azure site Recovery](../site-recovery/site-recovery-active-directory.md) para obter instruções sobre como disponibilizar um controlador de domínio no site de recuperação de desastres. Para o restante deste documento, presumimos que um controlador de domínio esteja disponível no site de DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Usar Azure Site Recovery para habilitar a proteção da VM do servidor de arquivos
Esta etapa requer que você prepare o ambiente do servidor de arquivos local, crie e prepare um cofre de Azure Site Recovery e habilite a proteção de arquivo da VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar o ambiente do servidor de arquivos local
1. Defina o **controle de conta de usuário** como **nunca notificar**. Isso é necessário para que você possa usar os scripts de automação do Azure para conectar os destinos iSCSI após o failover por Azure Site Recovery.
   
   1. Pressione a tecla Windows + Q e pesquise por **UAC**.  
   1. Selecione **alterar configurações de controle de conta de usuário**.  
   1. Arraste a barra até a parte inferior até **nunca notificar**.  
   1. Clique em **OK** e, em seguida, selecione **Sim** quando solicitado.  
   
      ![Configurações de controle de conta de usuário](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Instale o agente de VM em cada uma das VMs do servidor de arquivos. Isso é necessário para que você possa executar scripts de automação do Azure nas VMs com failover.
   
   1. [Baixe o agente](https://aka.ms/vmagentwin) para `C:\\Users\\<username>\\Downloads`.
   1. Abra o Windows PowerShell no modo de administrador (executar como administrador) e, em seguida, digite o seguinte comando para navegar até o local de download:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > O nome do arquivo pode ser alterado dependendo da versão.
      
1. Clique em **Seguinte**.
1. Aceite os **termos de contrato** e clique em **Avançar**.
1. Clique em **Concluir**.
1. Crie compartilhamentos de arquivos usando volumes gravados fora do armazenamento do StorSimple. Para obter mais informações, consulte [usar o serviço de StorSimple Manager para gerenciar volumes](storsimple-manage-volumes.md).
   
   1. Em suas VMs locais, pressione a tecla Windows + Q e pesquise por **iSCSI**.
   1. Selecione **iniciador iSCSI**.
   1. Selecione a guia **configuração** e copie o nome do iniciador.
   1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
   1. Selecione a guia **StorSimple** e, em seguida, selecione o serviço de StorSimple Manager que contém o dispositivo físico.
   1. Criar contêineres de volume e, em seguida, criar volume (s). (Esses volumes são para os compartilhamentos de arquivos nas VMs do servidor de arquivos). Copie o nome do iniciador e forneça um nome apropriado para os registros de controle de acesso ao criar os volumes.
   1. Selecione a guia **Configurar** e anote o endereço IP do dispositivo.
   1. Em suas VMs locais, acesse o iniciador **iSCSI** novamente e insira o IP na seção conexão rápida. Clique em **conexão rápida** (o dispositivo agora deve estar conectado).
   1. Abra a portal do Azure e selecione a guia **volumes e dispositivos** . Clique em **Configurar automaticamente**. O volume que você criou deve aparecer.
   1. No portal, selecione a guia **dispositivos** e, em seguida, selecione **criar um novo dispositivo virtual.** (Esse dispositivo virtual será usado se ocorrer um failover). Esse novo dispositivo virtual pode ser mantido em um estado offline para evitar custos extras. Para colocar o dispositivo virtual offline, vá para a seção **máquinas virtuais** no portal e desligue-o.
   1. Volte para as VMs locais e abra o gerenciamento de disco (pressione a tecla Windows + X e selecione **Gerenciamento de disco**).
   1. Você observará alguns discos extras (dependendo do número de volumes que você criou). Clique com o botão direito do mouse no primeiro, selecione **inicializar disco**e selecione **OK**. Clique com o botão  direito do mouse na seção não alocada, selecione **novo volume simples**, atribua uma letra de unidade a ele e conclua o assistente.
   1. Repita a etapa l para todos os discos. Agora você pode ver todos os discos neste **computador** no Windows Explorer.
   1. Use a função serviços de arquivo e armazenamento para criar compartilhamentos de arquivos nesses volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para criar e preparar um cofre de Azure Site Recovery
Consulte a [documentação do Azure site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar a usar o Azure site Recovery antes de proteger a VM do servidor de arquivos.

#### <a name="to-enable-protection"></a>Para habilitar a proteção
1. Desconecte os destinos iSCSI das VMs locais que você deseja proteger por meio de Azure Site Recovery:
   
   1. Pressione a tecla Windows + Q e pesquise por **iSCSI**.
   1. Selecione **Configurar iniciador iSCSI**.
   1. Desconecte o dispositivo StorSimple que você conectou anteriormente. Como alternativa, você pode desativar o servidor de arquivos por alguns minutos ao habilitar a proteção.
      
   > [!NOTE]
   > Isso fará com que os compartilhamentos de arquivos estejam temporariamente indisponíveis.
   
1. [Habilite a proteção da máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md) da VM do servidor de arquivos no portal de Azure site Recovery.
1. Quando a sincronização inicial for iniciada, você poderá reconectar o destino novamente. Vá para o iniciador iSCSI, selecione o dispositivo StorSimple e clique em **conectar**.
1. Quando a sincronização for concluída e o status da VM estiver **protegido**, selecione a VM, selecione a guia **Configurar** e atualize a rede da VM de forma adequada (essa é a rede da qual as VMs com failover serão parte). Se a rede não aparecer, significa que a sincronização ainda está em andamento.

### <a name="enable-protection-of-storsimple-volumes"></a>Habilitar a proteção de volumes do StorSimple
Se você não tiver selecionado a opção **habilitar um backup padrão para este volume** para os volumes do StorSimple, vá para **políticas de backup** no serviço de StorSimple Manager e crie uma política de backup adequada para todos os volumes. Recomendamos que você defina a frequência de backups para o RPO (objetivo de ponto de recuperação) que você gostaria de ver para o aplicativo.

### <a name="configure-the-network"></a>Configurar a rede
Para a VM do servidor de arquivos, defina as configurações de rede no Azure Site Recovery para que as redes VM sejam anexadas à rede de DR correta após o failover.

Você pode selecionar a VM na guia **itens replicados** para definir as configurações de rede, conforme mostrado na ilustração a seguir.

![Computação e rede](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Você pode criar um plano de recuperação no ASR para automatizar o processo de failover dos compartilhamentos de arquivos. Se ocorrer uma interrupção, você poderá colocar os compartilhamentos de arquivos em alguns minutos com apenas um único clique. Para habilitar essa automação, você precisará de uma conta de automação do Azure.

#### <a name="to-create-an-automation-account"></a>Para criar uma conta de automação
1. Vá para a seção &gt; de **automação** de portal do Azure.
1. Clique no botão **+ Adicionar** para abrir a folha abaixo.
   
   ![Adicionar Conta de Automatização](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Nome – Insira uma nova conta de automação
   - Assinatura-escolha a assinatura
   - Grupo de recursos-criar novo/escolher grupo de recursos existente
   - Local-escolha o local, mantenha-o na mesma área geográfica/região em que o dispositivo de nuvem StorSimple e as contas de armazenamento foram criadas.
   - Criar conta Executar como do Azure-selecione a opção **Sim** .
   
1. Vá para a conta de automação, clique em **Runbooks** &gt; **procurar Galeria** para importar todos os Runbooks necessários para a conta de automação.
1. Adicione os seguintes runbooks encontrando a marca de **recuperação de desastre** na Galeria:
   
   - Limpeza de volumes do StorSimple após o failover de teste (TFO)
   - Contêineres de volume do StorSimple de failover
   - Montar volumes no dispositivo StorSimple após o failover
   - Desinstalar a extensão de script personalizado na VM do Azure
   - Iniciar dispositivo virtual StorSimple
   
      ![Procurar na Galeria](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publique todos os scripts selecionando o runbook na conta de automação e clique em **Editar** &gt; **publicar** e, em seguida, em **Sim** para a mensagem de verificação. Após essa etapa, a guia **Runbooks** será exibida da seguinte maneira:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Na conta de automação, clique em **variáveis** &gt; **Adicionar uma variável** e adicione as variáveis a seguir. Você pode optar por criptografar esses ativos. Essas variáveis são específicas do plano de recuperação. Se o plano de recuperação, que você criará na próxima etapa, Name será TestPlan, as variáveis deverão ser TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName e assim por diante.

   - **BaseUrl**: A URL do Gerenciador de recursos para a nuvem do Azure. Obter usando **Get-AzEnvironment | SELECT – nome do objeto, cmdlet ResourceManagerUrl** .
   - _RecoveryPlanName_ **-ResourceGroupName**: O grupo do Gerenciador de recursos que tem o recurso StorSimple.
   - _RecoveryPlanName_ **-ManagerName**: O recurso StorSimple que tem o dispositivo StorSimple.
   - _RecoveryPlanName_ **-DeviceName**: O dispositivo StorSimple que precisa ser reprovado.
   - _RecoveryPlanName_ **-DeviceIpAddress**: O endereço IP do dispositivo (que pode ser encontrado na guia **dispositivos** em StorSimple Device Manager &gt; seção **configurações** &gt; configuração de DNS grupo de **configurações** de **rede** &gt; ).
   - _RecoveryPlanName_ **-VolumeContainers**: Uma cadeia de caracteres separada por vírgulas de contêineres de volume presentes no dispositivo que precisa passar por failover; por exemplo: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_ **-TargetDeviceName**: O dispositivo de nuvem StorSimple no qual os contêineres devem passar por failover.
   - _RecoveryPlanName_ **-TargetDeviceIpAddress**: O endereço IP do dispositivo de destino (isso pode ser encontrado na guia **rede** do grupo &gt; &gt; **configurações** da seção **máquina virtual** ).
   - _RecoveryPlanName_ **-StorageAccountName**: O nome da conta de armazenamento na qual o script (que deve ser executado na VM com failover) será armazenado. Pode ser qualquer conta de armazenamento que tenha algum espaço para armazenar o script temporariamente.
   - _RecoveryPlanName_ **-StorageAccountKey**: A chave de acesso para a conta de armazenamento acima.
   - _RecoveryPlanName_ **-VMGUIDS**: Ao proteger uma VM, o Azure Site Recovery atribui a cada VM uma ID exclusiva que fornece os detalhes da VM com failover. Para obter o VMGUID, selecione a **Guia serviços de recuperação** e clique em **Item** &gt; protegido **grupos** &gt; de proteção **computadores** &gt; **Propriedades**. Se você tiver várias VMs, adicione os GUIDs como uma cadeia de caracteres separada por vírgulas.

     Por exemplo, se o nome do plano de recuperação for fileServerpredayRP, a guia **variáveis**, **conexões** e **certificados** deverá aparecer da seguinte maneira depois que você adicionar todos os ativos.

      ![Elementos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Carregue o módulo runbook da série 8000 do StorSimple em sua conta de automação. Use as etapas a seguir para adicionar um módulo:
   
   1. Abra o PowerShell, crie uma nova pasta & altere o diretório para a pasta.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Baixe a CLI do NuGet na mesma pasta em etapa 1.
      Várias versões do NuGet. exe estão disponíveis nos [downloads do NuGet](https://www.nuget.org/downloads). Cada link de download aponta diretamente para um arquivo. exe, portanto, certifique-se de clicar com o botão direito do mouse e salvar o arquivo em seu computador em vez de executá-lo no navegador.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Baixar o SDK dependente
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Crie um módulo de runbook de automação do Azure para o gerenciamento de dispositivo do StorSimple 8000 Series. Use os comandos a seguir para criar um arquivo zip do módulo de automação.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importe o arquivo zip do módulo de automação do Azure (Microsoft. Azure. Management. StorSimple8000Series. zip) criado na etapa acima. Isso pode ser feito selecionando a conta de automação, clique em **módulos** em recursos compartilhados e, em seguida, clique em **Adicionar um módulo**.
   
   Depois de importar o módulo série 8000 do StorSimple, a guia **módulos** deve aparecer da seguinte maneira:
   
      ![Módulos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Vá para a seção **serviços de recuperação** e selecione o cofre de Azure site Recovery que você criou anteriormente.
1. Selecione a opção **planos de recuperação (site Recovery)** no grupo **gerenciar** e crie um novo plano de recuperação da seguinte maneira:
   
   - Clique no botão **+ plano de recuperação** , abre a folha abaixo.
      
      ![Criar plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Insira um nome do plano de recuperação, escolha origem, destino & valores do modelo de implantação.
   
   - Selecione as VMs do grupo de proteção que você deseja incluir no plano de recuperação e clique no botão **OK** .
   
   - Selecione o plano de recuperação que você criou anteriormente, clique no botão **Personalizar** para abrir o modo de exibição personalização do plano de recuperação.
   
   - Clique com o botão direito do mouse em **todos os grupos desligar** e clique em **Adicionar pré-ação**.
   
   - Abre a folha inserir ação, insira um nome, selecione a opção **do lado primário** na opção onde executar, selecione conta de automação (na qual você adicionou os runbooks) e, em seguida, selecione o runbook **failover-StorSimple-volume-containers** .
   
   - Clique com o **botão direito do mouse no grupo 1: Inicie** e clique na opção **Adicionar itens protegidos** e selecione as VMs que serão protegidas no plano de recuperação e clique no botão **OK** . Opcional, se já estiver selecionado em VMs.
   
   - Clique com o **botão direito do mouse no grupo 1: Inicie** e clique na opção **post action** e, em seguida, adicione todos os scripts a seguir:  
      
      - Start-StorSimple-Virtual-Appliance runbook  
      - Runbook de failover-StorSimple-volume-containers  
      - Montagem-volumes-After-runbook de failover  
      - Desinstalar o runbook de extensão de script personalizado  
        
   - Adicione uma ação manual após os 4 scripts acima no mesmo **grupo 1: Seção de pós** -etapas. Essa ação é o ponto no qual você pode verificar se tudo está funcionando corretamente. Essa ação precisa ser adicionada somente como parte do failover de teste (portanto, marque apenas a caixa de seleção **failover de teste** ).
    
   - Após a ação manual, adicione o script de **limpeza** usando o mesmo procedimento que você usou para os outros runbooks. **Salve** o plano de recuperação.
    
   > [!NOTE]
   > Ao executar um failover de teste, você deve verificar tudo na etapa de ação manual porque os volumes do StorSimple que foram clonados no dispositivo de destino serão excluídos como parte da limpeza depois que a ação manual for concluída.
       
      ![Plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Executar um failover de teste
Consulte o guia complementar da [solução de recuperação de desastre Active Directory](../site-recovery/site-recovery-active-directory.md) para obter considerações específicas para Active Directory durante o failover de teste. A configuração local não é incomodada quando ocorre o failover de teste. Os volumes do StorSimple que foram anexados à VM local são clonados para o dispositivo de nuvem StorSimple no Azure. Uma VM para fins de teste é colocada no Azure e os volumes clonados são anexados à VM.

#### <a name="to-perform-the-test-failover"></a>Para executar o failover de teste
1. Na portal do Azure, selecione seu cofre de Site Recovery.
1. Clique no plano de recuperação criado para a VM do servidor de arquivos.
1. Clique em **failover de teste**.
1. Selecione a rede virtual do Azure à qual as VMs do Azure serão conectadas depois que o failover ocorrer.
   
   ![Iniciar failover](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Clique em **OK** para iniciar a ativação pós-falha. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades ou no **trabalho failover de teste** em &gt; **trabalhos** &gt; de nome do cofre **site Recovery trabalhos**.
1. Após a conclusão do failover, você também deve ser capaz de ver a réplica do computador do Azure exibida no &gt; portal do Azure **máquinas virtuais**. Você pode executar suas validações.
1. Depois que as validações forem concluídas , clique em validações concluídas. Isso removerá os volumes do StorSimple e desligará o dispositivo de nuvem StorSimple.
1. Quando terminar, clique em **failover de teste de limpeza** no plano de recuperação. No Notes, anote e salve as observações associadas ao failover de teste. Isso excluirá a máquina virtual que foi criada durante o failover de teste.

## <a name="perform-a-planned-failover"></a>Executar um failover planejado
   Durante um failover planejado, a VM do servidor de arquivos local é desligada normalmente e um instantâneo de backup na nuvem dos volumes no dispositivo StorSimple é obtido. Os volumes do StorSimple têm failover para o dispositivo virtual, uma VM de réplica é colocada no Azure e os volumes são anexados à VM.

#### <a name="to-perform-a-planned-failover"></a>Para executar um failover planejado
1. Na portal do Azure, selecione os planos de recuperação &gt; do cofre **dos serviços de recuperação** **(site Recovery)** &gt; **recoveryplan_name** criados para a VM do servidor de arquivos.
1. Na folha do plano de recuperação, clique em **mais** &gt; **failover planejado**.

   ![Plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Na folha **confirmar failover planejado** , escolha os locais de origem e de destino e selecione rede de destino e clique no ícone de verificação ✓ para iniciar o processo de failover.
1. Depois que as máquinas virtuais de réplica são criadas, elas estão em um estado de confirmação pendente. Clique em **confirmar** para confirmar o failover.
1. Após a conclusão da replicação, as máquinas virtuais são iniciadas no local secundário.

## <a name="perform-a-failover"></a>Executar um failover
Durante um failover não planejado, os volumes do StorSimple são transferidos para o dispositivo virtual, uma VM de réplica será colocada no Azure e os volumes serão anexados à VM.

#### <a name="to-perform-a-failover"></a>Para executar um failover
1. Na portal do Azure, selecione os planos de recuperação &gt; do cofre **dos serviços de recuperação** **(site Recovery)** &gt; **recoveryplan_name** criados para a VM do servidor de arquivos.
1. Na folha do plano de recuperação, clique em **mais** &gt; **failover**.
1. Na folha **confirmar failover** , escolha os locais de origem e de destino.
1. Selecione **desligar máquinas virtuais e sincronize os dados mais recentes** para especificar que site Recovery deve tentar desligar a máquina virtual protegida e sincronizar os dados para que a versão mais recente dos dados seja reprovada.
1. Após o failover, as máquinas virtuais estão em um estado de confirmação pendente. Clique em **confirmar** para confirmar o failover.


## <a name="perform-a-failback"></a>Executar uma reativação pós-falha
Durante um failback, os contêineres de volume do StorSimple serão submetidos ao failover para o dispositivo físico após a realização de um backup.

#### <a name="to-perform-a-failback"></a>Para executar um failback
1. Na portal do Azure, selecione os planos de recuperação &gt; do cofre **dos serviços de recuperação** **(site Recovery)** &gt; **recoveryplan_name** criados para a VM do servidor de arquivos.
1. Na folha do plano de recuperação, clique em **mais** &gt; **failover planejado**.
1. Escolha os locais de origem e de destino, selecione as opções de sincronização de dados e de criação de VM apropriadas.
1. Clique no botão **OK** para iniciar o processo de failback.
   
   ![Iniciar failback](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Melhores práticas
### <a name="capacity-planning-and-readiness-assessment"></a>Planejamento de capacidade e avaliação de prontidão
#### <a name="hyper-v-site"></a>Site Hyper-V
Use a [ferramenta planejador de capacidade do usuário](https://www.microsoft.com/download/details.aspx?id=39057) para criar a infraestrutura de servidor, armazenamento e rede para seu ambiente de réplica do Hyper-V.

#### <a name="azure"></a>Azure
Você pode executar a [ferramenta de avaliação de prontidão de máquina virtual do Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que elas sejam compatíveis com as VMs do Azure e os serviços de Azure site Recovery. A ferramenta de avaliação de prontidão verifica as configurações da VM e avisa quando as configurações são incompatíveis com o Azure. Por exemplo, ele emitirá um aviso se uma unidade C: for maior que 127 GB.

O planejamento de capacidade é composto de pelo menos dois processos importantes:

   - Mapeando VMs Hyper-V locais para tamanhos de VM do Azure (como A6, A7, A8 e A9).
   - Determinando a largura de banda da Internet necessária.

## <a name="limitations"></a>Limitações
- No momento, somente um dispositivo StorSimple pode fazer failover (para um único dispositivo de nuvem StorSimple). O cenário de um servidor de arquivos que abrange vários dispositivos StorSimple ainda não tem suporte.
- Se você receber um erro ao habilitar a proteção para uma VM, certifique-se de que você desconectou os destinos iSCSI.
- Todos os contêineres de volume que foram agrupados em conjunto devido a políticas de backup que abrangem entre contêineres de volume passarão por failover juntos.
- Todos os volumes nos contêineres de volume que você escolheu serão reprovados.
- Volumes que somam mais de 64 TB não podem passar por failover porque a capacidade máxima de um único dispositivo de nuvem StorSimple é de 64 TB.
- Se o failover planejado/não planejado falhar e as VMs forem criadas no Azure, não limpe as VMs. Em vez disso, faça um failback. Se você excluir as VMs, as VMs locais não poderão ser ativadas novamente.
- Após um failover, se você não conseguir ver os volumes, vá para as VMs, abra o gerenciamento de disco, examine os discos novamente e coloque-os online.
- Em alguns casos, as letras da unidade no site de recuperação de desastre podem ser diferentes das letras locais. Se isso ocorrer, será necessário corrigir manualmente o problema após a conclusão do failover.
- Tempo limite do trabalho de failover: O script do StorSimple atingirá o tempo limite se o failover dos contêineres de volume levar mais tempo do que o limite de Azure Site Recovery por script (atualmente 120 minutos).
- Tempo limite do trabalho de backup: O script do StorSimple expira se o backup de volumes levar mais tempo do que o limite de Azure Site Recovery por script (atualmente 120 minutos).
   
  > [!IMPORTANT]
  > Execute o backup manualmente no portal do Azure e execute o plano de recuperação novamente.
   
- Tempo limite do trabalho de clonagem: O script do StorSimple expira se a clonagem de volumes levar mais tempo do que o limite de Azure Site Recovery por script (atualmente 120 minutos).
- Erro de sincronização de horário: Os scripts do StorSimple informam que os backups não foram bem-sucedidos, embora o backup seja bem-sucedido no Portal. Uma possível causa disso pode ser que a hora do dispositivo StorSimple pode estar fora de sincronia com a hora atual no fuso horário.
   
  > [!IMPORTANT]
  > Sincronizar a hora do dispositivo com a hora atual no fuso horário.
   
- Erro de failover do dispositivo: O script do StorSimple poderá falhar se houver um failover de dispositivo quando o plano de recuperação estiver em execução.
   
  > [!IMPORTANT]
  > Execute novamente o plano de recuperação depois que o failover do dispositivo for concluído.


## <a name="summary"></a>Resumo
Usando Azure Site Recovery, você pode criar um plano de recuperação de desastre automatizado completo para uma VM de servidor de arquivos que tenha compartilhamentos de arquivos hospedados no armazenamento do StorSimple. Você pode iniciar o failover em segundos a partir de qualquer lugar no caso de uma interrupção e colocar o aplicativo em funcionamento em alguns minutos.
