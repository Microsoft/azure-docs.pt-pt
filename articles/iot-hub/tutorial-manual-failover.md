---
title: Ativação pós-falha manual de um hub IoT do Azure | Microsoft Docs
description: Aprenda a executar uma falha manual do seu hub IoT para uma região diferente e confirme que está funcionando, e depois devolva-o à região original e verifique novamente.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 86b39beb2958194f7c86409c5d78992616234b05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769914"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Tutorial: Execute a falha manual para um hub IoT

A ativação pós-falha manual é uma funcionalidade do serviço Hub IoT que permite aos clientes executarem operações de [ativação pós-falha](https://en.wikipedia.org/wiki/Failover) no hub, de uma região primária para a região geograficamente emparelhada e correspondente no Azure. A ativação pós-falha manual pode ser feita em caso de um desastre regional ou de uma falha expandida do serviço. Também pode executar uma ativação pós-falha planeada para testar as capacidades de recuperação após desastre, embora seja recomendável a utilização de um hub IoT de teste em vez de um que esteja em execução na produção. A funcionalidade de ativação pós-falha manual é oferecida aos clientes sem custos adicionais.

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
> * Com o portal do Azure, vai criar um hub IoT. 
> * Executar uma ativação pós-falha. 
> * Ver o hub em execução na localização secundária.
> * Executar uma reativação pós-falha para devolver as operações do hub IoT à localização primária. 
> * Confirmar que o hub está a ser executado corretamente na localização correta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste tutorial utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

1. Inicie sessão no [portal Azure.](https://portal.azure.com) 

2. Clique em **+ Criar um recurso** e selecione **Internet das Coisas** e, em seguida, **Hub IoT**.

   ![Captura de ecrã que mostra a criação de um hub IoT](./media/tutorial-manual-failover/create-hub-01.png)

3. Selecione o separador **Basics.** Preencha os seguintes campos.

    **Subscrição**: selecione a subscrição do Azure que pretende utilizar.

    **Grupo de recursos**: clique em **Criar novo** e especifique **ManlFailRG** no nome do grupo de recursos.

    **Região**: selecione uma região próxima de si. Este tutorial utiliza `West US 2`. Uma ativação pós-falha só pode ser executada entre regiões do Azure geograficamente emparelhadas. A região geo-emparelhada com o West US 2 é WestCentralUS.
    
   **Nome do Hub IoT**: especifique um nome para o seu hub IoT. O nome do hub tem de ser globalmente exclusivo. 

   ![Captura de ecrã que mostra o painel Básico para criar um hub IoT](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Clique em **Rever + criar**. (Utiliza as predefinições de tamanho e dimensionamento.) 

4. Reveja as informações e clique em **Criar** para criar o hub IoT. 

   ![Captura de ecrã que mostra o passo final para criar um hub IoT](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Executar uma ativação pós-falha manual

Tenha em atenção que existe um limite de duas ativações pós-falha e duas reativações pós-falha por dia para um hub IoT.

1. Clique em **Grupos de recursos** e selecione o grupo de recursos **ManlFailRG**. Clique no seu hub na lista de recursos. 

1. Em **Definições** no painel Do Hub IoT, clique em **Failover**.

   ![Captura de ecrã que mostra o painel de propriedades do Hub IoT](./media/tutorial-manual-failover/trigger-failover-01.png)

1. No painel de falhas manual, vê a **localização da corrente** e a localização **Failover**. A localização atual indica sempre a localização em que o hub está atualmente ativo. A localização failover é a [região padrão azure geo-emparelhado](../best-practices-availability-paired-regions.md) que é emparelhado com a localização atual. Não é possível alterar os valores da localização. Para este tutorial, a `West US 2` localização atual é `West Central US`e a localização de failover é .

   ![Captura de ecrã que mostra o painel Ativação Pós-falha Manual](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Na parte superior do painel de falha manual, clique em **Iniciar a falha**. 

1. No painel de confirmação, preencha o nome do seu hub IoT para confirmar que é o que pretende falhar. Em seguida, para iniciar a falha, clique em **Failover**.

   O período de tempo que a ativação pós-falha manual demora é proporcional ao número de dispositivos que estão registados n o seu hub. Por exemplo, se tiver 100 000 dispositivos, pode demorar 15 minutos, mas se tiver cinco milhões de dispositivos, pode demorar uma hora ou mais.

   ![Captura de ecrã que mostra o painel Ativação Pós-falha Manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Enquanto o processo de failover manual está em andamento, um banner parece dizer-lhe que uma falha manual está em andamento. 

   ![Captura de ecrã que mostra a Ativação Pós-falha Manual em curso](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Se fechar o painel do Hub IoT e o abrir novamente clicando no painel do Grupo de Recursos, vê um banner que lhe diz que o centro está no meio de uma falha manual. 

   ![Screenshot mostrando IoT Hub falha em progresso](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Depois de terminado, as regiões atuais e failover na página Failover Manual são invertidas e o centro está novamente ativo. Neste exemplo, a localização `WestCentralUS` atual é agora e `West US 2`a localização de failover é agora . 

   ![Captura de ecrã que mostra que a ativação pós-falha está concluída](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   A página geral também mostra um banner indicando que a falha `West Central US`completa e o Hub IoT estão a correr dentro .

   ![Screenshot mostrando failover está completo na página de visão geral](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Executar uma reativação pós-falha 

Após ter executado uma ativação pós-falha manual, pode voltar a mudar as operações do hub para a região primária original – chama-se a isto uma reativação pós-falha. Se acabou de executar uma ativação pós-falha, tem de esperar aproximadamente uma hora antes de poder pedir uma reativação pós-falha. Se tentar efetuar a reativação pós-falha num período de tempo mais curto, é apresentada uma mensagem de erro.

Uma reativação pós-falha é executada tal como uma ativação pós-falha manual. São estes os passos: 

1. Para executar uma reativação pós-falha, volte ao painel Iot Hub do seu hub Iot.

2. Em **Definições** no painel Do Hub IoT, clique em **Failover**. 

3. Na parte superior do painel de falha manual, clique em **Iniciar a falha**. 

4. No painel de confirmação, preencha o nome do seu hub IoT para confirmar que é o que pretende falhar. Para iniciar a reativação pós-falha, clique em OK. 

   ![Captura de ecrã do pedido de reativação pós-falha manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   As faixas são apresentadas, conforme explicado na secção executar uma ativação pós-falha. Após a falha de reprovação estar completa, mostra novamente `West US 2` como a localização atual e `West Central US` como a localização de failover, como definido originalmente.

## <a name="clean-up-resources"></a>Limpar recursos 

Para remover os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT e o grupo de recursos. 

1. Clique em **Grupos de Recursos**. 

2. Localize e selecione o grupo de recursos **ManlFailRG**. Clique nela para abri-la. 

3. Clique em **Eliminar grupo de recursos**. Quando lhe for pedido, introduza o nome do grupo de recursos e clique em **Eliminar** para confirmar. 

## <a name="next-steps"></a>Passos seguintes

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
