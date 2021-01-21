---
title: Importar as informações do dispositivo
description: Defender os sensores IoT monitorizam e analisam o tráfego espelhado. Nestes casos, poderá querer importar dados para enriquecer a informação sobre dispositivos já detetados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb805f60ba9feb0ae2d1483b2ab2df4e03639d8
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625419"
---
# <a name="import-device-information-to-a-sensor"></a>Importar informações do dispositivo a um sensor

Um Azure Defender para monitores de sensores IoT e analisa o tráfego espelhado. Em alguns casos, devido a políticas de configuração de rede específicas da organização, algumas informações podem não ser transmitidas.

Nestes casos, poderá querer importar dados para enriquecer a informação sobre dispositivos já detetados. Estão disponíveis duas opções para importar informação aos sensores:

- **Importação do Mapa**: Atualizar o nome do dispositivo, tipo, grupo ou camada Purdue para o mapa.

- **Importação de Definições de Importação**: Dispositivo de importação OS, endereço IP, nível de correção ou estado de autorização.

## <a name="import-from-the-map"></a>Importar do mapa

Esta secção descreve como importar nomes de dispositivos, tipos, grupos ou camadas Purdue para o mapa do dispositivo. Faça isto do mapa.

Aqui estão os requisitos de importação:

- **Nomes**: Pode ser até 30 caracteres.

- **Tipo** ou **Camada Purdue**: Utilize as opções que aparecem na caixa de diálogo Propriedades do **Dispositivo.** (Clique à direita no dispositivo e selecione **Ver Propriedades**.)

- **Grupo de Dispositivos**: Crie um novo grupo de até 30 caracteres. 

> [!NOTE]
> Para evitar conflitos, não importe os dados que exportou de um sensor para outro sensor.

Importar:

1. No menu lateral, selecione **Dispositivos**.

2. No canto superior direito da janela **Dispositivos,** selecione :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Imagem da janela do dispositivo.":::

3. Selecione **dispositivos de exportação**. Uma vasta gama de informações aparece no ficheiro exportado. Estas informações incluem protocolos que o dispositivo utiliza e o estado de autorização do dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="A informação no ficheiro exportado.":::

4. No ficheiro CSV, altere apenas o nome do dispositivo, tipo, grupo e camada Purdue. Em seguida, guarde o ficheiro. 

   Utilize as normas de capitalização apresentadas no ficheiro exportado. Por exemplo, para a camada Purdue, use toda a capitalização de primeira letra.

5. A partir do menu de **importação/exportação** na janela do **dispositivo,** selecione **Dispositivos de Importação**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importar dispositivos através da janela do dispositivo.":::

6. Selecione **Dispositivos de Importação** e selecione o ficheiro CSV que pretende importar. As mensagens de estado de importação aparecem no ecrã até que a caixa de diálogo **dos Dispositivos de Importação** feche.

## <a name="import-from-import-settings"></a>Importação a partir de definições de importação

Esta secção descreve como importar o endereço IP do dispositivo, o sistema operativo, o nível de correção ou o estado de autorização para o mapa do dispositivo. Faça-o a partir da caixa de diálogo **'Definições de Importação'.**

Para importar o endereço IP, o sistema operativo e o nível de correção:

