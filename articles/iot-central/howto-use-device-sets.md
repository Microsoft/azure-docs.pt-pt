---
title: Conjuntos de dispositivos de utilização na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como um operador, como utilizar o dispositivo define na sua aplicação do Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: e1e7b91e0808b9e23e653acd43b95f24a46c7d27
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503203"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Utilize conjuntos de dispositivo na aplicação do Azure IoT Central

Este artigo descreve como, como um operador, para utilizar o dispositivo conjuntos na sua aplicação do Azure IoT Central.

Um conjunto de dispositivos é uma lista de dispositivos que são agrupados em conjunto, porque eles correspondem a critérios especificados. Dispositivo define ajuda que gere, visualizar, analisar e dispositivos à escala através do agrupamento de dispositivos em grupos mais pequenos e lógicos. Por exemplo, pode criar um dispositivo definido para listar todos os dispositivos de ar-condicionado em Seattle para ativar um técnico para encontrar os dispositivos para os quais eles são responsáveis. Este artigo mostra-lhe como criar e configurar conjuntos de dispositivo.

## <a name="create-a-device-set"></a>Criar um conjunto de dispositivos

Para criar um conjunto de dispositivos:

1. Escolher **conjuntos de dispositivo** no menu de navegação esquerdo.

1. Selecione **+ nova**.

    ![Novo conjunto de dispositivos](media/howto-use-device-sets/image1.png)

1. Dê um nome que seja exclusivo em toda a aplicação ao seu conjunto de dispositivos. Também pode adicionar uma descrição. Um conjunto de dispositivos só pode conter os dispositivos a partir de um modelo de único dispositivo. Escolha o modelo de dispositivo a utilizar para este conjunto.

1. Crie a consulta para identificar os dispositivos para o dispositivo definida ao selecionar uma propriedade, um operador de comparação e um valor. Pode adicionar várias consultas e dispositivos que cumprem **todos os** os critérios são colocados no conjunto de dispositivos. O conjunto de dispositivo que criou está acessível a qualquer pessoa que tenha acesso à aplicação, para que qualquer pessoa pode ver, modificar ou eliminar o conjunto de dispositivos.

    ![Consulta de conjunto de dispositivos](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > O conjunto de dispositivos é uma consulta dinâmica. Sempre que exibir a lista de dispositivos, pode haver diferentes dispositivos na lista. A lista depende de quais os dispositivos atualmente cumprem os critérios da consulta.

1. Escolha **Guardar**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configurar o dashboard para o seu conjunto de dispositivos

Depois de criar o conjunto de dispositivos, pode configurar seus **Dashboard**. O **Dashboard** é a home page de onde vai colocar imagens e links. Também pode adicionar grades que listam os dispositivos no conjunto de dispositivos.

1. Escolher **conjuntos de dispositivo** no menu de navegação esquerdo.

1. Selecione o seu conjunto de dispositivos.

1. Selecione o separador **Dashboard** .

1. Selecione **Editar**.

    ![Modo de design no](media/howto-use-device-sets/image3.png)

1. Para obter informações sobre como adicionar uma imagem, veja [preparar e carregar imagens para a sua aplicação do Azure IoT Central](howto-prepare-images.md).

1. Adicione um mosaico de ligação:
    1. Escolher **Link** no painel direito.
    1. Dar a sua ligação de um **Title**.
    1. Escolha um URL para seja aberta quando a ligação está selecionada.
    1. Forneça uma descrição que mostra abaixo da ligação a **Title**.
    1. Escolha **Guardar**.

        ![Guardar a ligação](media/howto-use-device-sets/image7.png)

    1. Pode mover e redimensionar o mosaico de ligação no **Dashboard**.

1. Adicione uma grade. Uma grade é uma tabela de dispositivos em dispositivos conjunto com as colunas que escolher.
    1. Escolher **Grid** no painel direito.
    1. Dê sua grelha de um **Title**.
    1. Selecione as colunas a serem apresentados ao escolher **Adicionar/remover**. No painel que aparece, escolha a coluna que apresentar e selecione a seta para a direita para selecioná-lo.
    1. Escolha **OK**.
    1. Escolha **Guardar**.

        ![Guardar a grelha](media/howto-use-device-sets/image9.png)

    1. Arraste e largue a grade colocá-lo na **Dashboard**.

        > [!NOTE]
        > Pode adicionar várias imagens, ligações e grades.
  
    1. Selecione **Done** (Concluído).

Para saber mais sobre como utilizar mosaicos no Azure IoT Central, consulte [utilizar mosaicos de dashboard](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Configurar um mapa de localização no seu dashboard de conjuntos de dispositivo

Pode adicionar um mapa para visualizar a localização dos dispositivos no seu conjunto de dispositivos.

Para adicionar que um mapa para o seu dispositivo define o dashboard, tem de ter configurado uma medida de localização ou a propriedade de localização no seu modelo de dispositivo. Para obter mais informações, consulte [criar uma medida de localização](howto-set-up-template.md) ou [criar uma propriedade de localização](howto-set-up-template.md).

1. No seu dispositivo definido **Dashboard**, selecione **mapa** da biblioteca.
2. Adicionar um título e escolher a medida de localização ou propriedade que configurou anteriormente.
3. Selecione **guardar** e o mosaico do mapa apresenta os último locais conhecidos dos dispositivos no seu conjunto de dispositivos.
4. Quando um operador visualiza o dashboard de conjuntos de dispositivo, o operador vê todos os mosaicos que tiver configurado, incluindo o mapa de localização.

Pode redimensionar o mosaico de mapa no dashboard. A seleção de um pin no mapa apresenta as informações do dispositivo, o nome e localização. Selecione o pop-up para ir para a página de propriedades do dispositivo.

## <a name="configure-the-list-for-your-device-set"></a>Configurar a lista para o seu conjunto de dispositivos

Depois de criar o conjunto de dispositivos, pode configurar o **lista**. O **lista** mostra todos os dispositivos com o dispositivo definidos numa tabela com as colunas que escolher.

1. Escolher **conjuntos de dispositivo** no menu de navegação esquerdo.

1. Escolha o **lista** separador.

1. Escolher **opções de coluna**.

    ![Opções de coluna](media/howto-use-device-sets/image11.png)

1. Escolha as colunas a serem apresentados ao selecionar a coluna que pretende mostrar e selecionar a seta para a direita para selecioná-lo.

    ![Escolher coluna](media/howto-use-device-sets/image12.png)

1. Escolha **OK**.

## <a name="analytics"></a>Análise

A análise em conjuntos de dispositivo é o mesmo que o principal do separador análise no menu de navegação esquerdo. Pode saber mais sobre a análise no artigo na [como criar análises](howto-use-device-sets.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar conjuntos de dispositivos na sua aplicação do Azure IoT Central, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules.md)
