---
title: O que é o Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma solução de SaaS de ponta a ponta que você pode usar para criar e gerenciar sua solução de IoT personalizada. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: af5b16448a39e0106dbc04724607ff603483484c
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997749"
---
# <a name="what-is-azure-iot-central-preview-features"></a>O que é o Azure IoT Central (recursos de visualização)?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Os recursos de [plug and Play de IOT](../iot-pnp/overview-iot-plug-and-play.md) no Azure IOT Central estão atualmente em visualização pública. Não use um aplicativo de IoT Central de Plug and Play habilitado para IoT para cargas de trabalho de produção. Para ambientes de produção, use um aplicativo IoT central criado a partir de um modelo de aplicativo atual, geralmente disponível.

O Azure IoT Central é uma solução de software como serviço IoT totalmente gerenciada que facilita a criação de produtos que conectam os mundos físicos e digitais. Pode dar vida à sua visão de produto ligado, ao:

- Efetuar a derivação de novas informações de dispositivos ligados para oferecer melhores produtos e experiências aos seus clientes.
- Criar novas oportunidades de negócio para a sua organização.

IoT Central do Azure, em comparação com um projeto IoT típico:

- Reduz a carga de gerenciamento.
- Reduz custos operacionais e sobrecargas.
- Torna mais fácil personalizar seu aplicativo, ao mesmo tempo em que trabalha com:
  - Tecnologias líderes na indústria, como o [Hub IoT](https://azure.microsoft.com/services/iot-hub/) e o [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - Funcionalidades de segurança de nível empresarial, como a encriptação ponto a ponto.

O vídeo seguinte apresenta uma descrição geral do Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Este artigo descreve os IoT Central do Azure:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.

## <a name="known-issues"></a>Problemas conhecidos

> [!Note]
> Esses problemas conhecidos só se aplicam ao aplicativo de visualização IoT Central.

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
- Ele só está disponível em regiões selecionadas.

## <a name="personas"></a>Pessoas fictícias

A documentação do IoT Central do Azure refere-se a quatro pessoas que interagem com um aplicativo de IoT Central do Azure:

- Um _construtor_ é responsável por definir os tipos de dispositivos que ligam à aplicação e por personalizar a aplicação para o operador.
- Um _operador_ gere os dispositivos ligados à aplicação.
- Um _administrador_ é responsável por tarefas administrativas, como a gestão de utilizadores e funções na aplicação.
- Um_programador de dispositivos_ cria o código que é executado num dispositivo ligado à sua aplicação.

## <a name="create-your-azure-iot-central-application"></a>Criar a sua aplicação do Azure IoT Central

Como construtor, utilize o Azure IoT Central para criar uma solução de IoT personalizada e alojada na cloud para a sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Você pode implantar rapidamente um novo aplicativo de IoT Central do Azure e, em seguida, personalizá-lo para seus requisitos específicos em seu navegador. Como um construtor, você usa as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é o plano gráfico que define as características e o comportamento de um tipo de dispositivo, como:

- Telemetria que envia.
- As propriedades de negócio que um operador pode modificar.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação.
- Propriedades definidas por um operador que determina o comportamento do dispositivo.

Este modelo de dispositivo inclui:

- Um _modelo de funcionalidade de dispositivo_ que descreve os recursos que um dispositivo deve implementar, como a telemetria que ele envia e as propriedades que ele relata.
- Propriedades de nuvem que não são armazenadas no dispositivo.
- Personalizações, painéis e formulários que fazem parte do seu aplicativo IoT Central.

### <a name="create-device-templates"></a>Criar modelos de dispositivo

O [plug and Play de IOT](../iot-pnp/overview-iot-plug-and-play.md) permite que IOT central integre dispositivos sem escrever nenhum código de dispositivo inserido. No núcleo do Plug and Play IoT está um esquema de modelo de capacidade de dispositivo que descreve os recursos do dispositivo. Em um aplicativo IoT Central Preview, os modelos de dispositivo usam esses modelos de capacidade de dispositivo de Plug and Play IoT.

Como um construtor, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente seu modelo de capacidade de dispositivo no código do dispositivo.
- Importe um modelo de capacidade de dispositivo do [Catálogo de dispositivos Azure Certified para IOT](https://aka.ms/iotdevcat) e adicione quaisquer propriedades de nuvem, personalizações e painéis de que seu aplicativo IOT central precisa.
- Crie um modelo de capacidade de dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo e conecte seu dispositivo ao seu aplicativo IoT Central. IoT Central localiza o modelo de capacidade de dispositivo em um repositório e cria um modelo de dispositivo simples para você.
- Crie um modelo de capacidade de dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de capacidade do dispositivo para seu aplicativo IoT Central e, em seguida, adicione quaisquer propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT Central precisa.

Como um construtor, você pode usar IoT Central para gerar código para dispositivos de teste para validar os modelos de dispositivo.

### <a name="customize-the-ui"></a>Personalizar a IU

Como construtor, também pode personalizar a IU da aplicação do Azure IoT Central para os operadores que são responsáveis pela utilização diária da aplicação. As personalizações que um construtor pode fazer incluem:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="connect-your-devices"></a>Ligar os dispositivos

Depois de o construtor definir os tipos de dispositivos que podem ligar à aplicação, um programador de dispositivos cria o código que irá ser executado nos dispositivos. Como programador de dispositivos, utilize os [SDKs open source do Azure IoT](https://github.com/Azure/azure-iot-sdks) da Microsoft para criar o código do dispositivo. Estes SDKs incluem um suporte abrangente de linguagem, plataforma e protocolo para satisfazer as suas necessidades de ligar os dispositivos à sua aplicação do Azure IoT Central. Os SDKs ajudam a implementar os seguintes recursos de dispositivo:

- Criar uma ligação segura.
- Enviar telemetria.
- Comunicar o estado.
- Receber atualizações de configuração.

Para obter mais informações, veja a mensagem de blogue [Vantagens de utilizar os SDKs do Azure IoT e problemas a evitar caso não o faça](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Gerir a aplicação

As aplicações do Azure IoT Central são totalmente alojadas pela Microsoft, o que reduz a sobrecarga de gestão das suas aplicações.

Como operador, pode utilizar a aplicação do Azure IoT Central para gerir os dispositivos na sua solução do Azure IoT Central. Os operadores executam tarefas como:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

Como um construtor, você pode definir regras e ações personalizadas que operam sobre o streaming de dados de dispositivos conectados. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Os administradores gerenciam o acesso ao seu aplicativo com [funções e permissões de usuário](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma descrição geral do Azure IoT Central, eis os passos sugeridos:

- Compreender as diferenças entre o [Azure IoT Central e os aceleradores de soluções do Azure IoT](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Siga uma sequência de tutoriais que mostram como:
  - [Como construtor, criar um modelo de dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Como construtor, adicionar regras para automatizar a solução](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Como operador, monitorizar os dispositivos](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Como um operador, adicione um dispositivo à sua solução](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- Saiba mais sobre [plug and Play de IOT](../iot-pnp/overview-iot-plug-and-play.md)
