---
title: Conectar-se a Azure Analysis Services com Power BI | Microsoft Docs
description: Saiba como se conectar a um servidor de Azure Analysis Services usando Power BI. Uma vez conectado, os usuários podem explorar dados de modelo.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573108"
---
# <a name="connect-with-power-bi"></a>Ligar com o Power BI

Depois de criar um servidor no Azure e implantar um modelo de tabela nele, os usuários em sua organização estarão prontos para se conectar e começar a explorar os dados. 

> [!TIP]
> Certifique-se de usar a versão mais recente do [Power bi desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ligar no Power BI Desktop

1. No Power BI Desktop, clique em **Obter Dados** > **Azure** > **Base de dados do Azure Analysis Services**.

2. Em **servidor**, insira o nome do servidor. Certifique-se de incluir a URL completa; por exemplo, asazure://westcentralus.asazure.windows.net/advworks.

3. No **banco de dados**, se você souber o nome do banco de dados de modelo de tabela ou a perspectiva à qual você deseja se conectar, Cole-o aqui. Caso contrário, você pode deixar esse campo em branco e selecionar um banco de dados ou uma perspectiva mais tarde.

4. Selecione uma opção de conexão e, em seguida, pressione **conectar**. 

    Ambas as opções **conectar ao vivo** e **importar** têm suporte. No entanto, recomendamos que você use conexões dinâmicas porque o modo de importação tem algumas limitações; mais notavelmente, o desempenho do servidor pode ser afetado durante a importação. Além disso, se o modelo for ser atualizado no serviço do Power BI, a configuração **permitir acesso de Power bi** se aplicará somente ao escolher **conectar em tempo real**.

5. Se solicitado, insira suas credenciais de logon. 

6. No **navegador**, expanda o servidor e selecione o modelo ou a perspectiva à qual você deseja se conectar e, em seguida, clique em **conectar**. Clique em um modelo ou perspectiva para mostrar todos os objetos dessa exibição.

    O modelo é aberto em Power BI Desktop com um relatório em branco no modo de exibição de relatório. A lista Campos exibe todos os objetos de modelo não ocultos. O estado de ligação é apresentado no canto inferior direito.

## <a name="connect-in-power-bi-service"></a>Conectar-se no Power BI (serviço)

1. Crie um arquivo de Power BI Desktop que tenha uma conexão dinâmica com seu modelo em seu servidor.
2. Em [Power bi](https://powerbi.microsoft.com), clique em **obter dados** > **arquivos**e, em seguida, localize e selecione o arquivo. pbix.

## <a name="see-also"></a>Consulte também
[Conectar-se a Azure Analysis Services](analysis-services-connect.md)   
[Bibliotecas de cliente](analysis-services-data-providers.md)

