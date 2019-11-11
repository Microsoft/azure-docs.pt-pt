---
title: Personalizar o painel do operador no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra como personalizar o painel do operador em um aplicativo IoT Central e gerenciar dispositivos.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 10/03/2019
ms.openlocfilehash: cb43facfc6b838a6e29a1b08e50648abf0f04911
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615304"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Tutorial: personalizar o painel do operador e gerenciar dispositivos no Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra, como um construtor, como personalizar o painel do operador em seu aplicativo de análise de IoT Central do Azure no repositório. Os operadores de aplicativo podem usar o painel personalizado para executar o aplicativo e gerenciar os dispositivos anexados.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Alterar o nome do painel
> * Personalizar blocos de imagem no painel
> * Organizar blocos para modificar o layout
> * Adicionar blocos de telemetria a condições de exibição
> * Adicionar blocos de propriedades para exibir os detalhes do dispositivo
> * Adicionar blocos de comando para executar comandos

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, o construtor deve concluir o primeiro tutorial para criar o aplicativo de análise de IoT Central do Azure no repositório e adicionar dispositivos:

* [Criar um aplicativo de análise no repositório no Azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) (obrigatório)

## <a name="change-the-dashboard-name"></a>Alterar o nome do painel
Para personalizar o painel do operador, edite o painel padrão em seu aplicativo. Opcionalmente, você pode criar novos painéis adicionais. A primeira etapa para personalizar o painel em seu aplicativo é alterar o nome.

1. Navegue até o site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) .

1. Abra o aplicativo de monitoramento de condição que você criou no tutorial [criar um aplicativo de análise na loja no Azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) .

