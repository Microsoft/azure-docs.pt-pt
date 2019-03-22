---
title: Migrar VMs do AWS para o Azure com o Azure com o serviço Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar VMs do Windows em execução no Amazon Web Services (AWS) para o Azure com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 17acd4eebe53704699d3ec9a3f4f121eed79794d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310392"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar VMs do Amazon Web Services (AWS) para o Azure

Este tutorial ensina-lhe a migrar máquinas virtuais (VMs) do Amazon Web Services (AWS) para VMs do Azure com o Azure Site Recovery. Quando migramos instâncias do AWS EC2 para o Azure, as VMs são tratadas como computadores físicos no local. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Preparar recursos do Azure
> * Preparar instâncias AWS EC2 para migração
> * Implementar um servidor de configuração
> * Ativar a replicação nas VMs
> * Testar a ativação pós-falha para confirmar que está tudo a confirmar
> * Executar uma ativação pós-falha única para o Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
- Certifique-se de que as VMs que quer migrar têm uma versão suportada do SO. As versões suportadas incluem: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - a versão de 64 bits do Windows Server 2008 R2 SP1 ou posterior
  - Red Hat Enterprise Linux 6.4 para 6.10, 7.1 a 7,6 (instâncias virtualizadas de HVM) *(instâncias que executem controladores RedHat PV não são suportadas).*
  - CentOS 6.4 para 6.10, 7.1 a 7,6 (instâncias virtualizadas de HVM)
 
- O Serviço de mobilidade tem de ser instalado em cada VM que queira replicar. 

    > [!IMPORTANT]
    > O Site Recovery instala este serviço automaticamente quando a replicação para a VM é ativada. Para a instalação automática, te de preparar uma conta nas instâncias EC2 que o Site Recovery vai utilizar para aceder à VM. Pode utilizar um domínio ou uma conta local. 
    > - Nas VMs do Linux, a conta deve ser a raiz no servidor do Linux de origem. 
    > - Nas VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o Controlo de Acesso de Utilizador Remoto no computador local:
    >
    >      No registo, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**,       adicione a entrada DWORD **LocalAccountTokenFilterPolicy** e defina o valor como **1**.

- Uma instância EC2 separada que possa ser utilizada como o servidor de configuração do Site Recovery. Essa instância deve executar o Window Server 2012 R2.

## <a name="prepare-azure-resources"></a>Preparar recursos do Azure

Tem de ter alguns recursos prontos no Azure para serem utilizados pelas instâncias do EC2 migradas. Esses recursos incluem uma conta de armazenamento, um cofre e uma rede virtual.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens das máquinas replicadas são guardadas no Armazenamento do Microsoft Azure. As VMs do Azure são criadas a partir do armazenamento quando efetuar a ativação pós-falha do local para o Azure.

