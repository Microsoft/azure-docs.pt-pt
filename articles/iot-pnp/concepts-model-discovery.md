---
title: Implementar IoT Plug e Reproduzir a descoberta do modelo de pré-visualização [ Microsoft Docs
description: Como desenvolvedor de soluções, saiba como pode implementar a descoberta do modelo IoT Plug e Play na sua solução.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 94e5e6fb826528aab1a358d44cb51bfcd6219032
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673575"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implemente a descoberta do modelo IoT Plug e Play Preview numa solução IoT

Este artigo descreve como, como um desenvolvedor de soluções, pode implementar a descoberta do modelo IoT Plug e Play Preview numa solução IoT.  A descoberta do modelo IoT Plug and Play é como os dispositivos IoT Plug e Play identificam os seus modelos e interfaces de capacidade suportada, e como uma solução IoT recupera esses modelos e interfaces de capacidade.

Existem duas grandes categorias de solução IoT: soluções construídas de propósito que funcionam com um conjunto conhecido de dispositivos IoT Plug and Play, e soluções orientadas por modelos que funcionam com qualquer dispositivo IoT Plug and Play.

Este artigo conceptual descreve como implementar a descoberta de modelos em ambos os tipos de solução.

## <a name="model-discovery"></a>Deteção de modelos

Quando um dispositivo IoT Plug and Play se conecta pela primeira vez ao seu hub IoT, envia uma mensagem de telemetria de informação de modelo. Esta mensagem inclui as iDs das interfaces que o dispositivo implementa. Para que a sua solução funcione com o dispositivo, deve resolver esses IDs e recuperar as definições para cada interface.

Aqui estão os passos que um dispositivo IoT Plug and Play toma quando utiliza o Serviço de Fornecimento de Dispositivos (DPS) para ligar a um hub:

1. Quando o dispositivo é ligado, liga-se ao ponto final global para DPS e autentica utilizando um dos métodos permitidos.
1. O DPS autentica então o dispositivo e olha para a regra que lhe diz a que hub IoT atribuir o dispositivo. Em seguida, o DPS regista o dispositivo com o eixo.
1. O DPS devolve uma cadeia de ligação IoT Hub ao dispositivo.
1. O dispositivo envia então uma mensagem de telemetria de descoberta para o seu Hub IoT. A mensagem de telemetria discovery contém as iDs das interfaces que o dispositivo implementa.
1. O dispositivo IoT Plug and Play está agora pronto para funcionar com uma solução que utiliza o seu hub IoT.

Se o dispositivo se ligar diretamente ao seu hub IoT, liga-se utilizando uma cadeia de ligação incorporada no código do dispositivo. O dispositivo envia então uma mensagem de telemetria de descoberta para o seu Hub IoT.

Consulte a interface [ModelInformation](concepts-common-interfaces.md) para saber mais sobre a mensagem de telemetria de informação do modelo.

### <a name="purpose-built-iot-solutions"></a>Soluções IoT construídas de propósito

Uma solução IoT construída de propósito funciona com um conjunto conhecido de modelos e interfaces de dispositivos IoT Plug e Play.

Terá o modelo de capacidade e as interfaces dos dispositivos que se ligarão à sua solução com antecedência. Utilize os seguintes passos para preparar a sua solução:

1. Guarde os ficheiros JSON da interface num [repositório de modelos](./howto-manage-models.md) onde a sua solução possa lê-los.
1. Escreva lógica na sua solução IoT com base nos modelos e interface de capacidade seleções IoT Plug e Play esperados.
1. Subscreva notificações do hub IoT que a sua solução utiliza.

Quando receber uma notificação para uma nova ligação do dispositivo, siga estes passos:

1. Leia a mensagem de telemetria discovery para recuperar os IDs do modelo de capacidade e interfaces implementadas pelo dispositivo.
1. Compare a identificação do modelo de capacidade com as Identidades dos modelos de capacidade que armazenou antes do tempo.
1. Agora sabe que tipo de dispositivo está ligado. Utilize a lógica que escreveu anteriormente para permitir que os utilizadores interajam com o dispositivo de forma adequada.

### <a name="model-driven-solutions"></a>Soluções orientadas por modelos

Uma solução IoT orientada para o modelo pode funcionar com qualquer dispositivo IoT Plug and Play. Construir uma Solução IoT orientada por modelos é mais complexo, mas o benefício é que a sua solução funcione com quaisquer dispositivos adicionados no futuro.

Para construir uma solução IoT orientada para o modelo, é necessário criar lógica contra os primitivos da interface IoT Plug e Play: telemetria, propriedades e comandos. A lógica da sua solução IoT representa um dispositivo combinando múltiplas capacidades de telemetria, propriedade e comando.

A sua solução também deve subscrever notificações do hub IoT que utiliza.

Quando a sua solução receber uma notificação para uma nova ligação do dispositivo, siga estes passos:

1. Leia a mensagem de telemetria discovery para recuperar os IDs do modelo de capacidade e interfaces implementadas pelo dispositivo.
1. Para cada ID, leia o ficheiro JSON completo para encontrar as capacidades do dispositivo.
1. Verifique se cada interface está presente em quaisquer caches que tenha construído para armazenar os ficheiros JSON recuperados anteriormente pela sua solução.
1. Em seguida, verifique se uma interface com esse ID está presente no repositório de modelo público. Para mais informações, consulte [o repositório de modelos públicos.](howto-manage-models.md)
1. Se a interface não estiver presente no repositório de modelos públicos, tente procurá-la em qualquer repositório de modelos da empresa conhecido da sua solução. Precisa de uma corda de ligação para aceder a um repositório modelo da empresa. Para mais informações, consulte [o repositório modelo da Empresa.](howto-manage-models.md)
1. Se não conseguir encontrar todas as interfaces no repositório de modelos públicos, ou num repositório de modelos da empresa, pode verificar se o dispositivo pode fornecer a definição de interface. Um dispositivo pode implementar a interface [ModelDefinition](concepts-common-interfaces.md) padrão para publicar informações sobre como recuperar ficheiros de interface com um comando.
1. Se encontrou ficheiros JSON para cada interface implementada pelo dispositivo, pode enumerar as capacidades do dispositivo. Use a lógica que escreveu anteriormente para permitir que os utilizadores interajam com o dispositivo.
1. A qualquer momento, pode chamar os gémeos digitais API para recuperar os ID do modelo de capacidade e iDs de interface para o dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a descoberta de modelos uma solução IoT, saiba mais sobre a [Plataforma Azure IoT](overview-iot-plug-and-play.md) para alavancar outras capacidades para a sua solução.
