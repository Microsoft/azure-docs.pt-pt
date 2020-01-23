---
title: Implantar módulos em escala em portal do Azure Azure IoT Edge
description: Utilizar o portal do Azure para criar implementações automáticas para grupos do IoT Edge de dispositivos
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510265"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementar e monitorizar os módulos do IoT Edge em escala no portal do Azure

Crie um **IOT Edge implantação automática** no portal do Azure para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso de [gerenciamento automático de dispositivo](/azure/iot-hub/iot-hub-automatic-device-management) do Hub IOT. As implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar os dispositivos utilizando etiquetas

Antes de poder criar uma implementação, terá de ser capazes de especificar quais os dispositivos que quer afetar. O Azure IoT Edge identifica dispositivos que utilizam **etiquetas** no dispositivo duplo. Cada dispositivo pode ter várias marcas que você define de qualquer forma que faça sentido para sua solução.

Por exemplo, se você gerenciar um campus de prédios inteligentes, poderá adicionar localização, tipo de sala e marcas de ambiente a um dispositivo:

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

O IoT Edge fornece dois tipos diferentes de implantações automáticas que você pode usar para personalizar seu cenário. Você pode criar uma *implantação*padrão, que inclui os módulos de tempo de execução do sistema e todos os módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implantação. Ou você pode criar uma *implantação em camadas*, que inclui apenas módulos e rotas personalizadas, não o tempo de execução do sistema. Muitas implantações em camadas podem ser combinadas em um dispositivo, além de uma implantação padrão. Para obter mais informações sobre como os dois tipos de implantações automáticas funcionam em conjunto, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

As etapas para criar uma implantação e uma implantação em camadas são muito semelhantes. Todas as diferenças são chamadas nas etapas a seguir.

