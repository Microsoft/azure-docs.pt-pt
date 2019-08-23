---
title: Definir um novo tipo de dispositivo no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra, como um construtor, como criar um novo tipo de dispositivo no aplicativo IoT Central do Azure. Você define a telemetria, o estado, as propriedades e os comandos para seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d58cec644c75baaac37862f445477da92075c44d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907342"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Tutorial: Definir um novo tipo de dispositivo no aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra-lhe como utilizar, enquanto construtor, um modelo de dispositivo para definir um novo tipo de dispositivo na aplicação do Azure IoT Central. Um modelo de dispositivo define os recursos do seu dispositivo. Os recursos incluem telemetria que o dispositivo envia, as propriedades do dispositivo e os comandos que um dispositivo responde.

Neste tutorial, você criará um modelo de dispositivo de **sensor de ambiente** . Um dispositivo de sensor ambiental:

* Envia telemetria como temperatura.
* Relata propriedades específicas do dispositivo, como o nível de brilho.
* Responde a comandos como ativar e desativar.
* Relata propriedades genéricas do dispositivo, como a versão do firmware e o número de série.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um novo modelo de dispositivo.
> * Importar um modelo de capacidade de dispositivo.
> * Criar propriedades de nuvem.
> * Defina uma visualização para a telemetria do dispositivo.
> * Publique seu modelo de dispositivo.
> * Crie um dispositivo simulado para seu modelo de dispositivo.
> * Exiba o dispositivo simulado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Se concluiu o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), pode reutilizar a aplicação que criou no início rápido. Caso contrário, execute os seguintes passos para criar uma aplicação do Azure IoT Central vazia:

