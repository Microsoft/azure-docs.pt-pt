---
title: Tutorial - Personalize o painel de instrumentos do operador no Azure IoT Central
description: Este tutorial mostra como personalizar o painel de instrumentos do operador numa aplicação IoT Central e gerir dispositivos.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 74ce36652b1a00ac6813c62a17d44e2a5486645f
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831731"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Tutorial: Personalize o painel de instrumentos do operador e gere os dispositivos no Azure IoT Central


Neste tutorial, como construtor, aprende-se a personalizar o painel de instrumentos do operador na sua aplicação de análise Azure IoT Central na loja. Os operadores de aplicações podem utilizar o painel de instrumentos personalizado para executar a aplicação e gerir os dispositivos anexados.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Mude o nome do painel de instrumentos
> * Personalize os azulejos de imagem no tablier
> * Organize azulejos para modificar o layout
> * Adicione telhas de telemetria para mostrar as condições
> * Adicione azulejos de propriedade para mostrar detalhes do dispositivo
> * Adicione telhas de comando para executar comandos

## <a name="prerequisites"></a>Pré-requisitos

O construtor deve completar o tutorial para criar a aplicação de análise Azure IoT Central na loja e adicionar dispositivos:

* [Criar uma aplicação de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (Obrigatório)

## <a name="change-the-dashboard-name"></a>Mude o nome do painel de instrumentos
Para personalizar o painel de instrumentos do operador, tem de editar o painel de instrumentos predefinido na sua aplicação. Além disso, pode criar novos dashboards adicionais. O primeiro passo para personalizar o dashboard na sua aplicação é alterar o nome.

1. Navegue para o site do [gestor de aplicações Azure IoT Central.](https://aka.ms/iotcentral)

1. Abra a aplicação de monitorização de condições que criou na aplicação de análise na loja no tutorial [Azure IoT Central.](./tutorial-in-store-analytics-create-app.md)

1. Selecione **Editar** na barra de ferramentas do painel de instrumentos. No modo de edição, pode personalizar a aparência, layout e conteúdo do painel de instrumentos.

    ![Painel de edição central Azure IoT](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. Opcionalmente, esconda o painel esquerdo. Esconder o painel esquerdo dá-lhe uma área de trabalho maior para editar o painel de instrumentos.

1. Insira um nome amigável para o seu painel de instrumentos em **nome dashboard.** Este tutorial usa uma empresa fictícia chamada Contoso, e o nome do painel de instrumentos é *Contoso dashboard*. 

1. Selecione **Guardar**. As alterações são guardadas no painel de instrumentos e o modo de edição está desativado.

    ![Azure IoT Central mudar nome do painel de instrumentos](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Personalize os azulejos de imagem no tablier
Um painel de aplicações Azure IoT Central é composto por um ou mais azulejos. Um azulejo é um recipiente retangular para visualizar o conteúdo num painel de instrumentos. Associa vários tipos de conteúdo com azulejos, arrastando, largando e redimensionando azulejos para personalizar um layout do painel de instrumentos. Existem vários tipos de azulejos para exibir conteúdo. Os azulejos de imagem contêm imagens e pode adicionar um URL que permite aos utilizadores clicar na imagem. Os azulejos da etiqueta exibem texto simples. Os azulejos de markdown contêm conteúdo formatado e permitem definir uma imagem, um URL, um título e um código de marcação que torna HTML. A telemetria, a propriedade ou os azulejos de comando exibem dados específicos do dispositivo. 

Nesta secção, aprende-se a personalizar os azulejos de imagem no tablier.

Para personalizar o azulejo de imagem que exibe uma imagem de marca no painel de instrumentos:

1. Selecione **Editar** na barra de ferramentas do painel de instrumentos. 

1. **Selecione Configurar** no azulejo de imagem que exibe a imagem da marca Northwind. 

    ![Azure IoT Central editar imagem da marca](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Alterar o **título**. O título aparece quando um utilizador paira sobre a imagem.

1. Selecione **imagem**. Um diálogo abre e permite-lhe fazer o upload de uma imagem personalizada. 

1. Opcionalmente, especifique um URL para a imagem.

1. Selecione **a configuração de Atualização**. O **botão de configuração 'Actualização'** guarda alterações no painel de instrumentos e deixa o modo de edição ativado.

    ![Azure IoT Central salvar imagem da marca](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Opcionalmente, selecione **Configurar** na documentação intitulada **azulejos** e especifique um URL para o conteúdo de suporte. 

Para personalizar o azulejo de imagem que exibe um mapa das zonas sensoriais da loja:

1. **Selecione Configurar** no azulejo de imagem que exibe o mapa da zona de loja padrão. 

1. Selecione **Imagem** e use o diálogo para carregar uma imagem personalizada de um mapa da zona da loja. 

1. Selecione **a configuração de Atualização**.

    ![Mapa da loja de salvamento Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    O mapa da loja Contoso mostra quatro zonas: duas zonas de check-out, uma zona de vestuário e cuidados pessoais, e uma zona para mercearias e mercearias. Neste tutorial, irá associar sensores a estas zonas para fornecer telemetria.

    ![Zonas de loja Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Selecione **Guardar**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Organize azulejos para modificar o layout
Um passo chave na personalização de um dashboard é reorganizar os azulejos para criar uma visão útil. Os operadores de aplicação utilizam o painel de instrumentos para visualizar a telemetria do dispositivo, gerir dispositivos e monitorizar as condições numa loja. A Azure IoT Central simplifica a tarefa de construtor de aplicações de criar um dashboard. O modo de edição do painel permite-lhe adicionar, mover, redimensionar e eliminar os azulejos. O **modelo de aplicação de check-out in-store** também simplifica a tarefa de criar um dashboard. Fornece um layout de painel de trabalho, com sensores conectados, e azulejos que exibem contagens de linha de checkout e condições ambientais.

Nesta secção, você reorganiza o dashboard na **análise in-store -** modelo de aplicação de check-out para criar um layout personalizado.

Para remover azulejos que não pretende utilizar na sua aplicação:

1. Selecione **Editar** na barra de ferramentas do painel de instrumentos. 

1. Selecione **X Delete** para remover os seguintes azulejos: De volta a todas as zonas , Visite o painel de **instrumentos,** **o tempo de espera** e os três azulejos associados ao  **Checkout 3**. O painel de instrumentos da loja Contoso não usa estes azulejos. 

    ![Azure IoT Central elimina telhas](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Percorra para ver os restantes azulejos do painel de instrumentos.

1. Selecione **X Delete** para remover os seguintes azulejos: **Zona de check-out de aquecimento,** **zona de check-out de arrefecimento,** **definições de sensores de ocupação,** **definições do sensor do termóstato** e **condições ambientais**. 

   ![Azure IoT Central apagar os azulejos restantes](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Selecione **Guardar**. A remoção de azulejos não reutilizados liberta espaço na página de edição e simplifica a visão do painel de instrumentos para os operadores.

1. Veja as alterações no painel de instrumentos.

   ![Azure IoT Central após apagar azulejos](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

Depois de remover azulejos não reutilizados, reorganize os azulejos restantes para criar um layout organizado. O novo layout inclui espaço para azulejos que adiciona num passo posterior.

Para reorganizar os azulejos restantes:

1. Selecione **Editar**.

1. Selecione o telha **de firmware de ocupação** e arraste-o para a direita do azulejo da bateria de **ocupação.**

1. Selecione o telha **de firmware do termóstato** e arraste-o para a direita do telha da bateria **do termóstato.**

1. Selecione **Guardar**.

1. Veja as alterações de layout. 

    ![Azulejos de bateria de firmware Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Adicione telhas de telemetria para mostrar as condições
Depois de personalizar o layout do painel de instrumentos, está pronto para adicionar azulejos para mostrar telemetria. Para criar um azulejo de telemetria, selecione um modelo de dispositivo e uma instância do dispositivo e, em seguida, selecione telemetria específica do dispositivo para visualizar no azulejo. O **modelo de aplicação de check-out in-store** inclui vários azulejos de telemetria no painel de instrumentos. Os quatro azulejos das duas zonas de check-out exibem telemetria a partir do sensor de ocupação simulado. O **azulejo de tráfego do People** mostra contagens nas duas zonas de check-out. 

Nesta secção, adicione mais dois azulejos de telemetria para mostrar telemetria ambiental a partir dos sensores RuuviTag que adicionou na aplicação de análise na loja no tutorial [Azure IoT Central.](./tutorial-in-store-analytics-create-app.md) 

Para adicionar azulejos para exibir dados ambientais dos sensores RuuviTag:

1. Selecione **Editar**.

1. Selecione `RuuviTag` na lista **de modelos do dispositivo.** 

1. Selecione uma **instância do dispositivo** de um dos dois sensores RuuviTag. No exemplo da loja Contoso, selecione `Zone 1 Ruuvi` para criar um azulejo de telemetria para a Zona 1. 

1. Selecione `Relative humidity` e na lista de `temperature` **Telemetria.** Estes são os itens de telemetria que exibem para cada zona do azulejo.

1. Selecione **Combine**. 

    ![Azure IoT Central adicionar azulejo RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    Um novo azulejo parece apresentar humidade combinada e telemetria de temperatura para o sensor selecionado. 

1. **Selecione Configurar** o novo azulejo para o sensor RuuviTag. 

1. Alterar o **ambiente do título** para zona *1*. 

1. Selecione **a configuração de Atualização**.

1. Repita os passos anteriores para criar um azulejo para a segunda instância do sensor. Deslove o **ambiente do título** para a zona *2* e, em seguida, selecione **a configuração de Atualização.**

1. Arraste o ambiente de azulejos intitulado **Zona 2** abaixo do **telha de firmware do termóstato.** 

1. Arraste o azulejo intitulado **Zona 1 ambiente** abaixo do azulejo **de tráfego do Povo.** 

1. Selecione **Guardar**. O painel de instrumentos exibe telemetria de zona nos dois novos azulejos.

    ![Azure IoT Central todos os azulejos RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

Para editar o azulejo **de tráfego people** para mostrar telemetria para apenas duas zonas de checkout:

1. Selecione **Editar**. 

1. **Selecione Configurar** no azulejo **de tráfego do Povo.**

1. Na **Telemetria** selecione **a contagem 1**, a contagem **2**, e **a contagem 3**. 

1. Selecione **a configuração de Atualização**. Limpa a configuração existente no azulejo. 

1. **Selecione Configurar** novamente no azulejo de **tráfego do Povo.**

1. Na **Telemetria** selecione **a contagem 1**, e a contagem **2**. 

1. Selecione **a configuração de Atualização**. 

1. Selecione **Guardar**.  Os ecrãs atualizados do painel contam apenas para as suas duas zonas de check-out, que são baseadas no sensor de ocupação simulado.

    ![Azure IoT Central pessoas traficam duas faixas de rodagem](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Adicione azulejos de propriedade para mostrar detalhes do dispositivo
Os operadores de aplicações usam o painel de instrumentos para gerir os dispositivos e monitorizar o estado do monitor. Adicione um azulejo para cada RuuviTag para permitir que os operadores vejam a versão do software. 

Para adicionar um azulejo de propriedade para cada RuuviTag:

1. Selecione **Editar**.

1. Selecione `RuuviTag` na lista **de modelos do dispositivo.** 

1. Selecione uma **instância do dispositivo** de um dos dois sensores RuuviTag. No exemplo da loja Contoso, selecione `Zone 1 Ruuvi` para criar um azulejo de telemetria para a Zona 1. 

1. Selecione **Propriedades > versão software**.

1. Selecione **Combine**. 

1. **Selecione Configurar** na versão de **software** recém-criada. 

1. Alterar o **título** para a *versão de software Ruuvi 1*.

1. Selecione **a configuração de Atualização**. 

1. Arraste a versão de **software do azulejo Intitulada Ruuv 1** abaixo do azulejo ambiente zona **1.**

1. Repita os passos anteriores para criar um azulejo de propriedade de versão de software para o segundo RuuviTag. 

1. Selecione **Guardar**.  

    ![Azulejos de propriedade Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Adicione telhas de comando para executar comandos
Os operadores de aplicações também usam o dashboard para gerir os dispositivos através de comandos de execução. Pode adicionar telhas de comando ao painel de instrumentos que executará comandos predefinidos num dispositivo. Nesta secção, adicione um azulejo de comando para permitir aos operadores reiniciarem o gateway rigado. 

Para adicionar um azulejo de comando para reiniciar o portal:

1. Selecione **Editar**. 

1. Selecione `C500` na lista **de modelos do dispositivo.** É o modelo para o portal Rigado C500. 

1. Selecione a instância do gateway na **instância do dispositivo**.

1. Selecione **o Comando > reiniciar** e arrastá-lo para o painel de instrumentos ao lado do mapa da loja. 

1. Selecione **Guardar**. 

1. Veja o seu painel de contoso completo. 

    ![Azure IoT Central completa personalização do painel de instrumentos](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Opcionalmente, selecione o azulejo **Reboot** para executar o comando de reinicialização no seu gateway.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Mude o nome do painel de instrumentos
* Personalize os azulejos de imagem no tablier
* Organize azulejos para modificar o layout
* Adicione telhas de telemetria para mostrar as condições
* Adicione azulejos de propriedade para mostrar detalhes do dispositivo
* Adicione telhas de comando para executar comandos

Agora que personalizou o dashboard na sua aplicação de análise Azure IoT Central na loja, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Dados de exportação e visualização de insights](./tutorial-in-store-analytics-export-data-visualize-insights.md)
