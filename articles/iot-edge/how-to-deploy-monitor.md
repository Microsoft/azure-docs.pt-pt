---
title: Implantar módulos em escala em portal do Azure Azure IoT Edge
description: Use o portal do Azure para criar implantações automáticas para grupos de dispositivos IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 068845bf8cda7ce6abf11eefad0ed176688b34c5
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665853"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implantar e monitorar módulos IoT Edge em escala usando o portal do Azure

Crie um **IOT Edge implantação automática** no portal do Azure para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso de [gerenciamento automático de dispositivo](/azure/iot-hub/iot-hub-automatic-device-management) do Hub IOT. As implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações quando necessário. 

Para obter mais informações, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcas

Antes de criar uma implantação, você precisa ser capaz de especificar quais dispositivos deseja afetar. Azure IoT Edge identifica os dispositivos usando **marcas** no dispositivo. Cada dispositivo pode ter várias marcas que você define de qualquer forma que faça sentido para sua solução. Por exemplo, se você gerenciar um campus de prédios inteligentes, poderá adicionar as seguintes marcas a um dispositivo:

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

Para obter mais informações sobre dispositivos gêmeos e marcas, consulte [entender e usar o dispositivo gêmeos no Hub IOT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

1. Na [portal do Azure](https://portal.azure.com), vá para o Hub IOT. 
1. Selecione **IOT Edge**.
1. Selecione **adicionar IOT Edge implantação**.

Há cinco etapas para criar uma implantação. As seções a seguir percorrem cada uma delas. 

### <a name="step-1-name-and-label"></a>Etapa 1: nome e rótulo

1. Dê à sua implantação um nome exclusivo que tenha até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares de chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux**, ou **version** e **3.0.1**.
1. Selecione **Avançar** para passar para a etapa dois. 

### <a name="step-2-add-modules-optional"></a>Etapa 2: Adicionar módulos (opcional)

Você pode adicionar até 20 módulos a uma implantação. 

Se você criar uma implantação sem módulos, ela removerá os módulos atuais dos dispositivos de destino. 

Para adicionar um módulo do Azure Stream Analytics, siga estas etapas:

1. Na seção **módulos de implantação** da página, clique em **Adicionar**.
1. Selecione **Azure Stream Analytics módulo**.
1. Escolha sua **assinatura** no menu suspenso.
1. Escolha o trabalho do IoT **Edge** no menu suspenso.
1. Selecione **salvar** para adicionar o módulo à implantação. 

Para adicionar um código personalizado como um módulo ou adicionar manualmente um módulo de serviço do Azure, siga estas etapas:

1. Na seção **configurações de registro de contêiner** da página, forneça os nomes e as credenciais para qualquer registro de contêiner privado que contenha as imagens de módulo para essa implantação. O agente de IoT Edge relatará o erro 500 se ele não conseguir encontrar a credencial do registro de contêiner para uma imagem do Docker.
1. Na seção **módulos de implantação** da página, clique em **Adicionar**.
1. Selecione **IOT Edge módulo**.
1. Dê um **nome**ao seu módulo.
1. Para o campo **URI da imagem** , insira a imagem de contêiner para seu módulo. 
1. Especifique as **Opções de criação de contêiner** que devem ser passadas para o contêiner. Para obter mais informações, consulte [Docker criar](https://docs.docker.com/engine/reference/commandline/create/).
1. Use o menu suspenso para selecionar uma política de **reinicialização**. Escolha uma das seguintes opções: 
   * **Sempre** -o módulo sempre reinicia se for desligado por qualquer motivo.
   * **nunca** -o módulo nunca será reiniciado se for desligado por qualquer motivo.
   * **em** caso de falha-o módulo será reiniciado se ele falhar, mas não se for desligado corretamente. 
   * Não **íntegro** – o módulo será reiniciado se ele falhar ou retornar um status não íntegro. Cabe a cada módulo implementar a função status de integridade. 
1. Use o menu suspenso para selecionar o **status desejado** para o módulo. Escolha uma das seguintes opções:
   * **executando-em** execução é a opção padrão. O módulo começará a ser executado imediatamente após ser implantado.
   * **parado** -depois de ser implantado, o módulo permanecerá ocioso até ser chamado para ser iniciado por você ou por outro módulo.
1. Selecione **definir propriedades desejadas do módulo** . se desejar adicionar marcas ou outras propriedades ao módulo.
1. Insira as **variáveis de ambiente** para este módulo. As variáveis de ambiente fornecem informações de configuração para um módulo.
1. Selecione **salvar** para adicionar o módulo à implantação. 

Depois de ter todos os módulos de uma implantação configurada, selecione **Avançar** para passar para a etapa três.

### <a name="step-3-specify-routes-optional"></a>Etapa 3: especificar rotas (opcional)

As rotas definem como os módulos se comunicam entre si dentro de uma implantação. Por padrão, o assistente fornece uma rota chamada **rota** e definido como **de/* para $upstream * *, o que significa que qualquer saída de mensagens por qualquer módulo é enviada para o Hub IOT.  

Adicione ou atualize as rotas com informações de [declarar rotas](module-composition.md#declare-routes)e, em seguida, selecione **Avançar** para continuar na seção revisão.

### <a name="step-4-specify-metrics-optional"></a>Etapa 4: especificar métricas (opcional)

As métricas fornecem contagens de resumo dos vários Estados que um dispositivo pode reportar como resultado da aplicação do conteúdo de configuração.

1. Insira um nome para o **nome da métrica**.

1. Insira uma consulta para **critérios de métrica**. A consulta é baseada no módulo de Hub IoT Edge [Propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties). A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Etapa 5: dispositivos de destino

Use a propriedade Tags de seus dispositivos para direcionar os dispositivos específicos que devem receber essa implantação. 

Como várias implantações podem ter como destino o mesmo dispositivo, você deve dar a cada implantação um número de prioridade. Se houver algum conflito, a implantação com a prioridade mais alta (valores maiores indicam prioridade mais alta) vence. Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganha. 

1. Insira um número inteiro positivo para a **prioridade**de implantação.
1. Insira uma **condição de destino** para determinar quais dispositivos serão direcionados a essa implantação. A condição é baseada nas marcas de dispositivo ou nas propriedades relatadas do dispositivo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`. 
1. Selecione **Avançar** para passar para a etapa final.

### <a name="step-6-review-deployment"></a>Etapa 6: examinar a implantação

Examine as informações de implantação e, em seguida, selecione **Enviar**.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

O Azure Marketplace é um Marketplace de aplicativos e serviços online, no qual você pode navegar por uma ampla variedade de aplicativos e soluções empresariais que são certificados e otimizados para serem executados no Azure, incluindo [módulos IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure Marketplace também pode ser acessado por meio do portal do Azure em **criar um recurso**.

Você pode implantar um módulo IoT Edge do Azure Marketplace ou do portal do Azure:

1. Localize um módulo e inicie o processo de implantação.

   * Portal do Azure: Localize um módulo e selecione **criar**.

   * Azure Marketplace:

     1. Localize um módulo e selecione **obtê-lo agora**.
     1. Confirme os termos de uso e a política de privacidade do provedor selecionando **continuar**.

1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.

1. Escolha **implantar em escala**.

1. Escolha se deseja adicionar o módulo a uma nova implantação ou a um clone de uma implantação existente; Se estiver clonando, selecione a implantação existente na lista.

1. Selecione **criar** para continuar o processo de criação de uma implantação em escala. Você poderá especificar os mesmos detalhes que faria para qualquer implantação.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Para exibir os detalhes de uma implantação e monitorar os dispositivos que o executam, use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT. 
1. Selecione **IOT Edge**.
1. Selecione **implantações de IOT Edge**. 

   ![Exibir implantações de IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de implantação. Para cada implantação, você pode exibir os seguintes detalhes:
   * **ID** -o nome da implantação.
   * **Condição de destino** -a marca usada para definir os dispositivos de destino.
   * **Priority** -o número de prioridade atribuído à implantação.
   * **Métricas do sistema** - **direcionadas** especifica o número de dispositivos gêmeos no Hub IOT que correspondem à condição de destino e **aplicado** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado ao seu módulo gêmeos no Hub IOT. 
   * **Métricas do dispositivo** – o número de dispositivos IOT Edge na implantação que relataram êxito ou erros do tempo de execução do cliente IOT Edge.
   * **Métricas personalizadas** – o número de dispositivos IOT Edge na implantação de dados de relatórios para qualquer métrica que você definiu para a implantação.
   * **Hora de criação** – o carimbo de data/hora de quando a implantação foi criada. Esse carimbo de data/hora é usado para quebrar ligações quando duas implantações têm a mesma prioridade. 
1. Selecione a implantação que você deseja monitorar.  
1. Inspecione os detalhes da implantação. Você pode usar guias para examinar os detalhes da implantação.

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos de destino. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Se um dispositivo não atender à condição de destino antiga, mas atender à nova condição de destino e essa implantação for a prioridade mais alta para esse dispositivo, essa implantação será aplicada ao dispositivo. 
* Se um dispositivo atualmente executando essa implantação não atender mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação de prioridade mais alta. 
* Se um dispositivo atualmente executando essa implantação não atender mais à condição de destino e não atender à condição de destino de nenhuma outra implantação, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Quando ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e assume a nova. 

Para modificar uma implantação, use as seguintes etapas: 

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT. 
1. Selecione **IOT Edge**.
1. Selecione **implantações de IOT Edge**. 

   ![Exibir implantações de IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implantação que você deseja modificar. 
1. Faça atualizações nos seguintes campos: 
   * Condição de destino
   * Métricas – você pode modificar ou excluir as métricas que você definiu ou adicionar novas.
   * Etiquetas
   * Prioridade
1. Selecione **Guardar**.
1. Siga as etapas em [monitorar uma implantação](#monitor-a-deployment) para observar a distribuição das alterações. 

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos assumem sua próxima implantação de prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída. 

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT. 
1. Selecione **IOT Edge**.
1. Selecione **implantações de IOT Edge**. 

   ![Exibir implantações de IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Use a caixa de seleção para selecionar a implantação que você deseja excluir. 
1. Selecione **Eliminar**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.  Isso significa que uma implantação com prioridade mais baixa será aplicada.  Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se você quiser remover todos os módulos do seu dispositivo, crie uma implantação com um módulo zero e implante-a nos mesmos dispositivos. Selecione **Sim** para continuar. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implantação de módulos em dispositivos IOT Edge](module-deployment-monitoring.md).
