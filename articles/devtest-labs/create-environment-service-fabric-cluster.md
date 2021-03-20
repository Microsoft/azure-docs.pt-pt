---
title: Criar um ambiente de cluster de tecido de serviço em Azure DevTest Labs
description: Aprenda a criar um ambiente com um cluster de tecido de serviço independente e comece e pare o cluster usando horários.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 530cf3b20820e34913612419d0ffa731a70f6a58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85484014"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Criar um ambiente com cluster de tecido de serviço independente em Azure DevTest Labs
Este artigo fornece informações sobre como criar um ambiente com um cluster de tecido de serviço independente em Azure DevTest Labs. 

## <a name="overview"></a>Descrição Geral
A DevTest Labs pode criar ambientes de teste independentes, conforme definidos pelos modelos de Gestão de Recursos Azure. Estes ambientes contêm recursos iaaS, como máquinas virtuais, e recursos PaaS, como o Service Fabric. A DevTest Labs permite-lhe gerir máquinas virtuais num ambiente, fornecendo comandos para controlar as máquinas virtuais. Estes comandos dão-lhe a capacidade de iniciar ou parar uma máquina virtual num horário. Da mesma forma, a DevTest Labs também pode ajudá-lo a gerir clusters de Tecidos de Serviço num ambiente. Pode iniciar ou parar um cluster de Tecido de Serviço num ambiente manualmente ou através de um horário.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Os clusters de tecido de serviço são criados usando ambientes em DevTest Labs. Cada ambiente é definido por um modelo de Gestor de Recursos Azure num repositório de Git. O [repositório público de Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) para DevTest Labs contém o modelo de Gestor de Recursos para criar um cluster de tecido de serviço na pasta [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Em primeiro lugar, crie um laboratório em Azure DevTest Labs usando instruções no seguinte artigo: [Criar um laboratório](devtest-lab-create-lab.md). Note que a opção **ambientes públicos** é **On** por padrão. 
2. Confirme que o fornecedor de Tecidos de Serviço está registado para a sua subscrição seguindo estes passos:
    1. Selecione **Subscrições** no menu de navegação à esquerda e selecione a sua **Subscrição**
    2. Na página **De Subscrição,** selecione **fornecedores de recursos** na secção **Definições** no menu esquerdo. 
    3. Se **o Microsoft.ServiecFabric** não estiver registado, selecione **Register**. 
3. Na página **DevTest Lab** para o seu laboratório, selecione **+ Adicione** na barra de ferramentas. 
    
    ![Adicione botão na barra de ferramentas](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na página base Escolha uma página **base,** selecione **o Cluster de Laboratório de Tecido de Serviço** na lista. 

    ![Selecione Cluster de Laboratório de Tecido de Serviço na lista](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na página **configurar definições,** faça os seguintes passos: 
    1. Especifique um **nome** para o seu **ambiente** de cluster . Este é o nome do grupo de recursos em Azure no qual o cluster de Tecido de Serviço vai ser criado. 
    2. Selecione o **sistema operativo (OS)** para as máquinas virtuais do cluster. O valor predefinido é: **Windows**.
    3. Especifique um nome para o **administrador** do cluster. 
    4. Especifique uma **palavra-passe** para o administrador. 
    5. Para o **certificado,** insira as informações do certificado como uma cadeia codificada Base64. Para criar um certificado, faça os seguintes passos:
        1. Descarregue o ficheiro **Create-ClusterCertificate.ps1** do [repositório git.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) Em alternativa, clone o repositório na sua máquina. 
        2. **Launch PowerShell**. 
        3. Executar o ficheiro **ps1** usando o comando `.\Create-ClusterCertificate.ps1` . Vê um ficheiro de texto aberto no bloco de notas com as informações necessárias para preencher os campos relacionados com o certificado nesta página. . 
    6. Introduza a **senha do certificado.**
    7. Especifique a **impressão digital** do seu certificado.
    8. **Selecione Adicionar** na página **Configurações configurações de configuração.** 

        ![Configurar configurações de cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Após a criação do cluster, vê-se um grupo de recursos com o nome do ambiente que forneceu no passo anterior. Quando se expande, vê-se o cluster de Tecidos de Serviço nele. Se o estado do grupo de recursos estiver preso na **Criação,** selecione **Refresh** na barra de ferramentas. O ambiente **de cluster de tecido** de serviço cria um cluster de 5 nós de nó 1-node em Linux ou Windows.

    No exemplo seguinte, **mysfabricclusterrg** é o nome do grupo de recursos que é criado especificamente para o cluster de Tecidos de Serviço. É importante notar que os ambientes de laboratório são autossuficientes dentro do grupo de recursos em que são criados. Significa que o modelo que define o ambiente, que só pode aceder a recursos dentro do grupo de recursos recém-criado ou [redes virtuais configuradas para serem usadas pelo laboratório.](devtest-lab-configure-vnet.md) Esta amostra acima cria todos os recursos necessários no mesmo grupo de recursos.

    ![Cluster criado](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Iniciar ou parar o cluster
Pode iniciar ou parar o cluster a partir da própria página do Laboratório DevTest ou da página de Cluster de Tecido de Serviço fornecida pela DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Da página do Laboratório DevTest
Pode iniciar ou parar o cluster na página do Laboratório DevTest para o seu laboratório. 

1. Selecione **três pontos (...)** para o cluster de Tecido de Serviço, como mostra a seguinte imagem: 

    ![Iniciar e parar comandos para o cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Você vê dois comandos no menu de contexto para **iniciar** e **parar** o cluster. O comando de partida começa todos os nós num aglomerado. O comando stop para todos os nós num aglomerado. Uma vez que um cluster é parado, o cluster de tecido de serviço em si permanece em um estado pronto, mas nenhum nós está disponível até que o comando de início seja reemitido no cluster no laboratório.

    Há algumas considerações a ter em conta ao utilizar um cluster de Tecido de Serviço num ambiente de teste. Pode levar algum tempo para que o cluster de tecido de serviço se reidrate completamente depois de os nós terem sido reiniciados. Para reter dados do encerramento para o arranque, os dados devem ser guardados num disco gerido ligado à máquina virtual. Existem implicações de desempenho quando se utiliza um disco gerido anexado, pelo que é recomendado apenas para ambientes de teste. Se o disco utilizado para o armazenamento de dados não for apoiado, perder-se-ão dados quando o comando stop é emitido no cluster.

### <a name="from-the-service-fabric-cluster-page"></a>A partir da página de Cluster de Tecido de Serviço 
Há outra maneira de começar ou parar o aglomerado. 

1. Selecione o seu cluster de tecido de serviço na vista da árvore na página DevTest Lab. 

    ![Selecione o seu cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na página **'Cluster' de Tecido de Serviço** para o cluster, vê comandos na barra de ferramentas para iniciar ou parar o cluster. 

    ![Iniciar ou parar comandos na página de Cluster de Tecido de Serviço](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurar o programa de arranque automático e de paragem automática
Os clusters de tecido de serviço também podem ser iniciados ou parados em um horário. Esta experiência é semelhante à experiência para máquinas virtuais em laboratório. Para poupar dinheiro, por padrão, todos os clusters criados num laboratório desligam-se automaticamente no momento definido pela [política de encerramento](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)do laboratório . Pode sobrepor-se especificando se o cluster deve ser desligado ou especificando a hora de encerramento do cluster. 

![Horários existentes para arranque automático e paragem automática](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Opte pelo horário de arranque automático
Para optar pelo calendário de arranque, faça os seguintes passos:

1. Selecione **Arranque automático** no menu esquerdo
2. Selecione **On** for **Deixe este conjunto de tecido de serviço ser programado para o arranque automático**. Esta página só é ativada se o proprietário do laboratório tiver permitido aos utilizadores iniciar automaticamente as suas máquinas virtuais ou aglomerados de Tecidos de Serviço.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Página de estrela de carro](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configurar definições de paragem automática 
Para alterar as definições para encerramento, faça os seguintes passos:

1. Selecione **desligar automaticamente** no menu esquerdo. 
2. Nesta página, pode optar por não desligar automaticamente selecionando **Off** for **Enabled**. 
3. Se tiver selecionado **On** for **Enabled,** siga estes passos:
    1. Especifique a **hora** do encerramento.
    2. Especifique o **intervalo de tempo** para a hora. 
    3. Especifique se deseja que a DevTest Labs envie **notificações** antes do encerramento automático. 
    4. Se selecionou **Sim** para a opção de notificação, especifique o **URL webhook** e/ou **endereço de e-mail** para enviar notificações. 
    5. Selecione **Guardar** na barra de ferramentas.

        ![Página de desligação automática](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para ver os nódinhos no cluster de Tecido de Serviço
A página de cluster de Tecido de Serviço que viu nos passos anteriores é específica para a página DevTest Labs. Não mostra os nós no aglomerado. Para ver mais informações sobre o cluster, siga estes passos:

1. Na página **do Laboratório DevTest** para o seu laboratório, selecione o **grupo de recursos** na vista da árvore na secção My virtual **Machines.**

    ![Selecionar o grupo de recursos](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na página **do Grupo de Recursos,** vê todos os recursos do grupo de recursos numa lista. Selecione o seu **cluster de Tecido** de Serviço na lista. 

    ![Selecione o seu cluster na lista](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Você vê a página de **Cluster de Tecido de Serviço** para o seu cluster. Esta é a página que o Tecido de Serviço fornece. Você vê toda a informação sobre os clusters tais como nós, tipos de nós, etc.

    ![Página inicial do Cluster de Tecido de Serviço](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para mais detalhes sobre ambientes: 

- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos em Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
