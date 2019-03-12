---
title: Personalizar as vistas do operador no Azure IoT Central | Microsoft Docs
description: EnqPersonalize as vistas do operador na sua aplicação do Azure IoT Central como construtor.
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 86c9a7794146edc4106d8ec30106e1c27556248f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769681"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Tutorial: Personalizar a vista do operador do Azure IoT Central (design de nova interface do Usuário)

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

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar sessão, deve concluir os dois tutoriais anteriores:

* [Definir um novo tipo de dispositivo na aplicação do Azure IoT Central](tutorial-define-device-type.md).
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurar o dashboard do seu dispositivo

Pode definir as informações que são apresentadas num dashboard do dispositivo como construtor. Na [definir um novo tipo de dispositivo em seu aplicativo](tutorial-define-device-type.md) tutorial, adicionou um gráfico de linhas e outras informações para o **ligado ar-condicionado** dashboard.

1. Para editar a **ligado ar-condicionado** modelo de dispositivo, escolha **modelos de dispositivos** no menu de navegação à esquerda:

    ![Página de modelos de dispositivo](media/tutorial-customize-operator/devicetemplates.png)

2. Para personalizar o dashboard do dispositivo, selecione o **ligado ar-condicionado (1.0.0)** dispositivos do modelo de dispositivo que criou no [definir um novo tipo de dispositivo na sua aplicação](tutorial-define-device-type.md) tutorial.

3. Para editar o dashboard, selecione o **Dashboard** separador.

4. Para adicionar um mosaico do Indicador Chave de Desempenho (KPI) ao dashboard, escolha **KPI**:

    Para definir o KPI, utilize as informações na tabela seguinte:

    | Definição     | Valor |
    | ----------- | ----- |
    | Name        | Temperatura máxima |
    | Intervalo de tempo  | Passado 1 semana |
    | Tipo de Medida | Telemetria |
    | Medida | temperatura |
    | Agregação | Máximo |
    | Visibilidade  | Ativado |

    ![Adicionar KPI](media/tutorial-customize-operator/addkpi.png)

5. Selecione **Guardar**. Agora pode ver o mosaico de KPI no dashboard:

    ![Mosaico de KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Para mover ou redimensionar um mosaico no dashboard, mova o ponteiro do rato sobre o mosaico. Pode arrastar o mosaico para uma localização nova ou redimensioná-la.

## <a name="configure-your-settings-layout"></a>Configurar o seu esquema de definições

Também pode configurar a vista do operador das definições do dispositivo como construtor. Um operador utiliza o separador de definições do dispositivo para configurar um dispositivo. Por exemplo, um operador utiliza o separador de definições para definir a temperatura de destino para o ar condicionado ligado.

1. Para editar o esquema de definições para sua ligado de ar-condicionado, escolha o **definições** separador.

2. Pode mover e redimensionar os mosaicos de definições:

    ![Editar o esquema de definições](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Configurar o seu esquema de propriedades

Para além do dashboard e das definições, também pode configurar a vista do operador das propriedades do dispositivo. Um operador utiliza o separador de propriedades do dispositivo para gerir metadados do dispositivo. Por exemplo, um operador utiliza o separador de propriedades para ver um número de série do dispositivo ou atualizar detalhes de contacto para o fabricante.

1. Para editar o layout de propriedades para sua ligado de ar-condicionado, escolha o **propriedades** separador.

2. Pode mover e redimensionar os campos de propriedades:

    ![Editar o esquema de propriedades](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Pré-visualização do dispositivo

Utilizar o **modelos de dispositivos** página para personalizar os separadores dashboard, definições e propriedades para um operador. Utilizar o **Device Explorer** página para ver e utilizar o modelo de dispositivo.

1. Para ver e utilizar o modelo conectado ar-condicionado como um operador, navegue para o **Device Explorer** página e selecione o dispositivo simulado que IoT Central gerado a partir do seu modelo:

    ![Ver e utilizar o modelo de dispositivo](media/tutorial-customize-operator/usetemplate.png)

2. Para atualizar a localização deste dispositivo, escolha **propriedades** e edite o valor no mosaico de localização. Em seguida, selecione **guardar**:

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

1. Para personalizar o dashboard de aplicações predefinido, navegue para o **Dashboard** página e selecione **editar** no canto superior direito da página. É apresentado um painel com uma biblioteca de objetos, que pode adicionar ao dashboard.

    ![Página de dashboard](media/tutorial-customize-operator/builderhome.png)

2. Para personalizar o dashboard, adicione mosaicos a partir da **biblioteca**. Escolha **Ligação** e adicione os detalhes do site da sua organização. Em seguida, escolha **Guardar**:

    ![Adicionar ligação ao dashboard](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Também pode adicionar ligações a páginas na sua aplicação do Azure IoT Central. Por exemplo, pode adicionar uma ligação a um dashboard de dispositivo ou à página de definições.

3. Opcionalmente, escolha **imagem** e carregar uma imagem a apresentar no seu dashboard. Uma imagem pode ter um URL para o qual navega, ao selecioná-lo:

    ![Adicionar uma imagem ao dashboard](media/tutorial-customize-operator/addimage.png)

    Para obter mais informações, veja [Como preparar e carregar imagens para a aplicação do Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Pré-visualização do dashboard

Para pré-visualizar o dashboard de aplicações como um operador, selecione **feito** no canto superior direito da página.

![Alternar Modo de Estrutura](media/tutorial-customize-operator/operatorviewhome.png)

Pode selecionar os mosaicos de imagem e ligação para navegar para os URLs que definir como um construtor.

## <a name="next-steps"></a>Passos Seguintes

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