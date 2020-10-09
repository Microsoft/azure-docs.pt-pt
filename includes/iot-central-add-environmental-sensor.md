---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 07/07/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b32465091f82fec0aeae288ee9bfd5540bfe8b9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87002051"
---
## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

Crie uma pasta chamada `environmental-sensor` na sua máquina local.

Descarregue o ficheiro JSON [do modelo de capacidade do sensor ambiental](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) e guarde-o na `environmental-sensor` pasta.

Utilize um editor de texto para substituir as duas instâncias do nome da `{YOUR_COMPANY_NAME_HERE}` sua empresa no ficheiro que `EnvironmentalSensorInline.capabilitymodel.json` descarregou. Use apenas os caracteres a-z, A-Z, 0-9, e subsuse.

Na sua aplicação Azure IoT Central, crie um modelo de dispositivo chamado *sensor ambiental* importando o ficheiro do modelo de capacidade do `EnvironmentalSensorInline.capabilitymodel.json` dispositivo:

![Modelo de dispositivo com modelo de capacidade de dispositivo importado](./media/iot-central-add-environmental-sensor/device-template.png)

O modelo de capacidade do dispositivo inclui duas interfaces: a interface padrão **de Informação do Dispositivo** e a interface personalizada do Sensor **Ambiental.** A interface **do Sensor Ambiental** define as seguintes capacidades:

| Tipo | Nome a Apresentar | Descrição |
| ---- | ------------ | ----------- |
| Propriedade | Estado do Dispositivo     | O estado do dispositivo. Dois estados online/offline estão disponíveis. |
| Propriedade (depreciada) | Nome do Cliente    | O nome do cliente que está a operar o dispositivo. |
| Propriedade (depreciada) | Nível de brilho | O nível de luminosidade para a luz do dispositivo. Pode ser especificado como 1 (alto), 2 (médio), 3 (baixo). |
| Telemetria | Temperatura | Temperatura atual detetada pelo dispositivo. |
| Telemetria | Humidade    | Humidade atual detetada pelo dispositivo. |
| Comando | piscar          | Comece a piscar o LED no aparelho para um intervalo de tempo. |
| Comando | turnon         | Ligue o LED no aparelho. |
| Comando | desvio        | Desligue o LED do dispositivo. |
| Comando | rundiagnósticos | Este comando assíncronos inicia uma execução de diagnóstico no dispositivo. |

Para personalizar como a propriedade **do Estado** do Dispositivo aparece na sua aplicação IoT Central, selecione **Personalizar** no modelo do dispositivo. Expandir a entrada **do Estado do Dispositivo,** inserir _online_ como o **nome verdadeiro** e _offline_ como o **nome falso**. Em seguida, guarde as alterações:

![Personalize o modelo do dispositivo](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Criar vistas

As vistas permitem interagir com dispositivos ligados à sua aplicação IoT Central. Por exemplo, pode ter vistas que exibem telemetria, vistas que exibem propriedades e vistas que lhe permitem editar propriedades escritas e em nuvem. As vistas fazem parte de um modelo de dispositivo.

Para adicionar algumas vistas padrão ao modelo do dispositivo **do sensor ambiental,** navegue para o modelo do seu dispositivo, selecione **Vistas**e selecione o azulejo **de vistas padrão de Geração.** Certifique-se de que **a visão geral** e **sobre** estão on e, **em**seguida, selecione Gerar **a visão padrão do painel de instrumentos**. Tem agora duas visões padrão definidas no seu modelo.

A interface **do Sensor Ambiental** inclui duas propriedades escritas - Nome do **Cliente** e **Nível de Luminosidade.** Para criar uma vista, pode utilizar para editar estas propriedades:

1. Selecione **Vistas** e, em seguida, selecione o dispositivo de edição e o azulejo **de dados em nuvem.**

1. _Insira propriedades_ como o nome do formulário.

1. Selecione as propriedades **do Nível de Brilho** e nome do **cliente.** Em seguida, **selecione Secção adicionar**.

1. Guarde as alterações.

![Adicionar uma vista para permitir a edição de propriedade](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publicar o modelo

Antes de poder adicionar um dispositivo que utilize o modelo do dispositivo **sensor Ambiental,** tem de publicá-lo.

No modelo do dispositivo, **selecione Publicar**. No modelo publicar este dispositivo para o painel **de aplicações,** selecione **Publicar**.

Para verificar se o modelo está pronto a ser utilizado, navegue para a página **dispositivos** na sua aplicação IoT Central. A secção **dispositivos** mostra uma lista dos dispositivos publicados na aplicação:

![Modelos publicados na página de dispositivos](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na sua aplicação Azure IoT Central, adicione um dispositivo real ao modelo de dispositivo que criou na secção anterior:

1. Na página **Dispositivos,** selecione o modelo do dispositivo **do sensor ambiental.**

1. Selecione **+ Novo**.

1. No **Criar um novo** diálogo do dispositivo, certifique-se de que o **Sensor** Ambiental **No**é o tipo de modelo e que simula **este dispositivo?**

1. Em seguida, selecione **Criar**.

Clique no nome do dispositivo e, em seguida, selecione **Connect**. Tome nota das informações de ligação do dispositivo na página **de Ligação** do Dispositivo - **ID,** **ID**do dispositivo e **tecla principal**. Precisa destes valores quando cria o código do dispositivo:

![Informações da ligação do dispositivo](./media/iot-central-add-environmental-sensor/device-connection.png)
