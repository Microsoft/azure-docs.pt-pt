---
title: incluir ficheiro
description: incluir ficheiro
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9618dc1cef8d04cc5906579af0ef372694aeaaa9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673986"
---
## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

Crie uma `environmental-sensor` pasta chamada na sua máquina local.

Descarregue o ficheiro JSON da capacidade `environmental-sensor` do sensor [ambiental](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) e guarde-o na pasta.

Utilize um editor de texto para `{YOUR_COMPANY_NAME_HERE}` substituir as duas `EnvironmentalSensorInline.capabilitymodel.json` instâncias do nome da sua empresa no ficheiro que descarregou.

Na sua aplicação Azure IoT Central, crie um `EnvironmentalSensorInline.capabilitymodel.json` modelo de dispositivo chamado sensor *ambiental* importando o ficheiro do modelo de capacidade do dispositivo:

![Modelo de dispositivo com modelo de capacidade de dispositivo importado](./media/iot-central-add-environmental-sensor/device-template.png)

O modelo de capacidade do dispositivo inclui duas interfaces: a interface padrão de **informação** do dispositivo e a interface personalizada do **Sensor Ambiental.** A interface **Sensor Ambiental** define as seguintes capacidades:

| Tipo | Nome a Apresentar | Descrição |
| ---- | ------------ | ----------- |
| Propriedade | Estado do Dispositivo     | O estado do dispositivo. Dois estados online/offline estão disponíveis. |
| Propriedade (rectilável) | Nome do cliente    | O nome do cliente que está a operar o dispositivo. |
| Propriedade (rectilável) | Nível de brilho | O nível de luminosidade para a luz no dispositivo. Pode ser especificado como 1 (alto), 2 (médio), 3 (baixo). |
| Telemetria | Temperatura | Temperatura atual detetada pelo dispositivo. |
| Telemetria | Humidade    | Humidade atual detetada pelo dispositivo. |
| Comando | piscar          | Comece a piscar o LED no dispositivo para um determinado intervalo de tempo. |
| Comando | turnon         | Ligue o LED no dispositivo. |
| Comando | desvio        | Desligue o LED do dispositivo. |
| Comando | diagnósticos de execução | Este comando assíncrono inicia uma execução de diagnóstico no dispositivo. |

Para personalizar a forma como a propriedade **do Estado** do Dispositivo exibe na sua aplicação IoT Central, selecione **Personalizar** no modelo do dispositivo. Expandir a entrada do Estado do **Dispositivo,** introduzir _online_ como nome **verdadeiro** e _offline_ como o **nome falso**. Em seguida, guarde as alterações:

![Personalize o modelo do dispositivo](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Criar vistas

As vistas permitem-lhe interagir com dispositivos ligados à sua aplicação IoT Central. Por exemplo, você pode ter vistas que exibem telemetria, vistas que exibem propriedades e vistas que permitem editar propriedades reempreensíveis e cloud. As vistas fazem parte de um modelo de dispositivo.

Para adicionar algumas vistas predefinidas ao seu modelo de **dispositivo de sensor Ambiental,** navegue para o seu modelo de dispositivo, selecione **Views**, e selecione o azulejo de **vistas Depreto sê-lo.** Certifique-se de que a **visão geral** e **sobre** estão **ligados**, e, em seguida, selecione **Generate 's) predefinido vista do dashboard (s)**. Tem agora duas visões predefinidas definidas no seu modelo.

A interface **sensor ambiental** inclui duas propriedades reempreensíveis - Nome **do Cliente** e **Nível de Brilho**. Para criar uma vista, pode usar para editar estas propriedades:

1. Selecione **Views** e, em seguida, selecione o **dispositivo de edição e** o azulejo de dados em nuvem.

1. _Insira propriedades_ como nome de formulário.

1. Selecione as propriedades **do Nível de Luminosidade** e do Nome do **Cliente.** Em seguida, selecione **a secção Adicionar**.

1. Guarde as alterações.

![Adicione uma vista para permitir a edição de propriedades](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publicar o modelo

Antes de poder adicionar um dispositivo que utilize o modelo do dispositivo de **sensor Ambiental,** deve publicá-lo.

No modelo do dispositivo, **selecione Publicar**. No publicar este modelo de dispositivo para o painel de **aplicações,** selecione **Publicar**.

Para verificar se o modelo está pronto a ser utilizado, navegue para a página **dispositivos** na sua aplicação IoT Central. A secção **Dispositivos** mostra uma lista dos dispositivos publicados na aplicação:

![Modelos publicados na página de dispositivos](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na sua aplicação Azure IoT Central, adicione um dispositivo real ao modelo de dispositivo que criou na secção anterior:

1. Na página **Dispositivos,** selecione o modelo do dispositivo **de sensor ambiental.**

    > [!TIP]
    > Certifique-se de selecionar o modelo a utilizar antes de selecionar **+ Novo,** caso contrário criará um dispositivo não associado.

1. Selecione **+ Novo**.

1. Certifique-se de que a **Simulação** está **desligada**. Em seguida, selecione **Criar**.

Clique no nome do dispositivo e, em seguida, selecione **Connect**. Tome nota das informações de ligação do dispositivo na página de **Ligação** do Dispositivo - **âmbito de identificação,** **ID do dispositivo**e chave **primária**. Precisa destes valores quando cria o código do dispositivo:

![Informações de ligação ao dispositivo](./media/iot-central-add-environmental-sensor/device-connection.png)
