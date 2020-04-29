---
title: Glossário de termos - IoT Plug e Pré-visualização de jogo / Microsoft Docs
description: Conceitos - um glossário de termos comuns relativos ao IoT Plug e play Preview.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767066"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossário de termos para IoT Plug e Play Preview

Definições de termos comuns como utilizados nos artigos IoT Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified para portal IoT

Pode utilizar o site [Azure Certified para IoT](https://aka.ms/ACFI) para:

- Complete o [processo de certificação](#device-certification) para o seu [dispositivo IoT Plug and Play](#iot-plug-and-play-device).
- Encontre [modelos](#device-capability-model)de capacidade do dispositivo .
- Publique um modelo de capacidade de dispositivo para o [repositório de modelos públicos](#public-model-repository).

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI é uma ferramenta transversal de linha de comando para gerir recursos Azure. A extensão Azure IoT para o Azure CLI é uma ferramenta de linha de comando para interagir com [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Pode utilizar a extensão para:

- Ligue-se a um dispositivo IoT Plug and Play.
- Veja a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhe com [propriedades](#properties)do dispositivo.
- Comandos [commands](#commands)do dispositivo de chamada .
- Gerir [os repositórios de](#model-repository) [modelos, interfaces](#interface)e [modelos](#device-capability-model)de capacidade do dispositivo.

## <a name="azure-iot-central"></a>Azure IoT Central

O Azure IoT Central é uma solução de software como serviço totalmente gerida que facilita a ligação, monitorização e gestão dos seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Pode utilizar [modelos](#device-capability-model) de capacidade do dispositivo para configurar automaticamente uma aplicação IoT Central para monitorizar e gerir os seus dispositivos.

## <a name="azure-iot-certification-service"></a>Serviço de certificação Azure IoT

O serviço de certificação Azure IoT executa um conjunto de testes de certificação quando submete um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para certificação através do portal [Azure Certified for IoT](#azure-certified-for-iot-portal). Antes de poder adicionar um dispositivo ao [catálogo certificado para dispositivos IoT,](#certified-for-iot-device-catalog)o dispositivo deve ser certificado.

## <a name="azure-iot-tools-extension"></a>Extensão de ferramentas Azure IoT

A Azure IoT Tools é uma coleção de extensões no [código Do Estúdio Visual](#visual-studio-code) que o ajudam a interagir com o IoT Hub e a desenvolver dispositivos IoT. Para o desenvolvimento do dispositivo IoT Plug and Play, ajuda-o a:

- Modelos e [interfaces](#interface)de capacidade do [dispositivo](#device-capability-model) autor.
- Publique para [repositórios de modelos.](#model-repository)
- Gerar código de esqueleto para implementar a aplicação do dispositivo.

## <a name="azure-iot-explorer-tool"></a>Ferramenta exploradora Azure IoT

O explorador Azure IoT é uma ferramenta gráfica que pode usar para interagir e testar os seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Depois de instalar a ferramenta na sua máquina local, pode usá-la para:

- Veja os dispositivos ligados ao seu [hub IoT](#azure-iot-hub).
- Ligue-se a um dispositivo IoT Plug and Play.
- Veja a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhe com [propriedades](#properties)do dispositivo.
- Comandos [commands](#commands)do dispositivo de chamada .

## <a name="azure-iot-hub"></a>Azure IoT Hub

O Hub IoT é um serviço gerido, alojado na cloud, que atua como um centro de mensagens para assegurar a comunicação bidirecional entre a aplicação IoT e os dispositivos que esta gere. [Os dispositivos IoT Plug e Play](#iot-plug-and-play-device) podem ligar-se a um hub IoT. Uma solução IoT utiliza um hub IoT para permitir:

- Dispositivos para enviar telemetria para uma solução baseada em nuvem.
- Uma solução baseada na nuvem para gerir dispositivos conectados.

## <a name="azure-iot-device-sdk"></a>Dispositivo Azure IoT SDK

Existem SDKs de dispositivos para vários idiomas que pode usar para construir aplicações de cliente de ioT Plug e Play. Um dos requisitos para a [certificação](#device-certification) do dispositivo é que o código cliente do dispositivo utiliza um dos SDKs do dispositivo Azure IoT.

## <a name="certified-for-iot-device-catalog"></a>Certificado para catálogo de dispositivos IoT

O [catálogo certificado para dispositivos IoT](https://catalog.azureiotsolutions.com/) lista [dispositivos IoT Plug e Play](#iot-plug-and-play-device) que passaram nos testes de certificação do [dispositivo.](#device-certification) Os [modelos](#device-capability-model) de capacidade do dispositivo para os dispositivos IoT Plug e Play no catálogo e publicados no repositório de modelos públicos.

## <a name="commands"></a>Comandos

Os comandos definidos numa [interface](#interface) representam métodos que podem ser executados no [fio digital](#digital-twin). Por exemplo, um comando para reiniciar um dispositivo.

## <a name="common-interface"></a>Interface comum

Espera-se que todos os [dispositivos IoT Plug e Play](#iot-plug-and-play-device) implementem [algumas interfaces comuns](#interface). Por exemplo, a interface de informação do dispositivo define informações sobre hardware e sistema operativo sobre o dispositivo. [A certificação do dispositivo](#device-certification) requer que o seu dispositivo implemente várias interfaces comuns. Pode obter definições comuns de interface a partir do repositório de modelos públicos.

## <a name="company-model-repository"></a>Repositório modelo da empresa

Uma organização pode usar um [repositório](#model-repository) de modelos da empresa como uma loja privada para [modelos](#device-capability-model) e [interfaces](#interface)de capacidade de dispositivos.

## <a name="connection-string"></a>Cadeia de ligação

Uma cadeia de ligação encapsula as informações necessárias para ligar a um ponto final. Uma cadeia de ligação normalmente inclui o endereço do ponto final e informações de segurança, mas os formatos de cordas de ligação variam entre os serviços. Existem dois tipos de cadeia de ligação associadas ao serviço IoT Hub:

- As cordas de ligação do dispositivo permitem que [os dispositivos IoT Plug e Play](#iot-plug-and-play-device) se conectem aos pontos finais virados para o dispositivo num hub IoT. O código do cliente num dispositivo utiliza a cadeia de ligação para estabelecer uma ligação segura com um hub IoT.
- As cordas de ligação IoT Hub permitem soluções e ferramentas traseiras para se ligarem de forma segura aos pontos finais virados para o serviço num hub IoT. Estas soluções e ferramentas gerem o hub IoT e os dispositivos ligados ao mesmo.
- As cordas de ligação repositório do modelo da empresa permitem soluções e ferramentas de back-end para se ligarem de forma segura a um [repositório](#company-model-repository)de modelos da Empresa . Estas soluções e ferramentas consomem ou gerem os [modelos](#device-capability-model) e [interfaces](#interface) de capacidade do dispositivo no repositório.

## <a name="device-capability-model"></a>Modelo de capacidade do dispositivo

Um modelo de capacidade de dispositivo descreve um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) e define o conjunto de [interfaces implementadas](#interface) pelo dispositivo. Um modelo de capacidade do dispositivo normalmente corresponde a um dispositivo físico, produto ou SKU. Utiliza a Linguagem de [Definição Dupla Digital](#digital-twin-definition-language) para definir um modelo de capacidade do dispositivo.

## <a name="device-certification"></a>Certificação de dispositivos

A certificação do dispositivo é o processo de certificação de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) para que possa ser adicionado ao catálogo certificado para [dispositivos IoT](#certified-for-iot-device-catalog) e ao seu [modelo](#device-capability-model) de capacidade de dispositivo e [interfaces adicionados](#interface) ao [repositório](#public-model-repository)de modelos públicos.

## <a name="device-developer"></a>Programador de dispositivo

Um desenvolvedor de dispositivos utiliza um modelo de capacidade do [dispositivo,](#device-capability-model) [interfaces,](#interface)e um [dispositivo Azure IoT SDK](#azure-iot-device-sdk) para implementar código para executar num [dispositivo IoT Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelação de dispositivos

Um desenvolvedor de [dispositivos](#device-developer) utiliza o [Idioma de Definição Dupla Digital](#digital-twin-definition-language) para modelar as capacidades de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). O modelo pode ser partilhado usando um repositório modelo. Um desenvolvedor de dispositivos pode gerar código de dispositivo esqueleto a partir do modelo. Um desenvolvedor de [soluções](#solution-developer) pode configurar uma solução IoT a partir do modelo.

## <a name="device-provisioning-service"></a>Serviço de Provisionamento de Dispositivos

[A Azure IoT Central](#azure-iot-central) utiliza o Serviço de Provisionamento de Dispositivos para gerir todo o registo e ligação do dispositivo. Para mais informações, consulte [a conectividade do dispositivo em Azure IoT Central](../iot-central/core/concepts-get-connected.md). Também pode utilizar o Serviço de Provisionamento de Dispositivos para gerir o registo e a ligação do dispositivo à sua solução IoT Hub IoT. Para mais informações, consulte os dispositivos de provisionamento com o Serviço de Provisionamento de [Dispositivos Hub Azure IoT](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registo de dispositivo

Antes de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) poder ligar-se a uma solução IoT, deve ser registado com a solução. [A Azure IoT Central](#azure-iot-central) utiliza o Serviço de Provisionamento de [Dispositivos](#device-provisioning-service) para gerir o registo do dispositivo. Numa solução IoT personalizada, pode registar dispositivos com o seu hub IoT no portal Azure ou programáticamente.

## <a name="device-first"></a>Dispositivo-primeiro

[A Azure IoT Central](#azure-iot-central) suporta um cenário de registo e ligação de primeiro dispositivo. Neste cenário, um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) pode ligar-se a uma aplicação IoT Central sem ser registado com antecedência. O registo ocorre automaticamente quando um dispositivo se liga pela primeira vez à aplicação.

## <a name="digital-twin"></a>Gémeo digital

Um gémeo digital é um modelo de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Um gémeo digital é modelado usando a Linguagem de [Definição Gémea Digital.](#digital-twin-definition-language) Você pode usar os [SDKs do dispositivo Azure IoT](#azure-iot-device-sdk) para interagir com gémeos digitais no tempo de execução. Por exemplo, pode definir um valor de propriedade num gémeo digital num dispositivo e o SDK comunica esta alteração à sua solução IoT na nuvem.

## <a name="digital-twin-change-events"></a>Eventos digitais de mudança de gémeos

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) está ligado a um hub [IoT,](#azure-iot-hub)o hub pode usar a sua capacidade de encaminhamento para enviar notificações de alterações digitais de gémeos. Por exemplo, sempre que um valor de [propriedade](#properties) muda num dispositivo, o IoT Hub pode enviar uma notificação para um ponto final, como uma fila de ônibus de serviço.

## <a name="digital-twin-definition-language"></a>Linguagem de definição dupla digital

Um idioma para descrever modelos e interfaces para [dispositivos IoT Plug e Play](#iot-plug-and-play-device). Utilize a Linguagem de [Definição Gémea Digital](https://aka.ms/DTDL) para descrever as capacidades de um [gémeo digital](#digital-twin) e permitir que a plataforma IoT e as soluções IoT aproveitem a semântica da entidade.

## <a name="digital-twin-route"></a>Rota dupla digital

Uma rota criada num [hub IoT](#azure-iot-hub) para entregar [eventos de mudança de gémeos digitais](#digital-twin-change-events) para e ponto final, como uma fila de ônibus de serviço.

## <a name="interface"></a>Interface

Uma interface descreve capacidades relacionadas que são implementadas por um [dispositivo IoT Plug e Play](#iot-plug-and-play-device) ou twin [digital](#digital-twin). Pode reutilizar interfaces em diferentes modelos de capacidade do [dispositivo.](#device-capability-model)

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT

A linguagem de consulta IoT Hub é usada para múltiplos fins. Por exemplo, pode utilizar o idioma para procurar [dispositivos registados](#device-registration) com o seu hub IoT ou refinar o comportamento [de encaminhamento duplo digital.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>Dispositivo IoT Plug and Play

Um dispositivo IoT Plug and Play é tipicamente um dispositivo de computação de pequena escala e autónomo que recolhe dados ou controla outros dispositivos, e que executa software ou firmware que implementa um modelo de capacidade do [dispositivo](#device-capability-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitorização ambiental, ou um controlador para um sistema de irrigação de agricultura inteligente. Pode escrever uma solução IoT hospedada em nuvem para comandar, controlar e receber dados dos dispositivos IoT Plug e Play. O Catálogo de catálogos de [dispositivos Azure Certified para IoT](#certified-for-iot-device-catalog) lista os dispositivos IoT Plug e Play. Cada dispositivo IoT Plug and Play no catálogo foi validado e tem um modelo de capacidade de [dispositivo](#device-capability-model).

## <a name="microsoft-partner-center"></a>Centro de Parceiros Microsoft

O [Microsoft Partner Center](https://docs.microsoft.com/partner-center/) é onde a sua organização gere a sua relação de ponta a ponta com a Microsoft. Precisa de uma conta Microsoft Partner Center antes de poder certificar o seu [dispositivo IoT Plug e Play](#iot-plug-and-play-device) no portal [Azure Certified for IoT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Deteção de modelos

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) se conecta a uma solução IoT, a solução pode descobrir as capacidades do dispositivo encontrando o modelo de capacidade do [dispositivo](#device-capability-model). Um dispositivo pode enviar o seu modelo de capacidade para a solução, ou a solução pode encontrar um modelo de capacidade de dispositivo num [repositório de modelos.](#model-repository)

## <a name="model-repository"></a>Repositório modelo

Um modelo de repositório armazena modelos e [interfaces](#interface)de capacidade do [dispositivo.](#device-capability-model) Há um único [repositório de modelos públicos.](#public-model-repository) As organizações podem criar os seus próprios repositórios de modelos organizacionais.

## <a name="model-repository-rest-api"></a>Modelo repositório REST API

Uma API para gerir e interagir com repositórios de modelos. Por exemplo, pode utilizar a API para adicionar [modelos](#device-capability-model) de capacidade de dispositivo e procurar modelos de capacidade.

## <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos numa [interface](#interface) que representa algum estado de um gémeo digital. Pode declarar propriedades como apenas leitura ou repreensíveis. As propriedades apenas de leitura, como o número de série, são definidas por código que corre no [próprio dispositivo IoT Plug and Play.](#iot-plug-and-play-device)  Propriedades writáveis, como um limiar de alarme, são tipicamente definidas a partir da solução IoT baseada na nuvem.

## <a name="public-model-repository"></a>Repositório de modelopúblico

Existe um único repositório de modelos públicos que armazena modelos e [interfaces](#interface) de capacidade de [dispositivos](#device-capability-model) para [dispositivos certificados.](#device-certification) O repositório de modelos públicos também armazena definições comuns de [interface.](#common-interface)

## <a name="registration-id"></a>ID de registo

Um ID de registo identifica exclusivamente um dispositivo no Serviço de Provisionamento de [Dispositivos](#device-provisioning-service). Este ID não é o mesmo que o ID do dispositivo que é um identificador único para um dispositivo num [hub IoT](#azure-iot-hub).

## <a name="scope-id"></a>ID de Âmbito

O âmbito de identificação do âmbito identifica exclusivamente uma instância do Serviço de [Provisionamento de Dispositivos.](#device-provisioning-service)

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado

As assinaturas de acesso partilhado são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. A autenticação de assinatura de acesso partilhado tem dois componentes: uma política de acesso partilhado e uma assinatura de acesso partilhado (muitas vezes chamada de símbolo). Um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) utiliza uma assinatura de acesso partilhado para autenticar com um hub [IoT](#azure-iot-hub).

## <a name="solution-developer"></a>Desenvolvedor de soluções

Um desenvolvedor de soluções cria a solução na parte de trás. Um desenvolvedor de soluções normalmente trabalha com recursos Azure como [IoT Hub](#azure-iot-hub) e [repositórios de modelos,](#model-repository)ou trabalha com [a IoT Central.](#azure-iot-central)

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos numa [interface](#interface) representam medições. Estas medições são tipicamente valores como as leituras de sensores que são enviadas pelo [dispositivo IoT Plug and Play](#iot-plug-and-play-device) como um fluxo de dados.

## <a name="visual-studio-code"></a>Código do Estúdio Visual

O código Visual Studio é um editor de código moderno disponível para várias plataformas. Extensões, como as do pacote [De ferramentas Azure IoT,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) permitem personalizar o editor para suportar uma vasta gama de cenários de desenvolvimento.
