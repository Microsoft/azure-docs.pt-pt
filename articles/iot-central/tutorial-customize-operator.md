---
title: Personalizar as vistas do operador no Azure IoT Central | Microsoft Docs
description: EnqPersonalize as vistas do operador na sua aplicação do Azure IoT Central como construtor.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d10e0b48da497dafe9bb841c68746328e8334c91
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215732"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Tutorial: Personalizar a vista do operador do Azure IoT Central

Este tutorial mostra-lhe como personalizar a vista do operador da sua aplicação como construtor. Quando fizer uma alteração à aplicação como construtor, pode pré-visualizar a vista do operador na aplicação do Microsoft Azure IoT Central.

Neste tutorial, irá personalizar a aplicação para apresentar informações relevantes sobre o dispositivo de ar condicionado ligado a um operador. As personalizações permitem ao operador gerir os dispositivos de ar condicionado ligados à aplicação.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o dashboard do seu dispositivo
> * Configurar o seu esquema de definições do dispositivo
> * Configurar o seu esquema de propriedades do dispositivo
> * Pré-visualizar o dispositivo como operador
> * Configurar o seu dashboard de aplicações predefinido
> * Pré-visualizar o dashboard de aplicações padrão como um operador

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar sessão, deve concluir os dois tutoriais anteriores:

* [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type.md).
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurar o dashboard do seu dispositivo

Pode definir as informações que são apresentadas num dashboard do dispositivo como construtor. No tutorial [Definir um novo tipo de dispositivo na sua aplicação](tutorial-define-device-type.md), adicionou um gráfico de linhas e outras informações ao dashboard **Ar Condicionado Ligado-1**.

1. Para editar o modelo do dispositivo **Ar condicionado ligado**, escolha **Explorador** no menu de navegação à esquerda:

    ![Página do Explorador](media/tutorial-customize-operator/explorer.png)

2. Para começar a personalizar o dashboard do dispositivo de ar condicionado ligado, selecione o modelo do dispositivo de **Ar Condicionado Ligado (1.0.0)**. Escolha o dispositivo de **Ar Condicionado Ligado-1** que criou no tutorial [Definir um novo tipo de dispositivo na sua aplicação](tutorial-define-device-type.md):

    ![Selecionar o dispositivo de ar condicionado ligado](media/tutorial-customize-operator/selectdevice.png)

    Quando estiver num dispositivo, como **Ar Condicionado Ligado-1**, pode selecionar **Editar Modelo** para fazer uma alteração no modelo subjacente. Para obter mais informações, veja [Criar uma nova versão do modelo de dispositivo](howto-version-devicetemplate.md).

3. Para editar o dashboard, escolha **Dashboard** e selecione **Editar Modelo**:

    ![Página do dashboard do modelo do dispositivo](media/tutorial-customize-operator/dashboard.png)

4. Para adicionar um mosaico do Indicador Chave de Desempenho (KPI) ao dashboard, escolha **KPI**:

    ![Adicionar KPI](media/tutorial-customize-operator/addkpi.png)

    Para definir o KPI, utilize as informações na tabela seguinte:

    | Definição     | Valor |
    | ----------- | ----- |
    | Name        | Temperatura máxima |
    | Medida | temperatura |
    | Agregação | Máximo |
    | Intervalo de tempo  | Passado 1 semana |

