---
title: Automatizar StorSimple fileshare DR com recuperação do site Azure [ Microsoft Docs
description: Descreve os passos e as melhores práticas para criar uma solução de recuperação de desastres para as partilhas de ficheiros hospedadas no armazenamento Microsoft Azure StorSimple.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67875400"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução automatizada de recuperação de desastres utilizando a Recuperação do Site Azure para ações de ficheiros alojadas no StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
O Microsoft Azure StorSimple é uma solução híbrida de armazenamento em nuvem que aborda as complexidades de dados não estruturados geralmente associados a partilhas de ficheiros. O StorSimple utiliza o armazenamento em nuvem como uma extensão da solução no local e automaticamente nivete os dados através do armazenamento no local e armazenamento em nuvem. A proteção integrada de dados, com instantâneos locais e em nuvem, elimina a necessidade de uma infraestrutura de armazenamento extensiva.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) é um serviço baseado em Azure que fornece capacidades de recuperação de desastres (DR) orquestrando replicação, failover e recuperação de máquinas virtuais. A Azure Site Recovery suporta uma série de tecnologias de replicação para replicar, proteger e falhar perfeitamente sobre máquinas virtuais e aplicações para nuvens privadas/públicas ou hospedadas.

Utilizando a Recuperação do Site Azure, a replicação de máquinas virtuais e as capacidades de instantâneo em nuvem StorSimple, pode proteger o ambiente completo do servidor de ficheiros. Em caso de perturbação, pode utilizar um único clique para colocar as suas partilhas de ficheiros online em Azure em poucos minutos.

Este documento explica em detalhe como pode criar uma solução de recuperação de desastres para as suas ações de ficheiro hospedadas no armazenamento StorSimple, e executar falhas planeadas, não planeadas e de teste usando um plano de recuperação de um clique. No essencial, mostra como pode modificar o Plano de Recuperação no cofre de recuperação do site Azure para permitir falhas no StorSimple durante cenários de desastre. Além disso, descreve configurações e pré-requisitos suportados. Este documento assume que está familiarizado com os fundamentos da Recuperação do Site Azure e das arquiteturas StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implementação de recuperação de sites azure suportadas
Os clientes podem implementar servidores de ficheiros como servidores físicos ou máquinas virtuais (VMs) em hiper-V ou VMware e, em seguida, criar partilhas de ficheiros a partir de volumes esculpidos a partir do armazenamento StorSimple. A Recuperação do Site Azure pode proteger as implementações físicas e virtuais para um local secundário ou para o Azure. Este documento cobre detalhes de uma solução DR com o Azure como o site de recuperação de um VM de servidor de ficheiros hospedado no Hyper-V e com partilhas de ficheiros no armazenamento StorSimple. Outros cenários em que o VM do servidor de ficheiros está num VMware VM ou numa máquina física podem ser implementados da mesma forma.

## <a name="prerequisites"></a>Pré-requisitos
Implementar uma solução de recuperação de desastres de um clique que utiliza a Recuperação do Site Azure para ações de ficheiros alojadas no armazenamento StorSimple tem os seguintes pré-requisitos:

   - No local, o Servidor de Ficheiros Windows Server 2012 R2 VM hospedado em Hiper-V ou VMware ou numa máquina física
   - Dispositivo de armazenamento StorSimple no local registado com gestor Azure StorSimple
   - StorSimple Cloud Appliance criado no gestor Azure StorSimple. O aparelho pode ser mantido em estado de paragem.
   - Partilhas de ficheiros alojadas nos volumes configurados no dispositivo de armazenamento StorSimple
   - Cofre de [serviços azure site recovery](../site-recovery/site-recovery-vmm-to-vmm.md) criado numa subscrição do Microsoft Azure