1. No [portal do Azure](https://portal.azure.com), no menu da esquerda, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.
2. Introduza um nome para a conta do Storage. Para estes tutoriais, utilizamos o nome **awsmigrated2017**. O nome tem de:
    - Ser exclusivo no Azure
    - Ter entre 3 e 24 carateres
    - Conter apenas números e letras minúsculas
3. Deixe as predefinições em **Modelo de implementação**, **Tipo de conta**, **Desempenho** e **Transferência segura necessária**.
5. Para **Replicação**, selecione o **RA-GRS** por predefinição.
6. Selecione a subscrição que pretende utilizar neste tutorial.
7. Em **Grupo de recursos**, selecione **Criar novo**. Neste exemplo, utilizamos **migrationRG** para o nome do grupo de recursos.
8. Em **Localização**, selecione **Europa Ocidental**.
9. Selecione **Criar** para criar a conta de armazenamento.

### <a name="create-a-vault"></a>Criar um cofre

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Procure e, em seguida, selecione **cofres dos serviços de recuperação**.
2. Na página do cofre dos Serviços de Recuperação, selecione **Adicionar**.
3. Para **Nome**, introduza **myVault**.
4. Para **Subscrição**, selecione a subscrição pretende utilizar.
4. Em **Grupo de recursos**, selecione **Utilizar existente** e depois selecione **migrationRG**.
5. Em **Localização**, selecione **Europa Ocidental**.
5. Selecione **Afixar ao dashboard** para ser capaz de aceder rapidamente ao novo cofre a partir do dashboard.
7. Quando terminar, selecione **Criar**.

Para ver o novo cofre, aceda a **Dashboard** > **Todos os recursos**. O novo cofre também aparece na página principal de **Cofres dos Serviços de Recuperação**.

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas após a migração (ativação pós-falha), são associadas a esta rede Azure.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** >
   **Rede virtual**.
3. Em **Nome**, introduza **myMigrationNetwork**.
4. Deixe o valor predefinido em **Espaço de endereços**.
5. Para **Subscrição**, selecione a subscrição pretende utilizar.
6. Em **Grupo de recursos**, selecione **Utilizar existente** e depois selecione **migrationRG**.
7. Em **Localização**, selecione **Europa Ocidental**.
8. Em **Sub-rede**, mantenha os valores predefinidos para **Nome** e **Intervalo de IP**.
9. Deixe a opção de **Pontos finais de serviço** desativada.
10. Quando terminar, selecione **Criar**.

## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura

Na página do seu cofre no portal do Azure, na secção **Introdução**, selecione **Site Recovery**, e então selecione **Preparar infraestrutura**. Siga os seguintes passos.

### <a name="1-protection-goal"></a>1: Objetivo de proteção

Na página **Objetivo da proteção**, selecione os seguintes valores:

|    |  |
|---------|-----------|
| Onde estão localizadas as máquinas? |Escolha **no local**.|
| Para onde pretende replicar as máquinas? |Selecionar **Para o Azure**.|
| As máquinas estão virtualizadas? |Selecione **Não virtualizadas / Outro**.|

Quando tiver terminado, selecione **OK** para avançar para a secção seguinte.

### <a name="2-prepare-source"></a>2: Preparar origem

Na página **Preparar origem**, selecione em **+ Servidor de Configuração**.

1. Utilize uma instância do EC2 que esteja a executar o Windows Server 2012 R2 para criar um servidor de configuração e registá-lo no seu cofre de recuperação.
2. Configure o proxy na VM de instância do EC2 que está a utilizar como servidor de configuração, para que possa aceder aos [URLs do serviço](site-recovery-support-matrix-to-azure.md).
3. Transfira [Microsoft Azure Site Recovery Unified Setup](https://aka.ms/unifiedinstaller_wus) (Configuração Unificada do Microsoft Azure Site Recovery). Pode transferi-lo para o seu computador local e, em seguida, copiá-lo para a VM que está a utilizar como servidor de configuração.
4. Clique no botão **Transferir** para transferir a chave de registo do cofre. Copie o ficheiro transferido para a VM que está a utilizar como o servidor de configuração.
5. Na VM, clique com botão direito do rato no instalador que transferiu para o Microsoft Azure Site Recovery Unified Setup e depois selecione **Executar como administrador**.

    1. Sob **Antes de Começar**, selecione **Instalar o servidor de configuração e o servidor de processos** e depois selecione **Seguinte**.
    2. Em **Licença de Software de Terceiros**, selecione **Aceito o contrato de licença de terceiros**, e depois selecione **Seguinte**.
    3. Em **Registo**, selecione **Procurar** e vá para a localização onde pretende colocar o ficheiro da chave de registo do cofre. Selecione **Seguinte**.
    4. Em **Definições da Internet**, selecione **Ligar ao Azure Site Recovery sem um servidor proxy**, e depois selecione **Seguinte**.
    5. A página **Verificação de Pré-requisitos**, executa verificações relativamente a vários itens. Quando terminar, selecione **Seguinte**.
    6. Em **Configuração de MySQL**, indique as palavras-passe necessárias e depois selecione **Seguinte**.
    7. Em **Detalhes do Ambiente**, selecione **Não**. Não precisa de proteger máquinas de VMware. Em seguida, selecione **Seguinte**.
    8. Em **Localização da Instalação**, selecione **Seguinte** para aceitar a predefinição.
    9. Em **Seleção de rede**, selecione **Seguinte** para aceitar a predefinição.
    10. Em **Resumo**, selecione **Instalar**.
    11. O **Progresso da Instalação** mostra-lhe informações sobre o processo de instalação. Quando terminar, selecione **Concluir**. Uma janela apresenta uma mensagem sobre uma reinicialização. Selecione **OK**. Em seguida, uma janela apresenta uma mensagem sobre a frase de acesso de ligação de servidor de configuração. Copie a frase de acesso para sua área de transferência e guarde-a em local seguro.
6. Na VM, execute o cspsconfigtool.exe para criar uma ou mais contas de gestão no servidor de configuração. Confirme que as contas de gestão têm permissões de administrador nas instâncias do EC2 que pretende migrar.

Quando terminar de configurar o servidor de configuração, regresse ao portal, selecione o servidor que acabou de criar para **Servidor de Configuração**. Selecione **OK** até 3: Prepare o destino.

### <a name="3-prepare-target"></a>3: Preparar o destino

Nesta secção, vai introduzir informações sobre os recursos que criou em [Preparar recursos do Azure](#prepare-azure-resources) mais atrás neste tutorial.

1. Em **Subscrição**, selecione a subscrição do Azure que utilizou no tutorial [Prepare Azure](tutorial-prepare-azure.md) (Preparar o Azure).
2. Selecione **Resource Manager** como o modelo de implementação.
3. O Site Recovery verifica que tem uma ou mais conta de armazenamento e rede do Azure compatível. Estes devem ser os recursos que criou em [Preparar recursos do Azure](#prepare-azure-resources) mais atrás neste tutorial.
4. Quando tiver terminado, selecione **OK**.


### <a name="4-prepare-replication-settings"></a>4: Preparar as definições de replicação

Antes de poder ativar a replicação, tem de criar uma política de replicação.

1. Selecione **Replicar e Associar**.
2. Em **Nome**, introduza **myReplicationPolicy**.
3. Deixe as restantes predefinições e depois selecione **OK** para criar a política. A política nova é associada automaticamente ao servidor de configuração.

### <a name="5-select-deployment-planning"></a>5: Selecione o planeamento da implementação

Em **Concluiu o planeamento da implementação**, selecione **Faço mais tarde**, e selecione **OK**.

Quando tiver terminado todas as cinco secções em **Preparar infraestrutura**, selecione **OK**.


## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação em cada VM que quer migrar. Quando a replicação for ativada, o Site Recovery instala o Serviço de mobilidade automaticamente.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
1. Na página do seu cofre, em **Introdução**, selecione **Site Recovery**.
2. Sob **para máquinas no local e VMs do Azure**, selecione **passo 1: Replicar aplicação**. Conclua as páginas do assistente com as seguintes informações. Selecione **OK** em cada página quando tiver terminado:
   - 1: Configurar origem

     |  |  |
     |-----|-----|
     | Origem: | Selecione **No Local**.|
     | Localização da origem:| Introduza o nome da sua instância do EC2 do servidor de configuração.|
     |Tipo de computador/máquina: | Selecione **Computadores**.|
     | Servidor de processos: | Selecione o servidor de configuração na lista pendente.|

   - 2: Configurar o destino

     |  |  |
     |-----|-----|
     | Destino: | Deixe a predefinição.|
     | Subscrição: | Selecione a subscrição que tem estado a utilizar.|
     | Grupo de recursos de ativação pós-falha:| Utilize o grupo de recursos que criou na secção [Preparar recursos do Azure](#prepare-azure-resources).|
     | Modelo de implementação de ativação pós-falha: | Selecione o **Resource Manager**.|
     | Conta de armazenamento: | Selecione a conta de armazenamento que criou no passo em [Preparar recursos do Azure](#prepare-azure-resources).|
     | Rede do Azure: | Escolha **Configurar agora para as máquinas selecionadas**.|
     | Rede do Azure de ativação pós-falha: | Escolha a rede que criou em [Preparar recursos do Azure](#prepare-azure-resources).|
     | Sub-rede: | Selecione a **predefinição** na lista pendente.|

   - 3: Selecionar máquinas físicas

     Selecione **Computador**, e depois introduza os valores para **Nome**, **Endereço IP** e **Tipo de SO** da instância do EC2 que pretende migrar. Selecione **OK**.

   - 4: Configurar propriedades

     Selecione a conta que criou no servidor de configuração e depois selecione **OK**.

   - 5: Configurar as definições de replicação

     Certifique-se de que a política de replicação selecionada na lista pendente é **myReplicationPolicy** e depois selecione **OK**.

3. Quando o assistente estiver concluído, selecione **Ativar a replicação**.

Para acompanhar o progresso da tarefa **Ativar Proteção**, vá a **Monitorização e relatórios** > **Trabalhos** > **Trabalhos do Site Recovery**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.        

Quando ativa a replicação para uma VM, as alterações podem demorar 15 minutos ou mais a produzir efeitos e a aparecer no portal.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Quando executa uma ativação pós-falha de teste, seguinte os seguintes eventos têm lugar:

- É executada uma verificação dos pré-requisitos, para garantir que estão satisfeitas todas as condições necessárias para a ativação pós-falha.
- A ativação pós-falha processa os dados para que possa ser criada uma VM do Azure. Se selecionar o último ponto de recuperação, será criado um ponto de recuperação a partir dos dados.
- É criada uma VM do Azure utilizando os dados processados no passo anterior.

No portal, execute a ativação pós-falha de teste:

1. Na página do seu cofre, aceda a **Itens protegidos** > **Itens Replicados**. Selecione a VM e, em seguida, selecione **Ativação pós-falha de teste**.
2. Selecione um ponto de recuperação a utilizar para a ativação pós-falha:
    - **Processado mais recentemente**: Efetua a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
    - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha de todas as VMs para o ponto de recuperação consistente com a aplicação mais recente. O carimbo de data/hora é apresentado.
    - **Custom**: Selecione qualquer ponto de recuperação.

3. Em **Ativação Pós-Falha de Teste**, selecione a rede do Azure de destino para as VMs do Azure que serão ligadas após a ocorrência da ativação pós-falha. Esta deve ser a rede que criou em [Preparar recursos do Azure](#prepare-azure-resources).
4. Selecione **OK** para iniciar a ativação pós-falha. Para acompanhar o progresso, selecione a VM para ver as respetivas propriedades. Ou pode selecionar a tarefa de **ativação pós-falha de teste** na página para o seu cofre. Para tal, selecione **Monitorização e relatórios** > **Tarefas** >  **Tarefas de Site Recovery**.
5. Quando a ativação pós-falha termina, a VM do Azure de réplica é apresentada no portal do Azure. Para ver a VM, selecione **Máquinas Virtuais**. Assegure-se de que a VM tem o tamanho adequado, está ligada à rede certa e está em execução.
6. Deverá conseguir ligar-se à VM replicada no Azure agora.
7. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, selecione **Limpar ativação pós-falha de teste**, no plano de recuperação. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste.

Em alguns cenários, a ativação pós-falha requer processamento adicional. O processamento demora 8 a 10 minutos a concluir.

## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute uma ativação pós-falha real para as instâncias do EC2, para migrá-las para as VMs do Azure:

1. Em **Itens protegidos** > **Itens replicados**, selecione instâncias do AWS e seguidamente selecione **Ativação Pós-falha**.
2. Em **Ativação Pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Selecione o ponto de recuperação mais recente e inicie a recuperação pós-falha. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
1. Assegure-se de que a VM aparece em **Itens replicados**.
2. Clique com o botão direito do rato em cada VM, e selecione **Concluir a Migração**. Isso faz o seguinte:

   - Desta forma, o processo de migração é concluído, a replicação da VM do AWS é parada e a faturação do Site Recovery para a VM também é parada.
   - Este passo limpa os dados de replicação. Não elimina as VMs migradas. 

     ![Concluir a migração](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Não cancele uma ativação pós-falha que está em curso*. Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que está em curso, a mesma parará, mas a VM não será replicada outra vez.  


## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a migrar instâncias do AWS EC2 para VMs do Azure. Para saber mais sobre as VMs do Azure, avance para os tutoriais relativos às VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](../virtual-machines/windows/tutorial-manage-vm.md)
