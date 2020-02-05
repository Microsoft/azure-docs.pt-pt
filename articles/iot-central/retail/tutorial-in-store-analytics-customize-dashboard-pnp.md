---
title: Tutorial - Personalize o painel de instrumentos do operador na Central Azure IoT
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
ms.openlocfilehash: fea3440dd7c017b85d6fffa2ec0fe5d26cd98b97
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022176"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Tutorial: Personalize o painel de instrumentos do operador e gereos dispositivos na Central Azure IoT


Neste tutorial, como construtor aprenda a personalizar o painel de instrumentos do operador na sua aplicação de análise Azure IoT Central na loja. Os operadores de aplicações podem utilizar o dashboard personalizado para executar a aplicação e gerir os dispositivos anexos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Alterar o nome do painel de instrumentos
> * Personalize azulejos de imagem no painel de instrumentos
> * Organize azulejos para modificar o layout
> * Adicione azulejos de telemetria para mostrar condições
> * Adicione azulejos de propriedade para exibir detalhes do dispositivo
> * Adicione azulejos de comando para executar comandos

## <a name="prerequisites"></a>Pré-requisitos

O construtor deve completar o tutorial para criar a aplicação de análise Azure IoT Central na loja e adicionar dispositivos:

* [Criar uma aplicação de análise na loja na Central Azure IoT](./tutorial-in-store-analytics-create-app-pnp.md) (Obrigatório)

## <a name="change-the-dashboard-name"></a>Alterar o nome do painel de instrumentos
Para personalizar o painel de instrumentos do operador, tem de editar o dashboard predefinido na sua aplicação. Além disso, pode criar novos dashboards adicionais. O primeiro passo para personalizar o dashboard na sua aplicação é alterar o nome.

