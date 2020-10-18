---
title: Automatizar o fileshare DR da StorSimple com a Recuperação do Site Azure Microsoft Docs
description: Descreve os passos e as melhores práticas para criar uma solução de recuperação de desastres para ações de ficheiros hospedadas no armazenamento Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: 30a5f92e0092d3e20db25b519fec46e6018dd543
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168023"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução automatizada de recuperação de desastres utilizando a recuperação do site Azure para ações de ficheiros hospedadas no StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
O Microsoft Azure StorSimple é uma solução híbrida de armazenamento em nuvem que aborda as complexidades de dados não estruturados geralmente associados a ações de ficheiros. O StorSimple utiliza o armazenamento em nuvem como uma extensão da solução no local e tiers automaticamente dados através do armazenamento no local e armazenamento em nuvem. A proteção integrada de dados, com instantâneos locais e em nuvem, elimina a necessidade de uma infraestrutura de armazenamento em expansão.

[A Azure Site Recovery](../site-recovery/site-recovery-overview.md) é um serviço baseado em Azure que fornece capacidades de recuperação de desastres (DR) orquestrando replicação, failover e recuperação de máquinas virtuais. A Azure Site Recovery suporta uma série de tecnologias de replicação para replicar, proteger e falhar perfeitamente sobre máquinas virtuais e aplicações para nuvens privadas/públicas ou hospedadas.

Utilizando a recuperação do site Azure, a replicação virtual da máquina e as capacidades de instantâneo em nuvem StorSimple, pode proteger o ambiente completo do servidor de ficheiros. Em caso de interrupção, pode utilizar um único clique para colocar as suas ações de ficheiros online em Azure em apenas alguns minutos.

Este documento explica em detalhe como pode criar uma solução de recuperação de desastres para as suas ações de ficheiros hospedadas no armazenamento StorSimple, e executar falhas planeadas, não planeadas e de teste usando um plano de recuperação de um clique. No essencial, mostra como pode modificar o Plano de Recuperação no seu cofre de recuperação do local de Azure para permitir falhas storSimple durante cenários de desastres. Além disso, descreve configurações suportadas e pré-requisitos. Este documento assume que está familiarizado com os fundamentos da Recuperação do Sítio Azure e das arquiteturas StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implementação do Azure Site Recovery
Os clientes podem implementar servidores de ficheiros como servidores físicos ou máquinas virtuais (VMs) em funcionamento em Hiper-V ou VMware e, em seguida, criar partilhas de ficheiros a partir de volumes esculpidos a partir do armazenamento StorSimple. A recuperação do local de Azure pode proteger as implementações físicas e virtuais para um site secundário ou para Azure. Este documento cobre detalhes de uma solução DR com a Azure como o site de recuperação de um servidor de ficheiros VM hospedado no Hyper-V e com ações de ficheiros no armazenamento StorSimple. Outros cenários em que o servidor de ficheiros VM está num VMware VM ou numa máquina física podem ser implementados da mesma forma.

## <a name="prerequisites"></a>Pré-requisitos
Implementar uma solução de recuperação de desastres de um clique que utiliza a Recuperação do Site Azure para ações de ficheiros hospedadas no armazenamento StorSimple tem os seguintes pré-requisitos:

   - No local Windows Server 2012 R2 Servidor de ficheiros VM hospedado em Hyper-V ou VMware ou uma máquina física
   - Dispositivo de armazenamento StorSimple no local registado com gerente da Azure StorSimple
   - StorSimple Cloud Appliance criado no gestor Azure StorSimple. O aparelho pode ser mantido num estado de paragem.
   - Ações de ficheiros acolhidas nos volumes configurados no dispositivo de armazenamento StorSimple
   - [Cofre de serviços de recuperação de site Azure](../site-recovery/site-recovery-vmm-to-vmm.md) criado numa subscrição do Microsoft Azure

