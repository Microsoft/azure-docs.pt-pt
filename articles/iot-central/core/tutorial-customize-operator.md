---
title: 'Tutorial: personalizar as exibições do operador no Azure IoT Central'
description: 'Tutorial: como um construtor, personalize as exibições do operador em seu aplicativo IoT Central do Azure.'
author: sandeeppujar
ms.author: sandeepu
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d835ce5a1cbdcbd9741c775c4e9bd2eea7c803d7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112334"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Tutorial: Personalizar a vista de operador do Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este tutorial mostra-lhe como personalizar a vista do operador da sua aplicação como construtor. Quando fizer uma alteração à aplicação como construtor, pode pré-visualizar a vista do operador na aplicação do Microsoft Azure IoT Central.

Neste tutorial, irá personalizar a aplicação para apresentar informações relevantes sobre o dispositivo de ar condicionado ligado a um operador. As personalizações permitem ao operador gerir os dispositivos de ar condicionado ligados à aplicação.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o dashboard do seu dispositivo
> * Configurar o seu esquema de definições do dispositivo
> * Configurar o seu esquema de propriedades do dispositivo
> * Pré-visualizar o dispositivo como operador
> * Configurar o painel do aplicativo padrão
> * Visualizar o painel de aplicativo padrão como um operador

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar sessão, deve concluir os dois tutoriais anteriores:

* [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type.md).
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurar o dashboard do seu dispositivo

Pode definir as informações que são apresentadas num dashboard do dispositivo como construtor. No tutorial [definir um novo tipo de dispositivo no aplicativo](tutorial-define-device-type.md) , você adicionou um gráfico de linhas e outras informações ao painel do **ar-condicionado conectado** .

1. Para editar o modelo de dispositivo de **ar-condicionado conectado** , escolha **modelos de dispositivo** no painel esquerdo:

    ![Página modelos de dispositivo](media/tutorial-customize-operator/devicetemplates.png)

2. Para personalizar o painel do dispositivo, selecione o dispositivo de modelo de dispositivo do **1.0.0 (ar condicionador)** que você criou no tutorial [definir um novo tipo de dispositivo no aplicativo](tutorial-define-device-type.md) .

3. Para editar o painel, selecione a guia **painel** .

4. Para adicionar um mosaico do Indicador Chave de Desempenho (KPI) ao dashboard, escolha **KPI**:

    Para definir o KPI, utilize as informações na tabela seguinte:

    | Definição     | Valor |
    | ----------- | ----- |
    | Nome        | Temperatura máxima |
    | Intervalo de tempo  | Passado 1 semana |
    | Tipo de medida | Telemetria |
    | Medida | temperatura |
    | Agregação | Máximo |
    | Visibilidade  | Ativado |

    ![Adicionar KPI](media/tutorial-customize-operator/addkpi.png)

