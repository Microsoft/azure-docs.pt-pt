---
title: Glossário de termos - IoT Plug e Play Preview / Microsoft Docs
description: Conceitos - um glossário de termos comuns relativos a IoT Plug e Play Preview.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81767066"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossário de termos para IoT Plug e Play Preview

Definições de termos comuns como usados nos artigos IoT Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Azure Certificado para portal IoT

Pode utilizar o website do [portal Azure Certified para:](https://aka.ms/ACFI)

- Preencha o [processo de certificação](#device-certification) do seu [dispositivo IoT Plug and Play](#iot-plug-and-play-device).
- Encontre [os modelos de capacidade do dispositivo](#device-capability-model).
- Publique um modelo de capacidade do dispositivo para o [repositório de modelos públicos](#public-model-repository).

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI é uma ferramenta de linha de comando transversal para gerir os recursos da Azure. A extensão Azure IoT para o Azure CLI é uma ferramenta de linha de comando para interagir e testar [dispositivos IoT Plug e Play](#iot-plug-and-play-device). Pode utilizar a extensão para:

- Ligue-se a um dispositivo IoT Plug and Play.
- Veja a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhar com [as propriedades do](#properties)dispositivo.
- Comandos do dispositivo [de chamada](#commands).
- Gerir [repositórios de modelos,](#model-repository) [interfaces](#interface)e [modelos de capacidade do dispositivo.](#device-capability-model)

## <a name="azure-iot-central"></a>Azure IoT Central

O Azure IoT Central é uma solução totalmente gerida de software como serviço que facilita a ligação, monitorização e gestão dos seus [dispositivos IoT Plug e Play](#iot-plug-and-play-device). Pode utilizar [os modelos de capacidade do dispositivo](#device-capability-model) para configurar automaticamente uma aplicação IoT Central para monitorizar e gerir os seus dispositivos.

## <a name="azure-iot-certification-service"></a>Serviço de certificação Azure IoT

O serviço de certificação Azure IoT realiza um conjunto de testes de certificação quando submete um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para certificação através do portal [Azure Certified for IoT](#azure-certified-for-iot-portal). Antes de poder adicionar um dispositivo ao [catálogo de dispositivos Certified for IoT,](#certified-for-iot-device-catalog)o dispositivo tem de ser certificado.

## <a name="azure-iot-tools-extension"></a>Extensão de ferramentas Azure IoT

Azure IoT Tools é uma coleção de extensões no [código Visual Studio](#visual-studio-code) que o ajudam a interagir com o IoT Hub e desenvolver dispositivos IoT. Para o desenvolvimento de dispositivos IoT Plug e Play, ajuda-o a:

- Modelos e [interfaces](#interface) [de capacidade do dispositivo](#device-capability-model) de autor.
- Publique para [repositórios de modelos.](#model-repository)
- Gere código de esqueleto para implementar a aplicação do dispositivo.

## <a name="azure-iot-explorer-tool"></a>Ferramenta exploradora Azure IoT

O explorador Azure IoT é uma ferramenta gráfica que pode utilizar para interagir e testar os seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Depois de instalar a ferramenta na sua máquina local, pode usá-la para:

- Ver os dispositivos ligados ao seu [hub IoT](#azure-iot-hub).
- Ligue-se a um dispositivo IoT Plug and Play.
- Veja a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhar com [as propriedades do](#properties)dispositivo.
- Comandos do dispositivo [de chamada](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

O Hub IoT é um serviço gerido, alojado na cloud, que atua como um centro de mensagens para assegurar a comunicação bidirecional entre a aplicação IoT e os dispositivos que esta gere. [Os dispositivos IoT Plug e Play](#iot-plug-and-play-device) podem ligar-se a um hub IoT. Uma solução IoT utiliza um hub IoT para permitir:

- Dispositivos para enviar telemetria para uma solução baseada na nuvem.
- Uma solução baseada em nuvem para gerir dispositivos conectados.

## <a name="azure-iot-device-sdk"></a>Dispositivo Azure IoT SDK

Existem SDKs de dispositivo para vários idiomas que pode usar para construir aplicações de clientes ioT plug e play. Um dos requisitos para a [certificação](#device-certification) do dispositivo é que o código do cliente do dispositivo utiliza um dos SDKs do dispositivo Azure IoT.

## <a name="certified-for-iot-device-catalog"></a>Certificado para catálogo de dispositivos IoT

O [catálogo certificado para dispositivos IoT](https://catalog.azureiotsolutions.com/) lista [dispositivos IoT Plug e Play](#iot-plug-and-play-device) que passaram nos testes de certificação do [dispositivo.](#device-certification) Os [modelos de capacidade](#device-capability-model) do dispositivo para os dispositivos IoT Plug e Play no catálogo e publicados no repositório de modelos públicos.

## <a name="commands"></a>Comandos

Os comandos definidos numa [interface](#interface) representam métodos que podem ser executados no [gémeo digital](#digital-twin). Por exemplo, uma ordem para reiniciar um dispositivo.

## <a name="common-interface"></a>Interface comum

Espera-se que todos os [dispositivos IoT Plug e Play](#iot-plug-and-play-device) implementem [algumas interfaces comuns.](#interface) Por exemplo, a interface de informação do dispositivo define informações de hardware e sistema operativo sobre o dispositivo. [A certificação do dispositivo](#device-certification) requer que o seu dispositivo implemente várias interfaces comuns. Pode recuperar definições comuns de interface a partir do repositório de modelos públicos.

## <a name="company-model-repository"></a>Repositório de modelo da empresa

Uma organização pode usar um [repositório de modelos da](#model-repository) empresa como uma loja privada para modelos e [interfaces](#interface) [de capacidade do dispositivo.](#device-capability-model)

## <a name="connection-string"></a>Cadeia de ligação

Uma cadeia de ligação encapsula as informações necessárias para ligar a um ponto final. Uma cadeia de ligação normalmente inclui o endereço do ponto final e informações de segurança, mas os formatos de cadeia de ligação variam entre os serviços. Existem dois tipos de cadeia de ligação associadas ao serviço IoT Hub:

- As cordas de ligação do dispositivo permitem que [os dispositivos IoT Plug e Play](#iot-plug-and-play-device) se conectem aos pontos finais virados para o dispositivo num hub IoT. O código do cliente num dispositivo utiliza a cadeia de ligação para estabelecer uma ligação segura com um hub IoT.
- As cordas de ligação IoT Hub permitem que soluções e ferramentas de back-end se conectem de forma segura aos pontos finais voltados para o serviço num hub IoT. Estas soluções e ferramentas gerem o hub IoT e os dispositivos que lhe estão ligados.
- As cordas de ligação do repositório do modelo da empresa permitem que soluções e ferramentas de back-end se conectem de forma segura a um [repositório de modelos da Empresa](#company-model-repository). Estas soluções e ferramentas consomem ou gerem os [modelos](#device-capability-model) e [interfaces](#interface) de capacidade do dispositivo no repositório.

## <a name="device-capability-model"></a>Modelo de capacidade do dispositivo

Um modelo de capacidade do dispositivo descreve um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) e define o conjunto de [interfaces implementadas](#interface) pelo dispositivo. Um modelo de capacidade do dispositivo normalmente corresponde a um dispositivo físico, produto ou SKU. Utiliza-se o [Idioma de Definição Digital Twin](#digital-twin-definition-language) para definir um modelo de capacidade do dispositivo.

## <a name="device-certification"></a>Certificação do dispositivo

A certificação do dispositivo é o processo de certificação de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para que possa ser adicionado ao catálogo certificado para [dispositivos IoT](#certified-for-iot-device-catalog) e ao seu modelo de capacidade de [dispositivo](#device-capability-model) e [interfaces adicionadas](#interface) ao [repositório de modelos públicos.](#public-model-repository)

## <a name="device-developer"></a>Programador de dispositivo

Um desenvolvedor de dispositivos utiliza um [modelo de capacidade do dispositivo,](#device-capability-model) [interfaces,](#interface)e um [dispositivo Azure IoT SDK](#azure-iot-device-sdk) para implementar código para executar num [dispositivo IoT Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelação do dispositivo

Um [desenvolvedor de dispositivos](#device-developer) utiliza a [Linguagem de Definição Digital Twin](#digital-twin-definition-language) para modelar as capacidades de um dispositivo [IoT Plug and Play](#iot-plug-and-play-device). O modelo pode ser partilhado usando um repositório de modelos. Um desenvolvedor de dispositivos pode gerar código de dispositivo esqueleto a partir do modelo. Um [desenvolvedor de soluções](#solution-developer) pode configurar uma solução IoT a partir do modelo.

## <a name="device-provisioning-service"></a>Serviço de Fornecimento de Dispositivos

[A Azure IoT Central](#azure-iot-central) utiliza o Serviço de Provisionamento de Dispositivos para gerir todo o registo e ligação do dispositivo. Para obter mais informações, consulte [a conectividade do dispositivo no Azure IoT Central](../iot-central/core/concepts-get-connected.md). Também pode utilizar o Serviço de Provisionamento de Dispositivos para gerir o registo e ligação do dispositivo à sua solução IoT Hub baseada em IoT Hub. Para obter mais informações, consulte [dispositivos de provisionamento com o Serviço de Provisionamento de Dispositivos Azure IoT Hub](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registo de dispositivo

Antes de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) poder ligar-se a uma solução IoT, este deve ser registado com a solução. [A Azure IoT Central](#azure-iot-central) utiliza o [Serviço de Provisionamento de Dispositivos](#device-provisioning-service) para gerir o registo do dispositivo. Numa solução IoT personalizada, pode registar dispositivos com o seu hub IoT no portal Azure ou programático.

## <a name="device-first"></a>Dispositivo em primeiro lugar

[A Azure IoT Central](#azure-iot-central) suporta um cenário de registo e ligação de primeiro dispositivo. Neste cenário, um [dispositivo IoT Plug e Play](#iot-plug-and-play-device) pode ligar-se a uma aplicação IoT Central sem ser registado com antecedência. O registo ocorre automaticamente quando um dispositivo se liga pela primeira vez à aplicação.

## <a name="digital-twin"></a>Gémeo digital

Um gémeo digital é um modelo de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Um gémeo digital é modelado utilizando a [Linguagem de Definição Digital Twin.](#digital-twin-definition-language) Você pode usar o [dispositivo Azure IoT SDKs](#azure-iot-device-sdk) para interagir com gémeos digitais no tempo de execução. Por exemplo, pode definir um valor de propriedade num twin digital num dispositivo e o SDK comunica esta alteração à sua solução IoT na nuvem.

## <a name="digital-twin-change-events"></a>Eventos digitais de mudança de gémeos

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) está ligado a um hub [IoT,](#azure-iot-hub)o hub pode utilizar a sua capacidade de encaminhamento para enviar notificações de alterações digitais de gémeos. Por exemplo, sempre que um valor [de propriedade](#properties) muda num dispositivo, o IoT Hub pode enviar uma notificação para um ponto final, como uma fila de ônibus de serviço.

## <a name="digital-twin-definition-language"></a>Linguagem de definição de gémeos digitais

Um idioma para descrever modelos e interfaces para [dispositivos IoT Plug e Play](#iot-plug-and-play-device). Utilize a [Linguagem de Definição Digital Twin](https://aka.ms/DTDL) para descrever as capacidades de um gémeo [digital](#digital-twin) e permita que a plataforma IoT e as soluções IoT alavancam a semântica da entidade.

## <a name="digital-twin-route"></a>Rota digital twin

Uma rota criada num [hub IoT](#azure-iot-hub) para entregar [eventos digitais de mudança de gémeos](#digital-twin-change-events) para e ponto final, como uma fila de Bus de Serviço.

## <a name="interface"></a>Interface

Uma interface descreve as capacidades relacionadas que são implementadas por um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) ou por um gémeo [digital.](#digital-twin) Pode reutilizar interfaces em diferentes [modelos de capacidade do dispositivo.](#device-capability-model)

## <a name="iot-hub-query-language"></a>IoT Hub linguagem de consulta

A linguagem de consulta IoT Hub é usada para vários fins. Por exemplo, pode utilizar o idioma para procurar [dispositivos registados](#device-registration) no seu hub IoT ou refinar o comportamento [de encaminhamento digital de dois.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play dispositivo

Um dispositivo IoT Plug and Play é tipicamente um dispositivo de computação autónomo e de pequena escala que recolhe dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de capacidade do dispositivo.](#device-capability-model)  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitorização ambiental, ou um controlador para um sistema de irrigação de agricultura inteligente. Pode escrever uma solução IoT hospedada em nuvem para comandar, controlar e receber dados de dispositivos IoT Plug e Play. O [catálogo de dispositivos Azure Certified for IoT](#certified-for-iot-device-catalog) lista dispositivos IoT Plug e Play disponíveis. Cada dispositivo IoT Plug and Play do catálogo foi validado e tem um [modelo de capacidade do dispositivo.](#device-capability-model)

## <a name="microsoft-partner-center"></a>Centro de Parceiros Microsoft

[O Microsoft Partner Center](https://docs.microsoft.com/partner-center/) é onde a sua organização gere a sua relação de ponta a ponta com a Microsoft. Precisa de uma conta no Microsoft Partner Center antes de poder certificar o seu [dispositivo IoT Plug and Play](#iot-plug-and-play-device) no portal [Azure Certified for IoT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Deteção de modelos

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) se conecta a uma solução IoT, a solução pode descobrir as capacidades do dispositivo encontrando o modelo de capacidade do [dispositivo](#device-capability-model). Um dispositivo pode enviar o seu modelo de capacidade para a solução, ou a solução pode encontrar um modelo de capacidade do dispositivo num [repositório de modelos](#model-repository).

## <a name="model-repository"></a>Repositório de modelos

Um repositório modelo armazena [modelos](#device-capability-model) e [interfaces](#interface)de capacidade do dispositivo . Há um único [repositório de modelos públicos.](#public-model-repository) As organizações podem criar os seus próprios repositórios de modelos organizacionais.

## <a name="model-repository-rest-api"></a>Modelo repositório REST API

Uma API para gerir e interagir com repositórios de modelos. Por exemplo, pode utilizar a API para adicionar [modelos de capacidade do dispositivo](#device-capability-model) e procurar modelos de capacidade.

## <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos numa [interface](#interface) que representam algum estado de um gémeo digital. Pode declarar propriedades apenas para leitura ou legitável. As propriedades apenas de leitura, como o número de série, são definidas por código em execução no [dispositivo IoT Plug e Play.](#iot-plug-and-play-device)  As propriedades writable, como um limiar de alarme, são tipicamente definidas a partir da solução IoT baseada na nuvem.

## <a name="public-model-repository"></a>Repositório de modelo público

Existe um único repositório de modelos públicos que armazena [modelos](#device-capability-model) e [interfaces](#interface) de capacidade do dispositivo para [dispositivos certificados.](#device-certification) O repositório de modelos públicos também armazena definições [comuns de interface.](#common-interface)

## <a name="registration-id"></a>ID de inscrição

Um ID de registo identifica exclusivamente um dispositivo no [Serviço de Provisionamento de Dispositivos](#device-provisioning-service). Este ID não é o mesmo que o ID do dispositivo que é um identificador único para um dispositivo num [hub IoT](#azure-iot-hub).

## <a name="scope-id"></a>ID de Âmbito

O âmbito de identificação do âmbito identifica exclusivamente uma instância [do Serviço de Provisionamento de Dispositivos.](#device-provisioning-service)

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado

As assinaturas de acesso partilhado são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. A autenticação de assinatura de acesso partilhado tem dois componentes: uma política de acesso partilhado e uma assinatura de acesso partilhado (muitas vezes chamada de símbolo). Um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) utiliza uma assinatura de acesso partilhado para autenticar com um hub [IoT](#azure-iot-hub).

## <a name="solution-developer"></a>Desenvolvedor de soluções

Um desenvolvedor de soluções cria a solução traseira. Um desenvolvedor de soluções normalmente trabalha com recursos Azure, como [IoT Hub](#azure-iot-hub) e [repositórios de modelos,](#model-repository)ou trabalha com [a IoT Central.](#azure-iot-central)

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos numa [interface](#interface) representam medições. Estas medições são tipicamente valores como leituras de sensores que são enviadas pelo [dispositivo IoT Plug e Play](#iot-plug-and-play-device) como um fluxo de dados.

## <a name="visual-studio-code"></a>Código do Estúdio Visual

O código Visual Studio é um editor de código moderno disponível para várias plataformas. Extensões, como as do pacote [Azure IoT Tools,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) permitem personalizar o editor para suportar uma ampla gama de cenários de desenvolvimento.
