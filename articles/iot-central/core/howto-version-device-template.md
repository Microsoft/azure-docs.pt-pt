---
title: Compreensão da versão do modelo do dispositivo para as suas aplicações Azure IoT Central [ Microsoft Docs
description: Iterar sobre os modelos do seu dispositivo criando novas versões e sem afetar os seus dispositivos conectados ao vivo
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 772521a8d3181721270d7fe4dbd11b7807c8d90e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583663"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão do modelo do dispositivo

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Um modelo de dispositivo inclui um esquema que descreve como um dispositivo interage com a IoT Central. Estas interações incluem telemetria, propriedades e comandos. Tanto o dispositivo como a aplicação IoT Central dependem de uma compreensão partilhada deste esquema para trocar informações. Só é possível fazer alterações limitadas no esquema sem quebrar o contrato, por isso a maioria das alterações de esquemas requerem uma nova versão do modelo do dispositivo. A versão do modelo do dispositivo permite que os dispositivos mais antigos continuem com a versão schema que compreendem, enquanto os dispositivos mais recentes ou atualizados utilizam uma versão mais recente do esquema.

O esquema num modelo de dispositivo é definido no modelo de capacidade do dispositivo (DCM) e nas suas interfaces. Os modelos de dispositivos incluem outras informações, tais como propriedades na nuvem, personalizações de exibição e vistas. Se fizer alterações nas partes do modelo do dispositivo que não definem como o dispositivo troca dados com a IoT Central, não precisa de ver o modelo.

Tem de publicar quaisquer alterações no modelo do dispositivo, quer necessitem ou não de uma atualização da versão, antes de um operador poder utilizá-lo. A IoT Central impede-o de publicar alterações de quebra num modelo de dispositivo sem primeiro verver o modelo.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo, consulte [Configurar e gerir um modelo](howto-set-up-template.md) de dispositivo

## <a name="versioning-rules"></a>Regras de versão

Esta secção resume as regras de versão aplicáveis aos modelos do dispositivo. Tanto os DCMs como as interfaces têm números de versão. O seguinte corte mostra o DCM para um dispositivo de sensor ambiental. O DCM tem duas interfaces: **DeviceInformation** e **EnvironmentalSensor**. Pode ver os números da`@id` versão no final dos campos. Para ver esta informação na IoT Central UI, selecione **Ver identidade** no editor de modelo de dispositivo.

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* Depois de publicado um DCM, não é possível remover nenhuma interface, mesmo numa nova versão do modelo do dispositivo.
* Depois de publicado um DCM, pode adicionar uma interface se criar uma nova versão do modelo do dispositivo.
* Depois de publicado um DCM, pode substituir uma interface por uma versão mais recente se criar uma nova versão do modelo do dispositivo. Por exemplo, se o modelo do dispositivo sensor v1 utilizar a interface EnvironmentalSensor v1, pode criar um modelo de dispositivo sensor v2 que utiliza a interface EnvironmentalSensor v2.
* Depois de publicada uma interface, não é possível remover nenhum dos conteúdos da interface, mesmo numa nova versão do modelo do dispositivo.
* Depois de publicada uma interface, pode adicionar itens ao conteúdo de uma interface se criar uma nova versão do modelo de interface e dispositivo. Itens que pode adicionar à interface incluem telemetria, propriedades e comandos.
* Depois de publicada uma interface, pode efazer alterações não-schema em itens existentes na interface se criar uma nova versão da interface e do modelo do dispositivo. Partes não-schema de um item de interface incluem o nome do visor e o tipo semântico. As partes de esquema de um item de interface que não pode alterar são nome, tipo de capacidade e esquema.

As seguintes secções passam por alguns exemplos de modificação de modelos de dispositivos em IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Personalize o modelo do dispositivo sem verversão

Certos elementos das capacidades do seu dispositivo podem ser editados sem necessidade de ver o modelo e interfaces do seu dispositivo. Por exemplo, alguns destes campos incluem nome de exibição, tipo semântico, valor mínimo, valor máximo, casas decimais, cor, unidade, unidade de exibição, comentários e descrição. Para adicionar uma destas personalizações:

1. Vá para a página modelos de **dispositivo.**
1. Selecione o modelo de dispositivo que pretende personalizar.
1. Escolha o separador **Personalizar.**
1. Todas as capacidades definidas no seu modelo de capacidade do dispositivo estão listadas aqui. Pode editar, guardar e utilizar todos estes campos sem a necessidade de ver o modelo do seu dispositivo. Se houver campos que deseje editar que sejam apenas de leitura, tem de ver o modelo do seu dispositivo para os alterar. Selecione um campo que deseje editar e introduzir em quaisquer novos valores.
1. Clique em **Guardar**. Agora estes valores sobrepõem tudo o que foi inicialmente guardado no modelo do seu dispositivo e são usados em toda a aplicação.

## <a name="version-a-device-template"></a>Versão um modelo de dispositivo

Criar uma nova versão do modelo do seu dispositivo cria uma versão de rascunho do modelo onde o modelo de capacidade do dispositivo pode ser editado. Quaisquer interfaces publicadas permanecem publicadas até serem versões individualmente. Para modificar uma interface publicada, crie primeiro uma nova versão do modelo do dispositivo.

Apenas versão do modelo do dispositivo quando está a tentar editar uma parte do modelo de capacidade do dispositivo que não pode editar na secção de personalização.

Para ver um modelo de dispositivo:

1. Vá para a página modelos de **dispositivo.**
1. Selecione o modelo do dispositivo que está a tentar ver.
1. Clique no botão **Versão** na parte superior da página e dê ao modelo um novo nome. A IoT Central sugere um novo nome, que pode editar.
1. Clique em **Criar**.
1. Agora o modelo do seu dispositivo está no modo de projeto. Pode ver que as suas interfaces ainda estão bloqueadas. Verifique quaisquer interfaces que queira modificar.

## <a name="version-an-interface"></a>Versão uma interface

A versão de uma interface permite-lhe adicionar, atualizar e remover as capacidades dentro da interface que já tinha criado.

Para ver uma interface:

1. Vá para a página modelos de **dispositivo.**
1. Selecione o modelo de dispositivo que tem num modo de projeto.
1. Selecione a interface que está no modo publicado que pretende ver e editar.
1. Clique no botão **Versão** na parte superior da página da interface.
1. Clique em **Criar**.
1. Agora a sua interface está em modo de projeto. Pode adicionar ou editar capacidades à sua interface sem quebrar as personalizações e vistas existentes.

## <a name="migrate-a-device-across-versions"></a>Migrar um dispositivo através de versões

Pode criar várias versões do modelo do dispositivo. Com o tempo, terá vários dispositivos conectados usando estes modelos de dispositivo. Pode migrar dispositivos de uma versão do seu modelo de dispositivo para outra. Os seguintes passos descrevem como migrar um dispositivo:

1. Vá para a página do **Device Explorer.**
1. Selecione o dispositivo necessário para migrar para outra versão.
1. Escolha **migrar**.
1. Selecione o modelo do dispositivo com o número da versão para onde pretende migrar o dispositivo e escolher **migrate**.

![Como migrar um dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Passos seguintes

Se é um operador ou construtor de soluções, um próximo passo sugerido é aprender [a gerir os seus dispositivos](./howto-manage-devices.md).

Se é um desenvolvedor de dispositivos, um próximo passo sugerido é ler sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md).
