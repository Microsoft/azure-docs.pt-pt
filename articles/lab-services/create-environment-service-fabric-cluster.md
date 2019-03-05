---
title: Criar um ambiente com um cluster do Service Fabric no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como criar um ambiente com um cluster autónomo do Service Fabric e iniciar e parar o cluster utilizando agendas.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318790"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Criar um ambiente com cluster autónomo do Service Fabric no Azure DevTest Labs
Este artigo fornece informações sobre como criar um ambiente com um cluster autónomo do Service Fabric no Azure DevTest Labs. 

## <a name="overview"></a>Descrição geral
DevTest Labs pode criar ambientes de teste autônomo, conforme definido por modelos de gestão de recursos do Azure. Estes ambientes contenham ambos os recursos de IaaS, como máquinas virtuais e recursos de PaaS, como o Service Fabric. DevTest Labs permite-lhe gerir máquinas virtuais num ambiente ao fornecer comandos para controlar as máquinas virtuais. Estes comandos dão-lhe a capacidade para iniciar ou parar uma máquina virtual com base numa agenda. Da mesma forma, os laboratórios DevTest pode também ajudar a gerir clusters do Service Fabric num ambiente. Pode iniciar ou parar um cluster do Service Fabric num ambiente manualmente ou através de uma agenda.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Clusters do Service Fabric são criados com ambientes no DevTest Labs. Cada ambiente é definido por um modelo do Azure Resource Manager num repositório Git. O [repositório de Git público](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) para os laboratórios DevTest contém o modelo do Resource Manager para criar um cluster do Service Fabric no [Cluster do ServiceFabric](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) pasta. 

1. Primeiro, crie um laboratório no Azure DevTest Labs com base nas instruções no seguinte artigo: [Criar um laboratório](devtest-lab-create-lab.md). Tenha em atenção que o **ambientes públicos** opção é **no** por predefinição. 
2. Confirme que o fornecedor de recursos de infraestrutura do serviço está registrado para a sua subscrição através dos seguintes passos:
    1. Selecione **subscrições** no menu de navegação à esquerda e selecione sua **subscrição**
    2. Na **subscrição** página, selecione **fornecedores de recursos** no **definições** secção no menu da esquerda. 
    3. Se **Microsoft.ServiecFabric** não estiver registado, selecione **registar**. 
3. Sobre o **laboratório Dev/Test** página para seu laboratório, selecione **+ adicionar** na barra de ferramentas. 
    
    ![Adicionar botão na barra de ferramentas](./media/create-environment-service-fabric-cluster/add-button.png)