1. Navegue para o site do gestor de [aplicações Azure IoT Central.](https://aka.ms/iotcentral)

1. Abra a aplicação de monitorização de condições que criou na aplicação de análise Create a store no tutorial [Central Azure IoT.](./tutorial-in-store-analytics-create-app-pnp.md)

1. **Selecione Editar** na barra de ferramentas do painel de instrumentos. No modo de edição, pode personalizar a aparência, layout e conteúdo do dashboard.

    ![Painel de edição Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. Opcionalmente, esconda o painel esquerdo. Esconder o painel esquerdo dá-lhe uma maior área de trabalho para editar o painel de instrumentos.

1. Introduza um nome amigável para o seu dashboard em **nome dashboard.** Este tutorial usa uma empresa fictícia chamada Contoso, e o nome do painel de exemplo é *Contoso dashboard*. 

1. Selecione **Guardar**. As alterações são guardadas no painel de instrumentos e o modo de edição está desativado.

    ![Azure IoT Central mudar nome do dashboard](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Personalize azulejos de imagem no painel de instrumentos
Um painel de aplicações Azure IoT Central é composto por um ou mais azulejos. Um azulejo é um recipiente retangular para exibir o conteúdo num painel de instrumentos. Você associa vários tipos de conteúdo com azulejos, e você arrasta, larga e redimensiona azulejos para personalizar um layout de dashboard. Existem vários tipos de azulejos para exibir conteúdo. Os azulejos de imagem contêm imagens e pode adicionar um URL que permite aos utilizadores clicar na imagem. Os azulejos da etiqueta exibem texto simples. Os azulejos de marcação contêm conteúdo formatado e permitem definir uma imagem, um URL, um título e código de marcação que se torna HTML. Telemetria, propriedade ou dados específicos do dispositivo de visualização de telhas de comando. 

Nesta secção, aprende-se a personalizar os azulejos de imagem no painel de instrumentos.

Para personalizar o azulejo de imagem que exibe uma imagem de marca no painel de instrumentos:

1. **Selecione Editar** na barra de ferramentas do painel de instrumentos. 

1. **Selecione Configure** no azulejo de imagem que exibe a imagem da marca Northwind. 

    ![Azure IoT Central edita imagem da marca](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. Alterar o **Título**. O título aparece quando um utilizador paira sobre a imagem.

1. Selecione **Imagem**. Um diálogo abre e permite-lhe carregar uma imagem personalizada. 

1. Opcionalmente, especifique um URL para a imagem.

1. Selecione **a configuração de atualização**. O botão de **configuração de atualização** guarda alterações no painel de instrumentos e deixa o modo de edição ativado.

    ![Azure IoT Central guarda imagem de marca](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Opcionalmente, **selecione Configure** no azulejo intitulado **Documentação**, e especifique um URL para o conteúdo do suporte. 

Para personalizar o azulejo de imagem que exibe um mapa das zonas de sensor na loja:

1. **Selecione Configurar** no azulejo de imagem que exibe o mapa de zona de loja predefinido. 

1. Selecione **Imagem**, e use o diálogo para carregar uma imagem personalizada de um mapa de zona de loja. 

1. Selecione **a configuração de atualização**.

    ![Mapa da loja Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    O mapa da loja Contoso mostra quatro zonas: duas zonas de check-out, uma zona de vestuário e cuidados pessoais, e uma zona para mercearias e mercearias. Neste tutorial, você irá associar sensores a estas zonas para fornecer telemetria.

    ![Zonas de loja central Azure IoT](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Selecione **Guardar**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Organize azulejos para modificar o layout
Um passo chave na personalização de um dashboard é reorganizar os azulejos para criar uma vista útil. Os operadores de aplicação usam o painel de instrumentos para visualizar a telemetria do dispositivo, gerir dispositivos e monitorizar as condições numa loja. A Azure IoT Central simplifica a tarefa de criação de um dashboard. O modo de edição do painel permite-lhe adicionar, mover, redimensionar e eliminar os azulejos rapidamente. O modelo **de aplicação in-store - o** modelo de aplicação de check-out também simplifica a tarefa de criar um dashboard. Fornece um layout de painel de instrumentos de trabalho, com sensores conectados, e azulejos que exibem contagens de linha de checkout e condições ambientais.

Nesta secção, reorganiza o dashboard na análise da loja - modelo de aplicação de **check-out** para criar um layout personalizado.

Para remover os azulejos que não pretende utilizar na sua aplicação:

1. **Selecione Editar** na barra de ferramentas do painel de instrumentos. 

1. Selecione **X Delete** para remover os **seguintes azulejos: Volte a todas as zonas,** visite o **painel de instrumentos da loja,** tempo de **espera,** e todos os três azulejos associados ao **Checkout 3**. O painel da loja Contoso não usa estes azulejos. 

    ![Azure IoT Central apaga azulejos](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Percorra para colocar os restantes azulejos do painel à vista.

1. Selecione **X Delete** para remover as seguintes telhas: **Zona de aquecimento,** zona de saída de **arrefecimento,** **regulações do sensor de ocupação,** regulações do sensor do **termóstato**e **condições**de ambiente . 

   ![Central Azure IoT apaga azulejos restantes](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Selecione **Guardar**. A remoção de azulejos não utilizados liberta espaço na página de edição e simplifica a vista do painel de instrumentos para os operadores.

1. Veja as suas alterações no painel de instrumentos.

   ![Central Azure IoT após apagar azulejos](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Depois de remover os azulejos não utilizados, reorganize os azulejos restantes para criar um layout organizado. O novo layout inclui espaço para azulejos que adiciona num passo posterior.

Para reorganizar os azulejos restantes:

1. Selecione **Editar**.

1. Selecione o azulejo de **firmware de ocupação** e arraste-o para a direita do azulejo da bateria **de Ocupação.**

1. Selecione o azulejo do **firmware termóstato** e arraste-o para a direita do azulejo da bateria do **termóstato.**

1. Selecione **Guardar**.

1. Veja as alterações de layout. 

    ![Telhas de bateria de firmware central Azure IoT](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Adicione azulejos de telemetria para mostrar condições
Depois de personalizar o layout do dashboard, está pronto para adicionar azulejos para mostrar telemetria. Para criar um azulejo de telemetria, selecione um modelo de dispositivo e uma instância do dispositivo e, em seguida, selecione a telemetria específica do dispositivo para exibir no azulejo. O modelo **de aplicação in-store - checkout** inclui vários azulejos de telemetria no painel de instrumentos. Os quatro azulejos das duas zonas de check-out exibem telemetria do sensor de ocupação simulado. O azulejo **de trânsito do Povo** mostra contagens nas duas zonas de check-out. 

Nesta secção, adicione mais dois azulejos de telemetria para mostrar telemetria ambiental dos sensores RuuviTag que adicionou na aplicação de análise Create a store no tutorial [Central Azure IoT.](./tutorial-in-store-analytics-create-app-pnp.md) 

Para adicionar azulejos para exibir dados ambientais dos sensores RuuviTag:

1. Selecione **Editar**.

1. Selecione `RuuviTag` na lista de modelos do **Dispositivo.** 

1. Selecione uma instância de **dispositivo** de um dos dois sensores RuuviTag. Na loja Exemplo Contoso, selecione `Zone 1 Ruuvi` para criar um azulejo de telemetria para a Zona 1. 

1. Selecione `Relative humidity` e `temperature` na lista de **Telemetria.** Estes são os itens de telemetria que exibem para cada zona no azulejo.

1. **Selecione Combine**. 

    ![Azure IoT Central adiciona azulejo RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Um novo azulejo parece apresentar humidade combinada e telemetria de temperatura para o sensor selecionado. 

1. **Selecione Configure** no novo azulejo para o sensor RuuviTag. 

1. Alterar o **título** para o ambiente da *Zona 1*. 

1. Selecione **a configuração de atualização**.

1. Repita os passos anteriores para criar um azulejo para a segunda instância do sensor. Detete o **título** para o *ambiente da Zona 2* e, em seguida, selecione a **configuração de Atualização.**

1. Arraste o **ambiente** de azulejos intitulado Zona 2 abaixo do azulejo do **termóstato.** 

1. Arraste o **azulejo** intitulado Ambiente zona 1 abaixo do azulejo **do tráfego das pessoas.** 

1. Selecione **Guardar**. O painel exibe telemetria de zona nos dois novos azulejos.

    ![Azure IoT Central todos os azulejos RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

Para editar o azulejo de **tráfego do Povo** para mostrar telemetria para apenas duas zonas de check-out:

1. Selecione **Editar**. 

1. **Selecione Configurar** no azulejo de **tráfego people.**

1. Na **Telemetria** selecione **contagem 1**, conte **2**, e conte **3**. 

1. Selecione **a configuração de atualização**. Limpa a configuração existente no azulejo. 

1. **Selecione Configurar** novamente no azulejo de **tráfego People.**

1. Na **Telemetria** selecione **a contagem 1**, e conte **2**. 

1. Selecione **a configuração de atualização**. 

1. Selecione **Guardar**.  Os ecrãs atualizados do dashboard contam apenas para as duas zonas de check-out, que são baseadas no sensor de ocupação simulado.

    ![Azure IoT Central pessoas traficam duas faixas de rodagem](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Adicione azulejos de propriedade para exibir detalhes do dispositivo
Os operadores de aplicação utilizam o painel de instrumentos para gerir os dispositivos e monitorizar o estado. Adicione um azulejo para cada RuuviTag para permitir que os operadores vejam a versão do software. 

Para adicionar um azulejo de propriedade para cada RuuviTag:

1. Selecione **Editar**.

1. Selecione `RuuviTag` na lista de modelos do **Dispositivo.** 

1. Selecione uma instância de **dispositivo** de um dos dois sensores RuuviTag. Na loja Exemplo Contoso, selecione `Zone 1 Ruuvi` para criar um azulejo de telemetria para a Zona 1. 

1. Selecione **Properties > Versão de Software.**

1. **Selecione Combine**. 

1. **Selecione Configure** na versão de software intitulada de **azulejos**recém-criados . 

1. Alterar o **Título** para *a versão de software Ruuvi 1*.

1. Selecione **a configuração de atualização**. 

1. Arraste a versão de **software Ruuv 1** intitulada Ruuv 1 abaixo do azulejo ambiente Zone **1.**

1. Repita os passos anteriores para criar um azulejo de propriedade de versão de software para o segundo RuuviTag. 

1. Selecione **Guardar**.  

    ![Azulejos de propriedade Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Adicione azulejos de comando para executar comandos
Os operadores de aplicação também usam o dashboard para gerir os dispositivos através da execução de comandos. Pode adicionar telhas de comando ao painel de instrumentos que executarão comandos predefinidos num dispositivo. Nesta secção, adicione um azulejo de comando para permitir que os operadores reiniciem o gateway Rigado. 

Para adicionar um azulejo de comando para reiniciar o portal:

1. Selecione **Editar**. 

1. Selecione `C500` na lista de modelos do **Dispositivo.** É o modelo para o gateway Rigado C500. 

1. Selecione a instância de gateway na **instância do Dispositivo**.

1. Selecione **Command > Reinicie-o** e arraste-o para o painel de instrumentos ao lado do mapa da loja. 

1. Selecione **Guardar**. 

1. Veja o seu painel de contoso completo. 

    ![Azure IoT Central completa personalização do dashboard](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Opcionalmente, selecione o azulejo **Reboot** para executar o comando de reinicialização no seu portal.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

* Alterar o nome do painel de instrumentos
* Personalize azulejos de imagem no painel de instrumentos
* Organize azulejos para modificar o layout
* Adicione azulejos de telemetria para mostrar condições
* Adicione azulejos de propriedade para exibir detalhes do dispositivo
* Adicione azulejos de comando para executar comandos

Agora que personalizou o dashboard na sua aplicação de análise Azure IoT Central na loja, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Dados de exportação e visualização de insights](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
