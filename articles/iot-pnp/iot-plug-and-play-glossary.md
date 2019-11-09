---
title: Glossário de termos – IoT Plug and Play Preview | Microsoft Docs
description: Conceitos – um glossário de termos comuns relacionados à visualização da IoT Plug and Play.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 10/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 22b2cb8caa815b2ce8343a060a5f12133b763b83
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890349"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossário de termos para a visualização de IoT Plug and Play

Definições de termos comuns, conforme usado nos artigos de Plug and Play de IoT.

## <a name="azure-certified-for-iot-portal"></a>Portal do Azure Certified para IoT

Você pode usar o site do [portal do Azure Certified para IOT](https://aka.ms/ACFI) para:

- Conclua o [processo de certificação](#device-certification) para seu dispositivo de plug and Play de [IOT](#iot-plug-and-play-device).
- Encontre [modelos de capacidade de dispositivo](#device-capability-model).
- Publique um modelo de capacidade de dispositivo no [repositório de modelo público](#public-model-repository).

## <a name="azure-cli"></a>CLI do Azure

O CLI do Azure é uma ferramenta de linha de comando de plataforma cruzada para gerenciar recursos do Azure. A extensão do Azure IoT para o CLI do Azure é uma ferramenta de linha de comando para interagir com e testar [dispositivos de plug and Play IOT](#iot-plug-and-play-device). Você pode usar a extensão para:

- Conecte-se a um dispositivo de Plug and Play IoT.
- Exiba a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhar com [as propriedades](#properties)do dispositivo.
- Chame os [comandos](#commands)do dispositivo.
- Gerencie [repositórios de modelo](#model-repository), [interfaces](#interface)e [modelos de capacidade de dispositivo](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

O Azure IoT Central é uma solução de software como serviço totalmente gerenciada que facilita a conexão, o monitoramento e o gerenciamento de seus [dispositivos IoT plug and Play](#iot-plug-and-play-device). Você pode usar [modelos de capacidade de dispositivo](#device-capability-model) para configurar automaticamente um aplicativo de IOT central para monitorar e gerenciar seus dispositivos.

## <a name="azure-iot-certification-service"></a>Serviço de certificação do Azure IoT

O serviço de certificação do Azure IoT executa um conjunto de testes de certificação quando você envia um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) para a certificação por meio do portal do [Azure Certified para IOT](#azure-certified-for-iot-portal). Para poder adicionar um dispositivo ao [Catálogo de dispositivos certificado para IOT](#certified-for-iot-device-catalog), o dispositivo deve ser certificado.

## <a name="azure-iot-tools-extension"></a>Extensão de ferramentas do Azure IoT

As ferramentas do Azure IoT são uma coleção de extensões no [Visual Studio Code](#visual-studio-code) que ajudam você a interagir com o Hub IOT e a desenvolver dispositivos IOT. Para o desenvolvimento de dispositivos IoT Plug and Play, ele ajuda você a:

- Crie [modelos](#device-capability-model) e [interfaces](#interface)de recursos de dispositivo.
- Publicar em [repositórios de modelo](#model-repository).
- Gere o código esqueleto para implementar o aplicativo do dispositivo.

## <a name="azure-iot-explorer-tool"></a>Ferramenta do Azure IoT Explorer

O Azure IoT Explorer é uma ferramenta gráfica que você pode usar para interagir e testar seus [dispositivos de plug and Play de IOT](#iot-plug-and-play-device). Depois de instalar a ferramenta no computador local, você pode usá-la para:

- Exibir os dispositivos conectados ao [Hub IOT](#azure-iot-hub).
- Conecte-se a um dispositivo de Plug and Play IoT.
- Exiba a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhar com [as propriedades](#properties)do dispositivo.
- Chame os [comandos](#commands)do dispositivo.

## <a name="azure-iot-hub"></a>Azure IoT Hub

O Hub IoT é um serviço gerido, alojado na cloud, que atua como um centro de mensagens para assegurar a comunicação bidirecional entre a aplicação IoT e os dispositivos que esta gere. Os [dispositivos IoT plug and Play](#iot-plug-and-play-device) podem se conectar a um hub IOT. Uma solução de IoT usa um hub IoT para habilitar:

- Dispositivos para enviar telemetria a uma solução baseada em nuvem.
- Uma solução baseada em nuvem para gerenciar dispositivos conectados.

## <a name="azure-iot-device-sdk"></a>SDK do dispositivo IoT do Azure

Há SDKs de dispositivo para vários idiomas que você pode usar para criar aplicativos cliente de dispositivo de Plug and Play de IoT. Um dos requisitos para a [certificação do dispositivo](#device-certification) é que o código do cliente do dispositivo usa um dos SDKs do dispositivo IOT do Azure.

## <a name="certified-for-iot-device-catalog"></a>Certificado para catálogo de dispositivos IoT

O [Catálogo de dispositivos certificado para IOT](https://catalog.azureiotsolutions.com/) lista os [dispositivos IOT plug and Play](#iot-plug-and-play-device) que passaram pelos testes de [certificação do dispositivo](#device-certification) . Os [modelos de capacidade de dispositivo](#device-capability-model) para os dispositivos IOT plug and Play no catálogo e publicados no repositório de modelo público.

## <a name="commands"></a>Comandos

Os comandos definidos em uma [interface](#interface) representam métodos que podem ser executados no [digital](#digital-twin). Por exemplo, um comando para reinicializar um dispositivo.

## <a name="common-interface"></a>Interface comum

Espera-se que todos os [dispositivos IoT plug and Play](#iot-plug-and-play-device) implementem algumas [interfaces](#interface)comuns. Por exemplo, a interface de informações do dispositivo define informações de hardware e do sistema operacional sobre o dispositivo. A [certificação de dispositivo](#device-certification) exige que seu dispositivo implemente várias interfaces comuns. Você pode recuperar definições de interface comuns do repositório de modelos públicos.

## <a name="company-model-repository"></a>Repositório de modelo da empresa

Uma organização pode usar um [repositório de modelos](#model-repository) da empresa como um repositório particular para modelos e [interfaces](#interface)de [recursos de dispositivo](#device-capability-model) .

## <a name="connection-string"></a>Cadeia de ligação

Uma cadeia de conexão encapsula as informações necessárias para se conectar a um ponto de extremidade. Uma cadeia de conexão normalmente inclui o endereço do ponto de extremidade e as informações de segurança, mas os formatos de cadeia de conexão variam entre os serviços. Há dois tipos de cadeia de conexão associados ao serviço do Hub IoT:

- As cadeias de conexão do dispositivo permitem que [dispositivos IoT plug and Play](#iot-plug-and-play-device) se conectem aos pontos de extremidade voltados para o dispositivo em um hub IOT. O código do cliente em um dispositivo usa a cadeia de conexão para estabelecer uma conexão segura com um hub IoT.
- As cadeias de conexão do Hub IoT permitem que soluções e ferramentas de back-end se conectem com segurança aos pontos de extremidade voltados para o serviço em um hub IoT. Essas soluções e ferramentas gerenciam o Hub IoT e os dispositivos conectados a ele.
- Cadeias de conexão do repositório de modelo da empresa habilitam soluções e ferramentas de back-end para se conectarem com segurança a um [repositório de modelos da empresa](#company-model-repository). Essas soluções e ferramentas consomem ou gerenciam os modelos e as [interfaces](#interface) de [funcionalidade do dispositivo](#device-capability-model) no repositório.

## <a name="device-capability-model"></a>Modelo de funcionalidade do dispositivo

Um modelo de funcionalidade de dispositivo descreve um [dispositivo de plug and Play IOT](#iot-plug-and-play-device) e define o conjunto de [interfaces](#interface) implementadas pelo dispositivo. Um modelo de capacidade de dispositivo geralmente corresponde a um dispositivo físico, produto ou SKU. Use a [linguagem de definição de entrelaçamento digital](#digital-twin-definition-language) para definir um modelo de capacidade de dispositivo.

## <a name="device-certification"></a>Certificação de dispositivo

A certificação de dispositivo é o processo de certificação de um [dispositivo de plug and Play IOT](#iot-plug-and-play-device) para que ele possa ser adicionado ao [Catálogo de dispositivos certificado para IOT](#certified-for-iot-device-catalog) e seu [modelo de funcionalidade de dispositivo](#device-capability-model) e [interfaces](#interface) adicionados ao [modelo público repositório](#public-model-repository).

## <a name="device-developer"></a>Programador de dispositivo

Um desenvolvedor de dispositivo usa um [modelo de funcionalidade de dispositivo](#device-capability-model), [interfaces](#interface)e um [SDK do dispositivo IOT do Azure](#azure-iot-device-sdk) para implementar o código para ser executado em um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelagem de dispositivo

Um [desenvolvedor de dispositivos](#device-developer) usa a [linguagem digital de definição de entrelaçamento](#digital-twin-definition-language) para modelar os recursos de um [dispositivo de plug and Play IOT](#iot-plug-and-play-device). O modelo pode ser compartilhado usando um repositório de modelo. Um desenvolvedor de dispositivos pode gerar um esqueleto do código de dispositivo do modelo. Um [desenvolvedor de soluções](#solution-developer) pode configurar uma solução de IOT do modelo.

## <a name="device-provisioning-service"></a>Serviço de provisionamento de dispositivos

O [Azure IOT central](#azure-iot-central) usa o serviço de provisionamento de dispositivos para gerenciar todo o registro e a conexão do dispositivo. Para obter mais informações, consulte [conectividade do dispositivo no Azure IOT central](../iot-central/preview/overview-iot-central-get-connected.md). Você também pode usar o serviço de provisionamento de dispositivos para gerenciar o registro e a conexão de dispositivos com sua solução IoT baseada no Hub IoT. Para obter mais informações, consulte [Provisionando dispositivos com o serviço de provisionamento de dispositivos no Hub IOT do Azure](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registo de dispositivo

Antes que um [dispositivo iot plug and Play](#iot-plug-and-play-device) possa se conectar a uma solução de IOT, ele deve ser registrado com a solução. O [Azure IOT central](#azure-iot-central) usa o [serviço de provisionamento de dispositivos](#device-provisioning-service) para gerenciar o registro de dispositivos. Em uma solução de IoT personalizada, você pode registrar dispositivos com o Hub IoT no portal do Azure ou de forma programática.

## <a name="device-first"></a>Dispositivo-primeiro

O [IOT central do Azure](#azure-iot-central) dá suporte a um cenário de registro e conexão do primeiro dispositivo. Nesse cenário, um [dispositivo IoT plug and Play](#iot-plug-and-play-device) pode se conectar a um aplicativo IOT central sem ser registrado antecipadamente. O registro ocorre automaticamente quando um dispositivo se conecta pela primeira vez ao aplicativo.

## <a name="digital-twin"></a>Entrelaçar digital

Uma teledigital é um modelo de um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device). Uma teledigital é modelada usando a [linguagem de definição de entrelaçamento digital](#digital-twin-definition-language). Você pode usar os [SDKs do dispositivo IOT do Azure](#azure-iot-device-sdk) para interagir com o digital gêmeos em tempo de execução. Por exemplo, você pode definir um valor de propriedade em uma troca digital em um dispositivo e o SDK comunica essa alteração à sua solução de IoT na nuvem.

## <a name="digital-twin-change-events"></a>Eventos de alteração de troca digital

Quando um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) é conectado a um [Hub IOT](#azure-iot-hub), o Hub pode usar sua capacidade de roteamento para enviar notificações de alterações de conexão digital. Por exemplo, sempre que um valor de [Propriedade](#properties) é alterado em um dispositivo, o Hub IOT pode enviar uma notificação para um ponto de extremidade, como uma fila do barramento de serviço.

## <a name="digital-twin-definition-language"></a>Linguagem de definição de entrelaçamento digital

Uma linguagem para descrever modelos e interfaces para [dispositivos de plug and Play IOT](#iot-plug-and-play-device). Use a [linguagem de definição de entrelaçamento digital](https://aka.ms/DTDL) para descrever os recursos de uma [teledigital](#digital-twin) e habilitar a plataforma IOT e as soluções de IOT para aproveitar a semântica da entidade.

## <a name="digital-twin-route"></a>Rota de entrelaçamento digital

Uma rota configurada em um [Hub IOT](#azure-iot-hub) para entregar [eventos de alteração de troca digital](#digital-twin-change-events) e ponto de extremidade, como uma fila do barramento de serviço.

## <a name="interface"></a>Interface

Uma interface descreve os recursos relacionados que são implementados por um [dispositivo de plug and Play IOT](#iot-plug-and-play-device) ou um " [digital](#digital-twin)". Você pode reutilizar interfaces em diferentes [modelos de capacidade de dispositivo](#device-capability-model).

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A linguagem de consulta do Hub IoT é usada para várias finalidades. Por exemplo, você pode usar a linguagem para procurar [dispositivos registrados](#device-registration) com o Hub IOT ou refinar o comportamento do [Roteamento de entrelaçamento digital](#digital-twin-route) .

## <a name="iot-plug-and-play-device"></a>Dispositivo de Plug and Play de IoT

Um dispositivo de Plug and Play IoT normalmente é um dispositivo de computação independente de pequena escala que coleta dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de capacidade de dispositivo](#device-capability-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitoramento ambiental ou um controlador para um sistema de irrigação inteligente do agricultura. Você pode escrever uma solução de IoT hospedada na nuvem para comando, controlar e receber dados de dispositivos de Plug and Play de IoT. O [Catálogo de dispositivos Azure Certified para IOT](#certified-for-iot-device-catalog) lista os dispositivos plug and Play IOT disponíveis. Cada dispositivo de Plug and Play IoT no catálogo foi validado e tem um [modelo de funcionalidade de dispositivo](#device-capability-model).

## <a name="microsoft-partner-center"></a>Centro de parceiros da Microsoft

O [Microsoft Partner Center](https://docs.microsoft.com/partner-center/) é onde sua organização gerencia sua relação de ponta a ponta com a Microsoft. Você precisa de uma conta do Microsoft Partner Center para poder certificar seu [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) no portal do [Azure Certified para IOT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Deteção de modelos

Quando um [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) se conecta a uma solução de IOT, a solução pode descobrir os recursos do dispositivo encontrando o [modelo de capacidade do dispositivo](#device-capability-model). Um dispositivo pode enviar seu modelo de capacidade para a solução, ou a solução pode encontrar um modelo de capacidade de dispositivo em um [repositório de modelo](#model-repository).

## <a name="model-repository"></a>Repositório de modelos

Um repositório de modelos armazena modelos e [interfaces](#interface)de [funcionalidade do dispositivo](#device-capability-model) . Há um único [repositório de modelo público](#public-model-repository). As organizações podem criar seus próprios repositórios de modelo organizacional.

## <a name="model-repository-rest-api"></a>API REST do repositório de modelos

Uma API para gerenciar e interagir com repositórios de modelo. Por exemplo, você pode usar a API para adicionar [modelos de capacidade de dispositivo](#device-capability-model) e Pesquisar modelos de funcionalidade.

## <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos em uma [interface](#interface) que representam algum estado de uma. digital. Você pode declarar propriedades como somente leitura ou gravável. Propriedades somente leitura, como número de série, são definidas pelo código em execução no próprio [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) .  As propriedades graváveis, como um limite de alarme, normalmente são definidas a partir da solução de IoT baseada em nuvem.

## <a name="public-model-repository"></a>Repositório de modelos públicos

Há um único repositório de modelo público que armazena [modelos de recursos de dispositivo](#device-capability-model) e [interfaces](#interface) para [dispositivos certificados](#device-certification). O repositório de modelo público também armazena definições de [interface comuns](#common-interface) .

## <a name="registration-id"></a>ID de registro

Uma ID de registro identifica exclusivamente um dispositivo no [serviço de provisionamento de dispositivos](#device-provisioning-service). Essa ID não é igual à ID do dispositivo que é um identificador exclusivo para um dispositivo em um [Hub IOT](#azure-iot-hub).

## <a name="scope-id"></a>ID do escopo

O escopo da ID de escopo identifica exclusivamente uma instância do [serviço de provisionamento de dispositivos](#device-provisioning-service) .

## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

As assinaturas de acesso compartilhado são um mecanismo de autenticação baseado em hashes seguros SHA-256 ou URIs. A autenticação de assinatura de acesso compartilhado tem dois componentes: uma política de acesso compartilhado e uma assinatura de acesso compartilhado (geralmente chamada de token). Um [dispositivo de plug and Play IOT](#iot-plug-and-play-device) usa uma assinatura de acesso compartilhado para autenticar com um [Hub IOT](#azure-iot-hub).

## <a name="solution-developer"></a>Desenvolvedor de soluções

Um desenvolvedor de soluções cria o back-end da solução. Um desenvolvedor de soluções normalmente trabalha com recursos do Azure, como o [Hub IOT](#azure-iot-hub) e [repositórios de modelo](#model-repository), ou funciona com [IOT central](#azure-iot-central).

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos em uma [interface](#interface) representam medidas. Essas medidas são normalmente valores como leituras de sensor que são enviadas pelo [dispositivo de plug and Play de IOT](#iot-plug-and-play-device) como um fluxo de dados.

## <a name="visual-studio-code"></a>Visual Studio Code

O Visual Studio Code é um editor de código moderno disponível para várias plataformas. As extensões, como as do pacote de [Ferramentas do Azure IOT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , permitem que você personalize o editor para dar suporte a uma ampla gama de cenários de desenvolvimento.
