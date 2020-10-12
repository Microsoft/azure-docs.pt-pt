---
title: Implementar módulos em escala no portal Azure - Azure IoT Edge
description: Utilize o portal Azure para criar implementações automáticas para grupos de dispositivos IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0c1d83c2dac0163cd9b9cbc07969103381e85471
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855380"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementar módulos IoT Edge em escala utilizando o portal Azure

Crie uma **implementação automática IoT Edge** no portal Azure para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para ioT Edge fazem parte da funcionalidade automática de [gestão](/azure/iot-hub/iot-hub-automatic-device-management) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o estado e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [as configurações automáticas Understand IoT Edge para dispositivos individuais ou à escala.](module-deployment-monitoring.md)

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando tags

Antes de poder criar uma implementação, tem de ser capaz de especificar quais os dispositivos que pretende afetar. A Azure IoT Edge identifica dispositivos que usam **etiquetas** no dispositivo twin. Cada dispositivo pode ter várias tags que define de qualquer forma que faça sentido para a sua solução.

Por exemplo, se você gere um campus de edifícios inteligentes, você pode adicionar localização, tipo de quarto e etiquetas de ambiente a um dispositivo:

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

Para obter mais informações sobre os gémeos e tags do dispositivo, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Create a deployment (Criar uma implementação)

O IoT Edge fornece dois tipos diferentes de implementações automáticas que pode utilizar para personalizar o seu cenário. Pode criar uma *implementação*padrão, que inclui os módulos de tempo de execução do sistema e quaisquer módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implantação. Ou pode criar uma *implementação em camadas*, que inclui apenas módulos e rotas personalizados, e não o tempo de funcionamento do sistema. Muitas implementações em camadas podem ser combinadas num dispositivo, em cima de uma implementação padrão. Para obter mais informações sobre como os dois tipos de implementações automáticas funcionam em conjunto, consulte [as implementações automáticas Understand IoT Edge para dispositivos individuais ou à escala](module-deployment-monitoring.md).

Os passos para criar uma implantação e uma implantação em camadas são muito semelhantes. Quaisquer diferenças são convocadas nos seguintes passos.