Além disso, se o Azure for o seu local de recuperação, execute a ferramenta de avaliação de [prontidão de máquina seleção azul](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que são compatíveis com vMs Azure e serviços de recuperação de sites Azure.

Para evitar problemas de latência (que podem resultar em custos mais elevados), certifique-se de que cria o seu StorSimple Cloud Appliance, a conta de automação e a conta de armazenamento na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Ativar DR para ações de ficheiro StorSimple
Cada componente do ambiente no local deve ser protegido para permitir a replicação e recuperação completas. Esta secção descreve como:
    
   - Configurar a replicação de Diretório Ativo e DNS (opcional)
   - Utilize a Recuperação do Site Azure para permitir a proteção do VM do servidor de ficheiros
   - Permitir a proteção dos volumes StorSimple
   - Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar a replicação de Diretório Ativo e DNS (opcional)
Se pretender proteger as máquinas que executam o Ative Directory e o DNS para que estejam disponíveis no site dr, é necessário protegê-las explicitamente (para que os servidores de ficheiros estejam acessíveis após a falha com a autenticação). Existem duas opções recomendadas com base na complexidade do ambiente no local do cliente.

#### <a name="option-1"></a>Opção 1
Se o cliente tiver um pequeno número de aplicações, um único controlador de domínio para todo o site no local, e estará falhando em todo o site, então recomendamos a utilização da replicação de Recuperação do Site Azure para replicar a máquina do controlador de domínio para um local secundário (isto é aplicável tanto para o site como para o local e para o local-para-Azure).

#### <a name="option-2"></a>Opção 2
Se o cliente tiver um grande número de aplicações, estiver a executar uma floresta de Diretório Ativo, e falhará em algumas aplicações de cada vez, então recomendamos a criação de um controlador de domínio adicional no site dr (ou um site secundário ou em Azure).

Consulte a [solução DE DR automatizada para Diretório Ativo e DNS utilizando](../site-recovery/site-recovery-active-directory.md) a Recuperação do Site Azure para obter instruções ao disponibilizar um controlador de domínio no site dr. Para o resto deste documento, assumimos que um controlador de domínio está disponível no site da DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Utilize a Recuperação do Site Azure para permitir a proteção do VM do servidor de ficheiros
Este passo requer que prepare o ambiente do servidor de ficheiros no local, crie e prepare um cofre de recuperação de sites Azure e permita a proteção de ficheiros do VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar o ambiente de servidor de ficheiros no local
1. Detete o Controlo da **Conta do Utilizador** para nunca **notificar**. Isto é necessário para que possa utilizar scripts de automação Azure para ligar os alvos iSCSI após a falha pela Recuperação do Site Azure.
   
   1. Prima a tecla Windows +Q e procure o **UAC**.  
   1. Selecione **alterar as definições**de controlo da conta do utilizador .  
   1. Arraste a barra para o fundo em direção a **Never Notifique**.  
   1. Clique em **OK** e, em seguida, selecione **Sim** quando solicitado.  
   
      ![Definições de controlo da conta do utilizador](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Instale o Agente VM em cada um dos VMs do servidor de ficheiros. Isto é necessário para que possa executar scripts de automação Azure nos VMs falhados.
   
   1. [Descarregue](https://aka.ms/vmagentwin) `C:\\Users\\<username>\\Downloads`o agente para .
   1. Abra o Windows PowerShell no modo Administrador (Executar como Administrador) e, em seguida, introduza o seguinte comando para navegar para o local de descarregamento:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > O nome do ficheiro pode ser alterado dependendo da versão.
      
1. Clique em **Seguinte**.
1. Aceite os **Termos do Acordo** e, em seguida, clique em **Seguinte**.
1. Clique em **Concluir**.
1. Crie partilhas de ficheiros utilizando volumes esculpidos a partir do armazenamento StorSimple. Para mais informações, consulte [Utilize o serviço StorSimple Manager para gerir volumes](storsimple-manage-volumes.md).
   
   1. Nos seus VMs no local, prima a tecla Windows +Q e procure **iSCSI**.
   1. Selecione **o iniciador iSCSI**.
   1. Selecione o separador **Configuração** e copie o nome do iniciador.
   1. Faça login no [portal Azure.](https://portal.azure.com/)
   1. Selecione o **separador StorSimple** e, em seguida, selecione o Serviço de Gerente StorSimple que contém o dispositivo físico.
   1. Criar recipientes de volume e, em seguida, criar volumes( s). (Estes volumes destinam-se à partilha de ficheiros(s) nos VMs do servidor de ficheiros). Copie o nome do iniciador e dê um nome adequado para os Registos de Controlo de Acesso quando criar os volumes.
   1. Selecione o **separador Configurar** e note o endereço IP do dispositivo.
   1. Nas suas VMs no local, vá ao **iniciador iSCSI** novamente e introduza o IP na secção Quick Connect. Clique em **Quick Connect** (o dispositivo deve agora ser ligado).
   1. Abra o portal Azure e selecione o separador **Volumes e Dispositivos.** Clique em **Configurar automaticamente**. O volume que criou deve aparecer.
   1. No portal, selecione o separador **Dispositivos** e, em seguida, selecione **Criar um Novo Dispositivo Virtual.** (Este dispositivo virtual será utilizado se ocorrer uma falha). Este novo dispositivo virtual pode ser mantido em estado offline para evitar custos adicionais. Para desligar o dispositivo virtual, vá à secção **De Máquinas Virtuais** no Portal e desligue-o.
   1. Volte aos VMs no local e abra a Gestão do Disco (prima a tecla Windows + X e selecione Gestão de **Discos).**
   1. Você vai notar alguns discos extra (dependendo do número de volumes que criou). Clique no primeiro, **selecione Inicialize o Disco,** e selecione **OK**. Clique na secção **Não atribuída,** selecione **Novo Volume Simples,** atribua-lhe uma letra de unidade e termine o assistente.
   1. Repita o passo L para todos os discos. Agora pode ver todos os discos **deste PC** no Windows Explorer.
   1. Utilize a função De Serviços de Arquivo e Armazenamento para criar partilhas de ficheiros nestes volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para criar e preparar um cofre de recuperação do local do Azure
Consulte a documentação de recuperação do [site Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar com a Recuperação do Site Azure antes de proteger o VM do servidor de ficheiros.

#### <a name="to-enable-protection"></a>Para permitir a proteção
1. Desligue os alvos iSCSI dos VMs no local que pretende proteger através da Recuperação do Site Azure:
   
   1. Prima tecla Windows + Q e procure **iSCSI**.
   1. **Selecione Configurar o iniciador iSCSI**.
   1. Desligue o dispositivo StorSimple que ligou anteriormente. Em alternativa, pode desligar o servidor de ficheiros durante alguns minutos ao ativar a proteção.
      
   > [!NOTE]
   > Isto fará com que as ações do ficheiro fiquem temporariamente indisponíveis.
   
1. [Ativar a proteção virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md) da máquina do servidor de ficheiros VM do portal de recuperação do site Azure.
1. Quando a sincronização inicial começar, pode voltar a ligar o alvo. Vá ao iniciador iSCSI, selecione o dispositivo StorSimple e clique em **Connect**.
1. Quando a sincronização estiver completa e o estado do VM estiver **protegido**, selecione o VM, selecione o **separador Configure** e atualize a rede do VM em conformidade (esta é a rede da rede da quais o falhou sobre os VM(s) será parte). Se a rede não aparecer, significa que a sincronização ainda está a decorrer.

### <a name="enable-protection-of-storsimple-volumes"></a>Permitir a proteção dos volumes StorSimple
Se não tiver selecionado a cópia de **segurança de 'Activar' para esta** opção de volume para os volumes StorSimple, vá a Políticas de **Backup** no serviço StorSimple Manager e crie uma política de backup adequada para todos os volumes. Recomendamos que detetete a frequência de backups para o objetivo do ponto de recuperação (RPO) que gostaria de ver para a aplicação.

### <a name="configure-the-network"></a>Configurar a rede
Para o VM do servidor de ficheiros, configure as definições de rede na Recuperação do Site Azure de modo a que as redes VM sejam anexadas à rede DR correta após a falha.

Pode selecionar o VM no separador de **itens replicados** para configurar as definições de rede, como mostra a seguinte ilustração.

![Computação e Rede](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Pode criar um plano de recuperação na ASR para automatizar o processo de failover das ações de ficheiro. Se ocorrer uma perturbação, pode trazer as partilhas do ficheiro em poucos minutos com apenas um clique. Para ativar esta automatização, necessitará de uma conta de automação Azure.

#### <a name="to-create-an-automation-account"></a>Criar uma conta de Automação
1. Vá à secção &gt; de **automação** do portal Azure.
1. Clique **+ Adicione** o botão, abre abaixo da lâmina.
   
   ![Adicionar Conta de Automatização](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Nome - Insira uma nova conta de automação
   - Subscrição - Escolha subscrição
   - Grupo de recursos - Criar novo/escolha grupo de recursos existente
   - Localização - Escolha a localização, mantenha-a na mesma geo/região em que foram criadas as Contas de Eletrodomésticos e Armazenamento StorSimple Cloud.
   - Criar azure Run Como conta - Selecione **Sim** opção.
   
1. Vá à conta de Automação, clique em **Runbooks** &gt; **Browse Gallery** para importar todos os livros necessários para a conta de automação.
1. Adicione os seguintes livros de execução encontrando a etiqueta de recuperação de **desastres** na galeria:
   
   - Limpeza de volumes StorSimple após falha de teste (TFO)
   - Recipientes de volume StorSimple Failover
   - Monte volumes no dispositivo StorSimple após falha
   - Desinstalar extensão de script personalizada em Azure VM
   - Iniciar o storSimple Virtual Appliance
   
      ![Galeria de Pesquisa](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publique todos os scripts selecionando o livro de execução na conta de automação e clique em **Editar** &gt; **Publicar** e, **em seguida, Sim** à mensagem de verificação. Após este passo, o separador **Runbooks** aparecerá da seguinte forma:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Na conta de automação, clique em **Variáveis** &gt; **Adicione uma variável** e adicione as seguintes variáveis. Pode optar por encriptar estes ativos. Estas variáveis são específicas do plano de recuperação. Se o seu plano de recuperação, que irá criar no próximo passo, o nome é TestPlan, então as suas variáveis devem ser TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName, e assim por diante.

   - **BaseUrl**: O url do Gestor de Recursos para a nuvem Azure. Obtenha a utilização **do Get-AzEnvironment [ Get-AzEnvironment ] Selecione-Object Name, ResourceManagerUrl** cmdlet.
   - _RecoveryPlanName_**-ResourceGroupName**: O grupo Gestor de Recursos que tem o recurso StorSimple.
   - _RecoveryPlanName_**-ManagerName**: O recurso StorSimple que tem o dispositivo StorSimple.
   - _RecoveryPlanName_**-DeviceName**: O dispositivo StorSimple que tem de ser falhado.
   - _RecoveryPlanName_**-DeviceIpAddress**: O endereço IP do dispositivo (isto pode ser encontrado no &gt; separador Dispositivos sob o grupo de definições de **Network** &gt; **Definições de DNS** da secção de **configurações** &gt; do Gestor de **Dispositivos** StorSimple).
   - _RecoveryPlanName_**-VolumeContainers**: Uma série separada de recipientes de volume separados por vírina presentes no dispositivo que precisam de ser reparadas; por exemplo: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: O StorSimple Cloud Appliance em que os recipientes devem ser reparados.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: O endereço IP do dispositivo alvo (isto pode &gt; ser encontrado no separador de definição de &gt; **configurações** da secção **máquina virtual).** **Networking**
   - _RecoveryPlanName_**-StorageAccountName**: O nome da conta de armazenamento no qual o script (que tem de ser executado no VM falhado) será armazenado. Esta pode ser qualquer conta de armazenamento que tenha algum espaço para armazenar o script temporariamente.
   - _RecoveryPlanName_**-StorageAccountKey**: A chave de acesso para a conta de armazenamento acima.
   - _RecoveryPlanName_**-VMGUIDS**: Ao proteger um VM, a Recuperação do Site Azure atribui a cada VM um ID único que dá os detalhes do falhado sobre o VM. Para obter o VMGUID, selecione o separador **Serviços** de Recuperação e clique em **Propriedades**de **Máquinas de** &gt; **Proteção** &gt; de **Itens** &gt; Protegidos . Se tiver vários VMs, adicione os GUIDs como uma corda separada da vírvia.

     Por exemplo, se o nome do plano de recuperação for fileServerpredayRP, então o separador **Variáveis,** **Ligações** e **Certificados** deve aparecer da seguinte forma depois de adicionar todos os ativos.

      ![Elementos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Carregue o módulo Runbook da série StorSimple 8000 na sua conta Automation. Utilize os passos abaixo para adicionar um módulo:
   
   1. Abra a powershell, crie uma nova pasta & mudar o diretório para a pasta.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Baixe o clI nuget sob a mesma pasta no Step1.
      Várias versões de nuget.exe estão disponíveis em [downloads de nuget](https://www.nuget.org/downloads). Cada link de descarregamento aponta diretamente para um ficheiro .exe, por isso certifique-se de clicar à direita e guardar o ficheiro para o seu computador em vez de executá-lo a partir do navegador.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Descarregue o SDK dependente
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Crie um Módulo de Rexecução de Automação Azure para a gestão de dispositivos da Série 8000 StorSimple 8000. Utilize os comandos abaixo para criar um ficheiro zip módulo Automation.
         
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
         
   1. Importar o ficheiro zip do módulo Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) criado em passo acima. Isto pode ser feito selecionando a Conta de Automação, clique em **Módulos** em RECURSOS PARTILHADOS e, em seguida, clique em **Adicionar um módulo**.
   
   Depois de importar o módulo da série StorSimple 8000, o separador **Módulos** deve aparecer da seguinte forma:
   
      ![Módulos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Vá à secção **de Serviços** de Recuperação e selecione o cofre azure de recuperação do local que criou anteriormente.
1. Selecione a opção Planos de **Recuperação (Recuperação do Site)** do grupo **Manage** e crie um novo plano de recuperação da seguinte forma:
   
   - Clique **+ Botão de plano de recuperação,** abre abaixo da lâmina.
      
      ![Criar plano de recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Introduza um nome de plano de recuperação, escolha Valores do modelo Source, Target & Deployment.
   
   - Selecione os VMs do grupo de proteção que pretende incluir no plano de recuperação e clique no botão **OK.**
   
   - Selecione plano de recuperação que criou anteriormente, clique em **Personalizar** o botão para abrir a vista de personalização do plano recovery.
   
   - Clique à direita em **Todos os grupos para desligar** e clique em Adicionar **pré-acção**.
   
   - Abre a lâmina de ação Inserir, introduza um nome, selecione a opção **lateral primária** em onde executar a opção, selecione a conta Automation (na qual adicionou os livros de execução) e, em seguida, selecione o livro de execução **Failover-StorSimple-Volume-Containers.**
   
   - Clique no **Grupo 1: Iniciar** e clicar Em adicionar itens **protegidos** e, em seguida, selecione os VMs que estão protegidos no plano de recuperação e clique no botão **Click Ok.** Opcional, se já tiver VMs selecionados.
   
   - Clique no **Grupo 1: Iniciar** e clicar na opção de ação **Post** e, em seguida, adicionar todos os seguintes scripts:  
      
      - Livro de execução start-StorSimple-Virtual-Appliance  
      - Fail over-StorSimple-volume-containers runbook  
      - Livro de escoamento de montes-volumes-pós-falha  
      - Livro de execução de extensão de script-desinstalação personalizado  
        
   - Adicione uma ação manual após os 4 scripts acima na mesma secção **do Grupo 1: Pós-passos.** Esta ação é o ponto em que pode verificar se está tudo a funcionar corretamente. Esta ação deve ser adicionada apenas como parte da falha do teste (por isso, apenas selecione a caixa de verificação **Failover de Teste).**
    
   - Após a ação manual, adicione o script **cleanup** utilizando o mesmo procedimento que utilizou para os outros livros de execução. **Salve** o plano de recuperação.
    
   > [!NOTE]
   > Ao executar uma falha de teste, deve verificar tudo na fase de ação manual, uma vez que os volumes StorSimple que tinham sido clonados no dispositivo-alvo serão eliminados como parte da limpeza após a conclusão da ação manual.
       
      ![Plano de Recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Realizar uma falha de teste
Consulte o guia de acompanhantes [ative Directory DR Solution](../site-recovery/site-recovery-active-directory.md) para considerações específicas do Diretório Ativo durante a falha do teste. A configuração no local não é perturbada quando ocorre a falha do teste. Os volumes StorSimple que foram ligados ao VM no local são clonados ao StorSimple Cloud Appliance em Azure. Um VM para efeitos de teste é criado em Azure e os volumes clonados são ligados ao VM.

#### <a name="to-perform-the-test-failover"></a>Para realizar o failover do teste
1. No portal Azure, selecione o seu cofre de recuperação do site.
1. Clique no plano de recuperação criado para o VM do servidor de ficheiros.
1. Clique em **Failover do Teste**.
1. Selecione a rede virtual Azure à qual os VMs Azure serão ligados após a falha ocorrer.
   
   ![Iniciar falha](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Clique em **OK** para iniciar a ativação pós-falha. Você pode rastrear o progresso clicando no VM para abrir as suas &gt; propriedades, ou no trabalho de **failover** test em nome de cofre **Empregos** &gt; Trabalhos de **Recuperação do Site**.
1. Após a falha concluída, também deverá ser capaz de ver a réplica &gt; da máquina Azure aparecer no portal Azure **Máquinas Virtuais**. Pode realizar as suas validações.
1. Depois de concluídas as validações, clique em **Validações Completas**. Isto removerá os Volumes StorSimple e desligará o StorSimple Cloud Appliance.
1. Uma vez feito, clique em teste de **limpeza falhando** no plano de recuperação. Em Notas, registe e guarde todas as observações associadas à ativação pós-falha de teste. Isto eliminará a máquina virtual que foi criada durante a falha do teste.

## <a name="perform-a-planned-failover"></a>Realizar uma falha planeada
   Durante uma falha planeada, o vM do servidor de ficheiros no local é desligado graciosamente e é tomada uma imagem de backup em nuvem dos volumes no dispositivo StorSimple. Os volumes StorSimple são falhados no dispositivo virtual, uma réplica VM é criada em Azure, e os volumes são ligados ao VM.

#### <a name="to-perform-a-planned-failover"></a>Para realizar uma falha planeada
1. No portal Azure, selecione **planos** de recuperação de serviços &gt; de **recuperação (Recuperação do Site)** &gt; **recoveryplan_name** criados para o servidor de ficheiros VM.
1. Na lâmina do plano de recuperação, **clique** &gt;em **mais falhas planeadas**.  

   ![Plano de Recuperação](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Na lâmina **De Failover Planeada de Confirmar,** escolha as localizações de origem e alvo e selecione a rede de destino e clique no ícone de verificação ✓ para iniciar o processo de failover.
1. Depois de serem criadas máquinas virtuais réplicas, estão num estado pendente. Clique **em comprometer-se** a cometer a falha.
1. Após a replicação estar completa, as máquinas virtuais iniciam-se no local secundário.

## <a name="perform-a-failover"></a>Realizar uma falha
Durante uma falha não planeada, os volumes StorSimple são falhados no dispositivo virtual, uma réplica VM será trazida para o Azure, e os volumes são anexados ao VM.

#### <a name="to-perform-a-failover"></a>Para realizar uma falha
1. No portal Azure, selecione **planos** de recuperação de serviços &gt; de **recuperação (Recuperação do Site)** &gt; **recoveryplan_name** criados para o servidor de ficheiros VM.
1. Na lâmina do plano de recuperação, clique em **mais** &gt; **failover**.  
1. Na lâmina **'Failover' confirme,** escolha a origem e os locais-alvo.
1. Selecione **Desligar máquinas virtuais e sincronizar os dados mais recentes** para especificar que a Recuperação do Site deve tentar desligar a máquina virtual protegida e sincronizar os dados para que a versão mais recente dos dados seja falhada.
1. Após a falha, as máquinas virtuais estão num estado pendente. Clique **em comprometer-se** a cometer a falha.


## <a name="perform-a-failback"></a>Executar uma reativação pós-falha
Durante uma falha, os recipientes de volume StorSimple são falhados de volta ao dispositivo físico após a tomada de uma cópia de segurança.

#### <a name="to-perform-a-failback"></a>Para realizar uma falha
1. No portal Azure, selecione **planos** de recuperação de serviços &gt; de **recuperação (Recuperação do Site)** &gt; **recoveryplan_name** criados para o servidor de ficheiros VM.
1. Na lâmina do plano de recuperação, clique em **Failover** **mais** &gt;planeado .  
1. Escolha as localizações de origem e destino, selecione as opções adequadas de sincronização de dados e criação de VM.
1. Clique no botão **OK** para iniciar o processo de reinicialização.
   
   ![Iniciar o failback](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Melhores práticas
### <a name="capacity-planning-and-readiness-assessment"></a>Avaliação do planeamento e prontidão da capacidade
#### <a name="hyper-v-site"></a>Site Hyper-V
Utilize a ferramenta de [planejador](https://www.microsoft.com/download/details.aspx?id=39057) de capacidade do utilizador para conceber o servidor, armazenamento e infraestrutura de rede para o seu ambiente de réplica Hyper-V.

#### <a name="azure"></a>Azure
Pode executar a ferramenta de avaliação de [prontidão de máquina seleção azul](https://azure.microsoft.com/downloads/vm-readiness-assessment/) em VMs para garantir que são compatíveis com VMs Azure e Serviços de Recuperação de Sites Azure. A Ferramenta de Avaliação de Prontidão verifica as configurações vM e avisa quando as configurações são incompatíveis com o Azure. Por exemplo, emite um aviso se um C: a unidade for superior a 127 GB.

O planeamento da capacidade é constituído por pelo menos dois processos importantes:

   - Mapeando no local Hiper-V VMs para tamanhos De VM Azure (tais como A6, A7, A8 e A9).
   - Determinar a largura de banda de Internet necessária.

## <a name="limitations"></a>Limitações
- Atualmente, apenas 1 dispositivo StorSimple pode ser falhado (para um único StorSimple Cloud Appliance). O cenário de um servidor de ficheiros que abrange vários dispositivos StorSimple ainda não é suportado.
- Se tiver um erro ao ativar a proteção para um VM, certifique-se de que desligou os alvos do iSCSI.
- Todos os contentores de volume que foram agrupados devido a políticas de backup que abrangem contentores de volume serão falhados em conjunto.
- Todos os volumes dos contentores de volume que escolheu serão falhados.
- Os volumes que somam mais de 64 TB não podem ser refalhados porque a capacidade máxima de um único StorSimple Cloud Appliance é de 64 TB.
- Se a falha planeada/não planeada falhar e os VMs forem criados em Azure, então não limpe os VMs. Em vez disso, faça uma falha. Se eliminar os VMs, os VMs no local não podem voltar a ser ligados.
- Depois de uma falha, se não conseguir ver os volumes, vá aos VMs, abra a Gestão do Disco, rescante os discos e, em seguida, traga-os online.
- Em alguns casos, as cartas de condução no site da DR podem ser diferentes das letras no local. Se isto ocorrer, terá de corrigir manualmente o problema após a falha estar terminada.
- Tempo limite de trabalho de failover: O script StorSimple irá esgotar-se se a falha dos contentores de volume demorar mais tempo do que o limite de recuperação do site Azure por script (atualmente 120 minutos).
- Tempo limite de trabalho de backup: O script StorSimple tem tempo para fora se a cópia de segurança dos volumes demorar mais tempo do que o limite de recuperação do site Azure por script (atualmente 120 minutos).
   
  > [!IMPORTANT]
  > Executar a cópia de segurança manualmente a partir do portal Azure e, em seguida, executar o plano de recuperação novamente.
   
- Tempo limite de trabalho do clone: O script StorSimple tem tempo para fora se a clonagem de volumes demorar mais tempo do que o limite de recuperação do site Azure por script (atualmente 120 minutos).
- Erro de sincronização de tempo: Os scripts StorSimple falham dizendo que as cópias de segurança não foram bem sucedidas, mesmo que a cópia de segurança tenha sucesso no portal. Uma possível causa para isso pode ser que o tempo do aparelho StorSimple possa estar dessincronizado com o tempo atual no fuso horário.
   
  > [!IMPORTANT]
  > Sincronize o tempo do aparelho com o tempo atual no fuso horário.
   
- Erro de falha do aparelho: O script StorSimple pode falhar se houver uma falha do aparelho quando o plano de recuperação estiver em funcionamento.
   
  > [!IMPORTANT]
  > Reexecutar o plano de recuperação depois de o aparelho falhar.


## <a name="summary"></a>Resumo
Utilizando a Recuperação do Site Azure, pode criar um plano completo de recuperação de desastres automatizado para um VM do servidor de ficheiros com partilhas de ficheiros hospedadas no armazenamento StorSimple. Pode iniciar a falha em segundos a partir de qualquer lugar em caso de perturbação e colocar a aplicação a funcionar em poucos minutos.
