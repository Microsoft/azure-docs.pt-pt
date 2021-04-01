---
title: Instale e utilize o explorador Azure IoT | Microsoft Docs
description: Instale a ferramenta exploradora Azure IoT e utilize-a para interagir com dispositivos IoT Plug e Play ligados ao hub IoT. Embora este artigo se concentre em trabalhar com dispositivos IoT Plug e Play, pode utilizar a ferramenta com qualquer dispositivo ligado ao seu hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: contperf-fy21q2
ms.openlocfilehash: b0a86a32148086a3b644d0bf147d12be0d812536
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97030320"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instale e utilize o explorador Azure IoT

O explorador Azure IoT é uma ferramenta gráfica para interagir com dispositivos ligados ao seu hub IoT. Este artigo centra-se na utilização da ferramenta para testar os seus dispositivos IoT Plug e Play. Depois de instalar a ferramenta na sua máquina local, pode usá-la para ligar a um hub. Pode utilizar a ferramenta para visualizar a telemetria que os dispositivos estão a enviar, trabalhar com as propriedades do dispositivo e invocar comandos.

Este artigo mostra-lhe como:

- Instale e configuure a ferramenta exploradora Azure IoT.
- Utilize a ferramenta para interagir e testar os seus dispositivos IoT Plug e Play.

Para obter informações mais gerais sobre a utilização da ferramenta, consulte a [leitura](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)do GitHub .

Para utilizar a ferramenta exploradora Azure IoT, precisa:

