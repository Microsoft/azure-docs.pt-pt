---
title: Criar implementações automáticas a partir do portal do Azure – Azure IoT Edge | Documentos da Microsoft
description: Utilizar o portal do Azure para criar implementações automáticas para grupos do IoT Edge de dispositivos
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 69ba0a882c0e52e7c0d063b8f77e7a0fe22526a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126369"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementar e monitorizar os módulos do IoT Edge em escala no portal do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

O Azure IoT Edge permite-lhe mover analytics no Edge e fornece uma interface na cloud, para que possa gerir e monitorizar os seus dispositivos IoT Edge sem ter de aceder fisicamente aos cada um deles. A capacidade para gerir remotamente dispositivos é cada vez mais importante, como soluções de Internet das coisas estão crescendo maiores e mais complexas. O Azure IoT Edge foi concebido para suportar suas metas de negócios, não importa o número de dispositivos, adicionar.

Pode gerir dispositivos individuais e implementar módulos-los um de cada vez. No entanto, se desejar fazer alterações em dispositivos em grande escala, pode criar uma **implementação automática do IoT Edge**, que faz parte da gestão automática de dispositivos no IoT Hub. As implementações são processos dinâmicos que permitem-lhe implementar vários módulos para vários dispositivos, ao mesmo tempo, controlar o estado de funcionamento dos módulos e fazer alterações quando for necessário. 

## <a name="identify-devices-using-tags"></a>Identificar os dispositivos utilizando etiquetas

