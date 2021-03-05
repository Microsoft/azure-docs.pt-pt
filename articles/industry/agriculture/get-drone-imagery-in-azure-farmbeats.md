---
title: Obter imagens de drones
description: Este artigo descreve como obter imagens de drones de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 18932d2ddb60242b4d7874dddf0349a62cd5c738
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177611"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Obtenha imagens de drones de parceiros de drones

Este artigo descreve como pode trazer dados ortomososicos dos seus parceiros de imagem de drones para o Azure FarmBeats Datahub. Um ortomosaico é uma ilustração aérea ou imagem que é geometricamente corrigida e cosida a partir de dados recolhidos por um drone.

Atualmente, os seguintes parceiros de imagem são apoiados.

  ![Parceiros de imagens de drones FarmBeats](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

A integração de dados de imagens de drones com o Azure FarmBeats ajuda-o a obter dados ortomososicos dos voos de drones que realiza na sua quinta no datahub. Depois de os dados estiverem disponíveis, pode vê-lo no Acelerador FarmBeats. Os dados podem ser usados para a fusão de dados e inteligência artificial e construção de modelos de machine learning.

## <a name="before-you-begin"></a>Antes de começar

  - Certifique-se de que instalou a Azure FarmBeats. Para obter informações sobre como instalar o FarmBeats, consulte [instalar a Azure FarmBeats](install-azure-farmbeats.md).
  - Certifique-se de que tem a quinta para a qual pretende imagens de drones definidas no seu sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Permitir a integração de imagens de drones com FarmBeats

Forneça as seguintes informações ao seu fornecedor de dispositivos para permitir a integração com o FarmBeats:
 - Ponto final de API
 - ID do inquilino
 - ID de Cliente
 - Segredo do cliente

Siga estes passos.

1. Faça o download deste [script](https://aka.ms/farmbeatspartnerscript)e extrafissa-o para a sua unidade local. Dois ficheiros estão dentro do ficheiro zip.
2. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra o Azure Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal.

    ![Abra a Azure Cloud Shell na barra superior direita do portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Certifique-se de que o ambiente está definido para **PowerShell**.

    ![Definição powerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Faça o upload dos dois ficheiros que descarregou do passo 1 na sua instância Cloud Shell.

    ![Carregar ficheiros](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Vá ao diretório onde os ficheiros foram enviados. Por padrão, são enviados para o diretório doméstico sob o nome de utilizador.
6. Execute o seguintes script:

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. Siga as instruções no ecrã para capturar os valores de API Endpoint, ID do Inquilino, ID do Cliente, Segredo de Cliente e Cadeia de Conexão EventHub.

    Depois de inserir as credenciais necessárias no sistema de software de drones do parceiro, pode importar todas as quintas do sistema FarmBeats. Em seguida, você pode usar os detalhes da fazenda para fazer o seu planejamento de rotas de voo e coleção de imagem de drone.

    Depois de as imagens em bruto serem processadas pelo software dos fornecedores de drones, o sistema de software de drones envia as imagens ortomosasicas costuradas e outras imagens processadas para o datahub.

## <a name="view-drone-imagery"></a>Ver imagens de drones

Depois de os dados terem sido enviados para o datahub da FarmBeats, pode consultar a Loja de Cena utilizando as APIs do FarmBeats Datahub.

Em alternativa, pode ver a mais recente imagem de drone na página **Farm Details.** Para ver a imagem, siga os passos.

1. Selecione a quinta para a qual as suas imagens foram carregadas. A página de detalhes **da Fazenda** aparece.
2. Percorra a secção de **Mapas de Precisão** mais recente.
3. Veja a imagem na secção **Imagem de Drone.**

    ![Secção de Imagens de Drones](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Baixar imagens de drones

Ao selecionar a secção Imagery Drone, abre-se um pop-up para mostrar uma imagem de alta resolução do drone ortomosaico.

![Ortomosaico de alta resolução](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Ver todos os mapas de drones

Ficheiros e imagens enviados pelo fornecedor de drones aparecem na secção **Maps.** Selecione a secção **Mapa,** filtro por **Quinta** e selecione os ficheiros apropriados para visualizar e descarregar.

  ![Secção de mapas](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a usar as [APIs](rest-api-in-azure-farmbeats.md) do Datahub do FarmBeats para obter as suas imagens de drones.
