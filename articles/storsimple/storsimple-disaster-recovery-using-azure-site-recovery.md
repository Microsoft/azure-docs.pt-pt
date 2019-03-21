---
title: Automatizar a partilha de ficheiros do StorSimple DR com o Azure Site Recovery | Documentos da Microsoft
description: Descreve os passos e melhores práticas para criar uma solução de recuperação após desastre para as partilhas de ficheiros alojado no armazenamento do Microsoft Azure StorSimple.
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
ms.author: vidarmsft
ms.openlocfilehash: f5eefd1d3fa26738729d98e60d8a56cd8d33d86c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084883"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução de recuperação após desastre automatizada com o Azure Site Recovery para partilhas de ficheiros alojadas no StorSimple
## <a name="overview"></a>Descrição geral
O Microsoft Azure StorSimple é uma solução de armazenamento de cloud híbrida que resolve as complexidades normalmente associadas a partilhas de ficheiros de dados não estruturados. StorSimple utiliza o armazenamento na cloud como uma extensão da solução no local e automaticamente os dados de camadas em armazenamento no local e o armazenamento na cloud. Integrado a proteção de dados, no local e instantâneos da cloud, que elimina a necessidade de uma infraestrutura de armazenamento sprawling.

[O Azure Site Recovery](../site-recovery/site-recovery-overview.md) é um serviço baseado no Azure que fornece capacidades de recuperação (DR) de desastres através da orquestração da replicação, ativação pós-falha e recuperação de máquinas virtuais. O Azure Site Recovery suporta um número de tecnologias de replicação para replicar de forma consistente, proteger e forma totalmente integrada a ativação pós-falha de máquinas virtuais e aplicações para nuvens pública/privada ou alojadas.

Utilizar o Azure Site Recovery, replicação da máquina virtual e capacidades de instantâneos de cloud do StorSimple, pode proteger o ambiente de servidor de ficheiro completo. Em caso de uma interrupção, pode usar um único clique para introduzir as partilhas de ficheiros online no Azure em apenas alguns minutos.

Este documento explica em detalhe como pode criar uma solução de recuperação após desastre para as partilhas de ficheiros alojadas no armazenamento do StorSimple e realizar planeada, não planeada e utilizar um plano de recuperação de um clique de ativações pós-falha de teste. Essencialmente, ele mostra como modificar o plano de recuperação no seu Cofre de recuperação de sites do Azure para ativar as ativações pós-falha de StorSimple durante os cenários de desastre. Além disso, ele descreve os pré-requisitos e configurações suportadas. Este documento parte do princípio de que está familiarizado com os fundamentos básicos de arquiteturas do Azure Site Recovery e o StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implementação suportadas do Azure Site Recovery
Os clientes podem implementar servidores de ficheiros como servidores físicos ou máquinas virtuais (VMs) em execução no Hyper-V ou VMware e, em seguida, criar partilhas de ficheiros dos volumes criadas fora do armazenamento do StorSimple. O Azure Site Recovery pode proteger as implementações físicas e virtuais para qualquer um de um site secundário ou para o Azure. Este documento aborda os detalhes de uma solução de DR com o Azure como site de recuperação para um VM alojada no Hyper-V do servidor de ficheiros e partilhas de ficheiros no armazenamento do StorSimple. Outros cenários em que o servidor de ficheiros VM está numa VM de VMware ou um computador físico podem ser implementados da mesma forma.

## <a name="prerequisites"></a>Pré-requisitos
Implementar uma solução de recuperação de desastres num único clique que utiliza o Azure Site Recovery para partilhas de ficheiros alojadas no armazenamento do StorSimple tem os seguintes pré-requisitos:

   - Servidor de ficheiros do Windows Server 2012 R2 alojada de VM no Hyper-V ou VMware ou uma máquina física no local
   - StorSimple armazenamento dispositivo no local registado com o Azure StorSimple manager
   - StorSimple Cloud Appliance criado no Azure StorSimple manager. A aplicação pode ser mantida num Estado de encerramento.
   - Partilhas de ficheiros alojadas em volumes configurados no dispositivo de armazenamento StorSimple
   - [Cofre de serviços do Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) criado numa subscrição do Microsoft Azure

