---
title: Compreender a versão do modelo do dispositivo para as suas aplicações Azure IoT Central ! Microsoft Docs
description: Iterate sobre os modelos do seu dispositivo criando novas versões e sem afetar os seus dispositivos conectados ao vivo
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 93545c63013c95e3db498b079061da3d9b189efd
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986451"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão do modelo do dispositivo

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Um modelo de dispositivo inclui um esquema que descreve como um dispositivo interage com o IoT Central. Estas interações incluem telemetria, propriedades e comandos. Tanto o dispositivo como a aplicação IoT Central baseiam-se numa compreensão partilhada deste esquema para a troca de informações. Só é possível fazer alterações limitadas no esquema sem quebrar o contrato, por isso a maioria das alterações de esquema requerem uma nova versão do modelo do dispositivo. A versão do modelo do dispositivo permite que os dispositivos mais antigos continuem com a versão de esquema que compreendem, enquanto os dispositivos mais recentes ou atualizados utilizam uma versão de esquema posterior.

O esquema num modelo de dispositivo é definido no modelo do dispositivo e nas suas interfaces. Os modelos do dispositivo incluem outras informações, tais como propriedades na nuvem, personalizações de exibição e vistas. Se fizer alterações nas partes do modelo do dispositivo que não definem como o dispositivo troca dados com a IoT Central, não precisa de ver versão do modelo.

Deve publicar sempre um modelo de dispositivo atualizado antes de um operador poder usá-lo. O IoT Central impede-o de publicar alterações de rutura num modelo de dispositivo sem antes ver o modelo.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo, consulte [Configurar e gerir um modelo de dispositivo](howto-set-up-template.md)

## <a name="versioning-rules"></a>Regras de versão

Esta secção resume as regras de versão que se aplicam aos modelos do dispositivo. Tanto os modelos de dispositivos como as interfaces têm números de versão. O seguinte corte mostra o modelo do dispositivo para um dispositivo termóstato. O modelo do dispositivo tem uma única interface. Pode ver o número da versão no final do `@id` campo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Para ver estas informações na UI Central IoT, selecione **Ver identidade** no editor de modelo do dispositivo:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Ver a identidade de uma interface para ver o número da versão":::

A lista a seguir mostra as regras que determinam quando deve criar uma nova versão:

* Depois de publicado um modelo de dispositivo, não é possível remover nenhuma interface, mesmo numa nova versão do modelo do dispositivo.
* Depois de publicado um modelo de dispositivo, pode adicionar uma interface se criar uma nova versão do modelo do dispositivo.
* Depois de publicado um modelo de dispositivo, pode substituir uma interface por uma versão mais recente se criar uma nova versão do modelo do dispositivo. Por exemplo, se o modelo do dispositivo sensor V1 utilizar a interface termostato v1, pode criar um modelo de dispositivo sensor V2 que utiliza a interface do termóstato V2.
* Depois de uma interface ser publicada, não é possível remover nenhum dos conteúdos da interface, mesmo numa nova versão do modelo do dispositivo.
* Depois de uma interface ser publicada, pode adicionar itens ao conteúdo de uma interface se criar uma nova versão da interface e do modelo do dispositivo. Os itens que pode adicionar à interface incluem telemetria, propriedades e comandos.
* Depois de uma interface ser publicada, pode fazer alterações não-esquema para os itens existentes na interface se criar uma nova versão da interface e do modelo do dispositivo. Partes não-esquema de um item de interface incluem o nome de exibição e o tipo semântico. As partes de esquema de um item de interface que não pode alterar são nome, tipo de capacidade e esquema.

As secções seguintes acompanham-no através de alguns exemplos de modificação de modelos de dispositivos no IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Personalize o modelo do dispositivo sem ver versão

Certos elementos das capacidades do seu dispositivo podem ser editados sem necessidade de ver o seu modelo e interfaces do dispositivo. Por exemplo, alguns destes campos incluem nome de exibição, tipo semântico, valor mínimo, valor máximo, casas decimais, cor, unidade, unidade de exibição, comentário e descrição. Para adicionar uma destas personalizações:

1. Aceda à página **de modelos do dispositivo.**
1. Selecione o modelo de dispositivo que deseja personalizar.
1. Escolha o separador **Personalizar.**
1. Todas as capacidades definidas no seu modelo de dispositivo estão listadas aqui. Pode editar, guardar e utilizar todos estes campos sem a necessidade de ver o seu modelo de dispositivo. Se houver campos que deseje editar que sejam apenas de leitura, tem de ver o seu modelo de dispositivo para alterá-los. Selecione um campo que deseja editar e introduza em quaisquer novos valores.
1. Selecione **Guardar**. Estes valores substituem tudo o que foi inicialmente guardado no modelo do dispositivo e são utilizados em toda a aplicação.

## <a name="version-a-device-template"></a>Versão um modelo de dispositivo

A criação de uma nova versão do seu modelo de dispositivo cria uma versão de projeto do modelo onde o modelo do dispositivo pode ser editado. Quaisquer interfaces publicadas permanecem publicadas até serem publicadas individualmente. Para modificar uma interface publicada, primeiro crie uma nova versão do modelo do dispositivo.

Apenas versão do modelo do dispositivo quando estiver a tentar editar uma parte do modelo do dispositivo que não pode editar na secção de personalizaçãos.

Para a versão de um modelo de dispositivo:

1. Aceda à página **de modelos do dispositivo.**
1. Selecione o modelo de dispositivo que está a tentar ver.
1. Selecione o botão **Versão** na parte superior da página e dê ao modelo um novo nome. IoT Central sugere um novo nome, que pode editar.
1. Selecione **Criar**.
1. Agora o modelo do seu dispositivo está no modo de projeto. Pode ver que as suas interfaces ainda estão bloqueadas. Veri o que quiser modificar.

## <a name="version-an-interface"></a>Versão uma interface

A versão de uma interface permite-lhe adicionar, atualizar e remover as capacidades dentro da interface que já tinha criado.

Para a versão de uma interface:

1. Aceda à página **de modelos do dispositivo.**
1. Selecione o modelo de dispositivo que tem num modo de projeto.
1. Selecione a interface que está no modo publicado que deseja ver e editar.
1. Selecione o botão **Versão** na parte superior da página de interface.
1. Selecione **Criar**.
1. Agora a sua interface está em modo de projeto. Pode adicionar ou editar capacidades à sua interface sem quebrar as personalizações e vistas existentes.

## <a name="migrate-a-device-across-versions"></a>Migrar um dispositivo através de versões

Pode criar várias versões do modelo do dispositivo. Com o tempo, terá vários dispositivos conectados usando estes modelos de dispositivo. Pode migrar dispositivos de uma versão do seu modelo de dispositivo para outra. Os seguintes passos descrevem como migrar um dispositivo:

1. Aceda à página **Dispositivos**.
1. Selecione o dispositivo de que necessita para migrar para outra versão.
1. Escolha **Migrar:**  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="Escolha a opção de começar a migrar um dispositivo":::
1. Selecione o modelo do dispositivo com o número de versão para o quais pretende migrar o dispositivo e **selecione Migrar**.

## <a name="next-steps"></a>Próximos passos

Se é um operador ou construtor de soluções, um próximo passo sugerido é [aprender a gerir os seus dispositivos](./howto-manage-devices.md).

Se você é um desenvolvedor de dispositivos, um próximo passo sugerido é ler sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md).
