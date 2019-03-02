---
title: Noções básicas sobre controle de versão de modelo de dispositivo para as suas aplicações do Azure IoT Central | Documentos da Microsoft
description: Iterar sobre os modelos de dispositivos através da criação de novas versões e sem causar impacto nos seus dispositivos ligados em direto
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3889aa1e8b19e203a3f6354c2d26fb82afe2b922
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214287"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão de modelo do dispositivo

O Azure IoT Central permite um desenvolvimento rápido de aplicativos de IoT. Pode iterar rapidamente sobre o design de modelo do dispositivo por adicionar, editar ou eliminar as medidas, as definições ou propriedades. Algumas dessas alterações poderiam ser intrusivas para os dispositivos atualmente ligados. O Azure IoT Central identifica estas alterações de última hora e fornece uma maneira de implantar com segurança essas atualizações para os dispositivos.

Um modelo do dispositivo tem um número de versão quando a criar. Por predefinição, o número da versão for 1.0.0. Se editar um modelo de dispositivo e se essa alteração pode afetar live dispositivos ligados, o Azure IoT Central pede-lhe para criar uma nova versão de modelo do dispositivo.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo, veja [configurar um modelo de dispositivo](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Alterações que uma alteração de versão de linha de comandos

Em geral, alterações a definições ou propriedades do seu modelo de dispositivo solicitar uma alteração de versão.

> [!NOTE]
> As alterações feitas ao modelo de dispositivo não pedir para a criação de uma nova versão quando nenhum dispositivo ou em mais um dispositivo está ligado.

A lista seguinte descreve as ações de utilizador que podem exigir uma nova versão:

* Propriedades (obrigatórios)
    * Adicionar ou excluir uma propriedade necessária
    * Alterar o nome do campo de uma propriedade, o nome do campo que é utilizado pelos seus dispositivos para enviar mensagens.
*  Propriedades (opcionais)
    * A eliminação de uma propriedade opcional
    * Alterar o nome do campo de uma propriedade, o nome do campo que é utilizado pelos seus dispositivos para enviar mensagens.
    * Alterar uma propriedade opcional para uma propriedade necessária
*  Definições
    * Adicionar ou excluir uma definição
    * Alterar o nome do campo de uma configuração, o nome do campo que é utilizado pelos seus dispositivos para enviar e receber mensagens.

## <a name="what-happens-on-version-change"></a>O que acontece na alteração de versão?

O que acontece a regras e dashboards do dispositivo quando ocorre uma alteração de versão?

**Regras** pode conter condições que dependem das propriedades. Se tiver removido um ou mais destas propriedades, estas regras poderiam ser quebradas em sua nova versão de modelo do dispositivo. Pode ir para estas regras específicas e as condições para corrigir as regras de atualização. Regras para a sua versão anterior deverá funcionar sem qualquer impacto.

**Dashboards de dispositivo** pode conter vários tipos de mosaicos. Alguns dos mosaicos podem conter definições e propriedades. Quando uma propriedade ou configuração utilizado num mosaico for removida, o mosaico é quebrado total ou parcialmente. Pode ir para o mosaico e corrigir o problema ao remover o mosaico ou a atualizar o conteúdo do mosaico.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrar de um dispositivo em todas as versões do modelo de dispositivo

Pode criar várias versões do modelo de dispositivo. Ao longo do tempo, terá vários dispositivos conectados usando esses modelos de dispositivo. Pode migrar dispositivos de uma versão do seu modelo de dispositivo para outro. Os passos seguintes descrevem como migrar de um dispositivo:

1. Vá para o **Device Explorer** página.
1. Selecione o dispositivo que tem de migrar para outra versão.
1. Escolher **migrar dispositivos**.
1. Selecione o número de versão que pretende migrar o dispositivo e escolher **migrar**.

![Como migrar de um dispositivo](media/howto-version-devicetemplate/pick-version.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar as versões do modelo de dispositivo na sua aplicação do Azure IoT Central, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules.md)