1. No [portal Azure,](https://portal.azure.com)vá ao seu IoT Hub.
1. No menu do painel esquerdo, selecione **IoT Edge** em **Gestão Automática de Dispositivos**.
1. Na barra superior, selecione **Criar Implementação** ou **Criar Implementação em camadas**.

Há cinco passos para criar uma implantação. As seguintes secções atravessam cada uma delas.

### <a name="step-1-name-and-label"></a>Passo 1: Nome e etiqueta

1. Dê à sua implantação um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /` .
1. Pode adicionar etiquetas como pares de valor-chave para ajudar a rastrear as suas implementações. Por exemplo, **HostPlatform** e **Linux,** ou **Versão** e **3.0.1**.
1. Selecione **Seguinte: Módulos** para mover-se para o segundo passo.

### <a name="step-2-modules"></a>Passo 2: Módulos

Pode adicionar até 50 módulos a uma implantação. Se criar uma implantação sem módulos, remove quaisquer módulos atuais dos dispositivos-alvo.

Nas implementações, pode gerir as definições para os módulos IoT Edge e IoT Edge hub. Selecione **Definições de tempo de** execução para configurar os dois módulos de tempo de execução. Na implementação em camadas, os módulos de tempo de execução não estão incluídos, pelo que não podem ser configurados.

Pode adicionar três tipos de módulos:

* Módulo IoT Edge
* Módulo marketplace
* Módulo Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Adicione um módulo IoT Edge

Para adicionar código personalizado como módulo, ou para adicionar manualmente um módulo de serviço Azure, siga estes passos:

1. Na secção **de Credenciais** de Registo de Contentores da página, forneça os nomes e credenciais para quaisquer registos privados de contentores que contenham as imagens do módulo para esta implantação. O Agente IoT Edge reportará o erro 500 se não encontrar a credencial de registo de contentores para uma imagem do Docker.
1. Na secção **módulos IoT Edge modules** da página, clique em **Adicionar**.
1. Selecione **o Módulo IoT Edge** do menu suspenso.
1. Dê ao seu módulo um **nome de módulo IoT Edge**.
1. Para o campo **Image URI,** introduza a imagem do recipiente para o seu módulo.
1. Utilize o menu suspenso para selecionar uma **política de Reinício**. Pode escolher uma das seguintes opções:
   * **sempre** - O módulo reinicia sempre se desligar por qualquer motivo.
   * **nunca** - O módulo nunca reinicia se desligar por qualquer motivo.
   * **falha -** O módulo reinicia se falhar, mas não se desligar limpo.
   * **on-unhealthy** - O módulo reinicia se falhar ou devolver um estado pouco saudável. Cabe a cada módulo implementar a função de estado de saúde.
1. Utilize o menu suspenso para selecionar o **Estado Desejado** para o módulo. Pode escolher uma das seguintes opções:
   * **executar** - Correr é a opção predefinida. O módulo começará a funcionar imediatamente após ser implantado.
   * **parado** - Depois de ser implantado, o módulo permanecerá inativo até ser chamado a arrancar por si ou por outro módulo.
1. Especifique quaisquer **Opções de Criação de Recipiente** que devem ser passadas para o recipiente. Para mais informações, consulte [o estivador criar.](https://docs.docker.com/engine/reference/commandline/create/)
1. Selecione **Configurações Twin do Módulo** se pretender adicionar tags ou outras propriedades ao módulo twin.
1. Introduza **Variáveis ambientais** para este módulo. As variáveis ambientais fornecem informações de configuração a um módulo.
1. **Selecione Adicionar** o seu módulo à implementação.

#### <a name="add-a-module-from-the-marketplace"></a>Adicione um módulo do Marketplace

Para adicionar um módulo do Mercado Azure, siga estes passos:

1. Na secção **módulos IoT Edge modules** da página, clique em **Adicionar**.
1. Selecione **o Módulo de Mercado** a partir do menu suspenso.
1. Escolha um módulo na página **IoT Edge Module Marketplace.** O módulo que seleciona está automaticamente configurado para a sua subscrição, grupo de recursos e dispositivo. Em seguida, aparece na sua lista de módulos IoT Edge. Alguns módulos podem necessitar de configuração adicional. Para mais informações, consulte [os módulos Deploy from Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Adicione um módulo Stream Analytics

Para adicionar um módulo da Azure Stream Analytics, siga estes passos:

1. Na secção **módulos IoT Edge modules** da página, clique em **Adicionar**.
1. Selecione **o módulo Azure Stream Analytics** a partir do menu suspenso.
1. No painel direito, escolha a sua **Assinatura.**
1. Escolha o seu trabalho IoT **Edge**.
1. **Selecione Guardar** para adicionar o seu módulo à implementação.

#### <a name="configure-module-settings"></a>Configurar configurações de módulos

Depois de adicionar um módulo a uma implementação, pode selecionar o seu nome para abrir a página **do Módulo de Borda IoT de Atualização.** Nesta página, pode editar as definições do módulo, variáveis ambientais, criar opções e módulo twin. Se adicionar um módulo do mercado, pode já ter alguns destes parâmetros preenchidos.

Se estiver a criar uma implementação em camadas, poderá estar a configurar um módulo que existe noutras implementações que visem os mesmos dispositivos. Para atualizar o módulo twin sem sobrepor outras versões, abra o **separador Definições Twin Module.** Crie uma nova **Propriedade Twin Module** com um nome único para uma subsecção dentro das propriedades desejadas pelo módulo twin, por `properties.desired.settings` exemplo. Se definir propriedades dentro apenas do `properties.desired` campo, substituirá as propriedades desejadas para o módulo definido em quaisquer implementações de menor prioridade.

![Definir propriedade twin módulo para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo twin em implementações em camadas, consulte [a implementação em camadas](module-deployment-monitoring.md#layered-deployment).

Uma vez que tenha todos os módulos para uma implementação configurada, selecione **Seguinte: Rotas** para passar para o passo três.

### <a name="step-3-routes"></a>Passo 3: Rotas

As rotas definem como os módulos comunicam entre si dentro de uma implantação. Por predefinição, o assistente dá-lhe uma rota chamada **a montante** e definida como **FROM /messages/ \* INTO $upstream**, o que significa que qualquer saída de mensagens por quaisquer módulos é enviada para o seu hub IoT.  

Adicione ou atualize as rotas com informações das [rotas Declara,](module-composition.md#declare-routes)em seguida, selecione **Seguinte** para continuar na secção de revisão.

Selecione **Seguinte: Métricas**.

### <a name="step-4-metrics"></a>Passo 4: Métricas

As métricas fornecem contagens sumárias dos vários estados que um dispositivo pode reportar como resultado da aplicação do conteúdo de configuração.

1. Insira um nome para **Nome Métrico**.

1. Introduza uma consulta para **critérios métricos**. A consulta baseia-se em [propriedades reportadas](module-edgeagent-edgehub.md#edgehub-reported-properties)do módulo hub IoT Edge twin . A métrica representa o número de linhas devolvidas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecione **Seguinte: Dispositivos-alvo**.

### <a name="step-5-target-devices"></a>Passo 5: Dispositivos-alvo

Utilize a propriedade tags dos seus dispositivos para direcionar os dispositivos específicos que devem receber esta implementação.

Uma vez que várias implementações podem visar o mesmo dispositivo, deve dar a cada implementação um número prioritário. Se houver algum conflito, a implantação com maior prioridade (valores maiores indicam maior prioridade) ganha. Se duas implementações têm o mesmo número de prioridade, a que foi criada mais recentemente ganha.

Se várias implementações direcionarem o mesmo dispositivo, apenas o que tem a maior prioridade é aplicado. Se várias implementações em camadas direcionar o mesmo dispositivo, então todas são aplicadas. No entanto, se algumas propriedades forem duplicadas, como se houver duas rotas com o mesmo nome, então a da maior prioridade de implantação em camadas substitui o resto.

Qualquer implantação em camadas dirigida a um dispositivo deve ter uma prioridade maior do que a implantação da base para ser aplicada.

1. Introduza um número inteiro positivo para a **prioridade**de implantação .
1. Introduza uma **condição de Destino** para determinar quais os dispositivos que serão alvo com esta implementação.A condição baseia-se em etiquetas gémeas do dispositivo ou no dispositivo que as propriedades reportadas por gémeos e devem corresponder ao formato de expressão.Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **Seguinte: Revisão + Criar** para passar ao passo final.

### <a name="step-6-review-and-create"></a>Passo 6: Rever e criar

Reveja as suas informações de implementação e, em seguida, **selecione Criar**.

Para monitorizar a sua implementação, consulte [as implementações do Monitor IoT Edge](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando modificar uma implementação, as alterações replicam-se imediatamente em todos os dispositivos direcionados. Pode modificar as seguintes definições e funcionalidades para uma implementação existente:

* Condições-alvo
* Métricas personalizadas
* Etiquetas
* Etiquetas
* Propriedades desejadas

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Modificar as condições-alvo, métricas personalizadas e etiquetas

1. No seu hub IoT, selecione **IoT Edge** a partir do menu do painel esquerdo.
1. Selecione o **separador IoT Edge implementações** e, em seguida, selecione a implementação que pretende configurar.
1. Selecione o **separador 'Estado-alvo'.** Altere a **Condição alvo** para direcionar os dispositivos pretendidos. Também pode ajustar a **Prioridade**.  Selecione **Guardar**.

    Se atualizar a condição do alvo, ocorrem as seguintes atualizações:

    * Se um dispositivo não satisfaça a antiga condição alvo, mas cumpre a nova condição alvo e esta implementação é a maior prioridade para esse dispositivo, então esta implementação é aplicada ao dispositivo.
    * Se um dispositivo atualmente em execução esta implementação deixar de cumprir a condição alvo, desinstala esta implementação e assume a próxima maior prioridade de implantação.
    * Se um dispositivo atualmente em execução esta implementação deixar de cumprir a condição alvo e não cumprir a condição alvo de quaisquer outras implementações, então não ocorre qualquer alteração no dispositivo. O dispositivo continua a executar os seus módulos atuais no seu estado atual, mas já não é gerido como parte desta implementação. Uma vez que satisfaça a condição alvo de qualquer outra implementação, desinstala esta implementação e assume a nova.

1. Selecione o separador **Métricas** e clique no botão **EditAr Métricas.** Adicione ou modifique métricas personalizadas, utilizando a sintaxe de exemplo como guia. Selecione **Guardar**.

    ![Editar métricas personalizadas numa implementação](./media/how-to-deploy-monitor/metric-list.png)

1. Selecione o **separador Etiquetas** e faça quaisquer alterações desejadas e selecione **Guardar**.

## <a name="delete-a-deployment"></a>Eliminar uma implantação

Quando elimina uma implantação, qualquer dispositivo implantado assume a sua próxima maior prioridade de implantação. Se os seus dispositivos não cumprirem a condição alvo de qualquer outra implantação, os módulos não são removidos quando a implementação é eliminada.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione o **separador IoT Edge Deployments.**

   ![Ver implementações IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Utilize a caixa de verificação para selecionar a implementação que pretende eliminar.
1. Selecione **Eliminar**.
1. Um aviso irá informá-lo que esta ação irá eliminar esta implementação e reverter para o estado anterior para todos os dispositivos.Será aplicada uma implantação com uma prioridade mais baixa.Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se pretender remover todos os módulos do seu dispositivo, crie uma implementação com zero módulos e coloque-o nos mesmos dispositivos.Selecione **Sim** para continuar.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).