1. Faça o download do ficheiro [devices_info_2.2.8 e up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) do Centro de [Ajuda](https://cyberx-labs.zendesk.com/hc/en-us) e introduza a informação da seguinte forma:

   - **Endereço IP**: Introduza o endereço IP do dispositivo.

   - **Sistema operativo**: Selecione a partir da lista de drop-down.

   - **Última atualização**: Utilize o formato YYYY-MM-DD.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="O ecrã das opções.":::

2. No menu lateral, selecione **Definições de Importação**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importe as suas definições.":::

3. Para fazer o upload da configuração necessária, na secção **Informação** do Dispositivo, selecione **Adicionar** e carregar o ficheiro CSV que preparou.

Para importar o estado de autorização:

1. Faça o download e guarde o ficheiro [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) do Centro de Ajuda Do Defender para IoT. Verifique se guardou o ficheiro como CSV.

2. Insira a informação como:

   - **Endereço IP**: O endereço IP do dispositivo.

   - **Nome**: O nome do dispositivo autorizado. Certifique-se de que os nomes estão corretos. Nomes dados aos dispositivos na lista importada substituem nomes de substituição mostrados no mapa do dispositivo.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Ficheiros Excel com lista de dispositivos importados.":::

3. No menu lateral, selecione **Definições de Importação**.

4. Na secção **Dispositivos Autorizados,** selecione **Adicionar** e carregar o ficheiro CSV que guardou.

Quando a informação é importada, recebe alertas sobre dispositivos não autorizados para todos os dispositivos que não aparecem nesta lista.

## <a name="import-device-information-to-the-sensor"></a>Importar informações do dispositivo ao sensor

O sensor monitoriza e analisa o tráfego espelhado. Em alguns casos, devido a políticas de configuração de rede específicas da organização, algumas informações podem não ser transmitidas.

Nestes casos, poderá querer importar dados para enriquecer a informação do dispositivo em dispositivos já detetados. Estão disponíveis duas opções para importar informação aos sensores:

- **Importação do Mapa**: Atualizar o nome do dispositivo, tipo, grupo ou camada Purdue para o mapa.

- **Importação de Definições de Importação**: Dispositivo de importação OS, endereço IP, nível de correção ou estado de autorização.

### <a name="import-from-the-map"></a>Importar do mapa

Esta secção descreve como importar nomes de dispositivos, tipos, grupos ou camadas Purdue para o mapa do dispositivo. Faça isto do mapa.

Aqui estão os requisitos de importação:

- **Nomes**: Pode ser até 30 caracteres.

- **Tipo** ou **Camada Purdue**: Utilize as opções que aparecem na caixa de diálogo Propriedades do **Dispositivo.** (Clique à direita no dispositivo e selecione **Ver Propriedades**.)

- **Grupo de Dispositivos**: Crie um novo grupo de até 30 caracteres. 

> [!NOTE]
> Para evitar conflitos, não importe os dados que exportou de um sensor para outro sensor.

Importar:

1. No menu lateral, selecione **Dispositivos**.

2. No canto superior direito da janela **Dispositivos,** selecione :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="A janela para escolher o seu dispositivo.":::

3. Selecione **dispositivos de exportação**. Uma vasta gama de informações aparece no ficheiro exportado. Exemplos incluem protocolos que o dispositivo utiliza e o estado de autorização do dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="A informação no ficheiro exportado.":::

4. No ficheiro CSV, basta alterar o nome do dispositivo, tipo, grupo e camada Purdue. Em seguida, guarde o ficheiro. 

   Utilize as normas de capitalização apresentadas no ficheiro exportado. Por exemplo, para a camada Purdue, use toda a capitalização de primeira letra.

5. A partir do menu de **entrega de importação/exportação** na janela do **dispositivo,** selecione **Dispositivos de Importação**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importe os seus dispositivos.":::

6. Selecione **Dispositivos de Importação** e selecione o ficheiro CSV que pretende importar. As mensagens de estado de importação aparecem no ecrã até que a caixa de diálogo **dos Dispositivos de Importação** feche.

### <a name="import-from-import-settings"></a>Importação a partir de definições de importação 

Esta secção descreve como importar o endereço IP do dispositivo, o sistema operativo, o nível de correção ou o estado de autorização para o mapa do dispositivo. Faça-o a partir da caixa de diálogo **'Definições de Importação'.**

Para importar o endereço IP, o sistema operativo e o nível de correção:

1. Faça o download do ficheiro [devices_info_2.2.8 e up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) do Centro de [Ajuda](https://cyberx-labs.zendesk.com/hc/en-us) e introduza a informação da seguinte forma:

   - **Endereço IP**: O endereço IP do dispositivo.

   - **Sistema operativo**: Selecione a partir da lista de drop-down.

   - **Última atualização**: Utilize o formato YYYY-MM-DD.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="O conteúdo no ecrã.":::

2. No menu lateral, selecione **Definições de Importação**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Preencha o ecrã das definições de importação.":::

3. Para fazer o upload da configuração necessária, na secção **Informação** do Dispositivo, selecione **Adicionar** e carregar o ficheiro CSV que preparou.

Para importar o estado de autorização:

1. Faça o download e guarde o ficheiro [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) do Centro de Ajuda Do Defender para IoT. Verifique se guardou o ficheiro como CSV.

2. Insira a informação como:

   - **Endereço IP**: O endereço IP do dispositivo.

   - **Nome**: O nome do dispositivo autorizado. Verifique se os nomes estão corretos. Nomes dados aos dispositivos na lista importada substituem nomes de substituição mostrados no mapa do dispositivo.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="A lista de importação para o mapa do dispositivo.":::

3. No menu lateral, selecione **Definições de Importação**.

4. Na secção **Dispositivos Autorizados,** selecione **Adicionar** e carregar o ficheiro CSV que guardou.

Quando a informação é importada, recebe alertas sobre dispositivos não autorizados para todos os dispositivos que não aparecem nesta lista.

## <a name="see-also"></a>Ver também

[Controlar que tráfego é monitorizado](how-to-control-what-traffic-is-monitored.md)

[Investigar as deteções do sensor num inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
