---
title: Implementar módulos em escala no portal Azure - Azure IoT Edge
description: Utilize o portal Azure para criar implementações automáticas para grupos de dispositivos IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: db27a466ca5f1370e8b43ceb472f5deeaba509f1
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200323"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implementar módulos IoT Edge em escala utilizando o portal Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Crie uma **implementação automática IoT Edge** no portal Azure para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para ioT Edge fazem parte da funcionalidade automática de [gestão](../iot-hub/iot-hub-automatic-device-management.md) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o estado e a saúde dos módulos e fazer alterações quando necessário.

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

O IoT Edge fornece dois tipos diferentes de implementações automáticas que pode utilizar para personalizar o seu cenário. Pode criar uma *implementação* padrão, que inclui os módulos de tempo de execução do sistema e quaisquer módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implantação. Ou pode criar uma *implementação em camadas*, que inclui apenas módulos e rotas personalizados, e não o tempo de funcionamento do sistema. Muitas implementações em camadas podem ser combinadas num dispositivo, em cima de uma implementação padrão. Para obter mais informações sobre como os dois tipos de implementações automáticas funcionam em conjunto, consulte [as implementações automáticas Understand IoT Edge para dispositivos individuais ou à escala](module-deployment-monitoring.md).

Os passos para criar uma implantação e uma implantação em camadas são muito semelhantes. Quaisquer diferenças são convocadas nos seguintes passos.

1. No [portal Azure,](https://portal.azure.com)vá ao seu IoT Hub.
1. No menu do painel esquerdo, selecione **IoT Edge** em **Gestão Automática de Dispositivos**.
1. Na barra superior, selecione **Criar Implementação** ou **Criar Implementação em camadas**.

Há cinco passos para criar uma implantação. As seguintes secções atravessam cada uma delas.

>[!NOTE]
>Os passos deste artigo refletem a versão mais recente do esquema do agente e hub IoT Edge. A versão 1.1 do Schema foi lançada juntamente com a versão 1.0.10 do IoT Edge e permite a ordem de arranque do módulo e as funcionalidades de priorização da rota.
>
>Se estiver a ser implantado para um dispositivo que executa a versão 1.0.9 ou mais cedo, edite as **Definições de Tempo** de Execução no passo dos **Módulos** do assistente para utilizar a versão 1.0 do esquema.

### <a name="step-1-name-and-label"></a>Passo 1: Nome e etiqueta

1. Dê à sua implantação um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /` .
1. Pode adicionar etiquetas como pares de valor-chave para ajudar a rastrear as suas implementações. Por exemplo, **HostPlatform** e **Linux,** ou **Versão** e **3.0.1**.
1. Selecione **Seguinte: Módulos** para mover-se para o segundo passo.

### <a name="step-2-modules"></a>Passo 2: Módulos

Pode adicionar até 50 módulos a uma implantação. Se criar uma implantação sem módulos, remove quaisquer módulos atuais dos dispositivos-alvo.

Nas implementações, pode gerir as definições para os módulos IoT Edge e IoT Edge hub. Selecione **Definições de tempo de** execução para configurar os dois módulos de tempo de execução. Na implementação em camadas, os módulos de tempo de execução não estão incluídos, pelo que não podem ser configurados.

Para adicionar código personalizado como módulo, ou para adicionar manualmente um módulo de serviço Azure, siga estes passos:

1. Na secção **de Definições** de Registo de Contentores da página, forneça as credenciais para aceder a quaisquer registos privados de contentores que contenham as imagens do módulo.
1. Na secção **módulos IoT Edge modules** da página, selecione **Adicionar**.
1. Escolha um dos três tipos de módulos do menu suspenso:

   * **Módulo IoT Edge** - Fornece o nome do módulo e a imagem do recipiente URI. Por exemplo, a imagem URI para o módulo Simulado DesteperatureSensor é `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Se a imagem do módulo for armazenada num registo de contentores privados, adicione as credenciais nesta página para aceder à imagem.
   * **Módulo marketplace** - Módulos alojados no Mercado Azure. Alguns módulos de marketplace requerem configuração adicional, por isso reveja os detalhes do módulo na lista de [Módulos de Borda IoT do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Módulo Azure Stream Analytics** - Módulos gerados a partir de uma carga de trabalho Azure Stream Analytics.

1. Se necessário, repita os passos 2 e 3 para adicionar módulos adicionais à sua implantação.

Depois de adicionar um módulo a uma implementação, pode selecionar o seu nome para abrir a página **do Módulo de Borda IoT de Atualização.** Nesta página, pode editar as definições do módulo, variáveis ambientais, criar opções, ordem de arranque e módulo twin. Se adicionar um módulo do mercado, pode já ter alguns destes parâmetros preenchidos. Para obter mais informações sobre as definições e regulação do módulo disponíveis, consulte [a configuração e gestão do Módulo.](module-composition.md#module-configuration-and-management)

Se estiver a criar uma implementação em camadas, poderá estar a configurar um módulo que existe noutras implementações que visem os mesmos dispositivos. Para atualizar o módulo twin sem sobrepor outras versões, abra o **separador Definições Twin Module.** Crie uma nova **Propriedade Twin Module** com um nome único para uma subsecção dentro das propriedades desejadas pelo módulo twin, por `properties.desired.settings` exemplo. Se definir propriedades dentro apenas do `properties.desired` campo, substituirá as propriedades desejadas para o módulo definido em quaisquer implementações de menor prioridade.

![Definir propriedade twin módulo para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo twin em implementações em camadas, consulte [a implementação em camadas](module-deployment-monitoring.md#layered-deployment).

Uma vez que tenha todos os módulos para uma implementação configurada, selecione **Seguinte: Rotas** para passar para o passo três.

### <a name="step-3-routes"></a>Passo 3: Rotas

No separador **Rotas,** define como as mensagens são passadas entre os módulos e o Hub IoT. As mensagens são construídas utilizando pares de nome/valor.

Por exemplo, uma rota com uma **rota** de nome e um valor **FROM /messages/ \* INTO $upstream** levaria qualquer saída de mensagens por quaisquer módulos e enviava-as para o seu hub IoT.  

Os parâmetros **Prioritários** e **Tempo para viver** são parâmetros opcionais que pode incluir numa definição de rota. O parâmetro prioritário permite-lhe escolher quais as rotas que devem ter as suas mensagens processadas primeiro, ou quais as rotas que devem ser processadas em último lugar. A prioridade é determinada pela definição de um número 0-9, onde 0 é prioridade máxima. O parâmetro de tempo para viver permite-lhe declarar quanto tempo as mensagens nessa rota devem ser mantidas até que sejam processadas ou removidas da fila.

Para obter mais informações sobre como criar rotas, consulte [rotas de declarações.](module-composition.md#declare-routes)

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

1. Introduza um número inteiro positivo para a **prioridade** de implantação .
1. Introduza uma **condição de Destino** para determinar quais os dispositivos que serão alvo com esta implementação. A condição baseia-se em etiquetas gémeas do dispositivo ou no dispositivo que as propriedades reportadas por gémeos e devem corresponder ao formato de expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

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
1. Um aviso irá informá-lo que esta ação irá eliminar esta implementação e reverter para o estado anterior para todos os dispositivos. Será aplicada uma implantação com uma prioridade mais baixa. Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se pretender remover todos os módulos do seu dispositivo, crie uma implementação com zero módulos e coloque-o nos mesmos dispositivos. Selecione **Sim** para continuar.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).