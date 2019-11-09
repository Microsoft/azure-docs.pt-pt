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
ms.openlocfilehash: 1269743d6c8354054a17b94d3470800a94180090
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893866"
---
# <a name="what-is-azure-iot-central-preview-features"></a>O que é o Azure IoT Central (recursos de visualização)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Os recursos de [plug and Play de IOT](../../iot-pnp/overview-iot-plug-and-play.md) no Azure IOT Central estão atualmente em visualização pública. Não use um aplicativo de IoT Central de Plug and Play habilitado para IoT para cargas de trabalho de produção. Para ambientes de produção, use um aplicativo IoT central criado a partir de um modelo de aplicativo atual, geralmente disponível.

O Azure IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo de desenvolvimento, gerenciamento e manutenção de soluções de IoT de nível empresarial. Optar por criar com o Azure IoT Central oferece a oportunidade de concentrar o tempo, o dinheiro e a energia ao transformar seus negócios com dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e continuamente em constante evolução.

A interface do usuário da Web permite que você monitore as condições do dispositivo, crie regras e gerencie milhões de dispositivos e seus dados ao longo de seu ciclo de vida. Além disso, ele permite que você atue em insights do dispositivo estendendo o IoT Intelligence em aplicativos de linha de negócios.

Este artigo descreve os IoT Central do Azure:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.
- Visão geral dos recursos de IoT Edge no IoT Central
- Como conectar seus dispositivos com o Azure IoT Edge Runtime ao seu aplicativo.


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
- Ele só está disponível nas regiões norte da Europa e centro dos EUA.
- Os modelos de funcionalidade do dispositivo devem ter todas as interfaces definidas embutidas no mesmo arquivo.

## <a name="personas"></a>Pessoas fictícias

A documentação do IoT Central do Azure refere-se a quatro pessoas que interagem com um aplicativo de IoT Central do Azure:

- Um _Solution Builder_ é responsável por definir os tipos de dispositivos que se conectam ao aplicativo e personalizando o aplicativo para o operador.
- Um _operador_ gere os dispositivos ligados à aplicação.
- Um _administrador_ é responsável por tarefas administrativas, como gerenciar [funções de usuário e permissões](howto-administer.md) no aplicativo.
- Um_programador de dispositivos_ cria o código que é executado num dispositivo ligado à sua aplicação.
- Um _desenvolvedor de dispositivo/módulo_ cria o código/módulo que é executado em um dispositivo conectado ao seu aplicativo.

## <a name="create-your-azure-iot-central-application"></a>Criar a sua aplicação do Azure IoT Central

Como construtor, utilize o Azure IoT Central para criar uma solução de IoT personalizada e alojada na cloud para a sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Você pode implantar rapidamente um novo aplicativo de IoT Central do Azure e, em seguida, personalizá-lo para seus requisitos específicos em seu navegador. Como um construtor, você usa as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é o plano gráfico que define as características e o comportamento de um tipo de dispositivo, como:

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

Os administradores gerenciam o acesso ao seu aplicativo com [funções e permissões de usuário](howto-administer.md).


## <a name="what-is-azure-iot-central-with-azure-iot-edge-preview-features"></a>O que é o Azure IoT Central com Azure IoT Edge (recursos de visualização)

IoT Central está expandindo seu portfólio ao dar suporte a dispositivos Azure IoT Edge. 

Agora, as empresas podem executar a inteligência de nuvem diretamente em dispositivos IoT gerenciados pelo Azure IoT Central. Esse novo recurso ajuda as empresas a se conectarem e gerenciarem Azure IoT Edge dispositivos que executam Azure IoT Edge tempo de execução, implantar módulos de software, publicar informações e executar ações em escala – tudo de dentro de IoT Central. 

[Visão geral de Azure IoT Edge](../../iot-edge/about-iot-edge.md)

### <a name="overview-of-iot-edge-capabilities-in-iot-central"></a>Visão geral dos recursos de IoT Edge no IoT Central

O runtime do Azure IoT Edge permite lógica personalizada e da cloud nos dispositivos IoT Edge. IoT Edge dispositivo é alimentado pelo tempo de execução e executa operações de gerenciamento e de comunicação. 

Azure IoT Edge Runtime executa as seguintes funções:

- Instalar e atualizar cargas de trabalho no dispositivo.
- Mantenha os padrões de segurança Azure IoT Edge no dispositivo.
- Verifique se os módulos IoT Edge estão sempre em execução.
- Relate a integridade do módulo para a nuvem para monitoramento remoto.
- Gerencie a comunicação entre dispositivos de folha downstream e um dispositivo IoT Edge, entre módulos em um dispositivo IoT Edge e entre um dispositivo IoT Edge e a nuvem.