Além disso, se o Azure for o seu local de recuperação, execute a [ferramenta de Avaliação de Prontidão virtual da máquina Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que são compatíveis com os serviços de VMs Azure e Azure Site Recovery.

Para evitar problemas de latência (que podem resultar em custos mais elevados), certifique-se de que cria o seu StorSimple Cloud Appliance, a sua conta de automação e a conta de armazenamento na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Ativar ações de ficheiros StorSimple
Cada componente do ambiente no local deve ser protegido para permitir a replicação e recuperação completas. Esta secção descreve como:
    
   - Configurar o Diretório Ativo e a replicação de DNS (opcional)
   - Utilize a recuperação do site Azure para permitir a proteção do servidor de ficheiros VM
   - Permitir a proteção de volumes StorSimple
   - Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar o Diretório Ativo e a replicação de DNS (opcional)
Se pretender proteger as máquinas que executam o Ative Directory e o DNS para que estejam disponíveis no site DR, tem de as proteger explicitamente (para que os servidores de ficheiros fiquem acessíveis após falha com autenticação). Existem duas opções recomendadas com base na complexidade do ambiente no local do cliente.

#### <a name="option-1"></a>Opção 1
Se o cliente tiver um pequeno número de aplicações, um único controlador de domínio para todo o local, e falhar em todo o site, então recomendamos a utilização da replicação do Azure Site Recovery para replicar a máquina de controlador de domínio para um site secundário (isto é aplicável tanto para o local-para-local como para o local-para-Azure).

#### <a name="option-2"></a>Opção 2
Se o cliente tiver um grande número de aplicações, estiver a executar uma floresta de Diretório Ativo, e falhar algumas aplicações de cada vez, então recomendamos a criação de um controlador de domínio adicional no site DR (seja um site secundário ou em Azure).

Consulte a [solução DE DR automatizada para o Diretório Ativo e DNS utilizando a Recuperação do Local de Azure](../site-recovery/site-recovery-active-directory.md) para obter instruções ao disponibilizar um controlador de domínio no site DR. Para o resto deste documento, assumimos que um controlador de domínio está disponível no site DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Utilize a recuperação do site Azure para permitir a proteção do servidor de ficheiros VM
Este passo requer que prepare o ambiente do servidor de ficheiros no local, crie e prepare um cofre de recuperação do local de Azure e permita a proteção de ficheiros do VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar o ambiente do servidor de ficheiros no local
1. Desa esta medida, **desa** **cos para nunca notificar.** Isto é necessário para que possa utilizar scripts de automatização Azure para ligar os alvos iSCSI após falha da Azure Site Recovery.
   
   1. Prima a tecla Do Windows +Q e procure **por UAC**.  
   1. Selecione **alterar as definições de Controlo de Conta de Utilizador**.  
   1. Arraste a barra para o fundo para **nunca notifique**.  
   1. Clique **em OK** e, em seguida, selecione **Sim** quando solicitado.  
   
      ![Definições de controlo da conta de utilizador](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Instale o Agente VM em cada um dos VMs do servidor de ficheiros. Isto é necessário para que possa executar scripts de automação Azure nas VMs falhadas.
   
   1. [Descarregue o agente](https://aka.ms/vmagentwin) para `C:\\Users\\<username>\\Downloads` .
   1. Abrir o Windows PowerShell no modo administrador (executar como administrador) e, em seguida, introduzir o seguinte comando para navegar para o local de descarregamento:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > O nome do ficheiro pode ser alterado dependendo da versão.
      
1. Clique em **Seguinte**.
1. Aceite os **Termos de Acordo** e, em seguida, clique em **Seguinte**.
1. Clique em **Concluir**.
1. Criar ações de ficheiros utilizando volumes esculpidos no armazenamento StorSimple. Para obter mais informações, consulte [o serviço StorSimple Manager para gerir volumes.](storsimple-manage-volumes.md)
   
   1. Nos seus VMs no local, prima a tecla Do Windows +Q e procure **o iSCSI**.
   1. Selecione **o iniciador iSCSI**.
   1. Selecione o **separador Configuração** e copie o nome do iniciador.
   1. Faça login no [portal Azure](https://portal.azure.com/).
   1. Selecione o **separador StorSimple** e, em seguida, selecione o Serviço de Gestor StorSimple que contém o dispositivo físico.
   1. Crie recipientes de volume e, em seguida, crie volumes. (Estes volumes são para a(s) partilha de ficheiros(s) nos VMs do servidor de ficheiros). Copie o nome do iniciador e dê um nome apropriado para os Registos de Controlo de Acesso quando criar os volumes.
   1. Selecione o **separador Configurar** e anotar o endereço IP do dispositivo.
   1. Nas suas VMs no local, vá novamente ao **iniciador iSCSI** e introduza o IP na secção Quick Connect. Clique em **"Ligação Rápida"** (o dispositivo deve agora ser ligado).
   1. Abra o portal Azure e selecione o **separador Volumes e Dispositivos.** Clique **em Configuração Automática**. O volume que criou deve aparecer.
   1. No portal, selecione o separador **Dispositivos** e, em seguida, **selecione Criar um novo dispositivo virtual.** (Este dispositivo virtual será utilizado se ocorrer uma falha). Este novo dispositivo virtual pode ser mantido em estado offline para evitar custos adicionais. Para desligar o dispositivo virtual, aceda à secção **Máquinas Virtuais** no Portal e desligue-o.
   1. Volte para os VMs no local e abra a Gestão do Disco (prima a tecla Windows + X e selecione **Gestão do Disco).**
   1. Irá notar alguns discos extra (dependendo do número de volumes que criou). Clique com o botão direito no primeiro, **selecione Initialize Disk**, e selecione **OK**. Clique à direita na secção **Não atribuída,** selecione **New Simple Volume,** atribua-lhe uma letra de unidade e termine o assistente.
   1. Repita o passo L para todos os discos. Agora pode ver todos os discos **deste PC** no Windows Explorer.
   1. Utilize a função de Serviços de Ficheiros e Armazenamento para criar ações de ficheiros nestes volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para criar e preparar um cofre de recuperação do local de Azure
Consulte a [documentação de recuperação do site Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar com a Recuperação do Site Azure antes de proteger o VM do servidor de ficheiros.

#### <a name="to-enable-protection"></a>Para permitir a proteção
1. Desligue o(s) alvo iSCSI dos VMs no local que pretende proteger através da Recuperação do Sítio azul:
   
   1. Prima a tecla Do Windows + Q e procure **o iSCSI**.
   1. Selecione **Configurar o iniciador iSCSI**.
   1. Desligue o dispositivo StorSimple que ligou anteriormente. Em alternativa, pode desligar o servidor de ficheiros durante alguns minutos ao ativar a proteção.
      
   > [!NOTE]
   > Isto fará com que as ações dos ficheiros não estejam temporariamente disponíveis.
   
1. [Ativar a proteção da máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md) do servidor de ficheiros VM a partir do portal de recuperação do site Azure.
1. Quando a sincronização inicial começar, pode voltar a ligar o alvo. Vá ao iniciador iSCSI, selecione o dispositivo StorSimple e clique em **Connect**.
1. Quando a sincronização estiver completa e o estado do VM estiver **protegido,** selecione o VM, selecione o **separador Configure** e atualize a rede do VM em conformidade (esta é a rede da qual a falha sobre VM(s) fará parte). Se a rede não aparecer, significa que a sincronização continua.

### <a name="enable-protection-of-storsimple-volumes"></a>Permitir a proteção de volumes StorSimple
Se não tiver selecionado a **Cópia de Segurança Para esta** opção de volume para os volumes StorSimple, vá às Políticas de **Backup** no serviço StorSimple Manager e crie uma política de backup adequada para todos os volumes. Recomendamos que desfase a frequência de backups para o objetivo do ponto de recuperação (RPO) que gostaria de ver para a aplicação.

### <a name="configure-the-network"></a>Configurar a rede
Para o servidor de ficheiros VM, configurar as definições de rede na Recuperação do Site Azure para que as redes VM sejam anexadas à rede DR correta após a falha.

Pode selecionar o VM no separador **itens replicados** para configurar as definições de rede, como mostra a seguinte ilustração.

![Computação e Rede](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Pode criar um plano de recuperação na ASR para automatizar o processo de failover das ações de ficheiro. Se ocorrer uma interrupção, pode trazer as ações do ficheiro em poucos minutos com apenas um clique. Para ativar esta automatização, necessitará de uma conta de automação Azure.

#### <a name="to-create-an-automation-account"></a>Para criar uma conta de Automação
1. Aceda à secção &gt; **de Automação** do portal Azure.
1. Clique **+ Adicione o** botão, abre abaixo da lâmina.
   
   ![Adicionar Conta de Automatização](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Nome - Introduza uma nova conta de automação
   - Assinatura - Escolha a subscrição
   - Grupo de recursos - Criar novo/escolher o grupo de recursos existente
   - Localização - Escolha a localização, mantenha-a na mesma geo/região em que foram criadas as Contas de Armazenamento e Eletrodomésticos StorSimple Cloud.
   - Criar Azure Run As account - Selecione **Sim** opção.
   
1. Vá à conta Automation, clique em **Runbooks** &gt; **Browse Gallery** para importar todos os livros de execução necessários para a conta de automação.
1. Adicione os seguintes livros de execução encontrando a etiqueta **de recuperação de desastres** na galeria:
   
   - Limpeza dos volumes StorSimple após falha de teste (TFO)
   - Recipientes de volume StorSimple de failover
   - Monte os volumes no dispositivo StorSimple após a falha
   - Desinstalar extensão de script personalizado em Azure VM
   - Iniciar O Aparelho Virtual StorSimple
   
      ![Procurar Galeria de Navegação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publique todos os scripts selecionando o runbook na conta de automação e clique em **Edit** &gt; **Publish** e, em seguida, **Sim** para a mensagem de verificação. Após este passo, o **separador Runbooks** aparecerá da seguinte forma:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Na conta de automatização, clique em **Variáveis** &gt; **Adicione uma variável** e adicione as seguintes variáveis. Pode optar por encriptar estes ativos. Estas variáveis são específicas do plano de recuperação. Se o seu plano de recuperação, que irá criar no próximo passo, o nome é TestPlan, então as suas variáveis devem ser TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName, e assim por diante.

   - **BaseUrl**: O url do Gestor de Recursos para a nuvem Azure. Utilize **o Get-AzEnvironment / Select-Object Name, ResourceManagerUrl** cmdlet.
   - _RecoveryPlanName_**-ResourceGroupName**: O grupo gestor de recursos que tem o recurso StorSimple.
   - _RecoveryPlanName_**-ManagerName**: O recurso StorSimple que tem o dispositivo StorSimple.
   - _RecoveryPlanName_**-DeviceName**: O dispositivo StorSimple que tem de ser chumbado.
   - _RecoveryPlanName_**-DeviceIpAddress**: O endereço IP do dispositivo (isto pode ser encontrado no separador **dispositivos** no separador StorSimple Device Manager Do grupo &gt; **Definições** DE &gt; **Rede** &gt; **DNS).**
   - _RecoveryPlanName_**-VolumeContainers**: Uma cadeia separada por vírgula de recipientes de volume presentes no dispositivo que precisa de ser chumbada; por exemplo: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: O aparelho de nuvem StorSimple no qual os recipientes devem ser chumbados.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: O endereço IP do dispositivo-alvo (isto pode ser encontrado no separador de rede de configurações do grupo de configurações **da máquina** &gt; **Settings** &gt; **Networking** virtual).
   - _RecoveryPlanName_**-StorageAccountName**: O nome da conta de armazenamento em que o script (que tem de ser executado no falhado sobre VM) será armazenado. Esta pode ser qualquer conta de armazenamento que tenha algum espaço para armazenar o script temporariamente.
   - _RecoveryPlanName_**-StorageAccountKey**: A chave de acesso para a conta de armazenamento acima.
   - _RecoveryPlanName_**-VMGUIDS**: Ao proteger um VM, a Recuperação do Sítio Azure atribui a cada VM um ID único que dá os detalhes do falhado sobre VM. Para obter o VMGUID, selecione o separador **Serviços de Recuperação** e clique em Propriedades de Grupos de Proteção de **Protected Item** &gt; **Protection Groups** &gt; **Artigos Protegidos** &gt; **Properties**. Se tiver vários VMs, adicione os GUIDs como uma corda separada por vírgula.

     Por exemplo, se o nome do plano de recuperação for ficheiroServerpredayRP, então o separador **Variáveis**, **Ligações** e **Certificados** deverá aparecer da seguinte forma depois de adicionar todos os ativos.

      ![Elementos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Faça upload do módulo Runbook série StorSimple 8000 na sua conta Automation. Utilize os passos abaixo para adicionar um módulo:
   
   1. Abra a powershell, crie uma nova pasta & alterar o diretório para a pasta.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Descarregue o nuget CLI sob a mesma pasta no Step1.
      Várias versões de nuget.exe estão disponíveis em [downloads nuget.](https://www.nuget.org/downloads) Cada link de descarregamento aponta diretamente para um ficheiro .exe, por isso certifique-se de clicar no botão direito e guardar o ficheiro para o seu computador em vez de executá-lo a partir do navegador.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Descarregue o SDK dependente
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Crie um módulo runbook Azure Automation para gestão de dispositivos StorSimple 8000 Series. Utilize os comandos abaixo para criar um ficheiro zip do módulo Automation.
         
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
         
   1. Importe o ficheiro zip do módulo Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) criado em passo acima. Isto pode ser feito selecionando a Conta Demômes, clique em **Módulos** em RECURSOS PARTILHADOS e, em seguida, clique em **Adicionar um módulo.**
   
   Depois de importar o módulo da série StorSimple 8000, o separador **Módulos** deve aparecer da seguinte forma:
   
      ![Módulos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Vá à secção **serviços de recuperação** e selecione o cofre de recuperação do local de Azure que criou anteriormente.
1. Selecione a opção **Planos de Recuperação (Recuperação** de Sítio) do grupo **Manage** e crie um novo plano de recuperação da seguinte forma:
   
   - Clique **+ Reboque** o botão plano, abra abaixo da lâmina.
      
      ![Criar plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Introduza um nome de plano de recuperação, escolha Valores de modelo de Origem, & Desajeição.
   
   - Selecione os VMs do grupo de proteção que pretende incluir no plano de recuperação e clique no botão **OK.**
   
   - Selecione o plano de recuperação que criou anteriormente, clique em **Personalizar** o botão para abrir a vista de personalização do plano de recuperação.
   
   - Clique no botão direito em **Todos os grupos desligados** e clique **em Adicionar pré-ação**.
   
   - Abre Insira a lâmina de ação, introduz um nome, selecione a opção **principal** em Onde executar a opção, selecione a conta Demômes (na qual adicionou os livros de execução) e, em seguida, selecione o runbook **Failover-StorSimple-Volume-Containers.**
   
   - Clique no **Grupo 1: Iniciar** e clicar Adicione a opção de **itens protegidos** e, em seguida, selecione os VMs que devem ser protegidos no plano de recuperação e clique no botão **Ok.** Opcional, se já estiver selecionado VMs.
   
   - Clique no **Grupo 1: Iniciar** e clicar Na opção de **ação do Post** e, em seguida, adicionar todos os seguintes scripts:  
      
      - Livro de execução start-StorSimple-Virtual-Appliance  
      - Falha no livro de recortes de contentores de volume StorSimple  
      - Livro de execuções de volumes de montagem após falha  
      - Desinstalar-personalizado-script-extensão-extensão  
        
   - Adicione uma ação manual após os 4 scripts acima no mesmo **Grupo 1: Secção pós-passo.** Esta ação é o ponto em que pode verificar se está tudo a funcionar corretamente. Esta ação só deve ser adicionada como parte do teste de failover (por isso, selecione apenas a caixa de verificação **de falha de teste).**
    
   - Após a ação manual, adicione o script **'Limpeza'** utilizando o mesmo procedimento que utilizou para os outros livros de execução. **Salve** o plano de recuperação.
    
   > [!NOTE]
   > Ao executar um teste de falha, deve verificar tudo na etapa de ação manual, porque os volumes StorSimple que tinham sido clonados no dispositivo-alvo serão eliminados como parte da limpeza após a conclusão da ação manual.
       
      ![Plano de Recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Realizar um teste de failover
Consulte o guia de acompanhantes [do Diretório Ativo DR Solution](../site-recovery/site-recovery-active-directory.md) para obter considerações específicas do Ative Directory durante o teste de failover. A configuração no local não é perturbada quando ocorre a falha do teste. Os volumes StorSimple que foram ligados aos VM no local são clonados ao StorSimple Cloud Appliance on Azure. Um VM para fins de teste é criado em Azure e os volumes clonados são ligados ao VM.

#### <a name="to-perform-the-test-failover"></a>Para realizar o teste failover
1. No portal Azure, selecione o cofre de recuperação do local.
1. Clique no plano de recuperação criado para o servidor de ficheiros VM.
1. Clique em **Teste Failover**.
1. Selecione a rede virtual Azure à qual os VMs Azure serão ligados após a falha.
   
   ![Iniciar Failover](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Clique em **OK** para iniciar a ativação pós-falha. Pode acompanhar o progresso clicando no VM para abrir as suas propriedades, ou no **trabalho de failover** test em trabalhos de &gt; **Jobs** &gt; **recuperação do local de empregos**de cofre.
1. Após a conclusão do failover, também deverá ser capaz de ver a réplica da máquina Azure aparecer no portal Azure &gt; **Virtual Machines**. Pode realizar as suas validações.
1. Depois de feitas as validações, clique em **Validações Concluídas.** Isto removerá os Volumes StorSimple e desligará o aparelho de nuvem StorSimple.
1. Uma vez feito, clique em **teste de limpeza no** plano de recuperação. Em Notas, registe e guarde todas as observações associadas à ativação pós-falha de teste. Isto eliminará a máquina virtual que foi criada durante o teste de failover.

## <a name="perform-a-planned-failover"></a>Executar uma falha planeada
   Durante uma falha planeada, o servidor de ficheiros VM no local é desligado graciosamente e uma imagem de backup em nuvem dos volumes no dispositivo StorSimple é tomada. Os volumes StorSimple são falhados no dispositivo virtual, uma réplica VM é criada em Azure, e os volumes são ligados ao VM.

#### <a name="to-perform-a-planned-failover"></a>Para realizar um fracasso planeado
1. No portal Azure, selecione Planos de recuperação de **serviços de** &gt; **recuperação de serviços (Recuperação de locais)** &gt; **recoveryplan_name** criados para o servidor de ficheiros VM.
1. Na lâmina do plano **More** de recuperação, clique &gt; **em "Mais Planeada" falha.**  

   ![Screenshot que destaca as opções planeadas de failover e Failover.](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Na lâmina **de failover planeada,** escolha as localizações de origem e alvo e selecione a rede alvo e clique no ícone de verificação ✓ para iniciar o processo de failover.
1. Depois de serem criadas máquinas virtuais réplicas, estão num estado pendente. Clique **em Comprometer-se** a cometer o failover.
1. Após a replicação estar concluída, as máquinas virtuais iniciam-se no local secundário.

## <a name="perform-a-failover"></a>Executar um failover
Durante uma falha não planeada, os volumes StorSimple são falhados no dispositivo virtual, uma réplica VM será criada em Azure, e os volumes são ligados ao VM.

#### <a name="to-perform-a-failover"></a>Para realizar um failover
1. No portal Azure, selecione Planos de recuperação de **serviços de** &gt; **recuperação de serviços (Recuperação de locais)** &gt; **recoveryplan_name** criados para o servidor de ficheiros VM.
1. Na lâmina do plano de recuperação, clique **em Mais** &gt; **Falha .**  
1. Na lâmina **De Confirmação,** escolha a origem e os locais de destino.
1. Selecione **Desligar as máquinas virtuais e sincronizar os dados mais recentes** para especificar que a Recuperação do Site deve tentar desligar a máquina virtual protegida e sincronizar os dados de modo a que a versão mais recente dos dados seja chumbada.
1. Após o fracasso, as máquinas virtuais estão em estado pendente. Clique **em Comprometer-se** a cometer o failover.


## <a name="perform-a-failback"></a>Executar uma reativação pós-falha
Durante uma falha, os recipientes de volume StorSimple são ressartados para o dispositivo físico após a tomada de uma cópia de segurança.

#### <a name="to-perform-a-failback"></a>Para executar uma falha
1. No portal Azure, selecione Planos de recuperação de **serviços de** &gt; **recuperação de serviços (Recuperação de locais)** &gt; **recoveryplan_name** criados para o servidor de ficheiros VM.
1. Na lâmina do plano **More** de recuperação, clique em &gt; **"Falha mais planeada".**  
1. Escolha as localizações de origem e alvo, selecione as opções de sincronização de dados e criação de VM apropriadas.
1. Clique no botão **OK** para iniciar o processo de insusição.
   
   ![Iniciar Falha](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Melhores práticas
### <a name="capacity-planning-and-readiness-assessment"></a>Planeamento de capacidades e avaliação de prontidão
#### <a name="hyper-v-site"></a>Site Hyper-V
Utilize a [ferramenta de planificador de capacidade do utilizador](https://www.microsoft.com/download/details.aspx?id=39057) para conceber o servidor, armazenamento e infraestrutura de rede para o seu ambiente de réplica Hyper-V.

#### <a name="azure"></a>Azure
Pode executar a [ferramenta de avaliação de prontidão virtual da máquina Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que são compatíveis com VMs Azure e Serviços de Recuperação do Local Azure. A Ferramenta de Avaliação de Prontidão verifica as configurações de VM e avisa quando as configurações são incompatíveis com o Azure. Por exemplo, emite um aviso se um C: a unidade é superior a 127 GB.

O planeamento da capacidade é composto por pelo menos dois processos importantes:

   - Mapeamento no local Hiper-VMs para tamanhos Azure VM (tais como A6, A7, A8 e A9).
   - Determinando a largura de banda necessária na Internet.

## <a name="limitations"></a>Limitações
- Atualmente, apenas 1 dispositivo StorSimple pode ser falhado (para um único aparelho de nuvem StorSimple). O cenário de um servidor de ficheiros que abrange vários dispositivos StorSimple ainda não está suportado.
- Se tiver um erro ao mesmo tempo que permite a proteção de um VM, certifique-se de que desligou os alvos do iSCSI.
- Todos os contentores de volume que foram agrupados devido às políticas de backup que se estendem por contentores de volume serão falhados em conjunto.
- Todos os volumes dos recipientes de volume escolhidos serão falhados.
- Os volumes que somam mais de 64 TB não podem ser ressoados porque a capacidade máxima de um único Aparelho em Nuvem StorSimple é de 64 TB.
- Se o failover planeado/não planeado falhar e os VMs forem criados em Azure, então não limpe os VMs. Em vez disso, faça uma falha. Se eliminar os VMs, os VMs no local não podem voltar a ser ligados.
- Depois de um failover, se não conseguir ver os volumes, vá aos VMs, abra a Gestão do Disco, rescana os discos e, em seguida, leve-os on-line.
- Em alguns casos, as letras de unidade no site dr podem ser diferentes das letras no local. Se isto ocorrer, terá de corrigir manualmente o problema após o fim da falha.
- Prazo de trabalho de failover: O script StorSimple irá esgotar-se se a falha dos recipientes de volume demorar mais tempo do que o limite de recuperação do local de Azure por script (atualmente 120 minutos).
- Tempo de trabalho de backup: O script StorSimple termina se a cópia de segurança dos volumes demorar mais tempo do que o limite de recuperação do local de Azure por script (atualmente 120 minutos).
   
  > [!IMPORTANT]
  > Executar a cópia de segurança manualmente a partir do portal Azure e, em seguida, executar novamente o plano de recuperação.
   
- Prazo de trabalho do clone: O script StorSimple termina se a clonagem de volumes demorar mais tempo do que o limite de recuperação do local de Azure por script (atualmente 120 minutos).
- Erro de sincronização de tempo: Os scripts StorSimple erros dizendo que as cópias de segurança não foram bem sucedidas, mesmo que a cópia de segurança tenha sido bem sucedida no portal. Uma causa possível para tal pode ser que o tempo do aparelho StorSimple possa estar dessincronizado com a hora atual no fuso horário.
   
  > [!IMPORTANT]
  > Sincronia o tempo do aparelho com a hora atual no fuso horário.
   
- Erro de falha do aparelho: A script StorSimple pode falhar se houver um aparelho a falhar quando o plano de recuperação estiver em funcionamento.
   
  > [!IMPORTANT]
  > Reencamúndir do plano de recuperação após a falha do aparelho estar completa.


## <a name="summary"></a>Resumo
Utilizando a Recuperação do Site Azure, pode criar um plano completo de recuperação de desastres automatizado para um servidor de ficheiros VM com partilhas de ficheiros hospedadas no armazenamento StorSimple. Pode iniciar a falha em segundos a partir de qualquer lugar em caso de interrupção e pôr a aplicação a funcionar em poucos minutos.