5. Escolha **Guardar**. Agora pode ver o mosaico de KPI no dashboard:

    ![Mosaico de KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Para mover ou redimensionar um mosaico no dashboard, mova o ponteiro do rato sobre o mosaico. Pode arrastar o mosaico para uma nova localização ou redimensioná-lo:

    ![Editar esquema do dashboard](media/tutorial-customize-operator/dashboardlayout.png)

7. Clique em **Concluído** quando terminar de fazer as alterações.

## <a name="configure-your-settings-layout"></a>Configurar o seu esquema de definições

Também pode configurar a vista do operador das definições do dispositivo como construtor. Um operador utiliza a página de definições do dispositivo para configurar um dispositivo. Por exemplo, um operador utiliza a página de definições para definir a temperatura de destino para o ar condicionado ligado.

1. Para editar o esquema de definições do ar condicionado ligado, escolha **Definições** e selecione **Editar Modelo**:

    ![Página de definições](media/tutorial-customize-operator/settings.png)

2. Pode mover e redimensionar os mosaicos de definições:

    ![Editar o esquema de definições](media/tutorial-customize-operator/settingslayout.png)

3. Clique em **Concluído** quando terminar de fazer as alterações.

> [!NOTE]
> No modo **Editar Modelo**, não pode editar os valores das definições.

## <a name="configure-your-properties-layout"></a>Configurar o seu esquema de propriedades

Para além do dashboard e das definições, também pode configurar a vista do operador das propriedades do dispositivo. Um operador utiliza a página de propriedades do dispositivo para gerir os metadados do dispositivo. Por exemplo, um operador utiliza a página de propriedades para ver um número de série do dispositivo ou atualizar os detalhes de contacto do fabricante.

1. Para editar o esquema de propriedades do ar condicionado ligado, escolha **Propriedades** e selecione **Editar Modelo**:

    ![Página Propriedades](media/tutorial-customize-operator/properties.png)

2. Pode mover e redimensionar os campos de propriedades:

    ![Editar o esquema de propriedades](media/tutorial-customize-operator/propertieslayout.png)

3. Clique em **Concluído** quando terminar de fazer as alterações.

> [!NOTE]
> No modo **Editar Modelo**, não pode editar os valores das propriedades.

## <a name="preview-the-device"></a>Pré-visualização do dispositivo

No modo **Editar Modelo**, pode personalizar as páginas do dashboard, das definições e da propriedades de um operador. Se não estiver no modo **Editar Modelo**, poderá ver a aplicação como um operador.

1. Para ver o dispositivo de ar condicionado ligado como um operador, clique em **Concluído** para parar a edição do modelo e voltar à vista do operador do dispositivo.

2. Para atualizar a Localização deste dispositivo, edite o valor no mosaico Localização e escolha **Guardar**:

    ![Editar um valor de propriedade](media/tutorial-customize-operator/editproperty.png)

3. Para enviar uma definição para o seu ar condicionado ligado, escolha **Definições**, altere um valor de definição num mosaico e escolha **Atualizar**:

    ![Enviar definição para dispositivo](media/tutorial-customize-operator/sendsetting.png)

    Quando o dispositivo reconhece o novo valor de definição, a definição é apresentada como **sincronizada** no mosaico.

4. Enquanto operador, pode ver o dashboard do dispositivo, conforme configurado pelo construtor:

    ![Vista do operador do dashboard do dispositivo](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Configurar o dashboard predefinido

Quando um construtor ou o operador inicia sessão a uma aplicação do Azure IoT Central, verão o dashboard de aplicações. Como um construtor, pode configurar o conteúdo do dashboard predefinido para incluir o conteúdo mais útil e relevante para um operador.

> [!NOTE]
> Os utilizadores também podem criar seus próprios dashboards pessoais e escolher uma como seu padrão.

1. Para personalizar o dashboard de aplicações predefinido, navegue para o **Dashboard** página e clique em **editar** no canto superior direito da página. É apresentado um painel com uma biblioteca de objetos, que pode adicionar ao dashboard.

    ![Página de dashboard](media/tutorial-customize-operator/builderhome.png)

2. Para personalizar o dashboard, adicione mosaicos a partir da **biblioteca**. Escolha **Ligação** e adicione os detalhes do site da sua organização. Em seguida, escolha **Guardar**:

    ![Adicionar ligação ao dashboard](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Também pode adicionar ligações a páginas na sua aplicação do Azure IoT Central. Por exemplo, pode adicionar uma ligação a um dashboard de dispositivo ou à página de definições.

3. Opcionalmente, escolha **imagem** e carregar uma imagem a apresentar no seu dashboard. Uma imagem pode ter um URL para o qual navega, quando clica nele:

    ![Adicionar uma imagem ao dashboard](media/tutorial-customize-operator/addimage.png)

    Para obter mais informações, veja [Como preparar e carregar imagens para a aplicação do Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Pré-visualização do dashboard

Para pré-visualizar o dashboard de aplicações como um operador, selecione **feito** no canto superior direito da página

![Alternar Modo de Estrutura](media/tutorial-customize-operator/operatorviewhome.png)

Pode clicar nos mosaicos de ligação e imagem para navegar para os URLs definidos como um construtor.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a personalizar a vista do operador da aplicação.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Configurar o dashboard do seu dispositivo
> * Configurar o seu esquema de definições do dispositivo
> * Configurar o seu esquema de propriedades do dispositivo
> * Pré-visualizar o dispositivo como operador
> * Configurar o seu dashboard de aplicações predefinido
> * Pré-visualizar o dashboard de aplicações padrão como um operador

Agora que aprendeu a personalizar a vista do operador da aplicação, os próximos passos sugeridos são:

* [Monitorizar os dispositivos (como administrador)](tutorial-monitor-devices.md)
* [Adicionar um novo dispositivo à sua aplicação (como operador e programador de dispositivo)](tutorial-add-device.md)