1. Selecione **Editar** na barra de ferramentas do painel. No modo de edição, você pode personalizar a aparência, o layout e o conteúdo do painel.

    ![Editar painel do Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. Opcionalmente, oculte o painel esquerdo. Ocultar o painel esquerdo proporciona uma área de trabalho maior para a edição do painel.

1. Insira um nome amigável para seu painel no **nome do painel.** Este tutorial usa uma empresa fictícia chamada contoso e o nome do painel de exemplo é *contoso Dashboard*. 

1. Selecione **Guardar**. Isso salva as alterações no painel e desabilita o modo de edição.

    ![Nome do painel de alteração do IoT Central do Azure](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Personalizar blocos de imagem no painel
Um painel de aplicativo IoT Central do Azure consiste em um ou mais blocos. Um bloco é um contêiner Retangular para exibir o conteúdo em um painel. Você associa vários tipos de conteúdo a blocos e arrasta, solta e redimensiona blocos para personalizar um layout de painel. Há vários tipos de blocos para exibir o conteúdo. Os blocos de imagem contêm imagens e você pode adicionar uma URL que permite aos usuários clicar na imagem. Blocos de rótulo exibem texto sem formatação. Blocos de redução contêm conteúdo formatado e permitem definir uma imagem, uma URL, um título e um código de redução que é renderizado como HTML. Os blocos de telemetria, propriedade ou comando exibem dados específicos do dispositivo. 

Nesta seção, você aprenderá a personalizar os blocos de imagem no painel.

Para personalizar o bloco de imagem que exibe uma imagem de marca no painel:

1. Selecione **Editar** na barra de ferramentas do painel. 

1. Selecione **Configurar** no bloco imagem que exibe a imagem da marca Northwind. 

    ![Imagem da marca de edição do Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. Altere o **título**. O título é exibido quando um usuário passa o mouse sobre a imagem.

1. Selecione **imagem**. Uma caixa de diálogo é aberta e permite que você carregue uma imagem personalizada. 

1. Opcionalmente, especifique uma URL para a imagem.

1. Selecione **Atualizar configuração**. O botão **Atualizar configuração** salva as alterações no painel e deixa o modo de edição habilitado.

    ![Imagem da marca de salvamento da IoT Central do Azure](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Opcionalmente, selecione **Configurar** no bloco intitulado **documentação**e especifique uma URL para o conteúdo de suporte. 

Para personalizar o bloco de imagem que exibe um mapa das zonas de sensor na loja:

1. Selecione **Configurar** no bloco imagem que exibe o mapa de zona de armazenamento padrão. 

1. Selecione **imagem**e use a caixa de diálogo para carregar uma imagem personalizada de um mapa de zona de repositório. 

1. Selecione **Atualizar configuração**.

    ![Mapa de armazenamento de salvamento do Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    O mapa da loja contoso de exemplo mostra quatro zonas: duas zonas de check-out, uma zona para roupas e cuidados pessoais e uma zona para supermercado e Deli. Neste tutorial, você associará sensores a essas zonas para fornecer telemetria.

    ![Zonas de armazenamento de IoT Central do Azure](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Selecione **Guardar**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Organizar blocos para modificar o layout
Uma etapa fundamental na personalização de um painel é reorganizar os blocos para criar uma exibição útil. Os operadores de aplicativo usam o painel para visualizar a telemetria do dispositivo, gerenciar dispositivos e monitorar condições em um repositório. O IoT Central do Azure simplifica a tarefa do Application Builder de criação de um Dashboard. O modo de edição do painel permite adicionar, mover, redimensionar e excluir blocos rapidamente. O modelo de aplicativo **análise na loja – check-out** também simplifica a tarefa de criar um painel. Ele fornece um layout de painel de trabalho, com sensores conectados e blocos que exibem as contagens de linha de check-out e as condições ambientais.

Nesta seção, você reorganiza o painel no modelo de aplicativo **análise de check-out na loja Analytics** para criar um layout personalizado.

Para remover blocos que você não planeja usar em seu aplicativo:

1. Selecione **Editar** na barra de ferramentas do painel. 

1. Selecione **X excluir** para remover os seguintes blocos: **de volta a todas as zonas**, **visite painel de armazenamento**, **tempo de espera**e todos os três blocos associados ao **check-out 3**. O painel da loja da Contoso não usa esses blocos. 

    ![Blocos de exclusão IoT Central do Azure](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Role para colocar os blocos de painel restantes no modo de exibição.

1. Selecione **X excluir** para remover os seguintes blocos: **zona de check-up de aquecimento**, **zona de check-out frio**, **configurações de sensor de ocupação**, configurações de **sensor de termostato**e **condições de ambiente**. 

   ![O Azure IoT Central excluir blocos restantes](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Selecione **Guardar**. A remoção de blocos não utilizados libera espaço na página de edição e simplifica a exibição de painel para operadores.

1. Exiba suas alterações no painel.

   ![IoT Central do Azure depois de excluir blocos](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Depois de remover blocos não utilizados, reorganize os blocos restantes para criar um layout organizado. O novo layout inclui espaço para blocos que você adiciona em uma etapa posterior.

Para reorganizar os blocos restantes:

1. Selecione **Editar**.

1. Selecione o bloco de **firmware de ocupação** e arraste-o para a direita do bloco de bateria de **ocupação** .

1. Selecione o bloco **firmware termostato** e arraste-o para a direita do bloco de bateria **termostato** .

1. Selecione **Guardar**.

1. Exiba as alterações de layout. 

    ![Blocos de bateria de firmware de IoT Central do Azure](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Adicionar blocos de telemetria a condições de exibição
Depois de personalizar o layout do painel, você estará pronto para adicionar blocos para mostrar a telemetria. Para criar um bloco de telemetria, selecione um modelo de dispositivo e uma instância de dispositivo e selecione telemetria específica do dispositivo para exibir no bloco. O modelo **de aplicativo análise na loja – check-out** inclui vários blocos de telemetria no painel. Os quatro blocos nas duas zonas de check-out exibem a telemetria do sensor de ocupação simulado. O bloco **tráfego de pessoas** mostra contagens nas duas zonas de check-out. 

Nesta seção, você adiciona mais dois blocos de telemetria para mostrar a telemetria ambiental dos sensores RuuviTag adicionados no tutorial [criar um aplicativo de análise na loja no Azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) . 

Para adicionar blocos para exibir dados ambientais dos sensores RuuviTag:

1. Selecione **Editar**.

1. Selecione `RuuviTag` na lista **modelo de dispositivo** . 

1. Selecione uma **instância de dispositivo** de um dos dois sensores RuuviTag. Na loja da Contoso de exemplo, selecione `Zone 1 Ruuvi` para criar um bloco de telemetria para Zona 1. 

1. Selecione `Relative humidity` e `temperature` na lista de **telemetria** . Estes são os itens de telemetria que são exibidos para cada zona no bloco.

1. Selecione **combinar**. 

    ![Azure IoT Central Adicionar bloco RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Um novo bloco é exibido para exibir a telemetria combinada de umidade e temperatura para o sensor selecionado. 

1. Selecione **Configurar** no novo bloco para o sensor RuuviTag. 

1. Altere o **título** para *zona 1 ambiente*. 

1. Selecione **Atualizar configuração**.

1. Repita as etapas anteriores para criar um bloco para a segunda instância do sensor. Defina o **título** para *zona 2 ambiente* e, em seguida, selecione **Atualizar configuração.**

1. Arraste o bloco intitulado **zona 2 ambiente** abaixo do bloco **firmware termostato** . 

1. Arraste o bloco intitulado **zona 1 ambiente** abaixo do bloco **tráfego de pessoas** . 

1. Selecione **Guardar**. O painel exibe a telemetria de zona nos dois novos blocos.

    ![IoT Central do Azure todos os blocos do RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

Para editar o bloco de **tráfego de pessoas** para mostrar a telemetria para apenas duas zonas de check-out:

1. Selecione **Editar**. 

1. Selecione **Configurar** no bloco **tráfego de pessoas** .

1. Na **telemetria** , selecione **contagem 1**, **contagem 2**e **contagem 3**. 

1. Selecione **Atualizar configuração**. Isso limpa a configuração existente no bloco. 

1. Selecione **Configurar** novamente no bloco **tráfego de pessoas** .

1. Na **telemetria** , selecione **contagem 1**e **contagem 2**. 

1. Selecione **Atualizar configuração**. 

1. Selecione **Guardar**.  O painel atualizado exibe contagens apenas para as duas zonas de check-out, que se baseiam no sensor de ocupação simulado.

    ![As pessoas do Azure IoT Central tráfego de duas pistas](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Adicionar blocos de propriedades para exibir os detalhes do dispositivo
Os operadores de aplicativo usam o painel para gerenciar dispositivos e monitorar o status. Adicione um bloco para cada RuuviTag para habilitar os operadores para exibir a versão do software. 

Para adicionar um bloco de propriedade para cada RuuviTag:

1. Selecione **Editar**.

1. Selecione `RuuviTag` na lista **modelo de dispositivo** . 

1. Selecione uma **instância de dispositivo** de um dos dois sensores RuuviTag. Na loja da Contoso de exemplo, selecione `Zone 1 Ruuvi` para criar um bloco de telemetria para Zona 1. 

1. Selecione **propriedades > versão do software**.

1. Selecione **combinar**. 

1. Selecione **Configurar** no bloco recém-criado intitulado **versão do software**. 

1. Altere o **título** para *Ruuvi 1 versão do software*.

1. Selecione **Atualizar configuração**. 

1. Arraste o bloco intitulado **Ruuv 1 versão do software** abaixo do bloco **zona 1 Environment** .

1. Repita as etapas anteriores para criar um bloco de propriedades de versão de software para o segundo RuuviTag. 

1. Selecione **Guardar**.  

    ![Blocos de propriedades do Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Adicionar blocos de comando para executar comandos
Os operadores de aplicativo também usam o painel para gerenciar dispositivos executando comandos. Você pode adicionar blocos de comando ao painel que executarão comandos predefinidos em um dispositivo. Nesta seção, você adiciona um bloco de comando para habilitar os operadores para reinicializar o gateway rigado. 

Para adicionar um bloco de comando para reinicializar o gateway:

1. Selecione **Editar**. 

1. Selecione `C500` na lista **modelo de dispositivo** . Este é o modelo para o gateway rigado C500. 

1. Selecione a instância de gateway na **instância do dispositivo**.

1. Selecione o **comando > reinicializar** e arraste-o para o painel ao lado do mapa do repositório. 

1. Selecione **Guardar**. 

1. Exiba seu painel contoso concluído. 

    ![Personalização do painel completo do Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Opcionalmente, selecione o bloco de **reinicialização** para executar o comando de reinicialização em seu gateway.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

* Alterar o nome do painel
* Personalizar blocos de imagem no painel
* Organizar blocos para modificar o layout
* Adicionar blocos de telemetria a condições de exibição
* Adicionar blocos de propriedades para exibir os detalhes do dispositivo
* Adicionar blocos de comando para executar comandos

Agora que você personalizou o painel em seu aplicativo de análise do Azure IoT Central no repositório, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Exportar dados e visualizar informações](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