Além disso, se o seu site de recuperação é o Azure, execute o [ferramenta de avaliação de preparação de máquinas virtuais do Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que sejam compatíveis com as VMs do Azure e o Azure Site Recovery services.

Para evitar problemas (que podem resultar em custos de superiores), certifique-se de que cria a StorSimple Cloud Appliance, a conta de automatização e o armazenamento de latência conta (s) na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Ativar a DR para partilhas de ficheiros do StorSimple
Cada componente do ambiente no local precisa ser protegido para ativar a replicação completa e a recuperação. Esta secção descreve como:
    
   - Configurar a replicação de Active Directory e DNS (opcional)
   - Utilizar o Azure Site Recovery para ativar a proteção de VM do servidor de ficheiros
   - Ativar a proteção de volumes do StorSimple
   - Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar a replicação de Active Directory e DNS (opcional)
Se pretender proteger os computadores a executar o Active Directory e DNS para que fiquem disponíveis no site de DR, é preciso protegê-los explicitamente (para que os servidores de arquivos estão acessíveis após a ativação pós-falha com a autenticação). Existem duas opções recomendadas com base na complexidade de ambiente de no local do cliente.

#### <a name="option-1"></a>Opção 1
Se o cliente tem um pequeno número de aplicativos, um controlador de domínio único para todo o site local e irá falhar ao longo de todo o site, em seguida, recomendamos que utilize a replicação do Azure Site Recovery para replicar a máquina de controlador de domínio para uma secundária site (isto é aplicável para o site a site e o site para o Azure).

#### <a name="option-2"></a>Opção 2
Se o cliente tem um grande número de aplicativos, está a executar uma floresta do Active Directory e será possível efetuar a ativação pós-falha alguns aplicativos ao mesmo tempo, em seguida, recomendamos a configuração de um controlador de domínio adicionais no site de DR (ambos um site secundário ou no Azure).

Consulte a [solução de DR de automatizada para o Active Directory e DNS com o Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) para obter instruções sobre quando tornar um controlador de domínio disponíveis no site de DR. No restante deste documento, partimos do princípio de que um controlador de domínio está disponível no site de DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Utilizar o Azure Site Recovery para ativar a proteção de VM do servidor de ficheiros
Este passo requer a preparação do ambiente de servidor de ficheiros no local, criar e preparar um cofre de recuperação de sites do Azure e ativar a proteção de ficheiros da VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar o ambiente de servidor de ficheiros no local
1. Definir o **User Account Control** ao **nunca notificar**. Isto é necessário para que possa utilizar scripts de automatização do Azure para ligar os destinos iSCSI após a ativação pós-falha pelo Azure Site Recovery.
   
   1. Prima a tecla Windows + Q e procure **UAC**.  
   1. Selecione **definições de controlo de conta de utilizador de alteração**.  
   1. Arraste a barra na parte inferior em direção **nunca notificar**.  
   1. Clique em **OK** e, em seguida, selecione **Sim** quando lhe for pedido.  
   
      ![Configurações de controle de conta de usuário](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Instale o agente de VM em cada uma das VMs do servidor de ficheiros. Isto é necessário para que possam executar scripts de automatização do Azure na ativação pós-falha nas VMs.
   
   1. [Transferir o agente](https://aka.ms/vmagentwin) para `C:\\Users\\<username>\\Downloads`.
   1. Abra o Windows PowerShell no modo de administrador (executar como administrador) e, em seguida, introduza o seguinte comando para navegar para a localização de transferência:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > O nome de ficheiro podem ser alterados dependendo da versão.
      
1. Clique em **Seguinte**.
1. Aceite o **termos do contrato** e, em seguida, clique em **próxima**.
1. Clique em **Concluir**.
1. Crie partilhas de ficheiros com volumes criadas fora do armazenamento do StorSimple. Para obter mais informações, consulte [utilizar o serviço StorSimple Manager para gerir volumes](storsimple-manage-volumes.md).
   
   1. Nas suas VMs no local, prima a tecla Windows + Q e procure **iSCSI**.
   1. Selecione **iniciador iSCSI**.
   1. Selecione o **configuração** separador e copie o nome de iniciador.
   1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
   1. Selecione o **StorSimple** separador e, em seguida, selecione o serviço StorSimple Manager que contenha o dispositivo físico.
   1. Criar contentores de volume e, em seguida, criar volumes. (Estes volumes destinam-se as partilhas de ficheiros no servidor de ficheiros VMs). Copie o nome de iniciador e dê um nome apropriado para os registos de controlo de acesso, quando cria os volumes.
   1. Selecione o **configurar** separador e tome nota do endereço IP do dispositivo.
   1. Nas suas VMs no local, vá para o **iniciador iSCSI** novamente e introduza o IP na secção Connect rápida. Clique em **ligar rápida** (o dispositivo deverá estar agora ligado).
   1. Abra o portal do Azure e selecione o **Volumes e dispositivos** separador. Clique em **automática configurar**. Deve aparecer o volume que criou.
   1. No portal, selecione o **dispositivos** separador e, em seguida, selecione **criar um novo dispositivo Virtual.** (Este dispositivo virtual irá ser utilizado se ocorrer uma ativação pós-falha). Este novo dispositivo virtual pode ser mantido num estado offline para evitar custos adicionais. Para colocar o dispositivo virtual offline, vá para o **máquinas virtuais** secção no Portal e encerrá-lo.
   1. Voltar para as VMs no local e abra a gestão de discos (prima a tecla Windows + X e selecione **gestão de discos**).
   1. Irá reparar alguns discos Extras (dependendo do número de volumes que criou). Com o botão direito do primeiro um, selecione **Inicializar disco**e selecione **OK**. Com o botão direito a **Unallocated** secção, selecione **Novo Volume simples**, atribuir uma letra de unidade e concluir o assistente.
   1. Repita o passo l para todos os discos. Agora, pode ver todos os discos no **este PC** no Explorador do Windows.
   1. Utilize a função Serviços de armazenamento de ficheiros e para criar partilhas de ficheiros nestes volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para criar e preparar um cofre do Azure Site Recovery
Consulte a [documentação do Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar a utilizar com o Azure Site Recovery antes de proteger a VM do servidor de ficheiros.

#### <a name="to-enable-protection"></a>Para ativar a proteção
1. Encerre o destino de iSCSI (s) as VMs no local que pretende proteger através do Azure Site Recovery:
   
   1. Prima a tecla Windows + Q e procure **iSCSI**.
   1. Selecione **configurar o iniciador iSCSI**.
   1. Desligar o dispositivo do StorSimple ligado anteriormente. Em alternativa, pode optar por desativar o servidor de ficheiros durante alguns minutos, ao ativar a proteção.
      
   > [!NOTE]
   > Isso fará com que as partilhas de ficheiros fique temporariamente indisponível.
   
1. [Ativar a proteção da máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md) do servidor de ficheiros VM a partir do portal do Azure Site Recovery.
1. Quando começa a sincronização inicial, poderá restabelecer a ligação de destino novamente. Vá para o iniciador de iSCSI, selecione o dispositivo StorSimple e clique em **Connect**.
1. Quando a sincronização estiver concluída e o estado da VM é **protegido**, selecione a VM, selecione a **configurar** separador e atualizar a rede da VM em conformidade (esta é a rede que a falha através de VM (s) serão uma parte do). Se a rede não aparecer, significa que a sincronização ainda está acontecendo.

### <a name="enable-protection-of-storsimple-volumes"></a>Ativar a proteção de volumes do StorSimple
Se não tiver selecionado o **ativar uma cópia de segurança predefinida para este volume** opção para os volumes do StorSimple, aceda à **políticas de cópia de segurança** no Gestor do StorSimple de serviço e criar uma política de cópia de segurança adequada para todos os volumes. Recomendamos que defina a frequência de cópias de segurança para o objetivo de ponto de recuperação (RPO) que gostaria de ver para a aplicação.

### <a name="configure-the-network"></a>Configurar a rede
Para o servidor de ficheiros VM, configure as definições de rede no Azure Site Recovery, para que as redes VM estão ligadas à rede correta do DR após a ativação pós-falha.

Pode selecionar a VM na **itens replicados** separador para configurar as definições de rede, conforme mostrado na ilustração seguinte.

![Computação e Rede](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Pode criar um plano de recuperação no ASR para automatizar o processo de ativação pós-falha das partilhas de ficheiros. Se ocorrer uma interrupção, pode abrir as partilhas de ficheiros em poucos minutos com apenas um único clique. Para ativar essa automação, precisa de uma conta de automatização do Azure.

#### <a name="to-create-an-automation-account"></a>Para criar uma conta de automatização
1. Aceda ao portal do Azure &gt; **automatização** secção.
1. Clique em **+ adicionar** botão, abre-se abaixo do painel.
   
   ![Adicionar Conta de Automatização](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Nomeie - introduzir uma nova conta de automatização
   - Subscrição – selecione a subscrição
   - Recursos de grupo - criar novo/escolher existente grupo de recursos
   - Local - escolher localização, mantê-lo na mesma geo/região em que foram criadas as contas de armazenamento e o StorSimple Cloud Appliance.
   - Criar conta Run As do Azure - selecione **Sim** opção.
   
1. Vá para a conta de automatização, clique em **Runbooks** &gt; **Galeria de procura** para importar todos os runbooks para a conta de automatização.
1. Adicionar os seguintes runbooks localizando **recuperação após desastre** etiqueta na galeria:
   
   - Limpar de volumes do StorSimple, após a ativação pós-falha de teste (TFO)
   - Contentores de volumes do StorSimple de ativação pós-falha
   - Montar volumes no dispositivo StorSimple após a ativação pós-falha
   - Desinstalar a extensão de script personalizado na VM do Azure
   - Iniciar a aplicação Virtual StorSimple
   
      ![Galeria de procura](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publicar todos os scripts ao selecionar o runbook na conta de automatização e clique em **edite** &gt; **publicar** e, em seguida **Sim** para a mensagem de verificação. Após este passo, o **Runbooks** separador será exibida da seguinte forma:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Na conta de automatização, clique em **variáveis** &gt; **adicionar uma variável** e adicione as seguintes variáveis. Pode optar por encriptar estes recursos. Estas variáveis são o plano de recuperação específico. Se planear a recuperação, que irá criar no próximo passo, nome é TestPlan, em seguida, as variáveis devem ser TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName e assim por diante.

   - **BaseUrl**: O url do Gestor de recursos para a cloud do Azure. Obter usando **Get-AzureRmEnvironment | Nome de Select-Object, ResourceManagerUrl** cmdlet.
   - *RecoveryPlanName***-ResourceGroupName**: O grupo do Gestor de recursos que tem o recurso do StorSimple.
   - *RecoveryPlanName***-ManagerName**: O recurso do StorSimple que tenha o dispositivo StorSimple.
   - *RecoveryPlanName***-DeviceName**: O dispositivo StorSimple que tem de efetuar a ativação pós-falha.
   - *RecoveryPlanName***-DeviceIpAddress**: O endereço IP do dispositivo (isso pode ser encontrado na **dispositivos** separador na secção de Gestor de dispositivos do StorSimple &gt; **definições** &gt; **rede** &gt; **As definições de DNS** grupo).
   - *RecoveryPlanName***-VolumeContainers**: Uma cadeia de caracteres separados por vírgulas dos contentores de volumes presentes no dispositivo que tem de efetuar a ativação pós-falha; Por exemplo: volcon1 volcon2, volcon3.
   - *RecoveryPlanName***-TargetDeviceName**: A StorSimple Cloud Appliance em que os contentores estão a efetuar a ativação pós-falha.
   - *RecoveryPlanName***-TargetDeviceIpAddress**: O endereço IP do dispositivo de destino (isso pode ser encontrado na **Máquina Virtual** secção &gt; **definições** grupo &gt; **redes** separador).
   - *RecoveryPlanName***-StorageAccountName**: O nome de conta de armazenamento na qual o script (que tem para executar na ativação pós-falha VM) será armazenado. Isso pode ser qualquer conta de armazenamento que tenha algum espaço para armazenar o script temporariamente.
   - *RecoveryPlanName***-StorageAccountKey**: A chave de acesso para a conta de armazenamento acima.
   - *RecoveryPlanName***-VMGUIDS**: Após proteger uma VM, o Azure Site Recovery atribui cada VM uma ID exclusiva que dá os detalhes da sobre a VM. Para obter o VMGUID, selecione o **serviços de recuperação** separador e clique em **Item protegido** &gt; **grupos de proteção** &gt;  **Máquinas** &gt; **propriedades**. Se tiver várias VMs, em seguida, adicione os GUIDs como uma cadeia separada por vírgulas.

     Por exemplo, se o nome do plano de recuperação é fileServerpredayRP, em seguida, sua **variáveis**, **ligações** e **certificados** separador deverá aparecer da seguinte forma depois de adicionar todos os recursos.

      ![Elementos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Carregar o módulo de Runbook de série StorSimple 8000 na conta de automatização. Utilize os passos abaixo para adicionar um módulo:
   
   1. Abra o powershell, crie uma nova pasta e altere o diretório para a pasta.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Baixe o nuget CLI sob a mesma pasta no passo 1.
      As várias versões do nuget.exe estão disponíveis na [nuget transfere](https://www.nuget.org/downloads). Cada ligação de transferência aponta diretamente para um ficheiro de .exe, por isso, certifique-se de que com o botão direito e guarde o ficheiro para o seu computador em vez de executá-lo a partir do browser.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Transferir o SDK dependente
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Crie um módulo de Runbook de automatização do Azure para gestão de dispositivos do StorSimple série 8000. Utilize o abaixo comandos para criar um ficheiro de zip do módulo de automatização.
         
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
         
   1. Importe o automatização do Azure ficheiro zip do módulo (Microsoft.Azure.Management.StorSimple8000Series.zip) criado nos passos acima. Isso pode ser feito ao selecionar a conta de automatização, clique em **módulos** sob recursos PARTILHADOS e clique em **adicione um módulo**.
   
   Depois de importar o módulo de série StorSimple 8000, o **módulos** separador deverá aparecer da seguinte forma:
   
      ![Módulos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Vá para o **serviços de recuperação** secção e selecione o Cofre de recuperação de sites do Azure que criou anteriormente.
1. Selecione o **(recuperação de sites) de planos de recuperação** opção a partir de **gerir** agrupar e criar um novo plano de recuperação da seguinte forma:
   
   - Clique em **+ o plano de recuperação** botão, abre-se abaixo do painel.
      
      ![Criar plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Introduza um nome de plano de recuperação, escolha valores do modelo de origem, destino e implementação.
   
   - Selecione as VMs do grupo de proteção que pretende incluir no plano de recuperação e clique em **OK** botão.
   
   - Selecione o plano de recuperação que criou anteriormente, clique em **personalizar** botão para abrir a vista de personalização do plano de recuperação.
   
   - Clique com o botão direito do rato em **encerramento de todos os grupos** e clique em **adicione a ação pré**.
   
   - Abre o painel de ação de inserir, introduza um nome, selecione **lado primário** opção onde executar a opção, selecione a conta de automatização (em que adicionou os runbooks) e, em seguida, selecione o **Volume-contentores de ativação pós-falha-StorSimple**  runbook.
   
   - Clique com o botão direito do rato no **grupo 1: Inicie** e clique em **itens protegidos de Add** opção, em seguida, selecione as VMs que estão a ser protegidas no plano de recuperação e clique em **Ok** botão. Opcional, se ainda estiver selecionada a VMs.
   
   - Clique com o botão direito do rato no **grupo 1: Inicie** e clique em **ação de postar** opção, em seguida, adicione os seguintes scripts:  
      
      - Start--aplicação Virtual StorSimple runbook  
      - Efetuar a ativação pós-falha-StorSimple-volume-contentores do runbook  
      - Runbook de montagem volumes depois de failover  
      - Runbook de desinstalar---extensão de script personalizado  
        
   - Adicionar uma ação manual após os scripts de 4 acima da mesma **grupo 1: Pós-passos** secção. Esta ação é o ponto em que pode verificar se tudo está funcionando corretamente. Esta ação tem de ser adicionada apenas como parte da ativação pós-falha de teste (por isso, selecione apenas a **ativação pós-falha de teste** caixa de verificação).
    
   - Após a ação manual, adicione a **limpeza** script, utilize o mesmo procedimento utilizado para os outros runbooks. **Guardar** o plano de recuperação.
    
   > [!NOTE]
   > Ao executar uma ativação pós-falha de teste, deve verificar tudo o que o passo de ação manual porque os volumes do StorSimple que tinham sido clonados no dispositivo de destino serão eliminados como parte da limpeza depois de concluída a ação manual.
       
      ![Plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Executar uma ativação pós-falha de teste
Consulte a [solução de DR de diretório do Active Directory](../site-recovery/site-recovery-active-directory.md) guia complementar para obter considerações específicas para o Active Directory durante a ativação pós-falha de teste. A configuração no local não é seja afetada em todos os quando ocorre a ativação pós-falha de teste. Os volumes do StorSimple que foram ligados à VM no local são clonados para a StorSimple Cloud Appliance no Azure. Uma VM para fins de teste é ativada no Azure e os volumes clonados estão ligados à VM.

#### <a name="to-perform-the-test-failover"></a>Para efetuar a ativação pós-falha de teste
1. No portal do Azure, selecione o Cofre de recuperação de sites.
1. Clique no plano de recuperação criado para a VM do servidor de ficheiros.
1. Clique em **ativação pós-falha de teste**.
1. Selecione a rede virtual do Azure para o qual as VMs do Azure serão ligadas após a ocorrência da ativação pós-falha.
   
   ![Iniciar ativação pós-falha](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Clique em **OK** para iniciar a ativação pós-falha. Pode controlar o progresso clicando na VM para abrir as respetivas propriedades ou na **tarefa de ativação pós-falha de teste** no nome do cofre &gt; **tarefas** &gt; **tarefas do Site Recovery**.
1. Depois de concluída a ativação pós-falha, deverá também conseguir ver a réplica do Azure machine aparecer no portal do Azure &gt; **máquinas virtuais**. Pode realizar as validações.
1. Depois das validações terminadas, clique em **validações completa**. Isto irá remover os Volumes do StorSimple e encerrar a StorSimple Cloud Appliance.
1. Quando tiver terminado, clique em **ativação pós-falha de teste de limpeza** no plano de recuperação. Nas notas para registar e guardar todas as observações associadas à ativação pós-falha de teste. Esta ação irá eliminar a máquina virtual que foram criados durante a ativação pós-falha de teste.

## <a name="perform-a-planned-failover"></a>Executar uma ativação pós-falha planeada
   Durante uma ativação pós-falha planeada, no local servidor de ficheiros que VM é encerrada corretamente e uma cópia de segurança de volumes no dispositivo StorSimple instantâneo de cloud. Os volumes do StorSimple com ativação pós-falha para o dispositivo virtual, uma VM de réplica está ligado no Azure e os volumes estão ligados à VM.

#### <a name="to-perform-a-planned-failover"></a>Para efetuar uma ativação pós-falha planeada
1. No portal do Azure, selecione **serviços de recuperação** cofre &gt; **planos de recuperação (recuperação de sites)** &gt; **recoveryplan_name** criado para o servidor de ficheiros de VM.
1. No painel de plano de recuperação, clique em **mais** &gt; **ativação pós-falha planeada**.

   ![Plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Sobre o **confirmar a ativação de pós-falha planeadas** painel, selecione a origem e a localizações dos destinos e a rede de destino selecione e clique no ícone de verificação ✓ para iniciar o processo de ativação pós-falha.
1. Depois de máquinas virtuais de réplica são criadas eles estão no estado de confirmação pendente. Clique em **consolidação** para consolidar a ativação pós-falha.
1. Após a conclusão da replicação, as máquinas virtuais começarão a localização secundária.

## <a name="perform-a-failover"></a>Execute uma ativação pós-falha
Durante uma ativação pós-falha não planeada, os volumes do StorSimple com ativação pós-falha para o dispositivo virtual, uma VM de réplica será colocada no Azure e os volumes estão ligados à VM.

#### <a name="to-perform-a-failover"></a>Para efetuar uma ativação pós-falha
1. No portal do Azure, selecione **serviços de recuperação** cofre &gt; **planos de recuperação (recuperação de sites)** &gt; **recoveryplan_name** criado para o servidor de ficheiros de VM.
1. No painel de plano de recuperação, clique em **mais** &gt; **ativação pós-falha**.
1. Sobre o **confirmar ativação pós-falha** painel, selecione a origem e localizações de destino.
1. Selecione **encerrar máquinas virtuais e sincronizar os dados mais recentes** para especificar que o Site Recovery deve tentar encerrar a máquina virtual protegida e sincronizar os dados para que a versão mais recente dos dados irá efetuar a ativação pós-falha.
1. Após a ativação pós-falha, as máquinas virtuais estão no estado de confirmação pendente. Clique em **consolidação** para consolidar a ativação pós-falha.


## <a name="perform-a-failback"></a>Executar uma reativação pós-falha
Durante uma reativação pós-falha, contentores de volumes do StorSimple são ativação pós-falha para o dispositivo físico depois de uma cópia de segurança está a ser utilizada.

#### <a name="to-perform-a-failback"></a>Para executar uma reativação pós-falha
1. No portal do Azure, selecione **serviços de recuperação** cofre &gt; **planos de recuperação (recuperação de sites)** &gt; **recoveryplan_name** criado para o servidor de ficheiros de VM.
1. No painel de plano de recuperação, clique em **mais** &gt; **ativação pós-falha planeada**.
1. Escolha as localizações de origem e destino, selecione a sincronização de dados apropriado e opções de criação de VM.
1. Clique em **OK** botão para iniciar o processo de reativação pós-falha.
   
   ![Iniciar a reativação pós-falha](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Melhores práticas
### <a name="capacity-planning-and-readiness-assessment"></a>Avaliação de preparação de planejamento e capacidade
#### <a name="hyper-v-site"></a>Site Hyper-V
Utilize o [ferramenta Planeador de capacidade de utilizador](https://www.microsoft.com/download/details.aspx?id=39057) para estruturar o servidor, armazenamento e infraestrutura de rede para o seu ambiente de réplica do Hyper-V.

#### <a name="azure"></a>Azure
Pode executar o [ferramenta de avaliação de preparação de máquinas virtuais do Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que sejam compatíveis com as VMs do Azure e serviços de recuperação de Site do Azure. A ferramenta de avaliação de preparação verifica as configurações de VM e avisa quando as configurações são incompatíveis com o Azure. Por exemplo, emite um aviso se uma unidade c: é maior do que 127 GB.

Planeamento da capacidade é constituído por, pelo menos, dois processos importantes:

   - Mapeamento de local VMs de Hyper-V para tamanhos de VM do Azure (por exemplo, A6, A7, A8 e A9).
   - Determinar a largura de banda de Internet necessária.

## <a name="limitations"></a>Limitações
- Atualmente, apenas 1 dispositivo do StorSimple pode efetuar a ativação pós-falha (para uma StorSimple Cloud Appliance único). O cenário de um servidor de ficheiros que abranja vários dispositivos do StorSimple ainda não é suportado.
- Se obtiver um erro ao ativar a proteção para uma VM, certifique-se de que já desconectados destinos iSCSI.
- Todos os contentores de volume foram agrupados em conjunto devido a políticas de cópia de segurança que abrangem em contentores de volumes irão efetuar a ativação pós-falha em conjunto.
- Todos os volumes dos contentores de volume tiver optado por irão efetuar a ativação pós-falha.
- Não é possível efetuar a volumes que adicionar até 64 TB, mais de ativação pós-falha porque a capacidade máxima de uma StorSimple Cloud Appliance único é 64 TB.
- Se falha a ativação pós-falha planeada/não planeada e as VMs são criadas no Azure, em seguida, não limpe as VMs. Em vez disso, fazer uma reativação pós-falha. Se eliminar as VMs, em seguida, as VMs no local não podem ser ativadas novamente.
- Após uma ativação pós-falha, se não for capaz de ver os volumes, vá para as VMs, abra a gestão de discos, reanalise os discos e, em seguida, colocá-los online.
- Em alguns casos, as letras de unidade no site de DR podem ser diferentes de letras no local. Se isto ocorrer, terá de corrigir manualmente o problema depois de concluída a ativação pós-falha.
- Tempo limite de tarefa de ativação pós-falha: O script de StorSimple expirará se a ativação pós-falha dos contentores de volumes demora mais tempo do que o limite do Azure Site Recovery por script (atualmente a 120 minutos).
- Tempo limite da tarefa de cópia de segurança: O script de StorSimple exceder o tempo limite se a cópia de segurança de volumes demora mais tempo do que o limite do Azure Site Recovery por script (atualmente a 120 minutos).
   
  > [!IMPORTANT]
  > Executar a cópia de segurança manualmente a partir do portal do Azure e, em seguida, execute novamente o plano de recuperação.
   
- Tempo limite de tarefa de clone: O script de StorSimple exceder o tempo limite se a clonagem de volumes demora mais tempo do que o limite do Azure Site Recovery por script (atualmente a 120 minutos).
- Erro de sincronização de hora: O StorSimple scripts erros horizontalmente dizendo que as cópias de segurança foram sem êxito, apesar da cópia de segurança é efetuada com êxito no portal. Uma causa possível para que isso pode ser que o tempo da aplicação StorSimple pode ser sincronizado com a hora atual no fuso horário.
   
  > [!IMPORTANT]
  > Sincronize a hora da aplicação com a hora atual no fuso horário.
   
- Erro de ativação pós-falha da aplicação: O script do StorSimple pode falhar se houver uma ativação pós-falha de aplicação quando o plano de recuperação está em execução.
   
  > [!IMPORTANT]
  > Volte a executar o plano de recuperação depois de concluída a ativação pós-falha da aplicação.


## <a name="summary"></a>Resumo
Com o Azure Site Recovery, pode criar um plano de recuperação após desastre automatizado completa para um servidor de ficheiros VM ter partilhas de ficheiros alojadas no armazenamento do StorSimple. Pode iniciar a ativação pós-falha dentro de segundos a partir de qualquer lugar em caso de uma interrupção e a obter o aplicativo em funcionamento dentro de alguns minutos.
