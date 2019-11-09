---
title: Obter imagens do drone
description: Descreve como obter as imagens de drone dos parceiros
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 19d709a2eb0af5bdb9d40a2bce96c5716ce3c6f6
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847420"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Obter imagens do drone de parceiros do drone

Este artigo descreve como você pode inserir dados do orthomosaic de seus parceiros do drone de imagens no Hub de dados FarmBeats do Azure. Um orthomosaic é uma ilustração/imagem aérea, que é corrigida geométrica e colado dos dados coletados pelo drone.

Atualmente, há suporte para os seguintes parceiros de imagens.

  ![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

A integração de dados de imagens de drone com o Azure FarmBeats ajuda a obter dados de orthomosaic dos voos drone que você realiza em seu farm no Hub de dados. Depois que os dados estiverem disponíveis, você poderá exibi-los no acelerador de FarmBeats, e ele pode ser usado para a fusão de dados e criação de modelo de ia/ML.

## <a name="before-you-begin"></a>Antes de começar

  - Verifique se você implantou o Azure FarmBeats. Para implantar, visite [implantar FarmBeats](prepare-for-deployment.md).
  - Verifique se você tem o farm (para o qual você deseja imagens de drone) definido no sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Habilitar a integração de imagens do drone com o FarmBeats   

Você precisa fornecer as seguintes informações ao seu provedor de dispositivo para habilitar a integração com o FarmBeats:  
 - Ponto Final de API  
 - ID do inquilino  
 - ID de Cliente  
 - Segredo do cliente  

Utilize os passos seguintes:

1. Baixe esse [script](https://aka.ms/farmbeatspartnerscript) e extraia-o em em sua unidade local. Você encontrará dois arquivos dentro desse arquivo ZIP.  
2. Entre em [portal do Azure](https://portal.azure.com/) e Abra Cloud Shell (essa opção está disponível na barra superior direita do Portal).   

    ![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Verifique se o ambiente está definido para o **PowerShell**

    ![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Carregue os dois arquivos que você baixou (da etapa 1 acima) em seu Cloud Shell.  

    ![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Vá para o diretório onde os arquivos foram carregados. (Por padrão, ele é carregado para o diretório base > nome de usuário.)  
6. Execute o seguintes script:

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```

7. Siga as instruções na tela para capturar os valores de ponto de extremidade de API, ID de locatário, ID do cliente, segredo do cliente e cadeia de conexão do EventHub.

    Depois de inserir as credenciais necessárias no sistema de software drone do parceiro, você poderá importar todos os farms do sistema FarmBeats e usar os detalhes do farm para fazer o planejamento do caminho de voo e a coleção de imagens de drone.

    Depois que as imagens brutas são processadas pelo software dos provedores do drone, o sistema de software drone carrega o orthomosaic colado e outras imagens processadas no Hub de dados.

## <a name="view-drone-imagery"></a>Exibir imagens de drone

Depois que os dados forem enviados para o Hub de dados do FarmBeats, você deverá ser capaz de consultar o repositório de cena usando as APIs do hub de dados do FarmBeats.

Como alternativa, você deve ser capaz de exibir a imagem mais recente do drone na página de **detalhes do farm** . Para exibir, siga as etapas:  

1. Selecione o farm para o qual suas imagens foram carregadas. A página detalhes do **farm** é exibida.
2. Role para baixo até a seção de **mapas de precisão** mais recente.
3. Você deve ser capaz de exibir a imagem na seção de **imagens drone** .

    ![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Baixar imagens do drone

Quando você seleciona a seção imagens de drone, um pop-up é aberto para mostrar uma imagem de alta resolução do drone orthomosaic.

![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Exibir todos os mapas do drone

Os arquivos e as imagens carregadas pelo provedor drone aparecem na seção mapas. Selecione a seção **mapas** , filtre por **farm** e escolha os arquivos apropriados para exibir e baixar:

  ![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como usar as [APIs](references-for-farmbeats.md#rest-api) do hub de dados FarmBeats para obter suas imagens do drone.
