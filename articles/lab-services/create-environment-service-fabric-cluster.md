---
title: Criar um ambiente de cluster de tecido de serviço em Azure DevTest Labs
description: Aprenda a criar um ambiente com um cluster self-contained Service Fabric, e inicie e pare o cluster usando horários.
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
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170344"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Criar um ambiente com cluster self-contained Service Fabric em Azure DevTest Labs
Este artigo fornece informações sobre como criar um ambiente com um cluster self-contained Service Fabric em Azure DevTest Labs. 

## <a name="overview"></a>Descrição geral
A DevTest Labs pode criar ambientes de teste independentes, tal como definidos pelos modelos de Gestão de Recursos Azure. Estes ambientes contêm tanto recursos IaaS, como máquinas virtuais, e recursos PaaS, como o Service Fabric. A DevTest Labs permite-lhe gerir máquinas virtuais num ambiente, fornecendo comandos para controlar as máquinas virtuais. Estes comandos dão-lhe a capacidade de iniciar ou parar uma máquina virtual num horário. Da mesma forma, a DevTest Labs também pode ajudá-lo a gerir os clusters de Tecido de Serviço num ambiente. Pode iniciar ou parar um cluster de Tecido de Serviço num ambiente manual ou através de um horário.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Os clusters de tecido de serviço são criados usando ambientes em Laboratórios DevTest. Cada ambiente é definido por um modelo de Gestor de Recursos Azure num repositório Git. O [repositório git público](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) para DevTest Labs contém o modelo de Gestor de Recursos para criar um cluster de tecido de serviço na pasta [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Primeiro, crie um laboratório em Azure DevTest Labs utilizando instruções no seguinte artigo: [Criar um laboratório](devtest-lab-create-lab.md). Note que a opção de **ambientes públicos** está **on** por padrão. 
2. Confirme que o fornecedor de Tecido de Serviço está registado para a sua subscrição seguindo estas etapas:
    1. Selecione **Subscrições** no menu de navegação à esquerda e selecione a sua **Subscrição**
    2. Na página **de Subscrição,** selecione **fornecedores de Recursos** na secção **Definições** no menu esquerdo. 
    3. Se o **Microsoft.ServiecFabric** não estiver registado, selecione **Register**. 
3. Na página **DevTest Lab** para o seu laboratório, selecione **+ Adicione** na barra de ferramentas. 
    
    ![Adicione o botão na barra de ferramentas](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na página base Escolha uma página **base,** selecione **Service Fabric Lab Cluster** na lista. 

    ![Selecione Cluster de Laboratório de Tecido de Serviço na lista](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na página de definições de **Configuração,** faça os seguintes passos: 
    1. Especifique um **nome** para o seu **ambiente**de cluster . Este é o nome do grupo de recursos em Azure no qual o cluster Service Fabric vai ser criado. 
    2. Selecione o **sistema operativo (OS)** para as máquinas virtuais do cluster. O valor predefinido é: **Windows**.
    3. Especifique um nome para o **administrador** do cluster. 
    4. Especifique uma **palavra-passe** para o administrador. 
    5. Para o **certificado,** introduza as informações do certificado como uma cadeia codificada Base64. Para criar um certificado, faça os seguintes passos:
        1. Descarregue o ficheiro **Create-ClusterCertificate.ps1** do [repositório Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Em alternativa, clone o repositório na sua máquina. 
        2. Lançamento **PowerShell**. 
        3. Executar o ficheiro **ps1** usando o comando `.\Create-ClusterCertificate.ps1`. Você vê um ficheiro de texto aberto no bloco de notas com as informações que precisa para preencher os campos relacionados com certificados nesta página. . 
    6. Introduza a **palavra-passe para o certificado**.
    7. Especifique a **impressão digital** para o seu certificado.
    8. Selecione **Adicionar** na página **Configurar Definições.** 

        ![Configurar as definições do cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Após a criação do cluster, você vê um grupo de recursos com o nome do ambiente que forneceu no passo anterior. Quando se expande, vê-se o cluster de Tecido de Serviço nele. Se o estado do grupo de recursos estiver preso na **Criação,** selecione **Refresh** na barra de ferramentas. O ambiente de **cluster service Fabric** cria um cluster de 5 nós de 1 nó no Linux ou Windows.

    No exemplo seguinte, **mysfabricclusterrg** é o nome do grupo de recursos que é criado especificamente para o cluster De Tecido de Serviço. É importante notar que os ambientes de laboratório são autossuficientes dentro do grupo de recursos em que são criados. Significa que o modelo que define o ambiente, que só pode aceder a recursos dentro do grupo de recursos recém-criado ou [redes virtuais configuradas para serem usadas pelo laboratório.](devtest-lab-configure-vnet.md) Esta amostra acima cria todos os recursos necessários no mesmo grupo de recursos.

    ![Cluster criado](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Iniciar ou parar o cluster
Pode iniciar ou parar o cluster da página de DevTest Lab em si ou da página de Cluster de Tecidos de Serviço fornecida pela DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>A partir da página de DevTest Lab
Pode começar ou parar o cluster na página do Laboratório DevTest para o seu laboratório. 

1. Selecione **três pontos (...)** para o cluster De Tecido de Serviço, como mostra a seguinte imagem: 

    ![Iniciar e parar comandos para o cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Você vê dois comandos no menu de contexto para **iniciar** e **parar** o cluster. O comando inicial começa todos os nós num aglomerado. O comando stop para todos os nós num aglomerado. Uma vez que um cluster é parado, o próprio cluster de Tecido de Serviço permanece em estado de pronto, mas não há nós disponíveis até que o comando de partida seja reemitido no cluster no laboratório.

    Existem algumas considerações a notar ao utilizar um cluster de tecido de serviço em ambiente de teste. Pode levar algum tempo para o cluster de tecido de serviço se hidratar completamente depois de os nós terem sido reiniciados. Para reter dados do encerramento ao arranque, os dados devem ser guardados num disco gerido ligado à máquina virtual. Existem implicações de desempenho ao utilizar um disco gerido anexado, pelo que é recomendado apenas para ambientes de teste. Se o disco utilizado para armazenamento de dados não for apoiado, então os dados perdem-se quando o comando de paragem é emitido no cluster.

### <a name="from-the-service-fabric-cluster-page"></a>A partir da página cluster de tecido de serviço 
Há outra maneira de começar ou parar o aglomerado. 

1. Selecione o seu cluster de tecido de serviço na vista da árvore na página DevTest Lab. 

    ![Selecione o seu cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na página cluster de tecido de **serviço** para o cluster, você vê comandos na barra de ferramentas para iniciar ou parar o cluster. 

    ![Iniciar ou parar comandos na página cluster de tecido de serviço](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configure o horário de arranque automático e o horário de paragem automática
Os clusters de tecido de serviço também podem ser iniciados ou parados num horário. Esta experiência é semelhante à experiência de máquinas virtuais num laboratório. Para poupar dinheiro, por defeito, todos os clusters criados num laboratório desligam-se automaticamente no momento definido pela política de [encerramento](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)do laboratório. Pode anular especificando se o cluster deve ser desligado ou especificando a hora em que o cluster é desligado. 

![Horários existentes para arranque automático e paragem automática](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Opte pelo horário de início do automóvel
Para optar pelo horário de arranque, faça os seguintes passos:

1. Selecione **Arranque Automático** no menu esquerdo
2. Selecione **para** permitir que este cluster de tecido de **serviço esteja programado para o arranque automático**. Esta página só está ativada se o proprietário do laboratório tiver permitido que os utilizadores iniciassem automaticamente as suas máquinas virtuais ou clusters de Tecido de Serviço.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Página de estrela auto](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configure as definições de paragem automática 
Para alterar as definições para o encerramento, faça os seguintes passos:

1. Selecione **a paragem automática** no menu esquerdo. 
2. Nesta página, pode optar por não desligar automaticamente **selecionando** para **Ativado**. 
3. Se tiver selecionado **on** for **Enabled,** siga estes passos:
    1. Especifique a **hora** do encerramento.
    2. Especifique o **fuso horário** para a hora. 
    3. Especifique se quer que a DevTest Labs envie **notificações** antes do encerramento automático. 
    4. Se selecionou **Sim** para a opção de notificação, especifique o URL do **Webhook** e/ou endereço de **e-mail** para enviar notificações. 
    5. Selecione **Guardar** na barra de ferramentas.

        ![Página de encerramento automático](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para ver nós no cluster De Tecido de Serviço
A página de cluster de tecido de serviço que viu nos passos anteriores é específica da página DevTest Labs. Não mostra os nós no aglomerado. Para ver mais informações sobre o cluster, siga estes passos:

1. Na página **do DevTest Lab** para o seu laboratório, selecione o **grupo de recursos** na vista da árvore na secção **Minhas máquinas virtuais.**

    ![Selecionar grupo de recursos](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na página do **Grupo de Recursos,** você vê todos os recursos do grupo de recursos numa lista. Selecione o seu **cluster de Tecido de Serviço** da lista. 

    ![Selecione o seu cluster na lista](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Você vê a página **de Cluster de Tecido de Serviço** para o seu cluster. Esta é a página que o Tecido de Serviço fornece. Você vê toda a informação sobre os clusters, tais como nós, tipos de nós, etc.

    ![Página inicial do Cluster de Tecido de Serviço](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para obter detalhes sobre ambientes: 

- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos em Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
