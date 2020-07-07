---
title: Instale e utilize o explorador Azure IoT ! Microsoft Docs
description: Instale a ferramenta exploradora Azure IoT e utilize-a para interagir com os dispositivos IoT Plug e Play Preview ligados ao meu hub IoT.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159188"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instale e utilize o explorador Azure IoT

O explorador Azure IoT é uma ferramenta gráfica para interagir e testar os seus dispositivos IoT Plug e Play Preview. Depois de instalar a ferramenta na sua máquina local, pode usá-la para ligar a um dispositivo. Pode utilizar a ferramenta para visualizar a telemetria que o dispositivo está a enviar, trabalhar com as propriedades do dispositivo e chamar comandos.

Este artigo mostra-lhe como:

- Instale e configuure a ferramenta exploradora Azure IoT.
- Utilize a ferramenta para interagir e testar os seus dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a ferramenta exploradora Azure IoT, precisa:

- Um centro de IoT Azure. Existem muitas formas de adicionar um hub IoT à sua subscrição Azure, como [criar um hub IoT utilizando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de ligação do hub IoT para executar a ferramenta exploradora Azure IoT. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um dispositivo registado no seu centro de IoT. Pode utilizar o seguinte comando Azure CLI para registar um dispositivo. Certifique-se de substituir os `{YourIoTHubName}` `{YourDeviceID}` espaços reservados pelos seus valores:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instalar explorador de IoT Azure

Vá ao [Azure IoT explorer lança](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para o lançamento mais recente. Faça o download e instale a versão mais recente da aplicação.

## <a name="use-azure-iot-explorer"></a>Use o explorador Azure IoT

Para um dispositivo, pode ligar o seu próprio dispositivo ou utilizar um dos nossos dispositivos simulados de amostra. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) para executar a amostra do dispositivo simulado.

### <a name="connect-to-your-hub"></a>Ligue-se ao seu hub

A primeira vez que dirige o explorador Azure IoT, é solicitado para a cadeia de ligação do seu hub IoT. Depois de adicionar a cadeia de ligação, selecione **Connect**. Pode utilizar as definições da ferramenta para mudar para outro hub IoT atualizando a cadeia de ligação.

A definição de modelo para um dispositivo IoT Plug and Play é armazenada no repositório público, num repositório da empresa ou no seu dispositivo conectado. Por predefinição, a ferramenta procura a definição do seu modelo no repositório de modelos públicos e no seu dispositivo conectado. Pode adicionar e remover fontes, ou configurar a prioridade das fontes em **Definições:**

Para adicionar uma fonte:

1. Ir para **Definições**.
1. Selecione **New** e escolha a sua fonte.
1. Se estiver a adicionar o repositório de modelos da sua empresa, forneça a cadeia de ligação.

Para remover uma fonte:

1. Ir para **Definições**.
1. Encontre a fonte que pretende remover.
1. Selecione **X** para removê-lo. Não é possível remover o repositório de modelos públicos porque as definições comuns de interface provêm deste repositório.

Alterar as prioridades de origem:

Pode arrastar e largar uma das fontes de definição do modelo para um ranking diferente na lista. Se houver um conflito, fontes de definição com rankings mais elevados sobrepõem-se a fontes com classificações mais baixas.

### <a name="view-devices"></a>Ver dispositivos

Depois de a ferramenta se ligar ao seu hub IoT, apresenta a página da lista **de Dispositivos** que lista as identidades do dispositivo registadas no seu hub IoT. Pode expandir qualquer entrada na lista para ver mais informações.

Na página da lista **de Dispositivos** pode:

- **Selecione Adicionar** para registar um novo dispositivo com o seu hub. Em seguida, introduza uma identificação do dispositivo. Utilize as definições predefinidos para gerar automaticamente teclas de autenticação e ativar a ligação ao seu hub.
- Selecione um dispositivo e, em seguida, **selecione Eliminar** para eliminar a identidade do dispositivo. Reveja os detalhes do dispositivo antes de concluir esta ação para ter a certeza de que está a eliminar a identidade do dispositivo certa.
- Consulta por `capabilityID` e `interfaceID` . Adicione o seu `capabilityID` ou como parâmetro para consultar os seus `interfaceID` dispositivos.

## <a name="interact-with-a-device"></a>Interaja com um dispositivo

Na página da lista **de Dispositivos,** selecione um valor na coluna **de identificação** do dispositivo para ver a página de detalhes do dispositivo registado. Para cada dispositivo existem duas secções: **Dispositivo** e **Digital Twin**.

### <a name="device"></a>Dispositivo

Esta secção inclui a **identidade**do dispositivo , **Dispositivo Twin,** **Telemetria,** **Método Direto** e separadores **de mensagens Cloud-to-device.**

- Pode visualizar e atualizar as informações de identidade do [dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md) no **separador identidade do Dispositivo.**
- Pode aceder ao dispositivo com informações [duplas](../iot-hub/iot-hub-devguide-device-twins.md) no **separador Device Twin.**
- Se um dispositivo estiver ligado e enviar dados ativamente, pode visualizar a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) no separador **Telemetria.**
- Pode ligar [diretamente](../iot-hub/iot-hub-devguide-direct-methods.md) para o dispositivo no **separador método Direto.**
- Pode enviar uma [mensagem nuvem-para-dispositivo](../iot-hub/iot-hub-devguide-messages-c2d.md) no separador **mensagens Cloud-to-device.**

### <a name="digital-twin"></a>Gémeo digital

Pode utilizar a ferramenta para visualizar a versão digital do dispositivo. Para um dispositivo IoT Plug and Play, todas as interfaces associadas ao modelo de capacidade do dispositivo são apresentadas nesta secção da ferramenta. Selecione uma interface para expandir os seus [primitivos IoT Plug e Play correspondentes](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

### <a name="interface"></a>Interface

Na página **Interface,** pode ver a definição JSON da interface.

#### <a name="properties"></a>Propriedades

Pode ver as propriedades apenas de leitura definidas numa interface na página **propriedades não escritas.** Pode atualizar as propriedades escritas definidas numa interface na página **propriedades writeable:**

1. Aceda à página **de propriedades writable.**
1. Clique na propriedade que gostaria de atualizar.
1. Introduza o novo valor para o imóvel.
1. Pré-visualizar a carga útil a enviar para o dispositivo.
1. Submeta a mudança.

Depois de submeter uma alteração, pode acompanhar o estado de atualização: **sincronização,** **sucesso**ou **erro**. Quando a sincronização estiver concluída, vê o novo valor do seu imóvel na coluna **Propriedade Reportada.** Se navegar para outras páginas antes de a sincronização terminar, a ferramenta ainda o notifica quando a atualização estiver concluída. Também pode utilizar o centro de notificação da ferramenta para ver o histórico de notificações.

#### <a name="commands"></a>Comandos

Para enviar um comando para um dispositivo, aceda à página **de Comandos:**

1. Na lista de comandos, expanda o comando que pretende acionar.
1. Introduza os valores necessários para o comando.
1. Pré-visualizar a carga útil a enviar para o dispositivo.
1. Submeta o comando.

#### <a name="telemetry"></a>Telemetria

Para ver a telemetria para a interface selecionada, aceda à sua página **de Telemetria.**

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como instalar e utilizar o explorador Azure IoT para interagir com os seus dispositivos IoT Plug e Play. Um próximo passo sugerido é aprender a instalar e utilizar a [extensão Azure CLI](./howto-install-pnp-cli.md).
