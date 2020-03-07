---
title: Implementar módulos à escala no portal Azure - Azure IoT Edge
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396733"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementar e monitorizar os módulos do IoT Edge em escala no portal do Azure

Crie uma **implementação automática IoT Edge** no portal Azure para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para IoT Edge fazem parte da funcionalidade [de gestão automática](/azure/iot-hub/iot-hub-automatic-device-management) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implementar vários módulos em vários dispositivos, rastrear o estado e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [as implementações automáticas De IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar os dispositivos utilizando etiquetas

Antes de poder criar uma implementação, terá de ser capazes de especificar quais os dispositivos que quer afetar. O Azure IoT Edge identifica dispositivos que utilizam **etiquetas** no dispositivo twin. Cada dispositivo pode ter várias tags que define de qualquer forma que faça sentido para a sua solução.

Por exemplo, se você gerir um campus de edifícios inteligentes, você pode adicionar localização, tipo de quarto e etiquetas ambientais a um dispositivo:

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

Para obter mais informações sobre gémeos e tags de dispositivos, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implementação

O IoT Edge fornece dois tipos diferentes de implementações automáticas que pode usar para personalizar o seu cenário. Pode criar uma *implementação*padrão, que inclui módulos de tempo de funcionamento do sistema e quaisquer módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implementação. Ou pode criar uma *implementação em camadas,* que inclui apenas módulos e rotas personalizadas, e não o tempo de funcionamento do sistema. Muitas implementações em camadas podem ser combinadas num dispositivo, em cima de uma implementação padrão. Para obter mais informações sobre como os dois tipos de implementações automáticas funcionam em conjunto, consulte [as implementações automáticas De Entendada IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

Os passos para criar uma implantação e uma implantação em camadas são muito semelhantes. Quaisquer diferenças são chamadas nos seguintes passos.

1. No [portal Azure,](https://portal.azure.com)vá ao seu Hub IoT.
1. No menu no painel esquerdo, selecione **IoT Edge** sob **gestão automática**de dispositivos .
1. Na barra superior, selecione **Criar implantação** ou **criar implantação em camadas**.

Existem cinco passos para criar uma implementação. As seções a seguir, percorra cada um deles.

### <a name="step-1-name-and-label"></a>Passo 1: Nome e etiqueta

1. Dê um nome exclusivo que é até 128 minúsculas de sua implementação. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Pode adicionar etiquetas como pares de valor-chave para ajudar a rastrear as suas implementações. Por exemplo, **HostPlatform** e **Linux**, ou **Versão** e **3.0.1**.
1. Selecione **Seguinte: Módulos** para passar para o segundo passo.

### <a name="step-2-modules"></a>Passo 2: Módulos

Pode adicionar até 20 módulos a uma implementação. Se criar uma implementação sem módulos, remove quaisquer módulos atuais dos dispositivos-alvo.

Nas implementações, pode gerir as definições para o agente IoT Edge e módulos de hub IoT Edge. Selecione **Definições de tempo** de execução para configurar os dois módulos de tempo de execução. Na implementação em camadas, os módulos de tempo de execução não estão incluídos, pelo que não podem ser configurados.

Pode adicionar três tipos de módulos:

* Módulo IoT Edge
* Módulo marketplace
* Módulo de Análise De Fluxo Azure

#### <a name="add-an-iot-edge-module"></a>Adicione um módulo IoT Edge

Para adicionar código personalizado como um módulo, ou para adicionar manualmente um módulo de serviço do Azure, siga estes passos:

1. Na secção credenciais de registo de **contentores** da página, forneça os nomes e credenciais para quaisquer registos de contentores privados que contenham as imagens do módulo para esta implementação. O Agente IoT Edge reportará o erro 500 se não encontrar a credencial do registo de contentores para uma imagem do Docker.
1. Na secção **Módulos IoT Edge** da página, clique em **Adicionar**.
1. Selecione **Módulo IoT Edge** a partir do menu suspenso.
1. Dê ao seu módulo um nome de **módulo IoT Edge**.
1. Para o campo **Image URI,** introduza a imagem do recipiente para o seu módulo.
1. Utilize o menu suspenso para selecionar uma política de **reiniciar**. Escolha uma das seguintes opções:
   * **sempre** - O módulo reinicia sempre se desligar por qualquer motivo.
   * **nunca** - O módulo nunca reinicia se desligar por qualquer motivo.
   * **on-failure** - O módulo reinicia se se despenhar, mas não se desligar de forma limpa.
   * **on-unhealthy** - O módulo reinicia se colidir ou devolver um estado pouco saudável. Cabe-lhe para cada módulo para implementar a função de estado de funcionamento.
1. Utilize o menu suspenso para selecionar o **Estado Desejado** para o módulo. Escolha uma das seguintes opções:
   * **execução** - Correr é a opção padrão. O módulo começará a executar imediatamente depois da implementação.
   * **parado** - Depois de ser implantado, o módulo permanecerá inativo até ser chamado para ser iniciado por si ou por outro módulo.
1. Especifique quaisquer opções de criação de **recipientes** que devem ser passadas para o recipiente. Para mais informações, consulte [docker criar](https://docs.docker.com/engine/reference/commandline/create/).
1. Selecione **Definições Twin do Módulo** Se pretender adicionar tags ou outras propriedades ao módulo twin.
1. Introduza **Variáveis ambientais** para este módulo. As variáveis ambientais fornecem informações de configuração a um módulo.
1. Selecione **Adicionar** para adicionar o seu módulo à implementação.

#### <a name="add-a-module-from-the-marketplace"></a>Adicione um módulo do Marketplace

Para adicionar um módulo do Azure Marketplace, siga estes passos:

1. Na secção **Módulos IoT Edge** da página, clique em **Adicionar**.
1. Selecione **Módulo marketplace** a partir do menu suspenso.
1. Escolha um módulo a partir da página Mercado do **Módulo IoT Edge.** O módulo que selecionar está automaticamente configurado para a sua subscrição, grupo de recursos e dispositivo. Em seguida, aparece na sua lista de módulos IoT Edge. Alguns módulos podem necessitar de configuração adicional. Para mais informações, consulte [Módulos de Implantação do mercado Azure](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Adicione um módulo Stream Analytics

Para adicionar um módulo do Azure Stream Analytics, siga estes passos:

1. Na secção **Módulos IoT Edge** da página, clique em **Adicionar**.
1. Selecione módulo **Azure Stream Analytics** a partir do menu suspenso.
1. No painel certo, escolha a sua **Subscrição**.
1. Escolha o seu trabalho ioT **Edge.**
1. Selecione **Guardar** para adicionar o seu módulo à implementação.

#### <a name="configure-module-settings"></a>Configurar as definições do módulo

Depois de adicionar um módulo a uma implementação, pode selecionar o seu nome para abrir a página **'Módulo 'Borda'.** Nesta página, pode editar as definições do módulo, variáveis ambientais, criar opções e módulo twin. Se tiver adicionado um módulo do mercado, pode já ter alguns destes parâmetros preenchidos.

Se estiver a criar uma implementação em camadas, poderá estar a configurar um módulo que existe noutras implementações visando os mesmos dispositivos. Para atualizar o módulo twin sem sobrepor outras versões, abra o separador **Definições Twin Module.** Crie um novo **Módulo Twin Property** com um nome único para uma subsecção dentro das propriedades desejadas pelo módulo twin, por exemplo, `properties.desired.settings`. Se definir propriedades dentro do campo `properties.desired`, irá substituir as propriedades desejadas para o módulo definido em quaisquer implementações prioritárias mais baixas.

![Definir propriedade twin módulo para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo twin em implementações em camadas, consulte a [implementação em camadas](module-deployment-monitoring.md#layered-deployment).

Uma vez configurado todos os módulos para uma implementação, selecione **Next: Routes** to move to step three.

### <a name="step-3-routes"></a>Passo 3: Rotas

Rotas definem como módulos se comunicam entre si dentro de uma implantação. Por predefinição, o assistente dá-lhe uma rota chamada **a montante** e definida como **FROM /messages/\* INTO $upstream**, o que significa que qualquer saída de mensagens por quaisquer módulos é enviada para o seu hub IoT.  

Adicione ou atualize as rotas com informações das [rotas declarar,](module-composition.md#declare-routes)em seguida, selecione **Next** para continuar na secção de revisão.

Selecione **Seguinte: Métricas**.

### <a name="step-4-metrics"></a>Passo 4: Métricas

As métricas fornecem contagens sumárias dos vários estados que um dispositivo pode reportar como resultado da aplicação do conteúdo de configuração.

1. Introduza um nome para **Nome Métrico**.

1. Insira uma consulta para **critérios métricos**. A consulta baseia-se em [propriedades reportadas](module-edgeagent-edgehub.md#edgehub-reported-properties)pelo módulo hub IoT Edge. A métrica representa o número de linhas devolvidas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecione **Seguinte: Dispositivos-alvo**.

### <a name="step-5-target-devices"></a>Passo 5: Dispositivos-alvo

Utilize a propriedade de etiquetas dos seus dispositivos para os dispositivos específicos que devem receber esta implementação de destino.

Uma vez que várias implementações podem direcionar o mesmo dispositivo, deve dar a cada implementação um número de prioridade. Se houver um conflito, o destacamento com a maior prioridade (valores maiores indicam maior prioridade) vence. Se duas implementações têm o mesmo número de prioridade, aquele que foi criado mais recentemente wins.

Se várias implementações visarem o mesmo dispositivo, então apenas é aplicada a única com maior prioridade. Se várias implementações em camadas atingirem o mesmo dispositivo, então todas são aplicadas. No entanto, se alguma propriedade for duplicada, como se houvesse duas rotas com o mesmo nome, então a da maior prioridade de implantação em camadas substitui o resto.

Qualquer implantação em camadas que direcione um dispositivo deve ter uma prioridade maior do que a implantação da base para ser aplicada.

1. Introduza um inteiro positivo para a **Prioridade**de implantação.
1. Introduza uma **condição de destino** para determinar quais os dispositivos que serão alvo com esta implementação. A condição baseia-se em etiquetas duplas do dispositivo ou propriedades reportadas de dispositivos twin e deve corresponder ao formato de expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **Seguinte: Rever + Criar** para passar ao passo final.

### <a name="step-6-review-and-create"></a>Passo 6: Rever e criar

Reveja as informações de implementação e, em seguida, selecione **Criar**.

## <a name="monitor-a-deployment"></a>Monitorizar uma implementação

Para ver os detalhes de uma implementação e monitorizar os dispositivos a executá-lo, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione o separador de **implementações IoT Edge.**

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de implementação. Para cada implementação, pode ver os seguintes detalhes:
   * **ID** - o nome da implantação.
   * **Tipo** - o tipo de implantação, seja **implantação** ou **implantação em camadas**.
   * **Condição do alvo** - a etiqueta utilizada para definir dispositivos-alvo.
   * **Prioridade** - o número prioritário atribuído à implantação.
   * **As métricas** do sistema - **Targeted** especifica o número de gémeos dispositivos no IoT Hub que correspondem à condição de alvo, e **Aplicou** especifica o número de dispositivos que tiveram o conteúdo de implementação aplicado aos seus gémeos módulos no IoT Hub.
   * **Métricas** do dispositivo - o número de dispositivos IoT Edge no relatório de implementação do sucesso ou erros do tempo de execução do cliente IoT Edge.
   * **Métricas Personalizadas** - o número de dispositivos IoT Edge nos dados de relatóriode implementação para quaisquer métricas que definiu para a implementação.
   * Tempo de **Criação** - o carimbo de tempo a partir de quando a implantação foi criada. Este timestamp é utilizado para dividir os empates quando duas implementações têm a mesma prioridade.
1. Selecione a implementação que pretende monitorizar.  
1. Inspecione os detalhes de implementação. Pode usar guias para rever os detalhes da implementação.

## <a name="modify-a-deployment"></a>Modificar uma implementação

Quando modifica uma implementação, as alterações são replicadas imediatamente para todos os dispositivos direcionados.

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um dispositivo não cumpre a condição de destino antigo, mas atenda à nova condição de destino e esta implementação é a prioridade mais alta para esse dispositivo, em seguida, esta implementação é aplicada ao dispositivo.
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino, desinstala esta implementação e sobre a implementação de prioridade mais alta seguinte.
* Se um dispositivo atualmente em execução nesta implementação já não cumpre a condição de destino e não cumpre a condição de destino de todas as implementações, em seguida, nenhuma alteração ocorre no dispositivo. O dispositivo continua a ser executada os respectivos módulos atuais no respetivo estado atual, mas não é gerido como parte desta implementação mais. Assim que ele atenda à condição de destino de qualquer outra implementação, desinstala esta implementação e demora no novo.

Para modificar uma implementação, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione o separador **IoT Edge Deployments.**

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implementação que pretende modificar.
1. Faça atualizações sobre os seguintes separadores:
   * **Condição-alvo**
   * **Métricas** - pode modificar ou eliminar métricas que definiu ou adicionar novas.
   * **Etiquetas**
   * **Módulos**
   * **Rotas**
   * **Implementação**

1. Selecione **Guardar**.
1. Siga os passos no [Monitor uma implementação](#monitor-a-deployment) para ver as alterações a serem lançadas.

## <a name="delete-a-deployment"></a>Eliminar uma implementação

Ao eliminar uma implementação, quaisquer dispositivos implantados assumem a sua próxima implementação de prioridade máxima. Se os dispositivos não cumprem a condição de destino de qualquer outra implementação, os módulos não são removidos quando a implementação é eliminada.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione o separador **IoT Edge Deployments.**

   ![Ver as implementações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Utilize a caixa de verificação para selecionar a implementação que pretende eliminar.
1. Selecione **Eliminar**.
1. Uma linha de comandos irá informá-lo a que esta ação irá eliminar esta implementação e reverter para o estado anterior para todos os dispositivos.  Isto significa que será aplicável um destacamento com uma prioridade mais baixa. Se nenhuma outra implementação for direcionada, nenhum módulo será removido. Se pretender remover todos os módulos do seu dispositivo, crie uma implementação com zero módulos e implementá-la nos dispositivos do mesmo. Selecione **Sim** para continuar.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).
