---
title: Obter imagens de drones
description: Este artigo descreve como obter imagens de drones de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132054"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Obtenha imagens de drones de parceiros de drones

Este artigo descreve como pode trazer dados ortomosaicos dos seus parceiros de imagens de drones para o Azure FarmBeats Datahub. Um ortomosaico é uma ilustração ou imagem aérea que é geometricamente corrigida e cosida a partir de dados recolhidos por um drone.

Atualmente, são apoiados os seguintes parceiros de imagem.

  ![FarmBeats parceiros de imagens de drones](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Integrar dados de imagens de drones com O Azure FarmBeats ajuda-o a obter dados ortomosaicos dos voos de drones que realiza na sua quinta para o datahub. Depois de os dados disponíveis, pode vê-lo no Acelerador FarmBeats. Os dados podem ser usados para fusão de dados e inteligência artificial e construção de modelos de aprendizagem automática.

## <a name="before-you-begin"></a>Antes de começar

  - Certifique-se de que instalou O Azure FarmBeats. Para obter informações sobre como instalar FarmBeats, consulte [Instalar O Talhetes Azure](install-azure-farmbeats.md).
  - Certifique-se de que tem a quinta para a qual pretende imagens de drones definidas no seu sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Permitir a integração de imagens de drones com farmBeats

Forneça as seguintes informações ao seu fornecedor de dispositivos para permitir a integração com a FarmBeats:
 - Ponto final de API
 - ID do inquilino
 - ID de Cliente
 - Segredo do cliente

Siga estes passos.

1. Descarregue este [script](https://aka.ms/farmbeatspartnerscript)e extrai-o para a sua unidade local. Dois ficheiros estão dentro do ficheiro postal.
2. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra o Azure Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal.

    ![Open Azure Cloud Shell na barra superior direita do portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Certifique-se de que o ambiente está definido para **powerShell**.

    ![Definição PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Faça upload dos dois ficheiros que descarregou do passo 1 na sua instância Cloud Shell.

    ![Carregar ficheiros](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Vá ao diretório onde os ficheiros foram enviados. Por padrão, são enviados para o diretório inicial com o nome de utilizador.
6. Execute o seguintes script:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Siga as instruções no ecrã para capturar os valores de API Endpoint, ID do Inquilino, ID do Cliente, Segredo de Cliente e String de Conexão EventHub.

    Depois de introduzir as credenciais necessárias no sistema de software de drones do parceiro, pode importar todas as explorações do sistema FarmBeats. Depois, pode utilizar os detalhes da quinta para fazer o planeamento da sua rota de voo e a recolha de imagens de drones.

    Depois de as imagens em bruto serem processadas pelo software dos fornecedores de drones, o sistema de software de drones envia o ortomosaico costurado e outras imagens processadas para o datahub.

## <a name="view-drone-imagery"></a>Ver imagens de drones

Depois de os dados forem enviados para o datahub FarmBeats, pode consultar a Loja da Cena utilizando APIs FarmBeats Datahub.

Em alternativa, pode ver a mais recente imagem de drone na página Dedetalhes da **Fazenda.** Para ver a imagem, siga os passos.

1. Selecione a quinta para a qual as suas imagens foram carregadas. A página de detalhes **da Quinta** aparece.
2. Desça até a última secção do **Precision Maps.**
3. Veja a imagem na secção **Imagens drone.**

    ![Secção de Imagens de Drones](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Descarregue imagens de drones

Ao selecionar a secção Drone Imagery, abre-se um pop-up para mostrar uma imagem de alta resolução do ortomosaico do drone.

![Ortomosaico de alta resolução](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Ver todos os mapas de drones

Os ficheiros e imagens enviados pelo fornecedor de drones aparecem na secção **Maps.** Selecione a secção **Maps,** filtro por **Quinta,** e selecione os ficheiros apropriados para visualizar e descarregar.

  ![Secção de mapas](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a usar [as APIs](rest-api-in-azure-farmbeats.md) farmBeats Datahub para obter as suas imagens de drones.
