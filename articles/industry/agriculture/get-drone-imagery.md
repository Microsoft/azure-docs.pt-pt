---
title: Obter imagens de drones
description: Este artigo descreve como obter imagens de drone de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 899abc4529e4ca24a4d8c6334d458c968a1fcffa
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851252"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Obter imagens do drone de parceiros do drone

Este artigo descreve como você pode inserir dados do orthomosaic de seus parceiros de drone de imagens no Azure FarmBeats Datahub. Um orthomosaic é uma ilustração ou imagem aérea que é corrigida geométrica e colado de dados coletados por um drone.

No momento, há suporte para os seguintes parceiros de imagens.

  ![Parceiros de FarmBeats drone de imagens](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

A integração de dados de imagens de drone com o Azure FarmBeats ajuda a obter dados de orthomosaic dos voos drone que você realiza em seu farm no Hub de dados. Depois que os dados estiverem disponíveis, você poderá exibi-los no acelerador de FarmBeats. Os dados podem ser usados para fusão de dados e inteligência artificial e criação de modelo de aprendizado de máquina.

## <a name="before-you-begin"></a>Antes de começar

  - Certifique-se de que você implantou o Azure FarmBeats. Para obter informações sobre como implantar o FarmBeats, consulte [implantar o FarmBeats](prepare-for-deployment.md).
  - Certifique-se de que você tenha o farm para o qual você deseja drone imagens definidas no sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Habilitar a integração de imagens do drone com o FarmBeats 

Forneça as seguintes informações ao seu provedor de dispositivo para habilitar a integração com o FarmBeats: 
 - Ponto de extremidade de API 
 - ID do inquilino 
 - ID de Cliente 
 - Segredo do cliente 

Siga estes passos.

1. Baixe esse [script](https://aka.ms/farmbeatspartnerscript)e extraia-o para a unidade local. Dois arquivos estão dentro do arquivo zip. 
2. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra o Azure Cloud Shell. Essa opção está disponível na barra de ferramentas no canto superior direito do Portal.

    ![Abrir Azure Cloud Shell na barra superior direita do portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Verifique se o ambiente está definido como **PowerShell**.

    ![Configuração do PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Carregue os dois arquivos que você baixou da etapa 1 em sua instância de Cloud Shell. 

    ![Carregar ficheiros](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Vá para o diretório onde os arquivos foram carregados. Por padrão, eles são carregados para o diretório base sob o nome de usuário. 
6. Execute o seguintes script:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Siga as instruções na tela para capturar os valores de ponto de extremidade de API, ID de locatário, ID do cliente, segredo do cliente e cadeia de conexão do EventHub.

    Depois de inserir as credenciais necessárias no sistema de software drone do parceiro, você pode importar todos os farms do sistema FarmBeats. Em seguida, você pode usar os detalhes do farm para fazer sua coleção de imagens de drone e planejamento de caminho de voo.

    Depois que as imagens brutas são processadas pelo software dos provedores do drone, o sistema de software drone carrega o orthomosaic colado e outras imagens processadas no Hub de dados.

## <a name="view-drone-imagery"></a>Exibir imagens de drone

Depois que os dados são enviados para o Hub de dados FarmBeats, você pode consultar o repositório de cena usando as APIs Datahub do FarmBeats.

Como alternativa, você pode exibir a imagem mais recente do drone na página de **detalhes do farm** . Para exibir a imagem, siga as etapas. 

1. Selecione o farm para o qual suas imagens foram carregadas. A página detalhes do **farm** é exibida.
2. Role para baixo até a seção de **mapas de precisão** mais recente.
3. Exiba a imagem na seção **imagens de drone** .

    ![Seção de imagens drone](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Baixar imagens do drone

Quando você seleciona a seção imagens de drone, um pop-up é aberto para mostrar uma imagem de alta resolução do drone orthomosaic.

![Orthomosaic de alta resolução](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Exibir todos os mapas do drone

Os arquivos e as imagens carregadas pelo provedor drone aparecem na seção **mapas** . Selecione a seção **mapas** , filtre por **farm**e selecione os arquivos apropriados para exibir e baixar.

  ![Seção de mapas](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como usar as [APIs](references-for-farmbeats.md#rest-api) do FarmBeats Datahub para obter suas imagens do drone.
