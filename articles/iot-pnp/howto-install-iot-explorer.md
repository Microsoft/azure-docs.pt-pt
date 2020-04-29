---
title: Instale e utilize o explorador Azure IoT [ Explorador De IoT] Microsoft Docs
description: Instale a ferramenta de explorador Azure IoT e use-a para interagir com os dispositivos IoT Plug e Play Preview ligados ao meu hub IoT.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159188"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instale e utilize o explorador Azure IoT

O explorador Azure IoT é uma ferramenta gráfica para interagir e testar os seus dispositivos IoT Plug e Play Preview. Depois de instalar a ferramenta na sua máquina local, pode usá-la para se ligar a um dispositivo. Pode utilizar a ferramenta para visualizar a telemetria que o dispositivo está a enviar, trabalhar com propriedades do dispositivo e chamar comandos.

Este artigo mostra-lhe como:

- Instale e configure a ferramenta exploradora Azure IoT.
- Utilize a ferramenta para interagir e testar os seus dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a ferramenta exploradora Azure IoT, precisa de:

- Um centro Azure IoT. Existem muitas formas de adicionar um hub IoT à sua subscrição Azure, como [criar um hub IoT utilizando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de ligação do hub IoT para executar a ferramenta de explorador Azure IoT. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um dispositivo registado no seu centro de IoT. Pode utilizar o seguinte comando Azure CLI para registar um dispositivo. Certifique-se de `{YourIoTHubName}` `{YourDeviceID}` substituir os espaços reservados e os seus valores:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instale explorador Azure IoT

Vá ao lançamento do [explorador Azure IoT](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para o lançamento mais recente. Descarregue e instale a versão mais recente da aplicação.

## <a name="use-azure-iot-explorer"></a>Use explorador Azure IoT

Para um dispositivo, pode ligar o seu próprio dispositivo ou utilizar um dos nossos dispositivos simulados de amostra. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) para executar a amostra do dispositivo simulado.

### <a name="connect-to-your-hub"></a>Ligue-se ao seu centro

A primeira vez que dirige o explorador Azure IoT, é solicitado para a corda de ligação do seu centro ioT. Depois de adicionar a corda de ligação, selecione **Connect**. Pode utilizar as definições da ferramenta para mudar para outro hub IoT atualizando a cadeia de ligação.

A definição de modelo para um dispositivo IoT Plug and Play é armazenada no repositório público, num repositório da empresa ou no seu dispositivo conectado. Por padrão, a ferramenta procura a definição do modelo no repositório de modelos públicos e no seu dispositivo conectado. Pode adicionar e remover fontes, ou configurar a prioridade das fontes em **Definições:**

Para adicionar uma fonte:

1. Ir para **Definições**.
1. Selecione **Novo** e escolha a sua fonte.
1. Se estiver a adicionar o repositório do modelo da empresa, forneça a cadeia de ligação.

Para remover uma fonte:

1. Ir para **Definições**.
1. Encontre a fonte que pretende remover.
1. Selecione **X** para removê-lo. Não é possível remover o repositório do modelo público porque as definições comuns de interface vêm deste repositório.

Alterar as prioridades de origem:

Pode arrastar e largar uma das fontes de definição do modelo para um ranking diferente na lista. Se houver um conflito, fontes de definição com classificações mais altas sobrepõem-se a fontes com classificações mais baixas.

### <a name="view-devices"></a>Ver dispositivos

Depois de a ferramenta se ligar ao seu hub IoT, apresenta a página da lista **de Dispositivos** que lista as identidades do dispositivo registadas com o seu hub IoT. Pode expandir qualquer entrada na lista para ver mais informações.

Na página da lista **de Dispositivos** pode:

- Selecione **Adicionar** para registar um novo dispositivo com o seu hub. Em seguida, introduza uma identificação do dispositivo. Utilize as definições predefinidas para gerar automaticamente chaves de autenticação e ativar a ligação ao seu hub.
- Selecione um dispositivo e, em seguida, selecione **Eliminar** para eliminar uma identidade do dispositivo. Reveja os detalhes do dispositivo antes de completar esta ação para ter a certeza de que está a apagar a identidade do dispositivo certo.
- Consulta por `capabilityID` `interfaceID`e . Adicione o `capabilityID` `interfaceID` seu ou como parâmetro para consultar os seus dispositivos.

## <a name="interact-with-a-device"></a>Interaja com um dispositivo

Na página da lista **de Dispositivos,** selecione um valor na coluna ID do **Dispositivo** para visualizar a página de detalhes do dispositivo registado. Para cada dispositivo existem duas secções: **Dispositivo** e **Digital Twin**.

### <a name="device"></a>Dispositivo

Esta secção inclui a Identidade do **Dispositivo,** **Dispositivo Twin,** **Telemetria,** **Método direto** e separadores **de mensagens Cloud-to-device.**

- Pode visualizar e atualizar as informações de identidade do [dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md) no separador **de identidade do Dispositivo.**
- Pode aceder à informação [de dois dispositivos](../iot-hub/iot-hub-devguide-device-twins.md) no separador **Device Twin.**
- Se um dispositivo estiver ligado e enviar dados ativamente, pode ver a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) no separador **Telemetria.**
- Pode ligar para um [método direto](../iot-hub/iot-hub-devguide-direct-methods.md) no separador método **Direct.**
- Pode enviar uma [mensagem cloud-to-device](../iot-hub/iot-hub-devguide-messages-c2d.md) no separador de **mensagens Cloud-to-device.**

### <a name="digital-twin"></a>Gémeo digital

Pode utilizar a ferramenta para visualizar a instância dupla digital do dispositivo. Para um dispositivo IoT Plug and Play, todas as interfaces associadas ao modelo de capacidade do dispositivo são apresentadas nesta secção da ferramenta. Selecione uma interface para expandir os seus [primitivos ioT](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)plug e play correspondentes .

### <a name="interface"></a>Interface

Na página **Interface,** pode ver a definição JSON da interface.

#### <a name="properties"></a>Propriedades

Pode visualizar as propriedades apenas para leitura definidas numa interface na página de **propriedades não reembolsáveis.** Pode atualizar as propriedades reempreensíveis definidas numa interface na página **de propriedades Writeable:**

1. Vá à página de **propriedades writáveis.**
1. Clique na propriedade que gostaria de atualizar.
1. Insira o novo valor para o imóvel.
1. Pré-visualizar a carga útil a enviar para o dispositivo.
1. Submeta a alteração.

Depois de submeter uma alteração, pode rastrear o estado da atualização: **sincronização,** **sucesso,** ou **erro**. Quando a sincronização estiver completa, você vê o novo valor da sua propriedade na coluna **Propriedade Reportada.** Se navegar para outras páginas antes de o sintetizador estar completo, a ferramenta ainda o notifica quando a atualização estiver completa. Também pode utilizar o centro de notificação da ferramenta para ver o histórico de notificações.

#### <a name="commands"></a>Comandos

Para enviar um comando para um dispositivo, vá à página **comandos:**

1. Na lista de comandos, expanda o comando que quer desencadear.
1. Introduza os valores necessários para o comando.
1. Pré-visualizar a carga útil a enviar para o dispositivo.
1. Submeta o comando.

#### <a name="telemetry"></a>Telemetria

Para ver a telemetria para a interface selecionada, vá à sua página de **Telemetria.**

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a instalar e utilizar o explorador Azure IoT para interagir com os seus dispositivos IoT Plug and Play. Um próximo passo sugerido é aprender a instalar e utilizar a [extensão Azure CLI](./howto-install-pnp-cli.md).