5. Selecione **Guardar**. Agora pode ver o mosaico de KPI no dashboard:

    ![Mosaico de KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Para mover ou redimensionar um mosaico no dashboard, mova o ponteiro do rato sobre o mosaico. Você pode arrastar o bloco para um novo local ou redimensioná-lo.

## <a name="configure-your-settings-layout"></a>Configurar o seu esquema de definições

Também pode configurar a vista do operador das definições do dispositivo como construtor. Um operador usa a guia Configurações do dispositivo para configurar um dispositivo. Por exemplo, um operador usa a guia Configurações para definir a temperatura de destino do ar condicionado conectado.

1. Para editar o layout de configurações para o ar-condicionado conectado, escolha a guia **configurações** .

2. Pode mover e redimensionar os mosaicos de definições:

    ![Editar o esquema de definições](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Configurar o seu esquema de propriedades

Para além do dashboard e das definições, também pode configurar a vista do operador das propriedades do dispositivo. Um operador usa a guia Propriedades do dispositivo para gerenciar metadados do dispositivo. Por exemplo, um operador usa a guia Propriedades para exibir um número de série do dispositivo ou atualizar detalhes de contato para o fabricante.

1. Para editar o layout de propriedades do ar-condicionado conectado, escolha a guia **Propriedades** .

2. Pode mover e redimensionar os campos de propriedades:

    ![Editar o esquema de propriedades](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Visualizar o dispositivo

Use a página **modelos de dispositivo** para personalizar as guias painel, configurações e propriedades de um operador. Use a página **Device Explorer** para exibir e usar o modelo de dispositivo.

1. Para exibir e usar o modelo de ar-condicionado conectado como um operador, navegue até a página **Device Explorer** e escolha o dispositivo simulado que IOT central gerado a partir de seu modelo:

    ![Exibir e usar o modelo de dispositivo](media/tutorial-customize-operator/usetemplate.png)

2. Para atualizar o local deste dispositivo, escolha **Propriedades** e edite o valor no bloco localização. Em seguida, selecione **salvar**:

    ![Editar um valor de propriedade](media/tutorial-customize-operator/editproperty.png)

3. Para enviar uma definição para o seu ar condicionado ligado, escolha **Definições**, altere um valor de definição num mosaico e escolha **Atualizar**:

    ![Enviar definição para dispositivo](media/tutorial-customize-operator/sendsetting.png)

    Quando o dispositivo reconhece o novo valor de definição, a definição é apresentada como **sincronizada** no mosaico.

4. Enquanto operador, pode ver o dashboard do dispositivo, conforme configurado pelo construtor:

    ![Vista do operador do dashboard do dispositivo](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Configurar o painel padrão

Quando um construtor ou operador entra em um aplicativo de IoT Central do Azure, ele vê o painel do aplicativo. Como um construtor, você pode configurar o conteúdo do painel padrão para incluir o conteúdo mais útil e relevante para um operador.

> [!NOTE]
> Os usuários também podem criar seus próprios painéis pessoais e escolher um como padrão.

1. Para personalizar o painel do aplicativo padrão, navegue até a página **painel** e selecione **Editar** na parte superior direita da página. Um painel é exibido com uma biblioteca de objetos que você pode adicionar ao painel.

    ![Página do painel](media/tutorial-customize-operator/builderhome.png)

2. Para personalizar o painel, adicione blocos da **biblioteca**. Escolha **Ligação** e adicione os detalhes do site da sua organização. Em seguida, escolha **Guardar**:

    ![Adicionar link ao painel](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Também pode adicionar ligações a páginas na sua aplicação do Azure IoT Central. Por exemplo, pode adicionar uma ligação a um dashboard de dispositivo ou à página de definições.

3. Opcionalmente, escolha **imagem** e carregue uma imagem para exibição em seu painel. Uma imagem pode ter uma URL para a qual você navega ao selecioná-la:

    ![Adicionar uma imagem ao painel](media/tutorial-customize-operator/addimage.png)

    Para obter mais informações, veja [Como preparar e carregar imagens para a aplicação do Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Visualizar o painel

Para visualizar o painel de aplicativos como um operador, selecione **concluído** no canto superior direito da página.

![Alternar Modo de Estrutura](media/tutorial-customize-operator/operatorviewhome.png)

Você pode selecionar os blocos de link e imagem para navegar até as URLs definidas como um construtor.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a personalizar a vista do operador da aplicação.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Configurar o dashboard do seu dispositivo
> * Configurar o seu esquema de definições do dispositivo
> * Configurar o seu esquema de propriedades do dispositivo
> * Pré-visualizar o dispositivo como operador
> * Configurar a sua home page predefinida
> * Pré-visualizar a home page predefinida como operador

Agora que aprendeu a personalizar a vista do operador da aplicação, os próximos passos sugeridos são:

* [Monitorizar os dispositivos (como administrador)](tutorial-monitor-devices.md)
* [Adicionar um novo dispositivo à sua aplicação (como operador e programador de dispositivo)](tutorial-add-device.md)