1. Navegue para a página do [Gestor de Aplicações](https://aka.ms/iotcentral) do Azure IoT Central.

1. Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-lo, caso contrário, assine-o usando uma conta Microsoft:

    ![Introduza a sua conta da organização](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação).

Para criar um novo aplicativo de IoT Central do Azure que usa recursos de visualização, incluindo a IoT Plug and Play:

1. Escolha **Avaliação**. Você não precisa de uma assinatura do Azure para criar um aplicativo de avaliação.

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Escolha **aplicativo de visualização**.

1. Opcionalmente, pode escolher um nome de aplicação amigável, como **Ares Condicionados Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para si. Pode alterar este prefixo de URL para algo mais memorável.

1. Se você estiver criando um aplicativo de avaliação, precisará fornecer seus detalhes de contato.

1. Selecione **Criar**.

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Você também precisa de uma cópia local do arquivo **EnvironmentalSensorInline. capabilitymodel. JSON** que contém o modelo de recurso de dispositivo de [plug and Play de IOT](https://aka.ms/iot-pnp-docs) . Você pode baixá-lo [aqui](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Clique com o botão direito do mouse na página e selecione **salvar como**.

Depois de baixar o arquivo, abra-o em um editor de texto e substitua as duas instâncias `<YOUR_COMPANY_NAME_HERE>` do pelo seu nome. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado.

## <a name="create-a-template"></a>Criar um modelo

Como um construtor, você pode criar e editar modelos de dispositivo em seu aplicativo. Depois de publicar um modelo de dispositivo, você pode gerar um dispositivo simulado ou conectar dispositivos reais que implementam o modelo de dispositivo. Os dispositivos simulados permitem testar o comportamento do seu aplicativo antes de conectar um dispositivo real.

Para adicionar um novo modelo de dispositivo ao seu aplicativo, vá para a página **modelos de dispositivo** . Para fazer isso, selecione a guia **modelos de dispositivo** no menu de navegação à esquerda.

![Página modelos de dispositivo](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Adicionar um modelo de capacidade de dispositivo

Há várias opções para criar um modelo de capacidade de dispositivo no IoT Central. Você pode optar por criar um modelo personalizado do zero, importar de um arquivo, selecionar no catálogo do dispositivo ou conectar um dispositivo IoT Plug and Play por meio de uma conexão do dispositivo-primeiro em que o modelo de capacidade do dispositivo foi publicado no repositório público. Neste tutorial, você importará um modelo de capacidade de dispositivo de um arquivo.

As etapas a seguir mostram como importar o modelo de capacidade para um dispositivo de **sensor ambiental** . Esses dispositivos enviam telemetria, como temperatura, para seu aplicativo:

1. Para adicionar um novo modelo de dispositivo, selecione **+ novo** na página **modelos de dispositivo** .

1. Escolha **personalizado** na lista de modelos.

1. Insira **sensor ambiental** como o nome do seu modelo de dispositivo.

1. Escolha **Importar modelo de funcionalidade** para criar um novo modelo de capacidade de dispositivo a partir de um arquivo JSON. Navegue até a pasta em que você salvou o arquivo **EnvironmentalSensorInline. capabilitymodel. JSON** em seu computador local. Selecione o arquivo **EnvironmentalSensorInline. capabilitymodel. JSON** e, em seguida, selecione **abrir**. O modelo de recurso do sensor de ambiente inclui as interfaces do **sensor ambiental** e de **informações do dispositivo** :

    ![Modelo de capacidade de dispositivo do sensor ambiental](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Essas interfaces definem os recursos de um dispositivo de **sensor ambiental** . Os recursos incluem a telemetria que um dispositivo envia, as propriedades que um dispositivo relata e os comandos aos quais um dispositivo responde.

### <a name="add-cloud-properties"></a>Adicionar propriedades de nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades de nuvem** e **+ Adicionar Propriedade de nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao modelo de dispositivo.

    | Nome a Apresentar      | Tipo de Semântica | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhum          | Date   |
    | Nome do cliente     | Nenhum          | Cadeia |

1. Selecione **salvar** para salvar as alterações:

    ![Propriedades de nuvem](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar uma interface ou adicionar recursos específicos de IoT Central a uma funcionalidade que não exige a versão do modelo de capacidade do dispositivo. Você pode personalizar os campos quando o modelo de funcionalidade estiver em um rascunho ou em um estado publicado. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Pode, por exemplo:

- Personalize o nome de exibição e as unidades de um recurso.
- Adicione uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especifique os valores iniciais, mínimos e máximos para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade.


## <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo do sensor ambiental para um operador. Suas personalizações permitem que o operador gerencie os dispositivos do sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador Use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="generate-default-views"></a>Gerar exibições padrão

Gerar exibições padrão é uma maneira rápida de começar a visualizar as informações importantes do dispositivo. Você pode ter até três exibições padrão geradas para seu modelo de dispositivo:

* A exibição **comandos** permite que o operador despache comandos para seu dispositivo.
* O modo de exibição de **visão geral** usa gráficos e métricas para exibir a telemetria do dispositivo.
* A exibição **sobre** exibe as propriedades do dispositivo.

Depois de selecionar **gerar exibições padrão**, elas serão adicionadas automaticamente na seção exibições do modelo de dispositivo.

### <a name="configure-a-view-to-visualize-devices"></a>Configurar uma exibição para visualizar dispositivos

Um painel de dispositivos permite que um operador visualize um dispositivo usando gráficos e métricas. Pode definir as informações que são apresentadas num dashboard do dispositivo como construtor. Você pode definir vários painéis para dispositivos. Para criar um painel para visualizar a telemetria do sensor ambiental , selecione exibições e, em seguida, **visualizando o dispositivo**:

1. Todas as propriedades do dispositivo, propriedades de nuvem, telemetria e opções estáticas são listadas em **Propriedades**. Você pode arrastar e soltar qualquer um desses itens para a exibição. Arraste a propriedade **nível de brilho** para a exibição. Você pode configurar o bloco usando o ícone de engrenagem.

1. Para adicionar um gráfico que plota telemetria, selecione **umidade** e **temperatura**e, em seguida, selecione **combinar**. Para exibir esse gráfico em um formato diferente, como um gráfico de pizza ou um gráfico de barras, selecione o botão **alterar visualização** na parte superior do bloco.

1. Selecione **salvar** para salvar sua exibição:

Você pode adicionar mais blocos que mostram outras propriedades ou valores de telemetria. Você também pode adicionar texto estático, links e imagens. Para mover ou redimensionar um bloco no painel, mova o ponteiro do mouse sobre o bloco e arraste o bloco para um novo local ou redimensione-o.

### <a name="add-a-device-form"></a>Adicionar um formulário de dispositivo

Um formulário de dispositivo permite que um operador Edite propriedades de dispositivo gravável e propriedades de nuvem. Como um construtor, você pode definir vários formulários e escolher quais propriedades de dispositivo e de nuvem mostrar em cada formulário. Você também pode exibir propriedades de dispositivo somente leitura em um formulário.

Para criar um formulário para exibir e editar as propriedades do sensor ambiental:

1. Navegue até **exibições** no modelo de **sensor ambiental** . Selecione o bloco **dispositivo de edição e dados de nuvem** para adicionar uma nova exibição.

1. Insira o nome do formulário **Propriedades do sensor ambiental**.

1. Arraste as propriedades de nuvem **nome do cliente** e **última data de serviço** até a seção existente no formulário.

1. Selecione o **nível de brilho** e as propriedades do dispositivo de **estado do dispositivo** . Em seguida, selecione **Adicionar seção**. Edite o título da seção para ser **Propriedades do sensor**. Selecione **Aplicar**.

1. Selecione o **modelo do dispositivo**, a **versão do software**, o **fabricante**e as propriedades do dispositivo do **fabricante do processador** . Em seguida, selecione **Adicionar seção**. Edite o título da seção para ser **Propriedades do dispositivo**. Selecione **Aplicar**.

1. Selecione **salvar** para salvar sua exibição.

## <a name="publish-device-template"></a>Publicar modelo de dispositivo

Para poder criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para o modelo de dispositivo na página **modelos de dispositivo** .

1. Selecione **Publicar**.

1. Na caixa de diálogo **publicar um modelo de dispositivo** , escolha **publicar**:

    ![Modelo publicado](media/tutorial-define-device-type-pnp/publishedmodel.png)

Depois que um modelo de dispositivo é publicado, ele fica visível na página **dispositivos** e no operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, personalizações e exibições, em um modelo de dispositivo publicado sem controle de versão. Depois de fazer alterações, selecione **publicar** para enviar por push essas alterações para o operador.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar um novo modelo de dispositivo
* Importar um modelo de capacidade de dispositivo.
* Criar propriedades de nuvem.
* Criar personalizações.
* Defina uma visualização para a telemetria do dispositivo.
* Publique seu modelo de dispositivo.

Agora que você criou um modelo de dispositivo em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Adicionar dispositivo](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