Antes de poder criar uma implementação, terá de ser capazes de especificar quais os dispositivos que quer afetar. O Azure IoT Edge identifica dispositivos que utilizam **etiquetas** no dispositivo duplo. Cada dispositivo pode ter várias etiquetas e pode defini-las qualquer forma que faça sentido para a sua solução. Por exemplo, se gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes etiquetas a um dispositivo:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Para obter mais informações sobre dispositivos duplos e etiquetas, consulte [compreender e utilizar dispositivos duplos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implementação

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 
1. Selecione **do IoT Edge**.
1. Selecione **adicionar implementação do IoT Edge**.

Existem cinco passos para criar uma implementação. As seções a seguir, percorra cada um deles. 

### <a name="step-1-name-and-label"></a>Passo 1: Nome e etiqueta

1. Dê um nome exclusivo que é até 128 minúsculas de sua implementação. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Pode adicionar etiquetas como pares chave-valor para ajudar a monitorizar as suas implementações. Por exemplo, **HostPlatform** e **Linux**, ou **versão** e **3.0.1**.
1. Selecione **seguinte** para mover para o passo dois. 

### <a name="step-2-add-modules-optional"></a>Passo 2: Adicionar módulos (opcionais)

Existem dois tipos de módulos que podem ser adicionados a uma implementação. A primeira é um módulo com base num serviço do Azure, como conta de armazenamento ou o Stream Analytics. O segundo é um módulo com o seu próprio código. Pode adicionar vários módulos de qualquer tipo para uma implementação. 

Se criar uma implementação sem módulos, ele remove quaisquer módulos atuais dos dispositivos. 

>[!NOTE]
>As funções do Azure não suporta a implementação de serviço do Azure automatizada ainda. Utilize a implementação do módulo personalizado para adicionar manualmente esse serviço para a sua implementação. 

Para adicionar um módulo do Azure Stream Analytics, siga estes passos:

1. Na **módulos de implementação** secção da página, clique em **Add**.
1. Selecione **módulo do Azure Stream Analytics**.
1. Escolha sua **subscrição** no menu pendente.
1. Escolha sua **tarefa do Edge** no menu pendente.
1. Selecione **guardar** para adicionar o seu módulo para a implementação. 

Para adicionar código personalizado como um módulo, ou para adicionar manualmente um módulo de serviço do Azure, siga estes passos:

1. Na **definições de registo de contentor** secção da página, fornecer os nomes e as credenciais para quaisquer registos de contentor privado que contêm as imagens de módulo para esta implementação. O agente do Edge irá reportar o erro 500 se ele não é possível localizar a credencial de registo de contentor para uma imagem do Docker.
1. Na **módulos de implementação** secção da página, clique em **Add**.
1. Selecione **módulo do IoT Edge**.
1. Dê o seu módulo uma **nome**.
1. Para o **URI da imagem** , insira a imagem de contentor para o seu módulo. 
1. Especificar qualquer **opções de criar contentor** que deve ser passado para o contentor. Para obter mais informações, consulte [docker criar](https://docs.docker.com/engine/reference/commandline/create/).
1. Utilize o menu pendente para selecionar uma **política de reinício**. Escolha uma das seguintes opções: 
   * **Sempre** -o módulo for reiniciado sempre se ele for encerrado por qualquer motivo.
   * **Nunca** -o módulo nunca reinicia se ele for encerrado por qualquer motivo.
   * **Falha no** -o módulo for reiniciado no caso de falha, mas não se ele encerre corretamente. 
   * **No mau** -o módulo for reiniciado, se ele falha ou devolve um Estado de mau estado de funcionamento. Cabe-lhe para cada módulo para implementar a função de estado de funcionamento. 
1. Utilize o menu pendente para selecionar o **estado pretendido** para o módulo. Escolha uma das seguintes opções:
   * **Executar** -esta é a opção predefinida. O módulo começará a executar imediatamente depois da implementação.
   * **Parado** -depois de ser implementado, o módulo permanecerá inativo até que a chamada para iniciar por si ou por outro módulo.
1. Selecione **duplo do módulo de conjunto de propriedades pretendidas** se pretender adicionar etiquetas ou outras propriedades para o duplo do módulo.
1. Introduza **variáveis de ambiente** para este módulo. Variáveis de ambiente fornecem informações de suplemento para um módulo de facilitar o processo de configuração.
1. Selecione **guardar** para adicionar o seu módulo para a implementação. 

Quando tiver todos os módulos para uma implementação configurado, selecione **seguinte** para mover para o terceiro passo.

### <a name="step-3-specify-routes-optional"></a>Passo 3: Especificar rotas (opcionais)

Rotas definem como módulos se comunicam entre si dentro de uma implantação. Por predefinição, o assistente apresenta uma rota chamada **rota** e definido como **FROM /\* em $ a montante**, que significa que todas as mensagens de saída por quaisquer módulos são enviadas ao seu hub IoT.  

Adicionar ou atualizar as rotas com as informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próxima** para continuar para a secção de revisão.

### <a name="step-4-specify-metrics-optional"></a>Passo 4: Especificar as métricas (opcionais)

Métricas fornecem contagens de resumidas dos diversos Estados em que um dispositivo pode comunicar de volta como resultado de aplicar o conteúdo de configuração.

1. Introduza um nome para **nome da métrica**.

1. Introduza uma consulta para **critérios de métrica**. A consulta baseia-se no módulo duplo de hub do IoT Edge [propriedades comunicadas](module-edgeagent-edgehub.md#edgehub-reported-properties). A métrica representa o número de linhas retornadas pela consulta.

Por exemplo:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="step-5-target-devices"></a>Passo 5: Dispositivos de destino

Utilize a propriedade de etiquetas dos seus dispositivos para os dispositivos específicos que devem receber esta implementação de destino. 

Uma vez que várias implementações podem direcionar o mesmo dispositivo, deve dar a cada implementação um número de prioridade. Se já houver um conflito, a implementação com a prioridade mais alta (valores mais altos indicam a prioridade mais alta) wins. Se duas implementações têm o mesmo número de prioridade, aquele que foi criado mais recentemente wins. 

1. Introduza um número inteiro positivo para a implementação **prioridade**. No caso de duas ou mais implementações destinam-se no mesmo dispositivo, será aplicada a implementação com o maior valor numérico da prioridade.
1. Introduza um **condição de destino** para determinar quais os dispositivos que serão visados para esta implementação. A condição baseia-se nas etiquetas de twin do dispositivo ou o dispositivo duplo propriedades comunicadas e deve corresponder ao formato de expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`. 
1. Selecione **seguinte** para avançar para a etapa final.

### <a name="step-6-review-deployment"></a>Passo 6: Implementação de revisão

Reveja as suas informações de implantação, em seguida, selecione **submeter**.

## <a name="deploy-modules-from-azure-marketplace"></a>Implementar módulos do Azure Marketplace

O Azure Marketplace é um mercado online de aplicações e serviços onde pode navegar por uma vasta gama de aplicações empresariais e soluções que estão certificadas e otimizadas para executar no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure Marketplace também pode ser acedido através do portal do Azure em **criar um recurso**.

Pode instalar o módulo do IoT Edge do Azure Marketplace ou do portal do Azure:

1. Encontre um módulo e iniciar o processo de implantação.

   * Portal do Azure: Encontre um módulo e selecione **criar**.

   * Azure Marketplace:

     1. Encontre um módulo e selecione **obter agora**.
     1. Concorda em termos do fornecedor de política de privacidade e de utilização selecionando **continuar**.

1. Escolha a sua subscrição e o IoT Hub para que o dispositivo de destino está ligado.

1. Escolher **implementar em escala**.

1. Escolha se pretende adicionar o módulo para uma nova implementação ou um clone de uma implementação existente; Se a clonagem, selecione a implementação existente na lista.

1. Selecione **criar** para continuar o processo de criação de uma implementação à escala. Poderá especificar os detalhes do mesmo, tal como faria para qualquer implementação.

## <a name="monitor-a-deployment"></a>Monitorizar uma implementação

Para ver os detalhes de uma implementação e monitorizar os dispositivos a executá-lo, utilize os seguintes passos:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT. 
1. Selecione **do IoT Edge**.
1. Selecione **implementações do IoT Edge**. 

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de implementação. Para cada implementação, pode ver os seguintes detalhes:
   * **ID** -o nome da implementação.
   * **Condição de destino** -a marca usada para definir os dispositivos visados.
   * **Prioridade** -o número de prioridade atribuído para a implementação.
   * **As métricas do sistema** - **direcionada** Especifica o número de dispositivos duplos no IoT Hub que correspondem à condição de destino, e **aplicados** Especifica o número de dispositivos que tenham o conteúdo de implementação aplicou para seus duplos de módulo do IoT Hub. 
   * **Métricas de dispositivo** -o número de dispositivos de ponta na implementação de relatórios de êxito ou erros de tempo de execução do cliente do IoT Edge.
   * **Métricas personalizadas** -o número de dispositivos de ponta na implementação de dados para qualquer métricas que definiu para a implementação de relatórios.
   * **Hora de criação** -o carimbo de hora de quando a implementação foi criada. Este timestamp é utilizado para dividir os empates quando duas implementações têm a mesma prioridade. 
1. Selecione a implementação que pretende monitorizar.  
1. Inspecione os detalhes de implementação. Pode usar guias para rever os detalhes da implementação.

## <a name="modify-a-deployment"></a>Modificar uma implementação

Quando modifica uma implementação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um dispositivo não cumpre a condição de destino antigo, mas atenda à nova condição de destino e esta implementação é a prioridade mais alta para esse dispositivo, em seguida, esta implementação é aplicada ao dispositivo. 
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino, desinstala esta implementação e sobre a implementação de prioridade mais alta seguinte. 
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino e não cumpre a condição de destino de todas as implementações, em seguida, nenhuma alteração ocorre no dispositivo. O dispositivo continua a ser executada os respectivos módulos atuais no respetivo estado atual, mas não é gerido como parte desta implementação mais. Assim que ele atenda à condição de destino de qualquer outra implementação, desinstala esta implementação e demora no novo. 

Para modificar uma implementação, utilize os seguintes passos: 

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT. 
1. Selecione **do IoT Edge**.
1. Selecione **implementações do IoT Edge**. 

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implementação que pretende modificar. 
1. Efetue as atualizações para os seguintes campos: 
   * Condição de destino
   * As métricas - pode modificar ou eliminar as métricas que definiu ou adicionar novos.
   * Etiquetas
   * Prioridade
1. Selecione **Guardar**.
1. Siga os passos em [monitorizar uma implementação](#monitor-a-deployment) para ver as alterações a implementar. 

## <a name="delete-a-deployment"></a>Eliminar uma implementação

Quando elimina uma implementação, demorar todos os dispositivos na sua implementação de prioridade mais alta seguinte. Se os dispositivos não cumprem a condição de destino de qualquer outra implementação, os módulos não são removidos quando a implementação é eliminada. 

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT. 
1. Selecione **do IoT Edge**.
1. Selecione **implementações do IoT Edge**. 

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Utilize a caixa de verificação para selecionar a implementação que pretende eliminar. 
1. Selecione **Eliminar**.
1. Uma linha de comandos irá informá-lo a que esta ação irá eliminar esta implementação e reverter para o estado anterior para todos os dispositivos.  Isso significa que uma implementação com uma prioridade mais baixa será aplicada.  Se se destinar sem implementações, não módulos serão removidos. Se pretender remover todos os módulos do seu dispositivo, crie uma implementação com zero módulos e implementá-la nos dispositivos do mesmo. Selecione **Sim** para continuar. 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [implementar módulos Edge dispositivos](module-deployment-monitoring.md).
