---
title: Noções básicas sobre o controle de versão de modelo de dispositivo para seus aplicativos do Azure IoT Central | Microsoft Docs
description: Iterar seus modelos de dispositivo criando novas versões e sem afetar seus dispositivos conectados ao vivo
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c718794528989fbc46b404617f16d3a91ade6011
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877313"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão de modelo de dispositivo

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

O Azure IoT Central permite o desenvolvimento rápido de aplicativos de IoT. Você pode iterar rapidamente seus designs de modelo de dispositivo adicionando, editando ou excluindo medidas, configurações ou propriedades. Algumas dessas alterações podem ser intrusivas para os dispositivos conectados no momento. O Azure IoT Central identifica essas alterações significativas e fornece uma maneira de implantar com segurança essas atualizações nos dispositivos.

Um modelo de dispositivo tem um número de versão ao criá-lo. Por padrão, o número de versão é 1.0.0. Se você editar um modelo de dispositivo e se essa alteração puder afetar os dispositivos conectados ao vivo, o Azure IoT Central solicitará que você crie uma nova versão de modelo de dispositivo.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo, consulte [configurar um modelo de dispositivo](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Alterações que solicitam uma alteração de versão

Em geral, as alterações nas configurações ou nas propriedades do seu modelo de dispositivo solicitam uma alteração de versão.

> [!NOTE]
> As alterações feitas no modelo de dispositivo não solicitam a criação de uma nova versão quando nenhum dispositivo ou no máximo um dispositivo está conectado.

A lista a seguir descreve as ações do usuário que podem exigir uma nova versão:

* Propriedades (obrigatória)
    * Adicionando ou excluindo uma propriedade obrigatória
    * Alterar o nome do campo de uma propriedade, o nome do campo que é usado pelos dispositivos para enviar mensagens.
*  Propriedades (opcional)
    * Excluindo uma propriedade opcional
    * Alterar o nome do campo de uma propriedade, o nome do campo que é usado pelos dispositivos para enviar mensagens.
    * Alterando uma propriedade opcional para uma propriedade obrigatória
*  Definições
    * Adicionando ou excluindo uma configuração
    * Alterando o nome do campo de uma configuração, nome do campo que é usado pelos dispositivos para enviar e receber mensagens.

## <a name="what-happens-on-version-change"></a>O que acontece na alteração de versão?

O que acontece com regras e painéis de dispositivos quando há uma alteração de versão?

**As regras** podem conter condições que dependem de propriedades. Se você tiver removido uma ou mais dessas propriedades, essas regras poderão ser quebradas na nova versão do modelo de dispositivo. Você pode acessar essas regras específicas e atualizar as condições para corrigir as regras. As regras para a versão anterior devem funcionar sem impacto.

Os **painéis de dispositivos** podem conter vários tipos de blocos. Alguns dos blocos podem conter configurações e propriedades. Quando uma propriedade ou configuração usada em um bloco é removida, o bloco é totalmente ou parcialmente rompido. Você pode ir até o bloco e corrigir o problema removendo o bloco ou atualizando o conteúdo do bloco.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrar um dispositivo entre versões de modelo de dispositivo

Você pode criar várias versões do modelo de dispositivo. Ao longo do tempo, você terá vários dispositivos conectados usando esses modelos de dispositivo. Você pode migrar dispositivos de uma versão do modelo de dispositivo para outra. As etapas a seguir descrevem como migrar um dispositivo:

1. Vá para a página de **Device Explorer** .
1. Selecione o dispositivo que você precisa migrar para outra versão.
1. Escolha **migrar dispositivo**.
1. Selecione o número de versão para o qual você deseja migrar odispositivo e escolha migrar.

![Como migrar um dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a usar as versões de modelo de dispositivo em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules.md)