- Um centro de IoT Azure. Existem muitas formas de adicionar um hub IoT à sua subscrição Azure, como [criar um hub IoT utilizando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de ligação do hub IoT para executar a ferramenta exploradora Azure IoT. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um dispositivo registado no seu centro de IoT. Pode utilizar o IoT Explorer para criar e gerir registos de dispositivos no seu Hub IoT.

## <a name="install-azure-iot-explorer"></a>Instalar explorador de IoT Azure

Vá ao [Azure IoT explorer lança](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para o lançamento mais recente. Faça o download e instale a versão mais recente da aplicação.

>[!Important]
> Atualizar para a versão 0.13.x para resolver os modelos de qualquer repositório baseado em [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)

## <a name="use-azure-iot-explorer"></a>Use o explorador Azure IoT

Para um dispositivo, pode ligar o seu próprio dispositivo ou utilizar um dos dispositivos simulados da amostra. Para alguns dispositivos simulados, por exemplo, escritos em diferentes idiomas, consulte a [aplicação de dispositivo ioT plug e play de amostra para](quickstart-connect-device.md) o arranque rápido do IoT Hub.

### <a name="connect-to-your-hub"></a>Ligue-se ao seu hub

A primeira vez que dirige o explorador Azure IoT, é solicitado para a cadeia de ligação do seu hub IoT. Depois de adicionar a cadeia de ligação, selecione **Connect**. Pode utilizar as definições da ferramenta para mudar para outro hub IoT atualizando a cadeia de ligação.

A definição de modelo para um dispositivo IoT Plug and Play é armazenada no repositório público, no dispositivo conectado ou numa pasta local. Por predefinição, a ferramenta procura a definição do seu modelo no repositório público e no seu dispositivo conectado. Pode adicionar e remover fontes, ou configurar a prioridade das fontes em **Definições:**

Para adicionar uma fonte:

1. Ir para **as definições de plug/ioT em casa/IoT**
2. **Selecione Adicionar** e escolher a sua fonte, a partir de um repositório ou pasta local.

Para remover uma fonte:

1. Ir para **as definições de plug/ioT em casa/IoT**
2. Encontre a fonte que pretende remover.
3. Selecione **X** para removê-lo.

Alterar as prioridades de origem:

Pode arrastar e largar uma das fontes de definição do modelo para um ranking diferente na lista.

### <a name="view-devices"></a>Ver dispositivos

Depois de a ferramenta se ligar ao seu hub IoT, apresenta a página da lista **de Dispositivos** que lista as identidades do dispositivo registadas no seu hub IoT. Pode selecionar qualquer entrada na lista para ver mais informações.

Na página da lista **de Dispositivos** pode:

- Selecione **Novo** para registar um novo dispositivo com o seu hub. Em seguida, introduza uma identificação do dispositivo. Utilize as definições predefinidos para gerar automaticamente teclas de autenticação e ativar a ligação ao seu hub.
- Selecione um dispositivo e, em seguida, **selecione Eliminar** para eliminar a identidade do dispositivo. Reveja os detalhes do dispositivo antes de concluir esta ação para ter a certeza de que está a eliminar a identidade do dispositivo certa.

## <a name="interact-with-a-device"></a>Interaja com um dispositivo

Na página da lista **de Dispositivos,** selecione um valor na coluna **de identificação** do dispositivo para ver a página de detalhes do dispositivo registado. Para cada dispositivo existem duas secções: **Dispositivo** e **Digital Twin**.

### <a name="device"></a>Dispositivo

Esta secção inclui a Identidade do **Dispositivo**, **Dispositivo Twin,** **Telemetria,** **Método Direto,** **Mensagem Nuvem-dispositivo,** Separadores **de Identidade do Módulo.**

- Pode visualizar e atualizar as informações de identidade do [dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md) no **separador identidade do Dispositivo.**
- Pode aceder ao dispositivo com informações [duplas](../iot-hub/iot-hub-devguide-device-twins.md) no **separador Device Twin.**
- Se um dispositivo estiver ligado e enviar dados ativamente, pode visualizar a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) no separador **Telemetria.**
- Pode ligar [diretamente](../iot-hub/iot-hub-devguide-direct-methods.md) para o dispositivo no **separador método Direto.**
- Pode enviar uma [mensagem nuvem-para-dispositivo](../iot-hub/iot-hub-devguide-messages-c2d.md) no separador **mensagens Cloud-to-device.**
- Pode aceder à informação de twin módulo do [módulo.](../iot-hub/iot-hub-devguide-module-twins.md)

### <a name="iot-plug-and-play-components"></a>Componentes IoT Plug e Play

Se o dispositivo estiver ligado ao hub utilizando um **Model ID,** a ferramenta mostra o separador **de componentes IoT Plug and Play** onde pode ver o **ID** do modelo .

Se o **ID** do Modelo estiver disponível numa das fontes configuradas - Repo Público ou Pasta Local, a lista de componentes é apresentada. A seleção de um componente mostra as propriedades, comandos e telemetria disponíveis.

Na página **'Componente',** pode ver as propriedades apenas de leitura, atualizar propriedades writable, invocar comandos e ver as mensagens de telemetria produzidas por este componente.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Ver componentes no explorador Azure IoT":::

#### <a name="properties"></a>Propriedades

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Ver propriedades no explorador Azure IoT":::

Pode ver as propriedades apenas de leitura definidas numa interface no **separador Propriedades (apenas para leitura).** Pode atualizar as propriedadesificáveis definidas numa interface no **separador Propriedades (writable):**

1. Aceda ao **separador Propriedades (writable).**
1. Clique na propriedade que gostaria de atualizar.
1. Introduza o novo valor para o imóvel.
1. Pré-visualizar a carga útil a enviar para o dispositivo.
1. Submeta a mudança.

Depois de submeter uma alteração, pode acompanhar o estado de atualização: **sincronização,** **sucesso** ou **erro**. Quando a sincronização estiver concluída, vê o novo valor do seu imóvel na coluna **Propriedade Reportada.** Se navegar para outras páginas antes de a sincronização terminar, a ferramenta ainda o notifica quando a atualização estiver concluída. Também pode utilizar o centro de notificação da ferramenta para ver o histórico de notificações.

#### <a name="commands"></a>Comandos

Para enviar um comando para um dispositivo, aceda ao separador **Comandos:**

1. Na lista de comandos, expanda o comando que pretende acionar.
1. Introduza os valores necessários para o comando.
1. Pré-visualizar a carga útil a enviar para o dispositivo.
1. Submeta o comando.

#### <a name="telemetry"></a>Telemetria

Para visualizar a telemetria para a interface selecionada, aceda ao separador **Telemetria.**

#### <a name="known-issues"></a>Problemas Conhecidos

Para obter uma lista das funcionalidades IoT suportadas pela versão mais recente da ferramenta, consulte a [lista de funcionalidades.](https://github.com/Azure/azure-iot-explorer/wiki)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como instalar e utilizar o explorador Azure IoT para interagir com os seus dispositivos IoT Plug e Play. Um próximo passo sugerido é aprender a [instalar e utilizar as ferramentas de autoria DTDL](howto-use-dtdl-authoring-tools.md).
