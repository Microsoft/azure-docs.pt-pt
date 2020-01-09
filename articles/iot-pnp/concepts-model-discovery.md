---
title: Implementar descoberta de modelo de visualização de IoT Plug and Play | Microsoft Docs
description: Como desenvolvedor de soluções, saiba como você pode implementar a descoberta de modelo de Plug and Play de IoT em sua solução.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 66da0321930ac38217a336380c9889963a433e67
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531365"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementar a descoberta do modelo de visualização de IoT Plug and Play em uma solução de IoT

Este artigo descreve como, como um desenvolvedor de soluções, você pode implementar a descoberta de modelo do IoT Plug and Play preview em uma solução de IoT.  A descoberta de modelo de Plug and Play IoT é como os dispositivos de IoT Plug and Play identificam seus modelos e interfaces de recursos com suporte e como uma solução de IoT recupera esses modelos e interfaces de recursos.

Há duas categorias amplas de solução de IoT: soluções criadas especificamente que funcionam com um conjunto conhecido de dispositivos IoT Plug and Play e soluções controladas por modelos que funcionam com qualquer dispositivo IoT Plug and Play.

Este artigo de conceito descreve como implementar a descoberta de modelo em ambos os tipos de solução.

## <a name="model-discovery"></a>Deteção de modelos

Quando um dispositivo de Plug and Play IoT se conecta pela primeira vez ao Hub IoT, ele envia uma mensagem de telemetria de informações de modelo. Essa mensagem inclui as IDs das interfaces que o dispositivo implementa. Para que sua solução funcione com o dispositivo, ela deve resolver essas IDs e recuperar as definições de cada interface.

Aqui estão as etapas que um dispositivo IoT Plug and Play pega quando usa o DPS (serviço de provisionamento de dispositivos) para se conectar a um Hub:

1. Quando o dispositivo é ativado, ele se conecta ao ponto de extremidade global do DPS e se autentica usando um dos métodos permitidos.
1. Em seguida, o DPS autentica o dispositivo e pesquisa a regra que informa a qual Hub IoT atribuir o dispositivo. O DPS registra o dispositivo com esse Hub.
1. O DPS retorna uma cadeia de conexão do Hub IoT para o dispositivo.
1. Em seguida, o dispositivo envia uma mensagem de telemetria de descoberta para o Hub IoT. A mensagem de telemetria de descoberta contém as IDs das interfaces que o dispositivo implementa.
1. O dispositivo de Plug and Play de IoT agora está pronto para trabalhar com uma solução que usa o Hub IoT.

Se o dispositivo se conectar diretamente ao Hub IoT, ele se conectará usando uma cadeia de conexão que é inserida no código do dispositivo. Em seguida, o dispositivo envia uma mensagem de telemetria de descoberta para o Hub IoT.

Consulte a interface [ModelInformation](concepts-common-interfaces.md) para saber mais sobre a mensagem de telemetria de informações do modelo.

### <a name="purpose-built-iot-solutions"></a>Soluções de IoT criadas por finalidade

Uma solução de IoT criada por finalidade funciona com um conjunto conhecido de interfaces e modelos de recursos de dispositivo de Plug and Play de IoT.

Você terá o modelo de funcionalidade e as interfaces para os dispositivos que se conectarão à sua solução antecipadamente. Use as seguintes etapas para preparar sua solução:

1. Armazene os arquivos JSON da interface no Azure em um local onde sua solução possa lê-los.
1. Grave a lógica em sua solução de IoT com base nos modelos de funcionalidade e na interface esperados do IoT Plug and Play.
1. Assine as notificações do Hub IoT que sua solução usa.

Ao receber uma notificação para uma nova conexão de dispositivo, siga estas etapas:

1. Leia a mensagem de telemetria de descoberta para recuperar as IDs do modelo de funcionalidade e interfaces implementadas pelo dispositivo.
1. Compare a ID do modelo de capacidade com as IDs dos modelos de recursos que você armazenou antes do tempo.
1. Agora você sabe que tipo de dispositivo se conectou. Use a lógica que você escreveu anteriormente para permitir que os usuários interajam com o dispositivo adequadamente.

### <a name="model-driven-solutions"></a>Soluções controladas por modelos

Uma solução de IoT controlada por modelos pode funcionar com qualquer dispositivo de Plug and Play de IoT. Criar uma solução de IoT controlada por modelo é mais complexo, mas o benefício é que sua solução funciona com qualquer dispositivo adicionado no futuro.

Para criar uma solução de IoT controlada por modelos, você precisa criar lógica em relação aos primitivos de interface de Plug and Play de IoT: telemetria, propriedades e comandos. A lógica da sua solução de IoT representa um dispositivo combinando vários recursos de telemetria, propriedade e comando.

Sua solução também deve assinar notificações do Hub IoT que ele usa.

Quando sua solução receber uma notificação para uma nova conexão de dispositivo, siga estas etapas:

1. Leia a mensagem de telemetria de descoberta para recuperar as IDs do modelo de funcionalidade e interfaces implementadas pelo dispositivo.
1. Para cada ID, leia o arquivo JSON completo para localizar os recursos do dispositivo.
1. Verifique se cada interface está presente em todos os caches que você criou para armazenar os arquivos JSON recuperados anteriormente por sua solução.
1. Em seguida, verifique se uma interface com essa ID está presente no repositório de modelo público. Para obter mais informações, consulte [repositório de modelos públicos](howto-manage-models.md).
1. Se a interface não estiver presente no repositório de modelos públicos, tente procurá-la em qualquer repositório de modelo da empresa conhecido pela sua solução. Você precisa de uma cadeia de conexão para acessar um repositório de modelos da empresa. Para obter mais informações, consulte [repositório de modelos da empresa](howto-manage-models.md).
1. Se você não encontrar todas as interfaces no repositório de modelo público ou em um repositório de modelo da empresa, poderá verificar se o dispositivo pode fornecer a definição de interface. Um dispositivo pode implementar a interface [ModelDefinition](concepts-common-interfaces.md) padrão para publicar informações sobre como recuperar arquivos de interface com um comando.
1. Se você tiver encontrado arquivos JSON para cada interface implementada pelo dispositivo, poderá enumerar os recursos do dispositivo. Use a lógica que você escreveu anteriormente para permitir que os usuários interajam com o dispositivo.
1. A qualquer momento, você pode chamar a API digital gêmeos para recuperar a ID do modelo de funcionalidade e as IDs de interface para o dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre a descoberta de modelos de uma solução de IoT, saiba mais sobre a [plataforma IOT do Azure](overview-iot-plug-and-play.md) para aproveitar outros recursos para sua solução.
