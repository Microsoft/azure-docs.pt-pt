---
title: Usar conjuntos de dispositivos em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como um operador, como usar conjuntos de dispositivos em seu aplicativo IoT Central do Azure.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 9576711c33979cef7e043c18ac3b56251dd8a806
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877347"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Usar conjuntos de dispositivos em seu aplicativo de IoT Central do Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como um operador, usar conjuntos de dispositivos em seu aplicativo IoT Central do Azure.

Um conjunto de dispositivos é uma lista de dispositivos agrupados, pois correspondem a alguns critérios especificados. Os conjuntos de dispositivos ajudam a gerenciar, Visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos lógicos menores. Por exemplo, você pode criar um dispositivo definido para listar todos os dispositivos de ar-condicionado em Seattle para permitir que um técnico encontre os dispositivos para os quais eles são responsáveis. Este artigo mostra como criar e configurar conjuntos de dispositivos.

## <a name="create-a-device-set"></a>Criar um conjunto de dispositivos

Para criar um conjunto de dispositivos:

1. Escolha **conjuntos de dispositivos** no menu de navegação à esquerda.

1. Selecione **+ novo**.

    ![Novo conjunto de dispositivos](media/howto-use-device-sets/image1.png)

1. Dê ao seu dispositivo um nome que seja exclusivo em todo o aplicativo. Também pode adicionar uma descrição. Um conjunto de dispositivos só pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo a ser usado para este conjunto.

1. Crie a consulta para identificar os dispositivos para o conjunto de dispositivos selecionando uma propriedade, um operador de comparação e um valor. Você pode adicionar várias consultas e dispositivos que atendem a **todos** os critérios são colocados no conjunto de dispositivos. O conjunto de dispositivos que você cria pode ser acessado por qualquer pessoa que tenha acesso ao aplicativo, para que qualquer pessoa possa exibir, modificar ou excluir o conjunto de dispositivos.

    ![Consulta de conjunto de dispositivos](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > O conjunto de dispositivos é uma consulta dinâmica. Toda vez que você exibir a lista de dispositivos, pode haver diferentes dispositivos na lista. A lista depende de quais dispositivos atendem atualmente aos critérios da consulta.

1. Escolha **Guardar**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configurar o painel para seu conjunto de dispositivos

Depois de criar o conjunto de dispositivos, você pode configurar seu **painel**. O **painel** é a página inicial onde você coloca imagens e links. Você também pode adicionar grades que listam os dispositivos no conjunto de dispositivos.

1. Escolha **conjuntos de dispositivos** no menu de navegação à esquerda.

1. Selecione o conjunto de dispositivos.

1. Selecione o separador **Dashboard** .

1. Selecione **Editar**.

    ![Modo de design ativado](media/howto-use-device-sets/image3.png)

1. Para obter informações sobre como adicionar uma imagem, consulte [preparar e carregar imagens para seu aplicativo de IOT central do Azure](howto-prepare-images.md).

1. Adicionar um bloco de link:
    1. Escolha **link** no painel direito.
    1. Dê um **título**ao seu link.
    1. Escolha uma URL a ser aberta quando o link for selecionado.
    1. Dê ao seu link uma descrição que mostre abaixo do **título**.
    1. Escolha **Guardar**.

        ![Salvar link](media/howto-use-device-sets/image7.png)

    1. Você pode mover e redimensionar o bloco de link no **painel**.

1. Adicione uma grade. Uma grade é uma tabela de dispositivos no conjunto de dispositivos com as colunas escolhidas.
    1. Escolha **grade** no painel direito.
    1. Dê um **título**à sua grade.
    1. Selecione as colunas a serem mostradas escolhendo **Adicionar/remover**. No painel que aparece, escolha a coluna que você deseja mostrar e escolha a seta para a direita para selecioná-la.
    1. Escolha **OK**.
    1. Escolha **Guardar**.

        ![Salvar grade](media/howto-use-device-sets/image9.png)

    1. Arraste e solte a grade para colocá-la no **painel**.

        > [!NOTE]
        > Você pode adicionar várias imagens, links e grades.
  
    1. Selecione **Done** (Concluído).

Para saber mais sobre como usar blocos no Azure IoT Central, confira [usar blocos do Dashboard](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Configurar um mapa de localização em seu painel de conjuntos de dispositivos

Você pode adicionar um mapa para visualizar o local dos dispositivos no seu conjunto de dispositivos.

Para adicionar um mapa ao seu painel de conjuntos de dispositivos, você deve ter configurado uma medida de local ou uma propriedade local em seu modelo de dispositivo. Para saber mais, confira [criar uma medida de local](howto-set-up-template.md) ou [criar uma propriedade de local](howto-set-up-template.md).

1. No **painel**do conjunto de dispositivos, selecione **mapa** na biblioteca.
2. Adicione um título e escolha a medida ou a propriedade local que você configurou anteriormente.
3. Selecione **salvar** e o bloco mapa exibe os últimos locais conhecidos dos dispositivos em seu conjunto de dispositivos.
4. Quando um operador exibe o painel de conjuntos de dispositivos, o operador vê todos os blocos que você configurou, incluindo o mapa de local.

Você pode redimensionar o bloco mapa no painel. A seleção de um PIN no mapa exibe as informações do dispositivo, o nome e o local. Selecione o pop-up para ir para a página de propriedades do dispositivo.

## <a name="configure-the-list-for-your-device-set"></a>Configurar a lista para seu conjunto de dispositivos

Depois de criar o conjunto de dispositivos, você pode configurar a **lista**. A **lista** mostra todos os dispositivos no conjunto de dispositivos em uma tabela com as colunas escolhidas.

1. Escolha **conjuntos de dispositivos** no menu de navegação à esquerda.

1. Escolha a guia **lista** .

1. Escolha **Opções de coluna**.

    ![Opções de coluna](media/howto-use-device-sets/image11.png)

1. Escolha as colunas a serem mostradas selecionando a coluna que você deseja mostrar e escolhendo a seta para a direita para selecioná-la.

    ![Escolher coluna](media/howto-use-device-sets/image12.png)

1. Escolha **OK**.

## <a name="analytics"></a>Análise

A análise em conjuntos de dispositivos é a mesma guia análise principal no menu de navegação à esquerda. Você pode aprender mais sobre a análise no artigo sobre [como criar análises](howto-use-device-sets.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a usar conjuntos de dispositivos em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules.md)