3. No **Vyberte bázi** página, selecione **Cluster de laboratório do Service Fabric** na lista. 

    ![Selecione o Cluster de laboratório do Service Fabric na lista](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Sobre o **configurar as definições de** página, efetue os seguintes passos: 
    1. Especifique um **name** para o seu cluster **ambiente**. Este é o nome do grupo de recursos no Azure em que o cluster do Service Fabric vai ser criada. 
    2. Selecione o **sistema operativo (SO)** para as máquinas de virtuais de cluster. O valor predefinido é: **Windows**.
    3. Especifique um nome para o **administrador** para o cluster. 
    4. Especifique um **palavra-passe** para o administrador. 
    5. Para o **certificado**, introduza as suas informações de certificado como uma cadeia codificada em Base64. Para criar um certificado, siga os passos abaixo:
        1. Transferir o **criar ClusterCertificate.ps1** ficheiro a partir do [repositório de Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Em alternativa, clone o repositório no seu computador. 
        2. Inicie o **Azure PowerShell**. 
        3. Executar o **ps1** com o comando de ficheiros `.\Create-ClusterCertificate.ps1`. Verá um arquivo de texto aberto no bloco de notas com as informações que necessárias para preencher os campos relacionados com o certificado nesta página. . 
    6. Introduza o **palavra-passe para o certificado**.
    7. Especifique a **thumbprint** para o seu certificado.
    8. Selecione **Add** sobre o **configurar definições de** página. 

        ![Configurar definições do cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Depois do cluster for criado, verá um grupo de recursos com o nome do ambiente que indicou no passo anterior. Se expandir, verá o cluster do Service Fabric no mesmo. Se o estado do grupo de recursos está bloqueado nos **Creating**, selecione **atualizar** na barra de ferramentas. O **cluster do Service Fabric** ambiente cria um cluster de 5 nós 1-nodetype no Linux ou Windows.

    No exemplo a seguir **mysfabricclusterrg** é o nome do grupo de recursos criada especificamente para o cluster do Service Fabric. É importante observar que os ambientes de laboratório são autônomos no grupo de recursos em que são criados. Isso significa que o modelo que define o ambiente, que só pode aceder a recursos no grupo de recursos recentemente criado ou [redes virtuais configuradas para serem utilizadas pelo laboratório](devtest-lab-configure-vnet.md). Este exemplo acima cria todos os recursos necessários no mesmo grupo de recursos.

    ![Cluster criado](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Iniciar ou parar o cluster
Pode iniciar ou parar o cluster a partir de qualquer um da laboratório Dev/Test própria página ou da página de Cluster do Service Fabric fornecida pelo DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Na página laboratório Dev/Test
Pode iniciar ou parar o cluster na página do laboratório Dev/Test para o laboratório. 

1. Selecione **reticências (...)**  para cluster do Service Fabric, conforme mostrado na imagem seguinte: 

    ![Iniciar e parar de comandos para o cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Vê dois comandos no menu de contexto para **começar** e **parar** o cluster. O comando start inicia todos os nós num cluster. O comando stop interrompe todos os nós num cluster. Assim que um cluster esteja parado, o cluster do Service Fabric propriamente dito permanece no estado pronto, mas não existem nós estão disponíveis até que o comando de início é emitida novamente no cluster no laboratório.

    Existem algumas considerações a ter em conta ao utilizar um cluster do Service Fabric num ambiente de teste. Pode demorar algum tempo para o cluster do Service Fabric realimentar completamente depois de tem sido reiniciados os nós. Para manter os dados de encerramento para inicialização, os dados tem de ser guardados num disco gerido anexado à máquina virtual. Existem implicações de desempenho ao utilizar um disco gerido anexado, pelo que é recomendado para ambientes de teste apenas. Se o disco utilizado para armazenamento de dados não é de segurança, em seguida, dados são perdidos quando o comando de paragem é emitido no cluster.

### <a name="from-the-service-fabric-cluster-page"></a>Na página Cluster do Service Fabric 
Existe outra maneira para iniciar ou parar o cluster. 

1. Selecione o cluster do Service Fabric na vista de árvore na página do laboratório Dev/Test. 

    ![Selecione o cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Sobre o **Cluster do Service Fabric** página para o cluster, consulte comandos na barra de ferramentas para iniciar ou parar o cluster. 

    ![Iniciar ou parar comandos na página Cluster do Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurar a inicialização automática e o agendamento de encerramento automático
Clusters do Service Fabric também podem ser iniciados ou parados com base numa agenda. Esta experiência é semelhante à experiência para máquinas virtuais num laboratório. Para poupar dinheiro, por predefinição, cada cluster criado num laboratório automaticamente encerra ao tempo definido pelo laboratório [política de encerramento](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). É possível substituir ao especificar se o cluster deve ser encerrado ou ao especificar o tempo que o cluster é encerrado. 

![Agendas existentes para inicialização automática e o encerramento automático](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Escolher o agendamento de início automático
Para optar ativamente a agenda de arranque, siga os passos abaixo:

1. Selecione **início automático** no menu da esquerda
2. Selecione **nos** para **permitir que este cluster do service fabric agendadas para início automático**. Esta página está ativada apenas se o proprietário de laboratório permitiu que os utilizadores para iniciar automaticamente suas máquinas virtuais ou clusters do Service Fabric.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Página de estrela automática](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configurar as configurações de encerramento automático 
Para alterar as definições para encerramento, siga os passos abaixo:

1. Selecione **encerramento automático** no menu da esquerda. 
2. Nesta página, pode optar por recusar encerramento automático selecionando **Off** para **ativado**. 
3. Se tiver selecionado **nos** para **ativado**, siga estes passos:
    1. Especifique a **tempo** para encerramento.
    2. Especifique a **fuso horário** para o tempo. 
    3. Especifique se pretende que o DevTest Labs para enviar **notificações** antes de encerramento automático. 
    4. Se tiver selecionado **Sim** para a opção de notificação, especifique a **URL do Webhook** e/ou **endereço de e-mail** para enviar notificações. 
    5. Selecione **Guardar** na barra de ferramentas.

        ![Encerrar a página de automática](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para ver os nós no cluster do Service Fabric
A página de cluster do Service Fabric, viu nos passos anteriores é específica para a página de DevTest Labs. Ele não mostra-lhe os nós do cluster. Para obter mais informações sobre o cluster, siga estes passos:

1. Na **laboratório Dev/Test** página para seu laboratório, selecione a **grupo de recursos** na vista de árvore no **minhas máquinas virtuais** secção.

    ![Selecionar grupo de recursos](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Sobre o **grupo de recursos** página, verá todos os recursos no grupo de recursos numa lista. Selecione seu **cluster do Service Fabric** da lista. 

    ![Selecione o cluster na lista](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Verá o **Cluster do Service Fabric** página para o seu cluster. Esta é a página que fornece recursos de infraestrutura do serviço. Ver todas as informações sobre os clusters, como nós, os tipos de nó, etc.

    ![Home page do Cluster do Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Passos Seguintes
Veja os artigos seguintes para obter detalhes sobre os ambientes: 

- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e utilizar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