1. Na [portal do Azure](https://portal.azure.com), vá para o Hub IOT.
1. No menu no painel esquerdo, selecione **IOT Edge** em gerenciamento de **dispositivo automático**.
1. Na barra superior, selecione **criar implantação** ou **criar implantação em camadas**.

Existem cinco passos para criar uma implementação. As seções a seguir, percorra cada um deles.

### <a name="step-1-name-and-label"></a>Etapa 1: nome e rótulo

1. Dê um nome exclusivo que é até 128 minúsculas de sua implementação. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares de chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux**, ou **version** e **3.0.1**.
1. Selecione **Avançar: módulos** para mover para a etapa dois.

### <a name="step-2-modules"></a>Etapa 2: módulos

Você pode adicionar até 20 módulos a uma implantação. Se você criar uma implantação sem módulos, ela removerá os módulos atuais dos dispositivos de destino.

Em implantações, você pode gerenciar as configurações para os módulos agente de IoT Edge e IoT Edge Hub. Selecione **configurações de tempo de execução** para configurar os dois módulos de tempo de execução. Em implantação em camadas, os módulos de tempo de execução não são incluídos, portanto não podem ser configurados.

Você pode adicionar três tipos de módulos:

* Módulo IoT Edge
* Módulo do Marketplace
* Módulo Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Adicionar um módulo IoT Edge

Para adicionar código personalizado como um módulo, ou para adicionar manualmente um módulo de serviço do Azure, siga estes passos:

1. Na seção **credenciais de registro de contêiner** da página, forneça os nomes e as credenciais para qualquer registro de contêiner privado que contenha as imagens de módulo para essa implantação. O agente de IoT Edge relatará o erro 500 se ele não conseguir encontrar a credencial do registro de contêiner para uma imagem do Docker.
1. Na seção **módulos IOT Edge** da página, clique em **Adicionar**.
1. Selecione **IOT Edge módulo** no menu suspenso.
1. Dê ao seu módulo um **nome de módulo IOT Edge**.
1. Para o **URI da imagem** , insira a imagem de contentor para o seu módulo.
1. Utilize o menu pendente para selecionar uma **política de reinício**. Pode escolher uma das seguintes opções:
   * **sempre** -o módulo sempre reinicia se for desligado por qualquer motivo.
   * **nunca** -o módulo nunca será reiniciado se for desligado por qualquer motivo.
   * **em** caso de falha-o módulo será reiniciado se ele falhar, mas não se for desligado corretamente.
   * Não **íntegro** – o módulo será reiniciado se ele falhar ou retornar um status não íntegro. Cabe-lhe para cada módulo para implementar a função de estado de funcionamento.
1. Utilize o menu pendente para selecionar o **estado pretendido** para o módulo. Pode escolher uma das seguintes opções:
   * **executando-em** execução é a opção padrão. O módulo começará a executar imediatamente depois da implementação.
   * **parado** -depois de ser implantado, o módulo permanecerá ocioso até ser chamado para ser iniciado por você ou por outro módulo.
1. Especificar qualquer **opções de criar contentor** que deve ser passado para o contentor. Para obter mais informações, consulte [docker criar](https://docs.docker.com/engine/reference/commandline/create/).
1. Selecione **configurações de módulo de configuração** se desejar adicionar marcas ou outras propriedades ao módulo.
1. Insira as **variáveis de ambiente** para este módulo. As variáveis de ambiente fornecem informações de configuração para um módulo.
1. Selecione **Adicionar** para adicionar o módulo à implantação.

#### <a name="add-a-module-from-the-marketplace"></a>Adicionar um módulo do Marketplace

Para adicionar um módulo do Azure Marketplace, siga estas etapas:

1. Na seção **módulos IOT Edge** da página, clique em **Adicionar**.
1. Selecione o **módulo Marketplace** no menu suspenso.
1. Escolha um módulo na página **IOT Edge do Marketplace do módulo** . O módulo selecionado é configurado automaticamente para sua assinatura, grupo de recursos e dispositivo. Em seguida, ele aparece na lista de módulos de IoT Edge. Alguns módulos podem exigir configuração adicional. Para obter mais informações, consulte [implantar módulos do Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Adicionar um módulo Stream Analytics

Para adicionar um módulo do Azure Stream Analytics, siga estes passos:

1. Na seção **módulos IOT Edge** da página, clique em **Adicionar**.
1. Selecione **Azure Stream Analytics módulo** no menu suspenso.
1. No painel direito, escolha sua **assinatura**.
1. Escolha seu trabalho do IoT **Edge**.
1. Selecione **guardar** para adicionar o seu módulo para a implementação.

#### <a name="configure-module-settings"></a>Definir configurações de módulo

Depois de adicionar um módulo a uma implantação, você pode selecionar seu nome para abrir a página **atualizar IOT Edge módulo** . Nessa página, você pode editar as configurações do módulo, as variáveis de ambiente, as opções criar e o módulo. Se você adicionou um módulo do Marketplace, talvez ele já tenha alguns desses parâmetros preenchidos.

Se você estiver criando uma implantação em camadas, poderá estar Configurando um módulo que existe em outras implantações direcionando os mesmos dispositivos. Para atualizar o módulo de texto sem substituir outras versões, abra a guia **configurações de configuração do módulo** . Crie uma nova **Propriedade MyModule** com um nome exclusivo para uma subseção dentro das propriedades desejadas do módulo r, por exemplo `properties.desired.settings`. Se você definir propriedades dentro do campo `properties.desired`, ele substituirá as propriedades desejadas para o módulo definido em qualquer implantação de prioridade mais baixa.

![Definir a propriedade addmodule para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo "em implantações em camadas", consulte [implantação em camadas](module-deployment-monitoring.md#layered-deployment).

Depois de ter todos os módulos de uma implantação configurada, selecione **Avançar: rotas** para mover para a etapa três.

### <a name="step-3-routes"></a>Etapa 3: rotas

Rotas definem como módulos se comunicam entre si dentro de uma implantação. Por padrão, o assistente fornece uma rota chamada **upstream** e definido como **de/messages/\* em $upstream**, o que significa que qualquer saída de mensagens por qualquer módulo é enviada para o Hub IOT.  

Adicionar ou atualizar as rotas com as informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próxima** para continuar para a secção de revisão.

Selecione **Avançar: métricas**.

### <a name="step-4-metrics"></a>Etapa 4: métricas

As métricas fornecem contagens de resumo dos vários Estados que um dispositivo pode reportar como resultado da aplicação do conteúdo de configuração.

1. Insira um nome para o **nome da métrica**.

1. Insira uma consulta para **critérios de métrica**. A consulta é baseada no módulo de Hub IoT Edge [Propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties). A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecione **Avançar: dispositivos de destino**.

### <a name="step-5-target-devices"></a>Etapa 5: dispositivos de destino

Utilize a propriedade de etiquetas dos seus dispositivos para os dispositivos específicos que devem receber esta implementação de destino.

Uma vez que várias implementações podem direcionar o mesmo dispositivo, deve dar a cada implementação um número de prioridade. Se houver algum conflito, a implantação com a prioridade mais alta (valores maiores indicam prioridade mais alta) vence. Se duas implementações têm o mesmo número de prioridade, aquele que foi criado mais recentemente wins.

Se várias implantações tiverem como destino o mesmo dispositivo, somente aquela com a prioridade mais alta será aplicada. Se várias implantações em camadas tiverem como destino o mesmo dispositivo, elas serão todas aplicadas. No entanto, se alguma das propriedades for duplicada, como se houver duas rotas com o mesmo nome, a da implantação em camadas de prioridade mais alta substituirá o restante.

Qualquer implantação em camadas direcionada a um dispositivo deve ter uma prioridade mais alta do que a implantação base para ser aplicada.

1. Introduza um número inteiro positivo para a implementação **prioridade**.
1. Introduza um **condição de destino** para determinar quais os dispositivos que serão visados para esta implementação. A condição é baseada nas marcas de dispositivo ou nas propriedades relatadas do dispositivo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **Avançar: revisar + criar** para passar para a etapa final.

### <a name="step-6-review-and-create"></a>Etapa 6: examinar e criar

Examine as informações de implantação e, em seguida, selecione **criar**.

## <a name="monitor-a-deployment"></a>Monitorizar uma implementação

Para ver os detalhes de uma implementação e monitorizar os dispositivos a executá-lo, utilize os seguintes passos:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.
1. Selecione **do IoT Edge**.
1. Selecione a guia **implantações IOT Edge** .

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de implementação. Para cada implantação, você pode exibir os seguintes detalhes:
   * **ID** -o nome da implementação.
   * **Tipo** -o tipo de implantação, **implantação** ou **implantação em camadas**.
   * **Condição de destino** -a marca usada para definir os dispositivos de destino.
   * **Prioridade** -o número de prioridade atribuído para a implementação.
   * **As métricas do sistema** - **direcionada** Especifica o número de dispositivos duplos no IoT Hub que correspondem à condição de destino, e **aplicados** Especifica o número de dispositivos que tenham o conteúdo de implementação aplicou para seus duplos de módulo do IoT Hub.
   * **Métricas do dispositivo** – o número de dispositivos IOT Edge na implantação que relataram êxito ou erros do tempo de execução do cliente IOT Edge.
   * **Métricas personalizadas** – o número de dispositivos IOT Edge na implantação de dados de relatórios para qualquer métrica que você definiu para a implantação.
   * **Hora de criação** – o carimbo de data/hora de quando a implantação foi criada. Este timestamp é utilizado para dividir os empates quando duas implementações têm a mesma prioridade.
1. Selecione a implementação que pretende monitorizar.  
1. Inspecione os detalhes de implementação. Pode usar guias para rever os detalhes da implementação.

## <a name="modify-a-deployment"></a>Modificar uma implementação

Quando modifica uma implementação, as alterações são replicadas imediatamente para todos os dispositivos direcionados.

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um dispositivo não cumpre a condição de destino antigo, mas atenda à nova condição de destino e esta implementação é a prioridade mais alta para esse dispositivo, em seguida, esta implementação é aplicada ao dispositivo.
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino, desinstala esta implementação e sobre a implementação de prioridade mais alta seguinte.
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino e não cumpre a condição de destino de todas as implementações, em seguida, nenhuma alteração ocorre no dispositivo. O dispositivo continua a ser executada os respectivos módulos atuais no respetivo estado atual, mas não é gerido como parte desta implementação mais. Assim que ele atenda à condição de destino de qualquer outra implementação, desinstala esta implementação e demora no novo.

Para modificar uma implementação, utilize os seguintes passos:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.
1. Selecione **do IoT Edge**.
1. Selecione a guia **Implantações IOT Edge** .

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implementação que pretende modificar.
1. Faça atualizações nas seguintes guias:
   * **Condição de destino**
   * **Métricas** – você pode modificar ou excluir as métricas que você definiu ou adicionar novas.
   * **Etiquetas**
   * **Módulos**
   * **Rotas**
   * **Implementação**

1. Selecione **Guardar**.
1. Siga os passos em [monitorizar uma implementação](#monitor-a-deployment) para ver as alterações a implementar.

## <a name="delete-a-deployment"></a>Eliminar uma implementação

Quando você exclui uma implantação, todos os dispositivos implantados assumem a próxima implantação de prioridade mais alta. Se os dispositivos não cumprem a condição de destino de qualquer outra implementação, os módulos não são removidos quando a implementação é eliminada.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.
1. Selecione **do IoT Edge**.
1. Selecione a guia **Implantações IOT Edge** .

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Utilize a caixa de verificação para selecionar a implementação que pretende eliminar.
1. Selecione **Eliminar**.
1. Uma linha de comandos irá informá-lo a que esta ação irá eliminar esta implementação e reverter para o estado anterior para todos os dispositivos.  Isso significa que uma implantação com prioridade mais baixa será aplicada. Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se pretender remover todos os módulos do seu dispositivo, crie uma implementação com zero módulos e implementá-la nos dispositivos do mesmo. Selecione **Sim** para continuar.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implantação de módulos em dispositivos IOT Edge](module-deployment-monitoring.md).
