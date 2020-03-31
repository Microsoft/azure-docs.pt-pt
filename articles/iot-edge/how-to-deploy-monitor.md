---
title: Implementar módulos à escala no portal Azure - Azure IoT Edge
description: Utilize o portal Azure para criar implementações automáticas para grupos de dispositivos IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271437"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementar e monitorizar módulos IoT Edge em escala utilizando o portal Azure

Crie uma **implementação automática IoT Edge** no portal Azure para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para IoT Edge fazem parte da funcionalidade [de gestão automática](/azure/iot-hub/iot-hub-automatic-device-management) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implementar vários módulos em vários dispositivos, rastrear o estado e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [as implementações automáticas De IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando tags

Antes de poder criar uma implementação, tem de ser capaz de especificar quais os dispositivos que pretende afetar. O Azure IoT Edge identifica dispositivos que utilizam **etiquetas** no dispositivo twin. Cada dispositivo pode ter várias tags que define de qualquer forma que faça sentido para a sua solução.

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

## <a name="create-a-deployment"></a>Criar uma implantação

O IoT Edge fornece dois tipos diferentes de implementações automáticas que pode usar para personalizar o seu cenário. Pode criar uma *implementação*padrão, que inclui módulos de tempo de funcionamento do sistema e quaisquer módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implementação. Ou pode criar uma *implementação em camadas,* que inclui apenas módulos e rotas personalizadas, e não o tempo de funcionamento do sistema. Muitas implementações em camadas podem ser combinadas num dispositivo, em cima de uma implementação padrão. Para obter mais informações sobre como os dois tipos de implementações automáticas funcionam em conjunto, consulte [as implementações automáticas De Entendada IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

Os passos para criar uma implantação e uma implantação em camadas são muito semelhantes. Quaisquer diferenças são chamadas nos seguintes passos.

1. No [portal Azure,](https://portal.azure.com)vá ao seu Hub IoT.
1. No menu no painel esquerdo, selecione **IoT Edge** sob **gestão automática**de dispositivos .
1. Na barra superior, selecione **Criar implantação** ou **criar implantação em camadas**.

Há cinco passos para criar uma implantação. As seguintes secções passam por cada uma.

### <a name="step-1-name-and-label"></a>Passo 1: Nome e etiqueta

1. Dê à sua implantação um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
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

Para adicionar código personalizado como módulo, ou para adicionar manualmente um módulo de serviço Azure, siga estes passos:

1. Na secção credenciais de registo de **contentores** da página, forneça os nomes e credenciais para quaisquer registos de contentores privados que contenham as imagens do módulo para esta implementação. O Agente IoT Edge reportará o erro 500 se não encontrar a credencial do registo de contentores para uma imagem do Docker.
1. Na secção **Módulos IoT Edge** da página, clique em **Adicionar**.
1. Selecione **Módulo IoT Edge** a partir do menu suspenso.
1. Dê ao seu módulo um nome de **módulo IoT Edge**.
1. Para o campo **Image URI,** introduza a imagem do recipiente para o seu módulo.
1. Utilize o menu suspenso para selecionar uma política de **reiniciar**. Pode escolher uma das seguintes opções:
   * **sempre** - O módulo reinicia sempre se desligar por qualquer motivo.
   * **nunca** - O módulo nunca reinicia se desligar por qualquer motivo.
   * **on-failure** - O módulo reinicia se se despenhar, mas não se desligar de forma limpa.
   * **on-unhealthy** - O módulo reinicia se colidir ou devolver um estado pouco saudável. Cabe a cada módulo implementar a função de estado de saúde.
1. Utilize o menu suspenso para selecionar o **Estado Desejado** para o módulo. Pode escolher uma das seguintes opções:
   * **execução** - Correr é a opção padrão. O módulo começará a funcionar imediatamente após a sua implantação.
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

Se estiver a criar uma implementação em camadas, poderá estar a configurar um módulo que existe noutras implementações visando os mesmos dispositivos. Para atualizar o módulo twin sem sobrepor outras versões, abra o separador **Definições Twin Module.** Crie um novo `properties.desired.settings`Módulo Twin **Property** com um nome único para uma subsecção dentro das propriedades desejadas pelo módulo twin, por exemplo. Se definir propriedades dentro `properties.desired` do campo, irá substituir as propriedades desejadas para o módulo definido em quaisquer implementações prioritárias mais baixas.

![Definir propriedade twin módulo para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo twin em implementações em camadas, consulte a [implementação em camadas](module-deployment-monitoring.md#layered-deployment).

Uma vez configurado todos os módulos para uma implementação, selecione **Next: Routes** to move to step three.

### <a name="step-3-routes"></a>Passo 3: Rotas

As rotas definem como os módulos comunicam entre si dentro de uma implantação. Por predefinição, o assistente dá-lhe uma rota chamada **a montante** e definida como **FROM /messages/\* INTO $upstream,** o que significa que qualquer saída de mensagens por quaisquer módulos é enviada para o seu hub IoT.  

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

Utilize a propriedade de tags dos seus dispositivos para direcionar os dispositivos específicos que devem receber esta implementação.

Uma vez que várias implementações podem visar o mesmo dispositivo, deve dar a cada implementação um número prioritário. Se houver um conflito, o destacamento com a maior prioridade (valores maiores indicam maior prioridade) vence. Se duas implementações têm o mesmo número prioritário, a que foi criada mais recentemente ganha.

Se várias implementações visarem o mesmo dispositivo, então apenas é aplicada a única com maior prioridade. Se várias implementações em camadas atingirem o mesmo dispositivo, então todas são aplicadas. No entanto, se alguma propriedade for duplicada, como se houvesse duas rotas com o mesmo nome, então a da maior prioridade de implantação em camadas substitui o resto.

Qualquer implantação em camadas que direcione um dispositivo deve ter uma prioridade maior do que a implantação da base para ser aplicada.

1. Introduza um inteiro positivo para a **Prioridade**de implantação.
1. Introduza uma **condição de destino** para determinar quais os dispositivos que serão alvo com esta implementação.A condição baseia-se em etiquetas duplas do dispositivo ou propriedades reportadas de dispositivos twin e deve corresponder ao formato de expressão.Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **Seguinte: Rever + Criar** para passar ao passo final.

### <a name="step-6-review-and-create"></a>Passo 6: Rever e criar

Reveja as informações de implementação e, em seguida, selecione **Criar**.

## <a name="monitor-a-deployment"></a>Monitorizar uma implantação

Para ver os detalhes de uma implementação e monitorizar os dispositivos que a executam, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione o separador de **implementações IoT Edge.**

   ![Ver implementações ioT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de destacamento.Para cada implementação, pode ver os seguintes detalhes:
   * **ID** - o nome da implantação.
   * **Tipo** - o tipo de implantação, seja **implantação** ou **implantação em camadas**.
   * **Condição do alvo** - a etiqueta utilizada para definir dispositivos-alvo.
   * **Prioridade** - o número prioritário atribuído à implantação.
   * **As métricas** - do sistema**Direcionadas** especifica o número de gémeos dispositivos no IoT Hub que correspondem à condição de alvo, e **a Applied** especifica o número de dispositivos que tiveram o conteúdo de implementação aplicado aos seus gémeos módulos no IoT Hub.
   * **Métricas** do dispositivo - o número de dispositivos IoT Edge no relatório de implementação do sucesso ou erros do tempo de execução do cliente IoT Edge.
   * **Métricas Personalizadas** - o número de dispositivos IoT Edge nos dados de relatóriode implementação para quaisquer métricas que definiu para a implementação.
   * Tempo de **Criação** - o carimbo de tempo a partir de quando a implantação foi criada. Este carimbo de tempo é usado para quebrar laços quando duas implementações têm a mesma prioridade.
1. Selecione a implementação que pretende monitorizar.  
1. Inspecione os detalhes da implantação. Pode utilizar separadores para rever os detalhes da implementação.

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando modifica uma implementação, as alterações replicam-se imediatamente em todos os dispositivos visados.

Se atualizar a condição-alvo, ocorrem as seguintes atualizações:

* Se um dispositivo não cumprir a condição de destino antiga, mas cumprir a nova condição-alvo e esta implementação for a maior prioridade para esse dispositivo, então esta implementação é aplicada ao dispositivo.
* Se um dispositivo atualmente em execução desta implementação já não cumprir a condição-alvo, desinstala esta implementação e assume a próxima maior implementação prioritária.
* Se um dispositivo que está atualmente a executar esta implementação já não cumprir a condição-alvo e não cumprir o estado-alvo de quaisquer outras implementações, então não ocorre qualquer alteração no dispositivo. O dispositivo continua a executar os seus módulos atuais no seu estado atual, mas já não é gerido como parte desta implementação. Uma vez que cumpre a condição-alvo de qualquer outra implementação, desinstala esta implementação e assume a nova.

Para modificar uma implementação, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione o separador **IoT Edge Deployments.**

   ![Ver implementações ioT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implementação que pretende modificar.
1. Faça atualizações sobre os seguintes separadores:
   * **Condição-alvo**
   * **Métricas** - pode modificar ou eliminar métricas que definiu ou adicionar novas.
   * **Etiquetas**
   * **Módulos**
   * **Rotas**
   * **Implantação**

1. Selecione **Guardar**.
1. Siga os passos no [Monitor uma implementação](#monitor-a-deployment) para ver as alterações a serem lançadas.

## <a name="delete-a-deployment"></a>Eliminar uma implantação

Ao eliminar uma implementação, quaisquer dispositivos implantados assumem a sua próxima implementação de prioridade máxima. Se os seus dispositivos não cumprirem o estado-alvo de qualquer outra implementação, os módulos não são removidos quando a implementação for eliminada.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione o separador **IoT Edge Deployments.**

   ![Ver implementações ioT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Utilize a caixa de verificação para selecionar a implementação que pretende eliminar.
1. Selecione **Eliminar**.
1. Um pedido irá informá-lo de que esta ação irá eliminar esta implementação e reverter para o estado anterior para todos os dispositivos.Isto significa que será aplicável um destacamento com uma prioridade mais baixa.Se nenhuma outra implementação for direcionada, nenhum módulo será removido. Se pretender remover todos os módulos do seu dispositivo, crie uma implementação com zero módulos e implemente-os para os mesmos dispositivos.Selecione **Sim** para continuar.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).
