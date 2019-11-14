---
title: O que é o Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT e ajuda a reduzir a carga e o custo das operações de gerenciamento de IoT e do desenvolvimento. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 5e84b8777fc7671a19b6d8974f1309eb5af35bd3
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048017"
---
# <a name="what-is-azure-iot-central-preview-features"></a>O que é o Azure IoT Central (recursos de visualização)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Os recursos de [plug and Play de IOT](../../iot-pnp/overview-iot-plug-and-play.md) no Azure IOT Central estão atualmente em visualização pública. Não use um [modelo de aplicativo](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) de IOT central plug and Play habilitado para IOT para cargas de trabalho de produção. Para ambientes de produção, use um aplicativo IoT central criado a partir de um [modelo de aplicativo](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)atual, geralmente disponível.

IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo de desenvolvimento, gerenciamento e manutenção de soluções de IoT de nível empresarial. Optar por criar com o IoT Central oferece a oportunidade de concentrar o tempo, o dinheiro e a energia ao transformar seus negócios com dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e continuamente em constante evolução.

A interface do usuário da Web permite que você monitore as condições do dispositivo, crie regras e gerencie milhões de dispositivos e seus dados ao longo de seu ciclo de vida. Além disso, ele permite que você atue em insights do dispositivo estendendo o IoT Intelligence em aplicativos de linha de negócios.

Este artigo descreve IoT Central:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.
- Azure IoT Edge recursos no IoT Central.
- Como conectar seus dispositivos com o Azure IoT Edge Runtime ao seu aplicativo.

## <a name="known-issues"></a>Problemas conhecidos

> [!Note]
> Esses problemas conhecidos só se aplicam aos aplicativos de visualização IoT Central.

- As regras não dão suporte a todas as ações (somente email).
- Para tipos complexos – regras, análise e grupos de dispositivos não têm suporte.
- A exportação de dados contínua não dá suporte ao formato Avro (incompatibilidade).
- Os dispositivos simulados não dão suporte a todos os tipos complexos.
- Não há suporte para geojson no momento.
- O bloco de mapa não tem suporte no momento.
- Os trabalhos não dão suporte a tipos complexos.
- Não há suporte para tipos de esquema de matriz.
- Não há suporte para exportação de modelo de aplicativo e cópia de aplicativo.
- Somente o SDK do dispositivo C e os SDKs do dispositivo e do serviço node. js têm suporte.
- Ele só está disponível nas regiões norte da Europa e centro dos EUA.
- Os modelos de funcionalidade do dispositivo devem ter todas as interfaces definidas embutidas no mesmo arquivo.

## <a name="personas"></a>Pessoas fictícias

A documentação IoT Central refere-se a quatro pessoas que interagem com um aplicativo IoT Central:

- Um _Solution Builder_ é responsável por definir os tipos de dispositivos que se conectam ao aplicativo e personalizando o aplicativo para o operador.
- Um _operador_ gere os dispositivos ligados à aplicação.
- Um _administrador_ é responsável por tarefas administrativas, como gerenciar [funções de usuário e permissões](howto-administer.md) no aplicativo.
- Um _desenvolvedor de dispositivos_ cria o código que é executado em um dispositivo ou IOT Edge módulo conectado ao seu aplicativo.

## <a name="create-your-iot-central-application"></a>Criar seu aplicativo IoT Central

Como um Solution Builder, você usa IoT Central para criar uma solução de IoT personalizada e hospedada na nuvem para sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Você pode implantar rapidamente um novo aplicativo IoT Central e, em seguida, personalizá-lo para seus requisitos específicos em seu navegador. Como um construtor de soluções, você usa as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é o plano gráfico que define as características e o comportamento de um tipo de dispositivo, como:

- Telemetria que envia.
- As propriedades de negócio que um operador pode modificar.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação.
- Propriedades, que um operador define, que determinam o comportamento do dispositivo.

Este modelo de dispositivo inclui:

- Um _modelo de funcionalidade de dispositivo_ que descreve os recursos que um dispositivo deve implementar, como a telemetria que ele envia e as propriedades que ele relata.
- Propriedades de nuvem que não são armazenadas no dispositivo.
- Personalizações, painéis e formulários que fazem parte do seu aplicativo IoT Central.

### <a name="create-device-templates"></a>Criar modelos de dispositivo

