---
title: Ativação pós-falha manual de um hub IoT do Azure | Microsoft Docs
description: Mostrar como executar uma ativação pós-falha manual de um hub IoT do Azure
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 308e452f33ded9be3b88ff370ed34326de54895c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876931"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Tutorial: Executar failover manual para um hub IoT

A ativação pós-falha manual é uma funcionalidade do serviço Hub IoT que permite aos clientes executarem operações de [ativação pós-falha](https://en.wikipedia.org/wiki/Failover) no hub, de uma região primária para a região geograficamente emparelhada e correspondente no Azure. A ativação pós-falha manual pode ser feita em caso de um desastre regional ou de uma falha expandida do serviço. Também pode executar uma ativação pós-falha planeada para testar as capacidades de recuperação após desastre, embora seja recomendável a utilização de um hub IoT de teste em vez de um que esteja em execução na produção. A funcionalidade de ativação pós-falha manual é oferecida aos clientes sem custos adicionais.

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
> * Com o portal do Azure, vai criar um hub IoT. 
> * Executar uma ativação pós-falha. 
> * Ver o hub em execução na localização secundária.
> * Executar uma reativação pós-falha para devolver as operações do hub IoT à localização primária. 
> * Confirmar que o hub está a ser executado corretamente na localização correta.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Clique em **+ Criar um recurso** e selecione **Internet das Coisas** e, em seguida, **Hub IoT**.

   ![Captura de ecrã que mostra a criação de um hub IoT](./media/tutorial-manual-failover/create-hub-01.png)

3. Selecione o separador **Básico**. Preencha os campos seguintes.

    **Subscrição**: selecione a subscrição do Azure que pretende utilizar.

    **Grupo de recursos**: clique em **Criar novo** e especifique **ManlFailRG** no nome do grupo de recursos.

    **Região**: selecione uma região perto de você. Este tutorial utiliza `West US 2`. Uma ativação pós-falha só pode ser executada entre regiões do Azure geograficamente emparelhadas. A região geograficamente emparelhada com oeste dos EUA 2 é WestCentralUS.
    
   **Nome do Hub IoT**: especifique um nome para o seu hub IoT. O nome do hub tem de ser globalmente exclusivo. 

   ![Captura de ecrã que mostra o painel Básico para criar um hub IoT](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Clique em **Rever + criar**. (Utiliza as predefinições de tamanho e dimensionamento.) 

4. Reveja as informações e clique em **Criar** para criar o hub IoT. 

   ![Captura de ecrã que mostra o passo final para criar um hub IoT](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Executar uma ativação pós-falha manual

Tenha em atenção que existe um limite de duas ativações pós-falha e duas reativações pós-falha por dia para um hub IoT.

1. Clique em **Grupos de recursos** e selecione o grupo de recursos **ManlFailRG**. Clique no seu hub na lista de recursos. 

1. Em **configurações** no painel Hub IOT, clique em **failover**.

   ![Captura de ecrã que mostra o painel de propriedades do Hub IoT](./media/tutorial-manual-failover/trigger-failover-01.png)

1. No painel failover manual, você vê o **local atual** e o **local de failover**. O local atual sempre indica o local no qual o Hub está ativo no momento. O local de failover é a [região emparelhada geograficamente padrão do Azure](../best-practices-availability-paired-regions.md) que é emparelhada com o local atual. Não é possível alterar os valores da localização. Para este tutorial, o local atual é `West US 2` e o local de failover `West Central US`é.

   ![Captura de ecrã que mostra o painel Ativação Pós-falha Manual](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Na parte superior do painel failover manual, clique em **Iniciar failover**. 

1. No painel de confirmação, preencha o nome do Hub IoT para confirmar que é aquele que você deseja fazer failover. Em seguida, para iniciar o failover, clique em **failover**.

   O período de tempo que a ativação pós-falha manual demora é proporcional ao número de dispositivos que estão registados n o seu hub. Por exemplo, se tiver 100 000 dispositivos, pode demorar 15 minutos, mas se tiver cinco milhões de dispositivos, pode demorar uma hora ou mais.

   ![Captura de ecrã que mostra o painel Ativação Pós-falha Manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Enquanto o processo de failover manual está em execução, uma faixa é exibida para informar que um failover manual está em andamento. 

   ![Captura de ecrã que mostra a Ativação Pós-falha Manual em curso](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Se você fechar o painel do Hub IoT e abri-lo novamente clicando nele no painel grupo de recursos, você verá uma faixa que informa que o Hub está no meio de um failover manual. 

   ![Captura de tela mostrando o failover do Hub IoT em andamento](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Após a conclusão, as regiões atual e de failover na página failover manual são invertidas e o Hub está ativo novamente. Neste exemplo, o local atual agora `WestCentralUS` é e o local do failover agora `West US 2`é. 

   ![Captura de ecrã que mostra que a ativação pós-falha está concluída](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   A página Visão geral também mostra uma faixa indicando que o failover foi concluído e que o Hub IoT `West Central US`está sendo executado no.

   ![Captura de tela mostrando o failover concluído na página Visão geral](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Executar uma reativação pós-falha 

Após ter executado uma ativação pós-falha manual, pode voltar a mudar as operações do hub para a região primária original – chama-se a isto uma reativação pós-falha. Se acabou de executar uma ativação pós-falha, tem de esperar aproximadamente uma hora antes de poder pedir uma reativação pós-falha. Se tentar efetuar a reativação pós-falha num período de tempo mais curto, é apresentada uma mensagem de erro.

Uma reativação pós-falha é executada tal como uma ativação pós-falha manual. São estes os passos: 

1. Para executar uma reativação pós-falha, volte ao painel Iot Hub do seu hub Iot.

2. Em **configurações** no painel Hub IOT, clique em **failover**. 

3. Na parte superior do painel failover manual, clique em **Iniciar failover**. 

4. No painel de confirmação, preencha o nome do Hub IoT para confirmar que é aquele que você deseja fazer failback. Para iniciar a reativação pós-falha, clique em OK. 

   ![Captura de ecrã do pedido de reativação pós-falha manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   As faixas são exibidas conforme explicado na seção executar um failover. Depois que o failback for concluído, ele será `West US 2` exibido novamente como o local `West Central US` atual e como o local de failover, como definido originalmente.

## <a name="clean-up-resources"></a>Limpar recursos 

Para remover os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT e o grupo de recursos. 

1. Clique em **Grupos de Recursos**. 

2. Localize e selecione o grupo de recursos **ManlFailRG**. Clique nela para abri-la. 

3. Clique em **Eliminar grupo de recursos**. Quando lhe for pedido, introduza o nome do grupo de recursos e clique em **Eliminar** para confirmar. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a configurar e executar uma ativação pós-falha manual e como pedir uma reativação pós-falha, executando as seguintes tarefas:

> [!div class="checklist"]
> * Com o portal do Azure, vai criar um hub IoT. 
> * Executar uma ativação pós-falha. 
> * Ver o hub em execução na localização secundária.
> * Executar uma reativação pós-falha para devolver as operações do hub IoT à localização primária. 
> * Confirmar que o hub está a ser executado corretamente na localização correta.

Avance para o tutorial seguinte para aprender a gerir o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Gerir o estado de um dispositivo IoT](tutorial-device-twins.md)