![IoT Central Azure IoT Edge visão geral](./media/overview-iot-central/iotedge.png)

O Azure IoT Central executa as seguintes funções: 

- Azure IoT Edge suporte ao modelo de dispositivo que descreve os recursos que um dispositivo Azure IoT Edge deve implementar, como 
  1. recurso de upload de manifesto de implantação, que ajudará a gerenciar um manifesto para uma frota de dispositivos
  2. módulos, que serão executados no dispositivo Azure IoT Edge
  3. telemetria que cada módulo envia
  4. Propriedades cada módulo relatório e 
  5. comando cada módulo responde a
  6. Estabelecer relações entre Azure IoT Edge modelo de funcionalidade de dispositivo de gateway e o modelo de funcionalidade de dispositivo downstream
  7. Propriedades de nuvem que não são armazenadas no dispositivo Azure IoT Edge
  8. Personalizações, painéis e formulários que fazem parte do seu aplicativo IoT Central

  [Criar Azure IoT Edge modelo de dispositivo](./tutorial-define-edge-device-type.md)
   
- Provisionamento de dispositivos Azure IoT Edge em escala usando o serviço de provisionamento de dispositivos IoT do Azure
- Regras de gatilho e ações executadas em dispositivos Azure IoT Edge
- Crie painéis e análises 
- Exportação de dados contínuas de telemetria fluindo de dispositivos Azure IoT Edge

### <a name="azure-iot-edge-device-types-in-iot-central"></a>Azure IoT Edge tipos de dispositivo no IoT Central

O Azure IoT Central classifica Azure IoT Edge tipos de dispositivo da seguinte maneira:

- Azure IoT Edge dispositivo como um dispositivo de folha. Azure IoT Edge dispositivo pode ter dispositivos downstream, mas os dispositivos downstream não são provisionados no IoT Central
- Azure IoT Edge dispositivo como um dispositivo de gateway com dispositivos downstream. O dispositivo de gateway e os dispositivos downstream são provisionados no IoT Central

![IoT Central Azure IoT Edge visão geral](./media/overview-iot-central/gatewayedge.png)

### <a name="azure-iot-edge-patterns-supported-in-iot-central"></a>Padrões de Azure IoT Edge com suporte no IoT Central

- Azure IoT Edge como dispositivo de folha ![Azure IoT Edge como dispositivo de folha](./media/overview-iot-central/edgeasleafdevice.png) Azure IoT Edge dispositivo será provisionado no IoT Central e em todos os dispositivos downstream e sua telemetria será representada como proveniente do dispositivo Azure IoT Edge. Dispositivos downstream se qualquer um conectado ao dispositivo Azure IoT Edge não será provisionado no IoT Central. 

- Azure IoT Edge dispositivo de gateway conectado a dispositivos downstream com identidade ![Azure IoT Edge com a identidade do dispositivo downstream](./media/overview-iot-central/edgewithdownstreamdeviceidentity.png) Azure IoT Edge dispositivo será provisionado no IoT Central junto com os dispositivos downstream conectados ao dispositivo Azure IoT Edge. O tempo de execução de suporte para o provisionamento de dispositivos downstream por meio do gateway é planejado para o futuro. IoT Central irá acender o provisionamento da nuvem primeiro dos dispositivos downstream e as credenciais serão gerenciadas manualmente no dispositivo downstream. Primeiro provisionamento do dispositivo de dispositivos downstream planejados para os semestres futuros. 

- Azure IoT Edge dispositivo de gateway conectado a dispositivos downstream com identidade fornecida pelo gateway de borda ![Azure IoT Edge com dispositivo downstream sem identidade](./media/overview-iot-central/edgewithoutdownstreamdeviceidentity.png) o dispositivo Azure IoT Edge será provisionado no IoT Central junto com os dispositivos downstream conectados ao dispositivo Azure IoT Edge. O suporte ao tempo de execução do gateway que fornece identidade para dispositivos downstream e o provisionamento de dispositivos downstream é planejado para o futuro. Você pode colocar seu próprio módulo de tradução de identidade e IoT Central dará suporte a esse padrão. 

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Azure IoT Central, eis os passos sugeridos:

- Compreender as diferenças entre o [Azure IoT Central e os aceleradores de soluções do Azure IoT](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Saiba mais sobre [plug and Play de IOT](../../iot-pnp/overview-iot-plug-and-play.md)
- Saiba como [criar Azure IOT Edge modelo de dispositivo](./tutorial-define-edge-device-type.md)