O [plug and Play de IOT](../../iot-pnp/overview-iot-plug-and-play.md) permite que IOT central integre dispositivos sem escrever nenhum código de dispositivo inserido. No núcleo do Plug and Play IoT, é um esquema de modelo de capacidade de dispositivo que descreve os recursos do dispositivo. Em um aplicativo IoT Central Preview, os modelos de dispositivo usam esses modelos de capacidade de dispositivo de Plug and Play IoT.

Como um Solution Builder, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente seu modelo de capacidade de dispositivo no código do dispositivo.
- Importe um modelo de capacidade de dispositivo do [Catálogo de dispositivos Azure Certified para IOT](https://aka.ms/iotdevcat) e adicione quaisquer propriedades de nuvem, personalizações e painéis de que seu aplicativo IOT central precisa.
- Crie um modelo de capacidade de dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo e conecte seu dispositivo ao seu aplicativo IoT Central. IoT Central localiza o modelo de capacidade de dispositivo em um repositório e cria um modelo de dispositivo simples para você.
- Crie um modelo de capacidade de dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de capacidade do dispositivo para seu aplicativo IoT Central e, em seguida, adicione quaisquer propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT Central precisa.

Como um Solution Builder, você pode usar IoT Central para gerar código para dispositivos de teste para validar os modelos de dispositivo.

### <a name="customize-the-ui"></a>Personalizar a IU

Como um Solution Builder, você também pode personalizar a interface do usuário do aplicativo IoT Central para os operadores responsáveis pelo uso diário do aplicativo. As personalizações que um Solution Builder pode fazer incluem:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="connect-your-devices"></a>Ligar os dispositivos

Depois de o construtor definir os tipos de dispositivos que podem ligar à aplicação, um programador de dispositivos cria o código que irá ser executado nos dispositivos. Como programador de dispositivos, utilize os [SDKs open source do Azure IoT](https://github.com/Azure/azure-iot-sdks) da Microsoft para criar o código do dispositivo. Esses SDKs têm amplo suporte a linguagem, plataforma e protocolo para atender às suas necessidades de conexão de dispositivos ao seu aplicativo IoT Central. Os SDKs ajudam a implementar os seguintes recursos de dispositivo:

- Criar uma ligação segura.
- Enviar telemetria.
- Comunicar o estado.
- Receber atualizações de configuração.

Para obter mais informações, veja a mensagem de blogue [Vantagens de utilizar os SDKs do Azure IoT e problemas a evitar caso não o faça](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

### <a name="azure-iot-edge-devices"></a>Dispositivos do IoT Edge do Azure

Além de dispositivos criados usando os [SDKs do IOT do Azure](https://github.com/Azure/azure-iot-sdks), você também pode conectar [Azure IOT Edge dispositivos](../../iot-edge/about-iot-edge.md) a um aplicativo IOT central. Azure IoT Edge permite que você execute a inteligência de nuvem e a lógica personalizada diretamente em dispositivos IoT gerenciados pelo IoT Central. O tempo de execução do IoT Edge permite que você:

- Instalar e atualizar cargas de trabalho no dispositivo.
- Mantenha os padrões de segurança Azure IoT Edge no dispositivo.
- Verifique se os módulos IoT Edge estão sempre em execução.
- Relate a integridade do módulo para a nuvem para monitoramento remoto.
- Gerencie a comunicação entre dispositivos de folha downstream e um dispositivo IoT Edge, entre módulos em um dispositivo IoT Edge e entre um dispositivo IoT Edge e a nuvem.

Para obter mais informações, consulte [Azure IOT Edge dispositivos e IOT central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Gerir a aplicação

IoT Central aplicativos são totalmente hospedados pela Microsoft, o que reduz a sobrecarga de administração do gerenciamento de seus aplicativos.

Como um operador, você usa o aplicativo IoT Central para gerenciar os dispositivos em sua solução de IoT Central. Os operadores executam tarefas como:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

Como um construtor de soluções, você pode definir regras e ações personalizadas que operam sobre o streaming de dados de dispositivos conectados. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Os administradores gerenciam o acesso ao seu aplicativo com [funções e permissões de usuário](howto-administer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma visão geral de IoT Central, aqui estão as próximas etapas sugeridas:

- Entenda as diferenças entre [IOT central e os aceleradores de solução de IOT do Azure](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Saiba mais sobre [plug and Play de IOT](../../iot-pnp/overview-iot-plug-and-play.md)
- Saiba como [criar Azure IOT Edge modelo de dispositivo](./tutorial-define-edge-device-type.md)
