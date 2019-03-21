---
title: Ativação pós-falha manual de um hub IoT do Azure | Microsoft Docs
description: Mostrar como executar uma ativação pós-falha manual de um hub IoT do Azure
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: ff8ef576a0dae0f03cc0c80f83ce0c24cf3945a2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093740"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Tutorial: Efetuar ativação pós-falha manual para um hub IoT (pré-visualização pública)

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

    **Região**: selecione uma região perto de si e que faça parte da pré-visualização. Este tutorial utiliza `westus2`. Uma ativação pós-falha só pode ser executada entre regiões do Azure geograficamente emparelhadas. A região geograficamente emparelhada com westus2 é WestCentralUS.
    
   > [!NOTE]
   > Ativação pós-falha manual está atualmente em pré-visualização pública e está *não* disponível nas seguintes regiões do Azure: E.U.A. leste, E.U.A. oeste, Europa do Norte, Europa Ocidental, sul do Brasil e dos E.U.A. centro-Sul.

   **Nome do Hub IoT**: especifique um nome para o seu hub IoT. O nome do hub tem de ser globalmente exclusivo. 

   ![Captura de ecrã que mostra o painel Básico para criar um hub IoT](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Clique em **Rever + criar**. (Utiliza as predefinições de tamanho e dimensionamento.) 

4. Reveja as informações e clique em **Criar** para criar o hub IoT. 

   ![Captura de ecrã que mostra o passo final para criar um hub IoT](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Executar uma ativação pós-falha manual

Tenha em atenção que existe um limite de duas ativações pós-falha e duas reativações pós-falha por dia para um hub IoT.

1. Clique em **Grupos de recursos** e selecione o grupo de recursos **ManlFailRG**. Clique no seu hub na lista de recursos. 

2. Em **Resiliência** no painel Hub IoT, clique em **Ativação pós-falha manual (pré-visualização)**. Tenha em atenção que, se o seu hub não estiver configurado numa região válida, a opção de ativação pós-falha manual vai estar desativada.

   ![Captura de ecrã que mostra o painel de propriedades do Hub IoT](./media/tutorial-manual-failover/trigger-failover-01.png)

3. No painel Ativação pós-falha manual, irá ver a **Localização Primária do Hub IoT** e a **Localização Secundária do Hub IoT**. A localização primária é inicialmente definida como a localização que especificou ao criar o hub IoT e indica sempre a localização na qual o hub está atualmente ativo. A localização secundária é a [região geograficamente emparelhada do Azure](../best-practices-availability-paired-regions.md) padrão, que está emparelhada à localização primária. Não é possível alterar os valores da localização. Neste tutorial, a localização primária é `westus2` e a localização secundária é `WestCentralUS`.

   ![Captura de ecrã que mostra o painel Ativação Pós-falha Manual](./media/tutorial-manual-failover/trigger-failover-02.png)

3. Na parte superior do painel Ativação pós-falha manual, clique em **Iniciar ativação pós-falha**. Verá o painel **Confirmar ativação pós-falha manual**. Preencha o nome do seu hub IoT para confirmar que é nele que pretende executar a ativação pós-falha. Em seguida, para iniciar a ativação pós-falha, clique em **OK**.

   O período de tempo que a ativação pós-falha manual demora é proporcional ao número de dispositivos que estão registados n o seu hub. Por exemplo, se tiver 100 000 dispositivos, pode demorar 15 minutos, mas se tiver cinco milhões de dispositivos, pode demorar uma hora ou mais.

4. No painel **Confirmar ativação pós-falha manual**, preencha o nome do seu hub IoT para confirmar que é nele que pretende executar a ativação pós-falha. Em seguida, para iniciar a ativação pós-falha, clique em OK. 

   ![Captura de ecrã que mostra o painel Ativação Pós-falha Manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Enquanto o processo de ativação pós-falha manual estiver em execução, é apresentada uma faixa no painel Ativação Pós-falha Manual informando-o de que está em curso uma ativação pós-falha manual. 

   ![Captura de ecrã que mostra a Ativação Pós-falha Manual em curso](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Se fechar o painel Hub IoT e o abrir novamente ao clicar no mesmo no painel Grupo de Recursos, verá uma faixa informando-o de que o hub não está ativo. 

   ![Captura de ecrã que mostra o Hub IoT inativo](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Depois de terminar, as regiões primária e secundária da página Ativação Pós-falha Manual são invertidas e o hub fica novamente ativo. Neste exemplo, a localização primária é agora `WestCentralUS` e a localização secundária é agora `westus2`. 

   ![Captura de ecrã que mostra que a ativação pós-falha está concluída](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Executar uma reativação pós-falha 

Após ter executado uma ativação pós-falha manual, pode voltar a mudar as operações do hub para a região primária original – chama-se a isto uma reativação pós-falha. Se acabou de executar uma ativação pós-falha, tem de esperar aproximadamente uma hora antes de poder pedir uma reativação pós-falha. Se tentar efetuar a reativação pós-falha num período de tempo mais curto, é apresentada uma mensagem de erro.

Uma reativação pós-falha é executada tal como uma ativação pós-falha manual. São estes os passos: 

1. Para executar uma reativação pós-falha, volte ao painel Iot Hub do seu hub Iot.

2. Em **Resiliência** no painel Hub IoT, clique em **Ativação pós-falha manual (pré-visualização)**. 

3. Na parte superior do painel Ativação pós-falha manual, clique em **Iniciar ativação pós-falha**. Verá o painel **Confirmar ativação pós-falha manual**. 

4. No painel **Confirmar ativação pós-falha manual**, preencha o nome do seu hub IoT para confirmar que é nele que pretende executar a reativação pós-falha. Para iniciar a reativação pós-falha, clique em OK. 

   ![Captura de ecrã do pedido de reativação pós-falha manual](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   Faixas são apresentadas como explicado na executar uma seção de ativação pós-falha. Após a conclusão da reativação pós-falha, mostra novamente `westus2` como a localização primária e `WestCentralUS` como a localização secundária, conforme definido originalmente.

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
