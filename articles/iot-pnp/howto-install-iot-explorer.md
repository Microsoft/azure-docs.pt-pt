---
title: Instalar e usar o Azure IoT Explorer | Microsoft Docs
description: Instale a ferramenta do Azure IoT Explorer e use-a para interagir com os dispositivos de visualização de Plug and Play de IoT conectados ao meu Hub IoT.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: f59e449589c7f3027dc8a9daf9d8d12f04831dd7
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960576"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalar e usar o Azure IoT Explorer

O Azure IoT Explorer é uma ferramenta gráfica para interagir com e testar seus dispositivos IoT Plug and Play Preview. Depois de instalar a ferramenta em seu computador local, você pode usá-la para se conectar a um dispositivo. Você pode usar a ferramenta para exibir a telemetria que o dispositivo está enviando, trabalhar com propriedades de dispositivo e chamar comandos.

Este artigo mostra como:

- Instale e configure a ferramenta do Azure IoT Explorer.
- Use a ferramenta para interagir com e testar seus dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a ferramenta do Azure IoT Explorer, você precisa de:

- Um hub IoT do Azure. Há várias maneiras de adicionar um hub IoT à sua assinatura do Azure, como [a criação de um hub IOT usando o CLI do Azure](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de conexão do Hub IoT para executar a ferramenta do Azure IoT Explorer. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um dispositivo registrado em seu hub IoT. Você pode usar o comando CLI do Azure a seguir para registrar um dispositivo. Certifique-se de substituir os espaços reservados `{YourIoTHubName}` e `{YourDeviceID}` pelos valores:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Vá para [versões do Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para a versão mais recente. Baixe e instale a versão mais recente do aplicativo.

## <a name="use-azure-iot-explorer"></a>Usar o Azure IoT Explorer

Para um dispositivo, você pode conectar seu próprio dispositivo ou usar um dos nossos dispositivos simulados de exemplo. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) para executar o exemplo de dispositivo simulado.

### <a name="connect-to-your-hub"></a>Conectar-se ao seu hub

Na primeira vez que você executar o Azure IoT Explorer, sua cadeia de conexão do Hub IoT será solicitada. Depois de adicionar a cadeia de conexão, selecione **conectar**. Você pode usar as configurações da ferramenta para alternar para outro hub IoT atualizando a cadeia de conexão.

A definição de modelo para um dispositivo de Plug and Play IoT é armazenada no repositório público, em um repositório da empresa ou em seu dispositivo conectado. Por padrão, a ferramenta procura a definição do modelo no repositório de modelo público e seu dispositivo conectado. Você pode adicionar e remover fontes ou configurar a prioridade das fontes em **configurações**:

Para adicionar uma fonte:

1. Aceda a **definições**.
1. Selecione **novo** e escolha sua fonte.
1. Se você estiver adicionando o repositório de modelos da empresa, forneça a cadeia de conexão.

Para remover uma fonte:

1. Aceda a **definições**.
1. Localize a origem que você deseja remover.
1. Selecione **X** para removê-lo. Não é possível remover o repositório de modelos públicos porque as definições de interface comuns são provenientes desse repositório.

Alterar as prioridades de origem:

Você pode arrastar e soltar uma das fontes de definição de modelo para uma classificação diferente na lista. Se houver um conflito, as fontes de definição com classificações maiores substituirão as fontes pelas classificações mais baixas.

### <a name="view-devices"></a>Ver dispositivos

Depois que a ferramenta se conectar ao Hub IoT, ela exibirá a página de lista de **dispositivos** que lista as identidades de dispositivo registradas com o Hub IOT. Você pode expandir qualquer entrada na lista para ver mais informações.

Na página lista de **dispositivos** , você pode:

- Selecione **Adicionar** para registrar um novo dispositivo com o Hub. Em seguida, insira uma ID do dispositivo. Use as configurações padrão para gerar automaticamente chaves de autenticação e habilitar a conexão com o Hub.
- Selecione um dispositivo e, em seguida, selecione **excluir** para excluir uma identidade de dispositivo. Examine os detalhes do dispositivo antes de concluir esta ação para certificar-se de que você está excluindo a identidade correta do dispositivo.
- Consulta por `capabilityID` e `interfaceID`. Adicione seu `capabilityID` ou `interfaceID` como um parâmetro para consultar seus dispositivos.

## <a name="interact-with-a-device"></a>Interagir com um dispositivo

Na página lista de **dispositivos** , selecione um valor na coluna **ID do dispositivo** para exibir a página de detalhes do dispositivo registrado. Para o dispositivo, há duas seções: **Dispositivo** e e/ou **digital**.

### <a name="device"></a>Dispositivo

Esta seção inclui as guias **identidade do dispositivo**, **dispositivo**e **telemetria** .

- Você pode exibir e atualizar as informações de [identidade do dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md) na guia **identidade do dispositivo** .
- Você pode acessar as informações de FileUp do [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) na guia de **dispositivo de entrelaçamento** .
- Se um dispositivo estiver conectado e enviando dados ativamente, você poderá exibir a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) na guia **telemetria** .

### <a name="digital-twin"></a>Entrelaçar digital

Você pode usar a ferramenta para exibir a instância de cópia digital do dispositivo. Para um dispositivo de Plug and Play IoT, todas as interfaces associadas ao modelo de capacidade de dispositivo são exibidas neste artigo. Selecione uma interface para expandir seus [primitivos de plug and Play IOT](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)correspondentes.

#### <a name="properties"></a>properties

Você pode exibir as propriedades somente leitura definidas em uma interface na página **Propriedades** . Você pode atualizar as propriedades graváveis definidas em uma interface na página de **Propriedades graváveis** .

1. Vá para a página de **Propriedades graváveis** .
1. Clique na propriedade que você deseja atualizar.
1. Insira o novo valor para a propriedade.
1. Visualize o conteúdo a ser enviado para o dispositivo.
1. Envie a alteração.

Depois de enviar uma alteração, você pode acompanhar o status da atualização: **sincronização**, **êxito**ou **erro**. Quando a sincronização estiver concluída, você verá o novo valor da propriedade na coluna **Propriedade relatada** . Se você navegar para outras páginas antes de a sincronização ser concluída, a ferramenta ainda o notificará quando a atualização for concluída. Você também pode usar o centro de notificações da ferramenta para ver o histórico de notificações.

#### <a name="commands"></a>Comandos

Para enviar um comando para um dispositivo, vá para a página **comandos** :

1. Na lista de comandos, expanda o comando que você deseja disparar.
1. Insira os valores necessários para o comando.
1. Visualize o conteúdo a ser enviado para o dispositivo.
1. Envie o comando.

#### <a name="telemetry"></a>Telemetria

Para exibir a telemetria da interface selecionada, acesse sua página de **telemetria** .

## <a name="next-steps"></a>Passos seguintes

Neste artigo de instruções, você aprendeu a instalar e usar o Azure IoT Explorer para interagir com seus dispositivos de Plug and Play de IoT. Uma próxima etapa sugerida é aprender a [instalar e usar a extensão CLI do Azure](./howto-install-pnp-cli.md).
