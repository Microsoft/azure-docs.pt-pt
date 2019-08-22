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
ms.openlocfilehash: c7d8b946fc587d58f13e16c1d7d806390e712104
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879555"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalar e usar o Azure IoT Explorer

O Azure IoT Explorer é uma ferramenta gráfica para interagir com e testar seus dispositivos IoT Plug and Play Preview. Depois de instalar a ferramenta em seu computador local, você pode usá-la para se conectar a um dispositivo. Você pode usar a ferramenta para exibir a telemetria que o dispositivo está enviando, trabalhar com propriedades de dispositivo e chamar comandos.

Este artigo mostra como:

- Instale e configure a ferramenta do Azure IoT Explorer.
- Use a ferramenta para interagir com e testar seus dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a ferramenta do Azure IoT Explorer, você precisa de:

- Um hub IoT do Azure. Há várias maneiras de adicionar um hub IoT à sua assinatura do Azure, como [a criação de um hub IOT usando o CLI do Azure](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de conexão do Hub IoT para executar a ferramenta do Azure IoT Explorer. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um dispositivo registrado em seu hub IoT. Você pode usar o comando CLI do Azure a seguir para registrar um dispositivo. Certifique-se de substituir `{YourIoTHubName}` os `{YourDeviceID}` espaços reservados e pelos seus valores:

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

### <a name="overview-page"></a>Página de visão geral

#### <a name="device-overview"></a>Visão geral do dispositivo

Depois que a ferramenta se conectar ao Hub IoT, ela exibirá uma página de visão geral que lista todas as identidades de dispositivo registradas com o Hub IoT do Azure. Selecione um dispositivo para exibir mais detalhes.

#### <a name="device-management"></a>Gestão de dispositivos

- Para registrar um novo dispositivo com o Hub, selecione **Adicionar**. Insira uma ID de dispositivo. Use as configurações padrão para gerar automaticamente as chaves de autenticação e habilitar a conexão com o Hub.
- Para excluir uma identidade de dispositivo, selecione **excluir**. Examine os detalhes do dispositivo antes de concluir esta ação para certificar-se de que você está excluindo a identidade correta do dispositivo.
- A ferramenta dá suporte à consulta `capabilityID` por `interfaceID`e. Adicione seu `capabilityID` ou `interfaceID` como um parâmetro para consultar seus dispositivos.

## <a name="interact-with-a-device"></a>Interagir com um dispositivo

Clique duas vezes em um dispositivo na página Visão geral para exibir o próximo nível de detalhe. Há duas seções: **Dispositivo** e e/ou **digital**.

### <a name="device"></a>Dispositivo

Esta seção inclui as guias **identidade do dispositivo**, telemetria e dispositivos de **entrelaçamento** .

- Você pode exibir e atualizar as informações de identidade do dispositivo na guia **identidade do dispositivo** .
- Se um dispositivo estiver conectado e enviando dados ativamente, você poderá exibir a telemetria na guia telemetria.
- Você pode acessar as informações de FileUp do dispositivo na guia de **dispositivo** de entrelaçamento.

### <a name="digital-twin"></a>Entrelaçar digital

Você pode usar a ferramenta para exibir a instância de cópia digital do dispositivo. Para um dispositivo de Plug and Play IoT, todas as interfaces associadas ao modelo de capacidade de dispositivo são exibidas neste artigo. Selecione uma interface para expandir seus primitivos de [plug and Play IOT](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)correspondentes.

#### <a name="properties"></a>properties

Você pode exibir as propriedades somente leitura definidas em uma interface na página **Propriedades** . Você pode atualizar as propriedades graváveis definidas em uma interface na página de **Propriedades graváveis** .

1. Vá para a página de **Propriedades graváveis** .
1. Clique na propriedade que você deseja atualizar.
1. Insira o novo valor para a propriedade.
1. Visualize o conteúdo a ser enviado para o dispositivo.
1. Envie a alteração.

Depois de enviar uma alteração, você pode acompanhar o status da atualização: **sincronização**, **êxito**ou **erro**. Quando a sincronização estiver concluída, você verá o novo valor da propriedade na coluna **Propriedade** relatada. Se você navegar para outras páginas antes de a sincronização ser concluída, a ferramenta ainda o notificará quando a atualização for concluída. Você também pode usar o centro de notificações da ferramenta para ver o histórico de notificações.

#### <a name="commands"></a>Comandos

Para enviar um comando para um dispositivo, vá para a página **comandos** :

1. Na lista de comandos, expanda o comando que você deseja disparar.
1. Insira os valores necessários para o comando.
1. Visualize o conteúdo a ser enviado para o dispositivo.
1. Envie o comando.

#### <a name="telemetry"></a>Telemetria

Para exibir a telemetria da interface selecionada, acesse sua página de telemetria.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de instruções, você aprendeu a instalar e usar o Azure IoT Explorer para interagir com seus dispositivos de Plug and Play de IoT. Uma próxima etapa sugerida é aprender a [gerenciar modelos](./howto-manage-models.md).
