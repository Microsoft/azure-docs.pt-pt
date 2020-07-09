---
title: Implementar IoT Plug e Play Preview modelo de descoberta / Microsoft Docs
description: Como desenvolvedor de soluções, saiba como pode implementar a descoberta do modelo IoT Plug e Play na sua solução.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 74eb38269a3c7fbdc6d95554a8a8cef14eb0b787
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770463"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementar ioT plug e reproduzir a descoberta do modelo de pré-visualização numa solução IoT

Este artigo descreve como, como desenvolvedor de soluções, pode implementar a descoberta do modelo IoT Plug e Play Preview numa solução IoT.  A descoberta do modelo IoT Plug e Play é como os dispositivos IoT Plug e Play identificam os seus modelos e interfaces de capacidade suportados, e como uma solução IoT recupera esses modelos e interfaces de capacidade.

Existem duas grandes categorias de solução IoT: soluções construídas de propósito que funcionam com um conjunto conhecido de dispositivos IoT Plug e Play, e soluções orientadas para modelos que funcionam com qualquer dispositivo IoT Plug e Play.

Este artigo de conceito descreve como implementar a descoberta de modelos em ambos os tipos de solução.

## <a name="model-discovery"></a>Deteção de modelos

Quando um dispositivo IoT Plug and Play se liga pela primeira vez ao seu hub IoT, envia uma mensagem de telemetria de informação do modelo. Esta mensagem inclui os IDs das interfaces que o dispositivo implementa. Para que a sua solução funcione com o dispositivo, deve resolver esses IDs e recuperar as definições para cada interface.

Aqui estão os passos que um dispositivo IoT Plug and Play toma quando utiliza o Serviço de Provisionamento de Dispositivos (DPS) para ligar a um hub:

1. Quando o dispositivo é ligado, liga-se ao ponto final global para DPS e autentica-se utilizando um dos métodos permitidos.
1. Em seguida, o DPS autentica o dispositivo e procura a regra que lhe diz a que hub IoT atribuir o dispositivo. Em seguida, o DPS regista o dispositivo com aquele hub.
1. O DPS devolve ao dispositivo uma cadeia de ligação IoT Hub.
1. Em seguida, o dispositivo envia uma mensagem de telemetria de descoberta para o seu Hub IoT. A mensagem de telemetria discovery contém os IDs das interfaces que o dispositivo implementa.
1. O dispositivo IoT Plug and Play está agora pronto para trabalhar com uma solução que utiliza o seu hub IoT.

Se o dispositivo ligar diretamente ao seu hub IoT, ele liga-se utilizando uma cadeia de ligação que está incorporada no código do dispositivo. Em seguida, o dispositivo envia uma mensagem de telemetria de descoberta para o seu Hub IoT.

Consulte a interface [ModelInformation](concepts-common-interfaces.md) para saber mais sobre a mensagem de telemetria de informação do modelo.

### <a name="purpose-built-iot-solutions"></a>Soluções IoT construídas de propósito

Uma solução IoT construída de propósito funciona com um conjunto conhecido de modelos e interfaces de capacidade ioT plug e play.

Terá o modelo de capacidade e interfaces para os dispositivos que se ligarão à sua solução com antecedência. Utilize os seguintes passos para preparar a sua solução:

1. Guarde os ficheiros JSON da interface num [repositório de modelos](./howto-manage-models.md) onde a sua solução os possa ler.
1. Escreva lógica na sua solução IoT com base nos modelos e interface de capacidade ioT plug e play esperados.
1. Subscreva as notificações do hub IoT que a sua solução utiliza.

Quando receber uma notificação para uma nova ligação do dispositivo, siga estes passos:

1. Leia a mensagem de telemetria de descoberta para recuperar os IDs do modelo de capacidade e interfaces implementadas pelo dispositivo.
1. Compare o ID do modelo de capacidade com os IDs dos modelos de capacidade que armazenou antes do tempo.
1. Agora sabe que tipo de dispositivo ligou. Utilize a lógica que escreveu anteriormente para permitir que os utilizadores interajam adequadamente com o dispositivo.

### <a name="model-driven-solutions"></a>Soluções orientadas por modelos

Uma solução IoT orientada por modelos pode funcionar com qualquer dispositivo IoT Plug e Play. Construir um modelo impulsionado pela IoT Solution é mais complexo, mas o benefício é que a sua solução funciona com quaisquer dispositivos adicionados no futuro.

Para construir uma solução IoT orientada por modelos, é necessário criar lógica contra os primitivos da interface IoT Plug e Play: telemetria, propriedades e comandos. A lógica da sua solução IoT representa um dispositivo combinando múltiplas capacidades de telemetria, propriedade e comando.

A sua solução também deve subscrever notificações do hub IoT que utiliza.

Quando a sua solução receber uma notificação para uma nova ligação do dispositivo, siga estes passos:

1. Leia a mensagem de telemetria de descoberta para recuperar os IDs do modelo de capacidade e interfaces implementadas pelo dispositivo.
1. Para cada ID, leia o ficheiro JSON completo para encontrar as capacidades do dispositivo.
1. Verifique se cada interface está presente em caches que construiu para armazenar os ficheiros JSON recuperados anteriormente pela sua solução.
1. Em seguida, verifique se uma interface com esse ID está presente no repositório de modelo público. Para mais informações, consulte [o repositório de modelos públicos.](howto-manage-models.md)
1. Se a interface não estiver presente no repositório de modelos públicos, tente procurá-la em qualquer repositório de modelo da empresa conhecidos da sua solução. Precisa de uma cadeia de ligação para aceder a um repositório de modelos da empresa. Para mais informações, consulte [o repositório de modelos da Empresa.](howto-manage-models.md)
1. Se não conseguir encontrar todas as interfaces no repositório de modelos públicos ou num repositório de modelos da empresa, pode verificar se o dispositivo pode fornecer a definição de interface. Um dispositivo pode implementar a interface padrão [ModelDefinition](concepts-common-interfaces.md) para publicar informações sobre como recuperar ficheiros de interface com um comando.
1. Se encontrar ficheiros JSON para cada interface implementada pelo dispositivo, pode enumerar as capacidades do dispositivo. Utilize a lógica que escreveu anteriormente para permitir que os utilizadores interajam com o dispositivo.
1. A qualquer momento, pode ligar para a API dos gémeos digitais para recuperar o ID do modelo de capacidade e os IDs de interface para o dispositivo.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu sobre a descoberta de modelos uma solução IoT, saiba mais sobre a [Plataforma Azure IoT](overview-iot-plug-and-play.md) para aproveitar outras capacidades para a sua solução.
