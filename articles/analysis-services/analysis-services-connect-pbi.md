---
title: Ligue-se aos Serviços de Análise Azure com o Power BI [ Microsoft Docs
description: Aprenda a ligar-se a um servidor de Serviços de Análise Azure utilizando o Power BI. Uma vez conectados, os utilizadores podem explorar dados do modelo.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573108"
---
# <a name="connect-with-power-bi"></a>Ligar com o Power BI

Uma vez criado um servidor no Azure e implementado um modelo tabular para o mesmo, os utilizadores da sua organização estão prontos para se conectarem e começarem a explorar dados. 

> [!TIP]
> Certifique-se de utilizar a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ligar no Power BI Desktop

1. No Power BI Desktop, clique em **Obter dados** > **Azure** > **Azure Analysis Services**base de dados .

2. No **Servidor,** introduza o nome do servidor. Certifique-se de incluir o URL completo; por exemplo, asazure://westcentralus.asazure.windows.net/advworks.

3. Na **Base de Dados**, se souber o nome da base de dados ou perspetiva do modelo tabular, pretende ligar-se, cola-o aqui. Caso contrário, pode deixar este campo em branco e selecionar uma base de dados ou perspetiva mais tarde.

4. Selecione uma opção de ligação e, em seguida, prima **Connect**. 

    As opções **connect live** e **import** são suportadas. No entanto, recomendamos que utilize ligações ao vivo porque o modo Import tem algumas limitações; mais notavelmente, o desempenho do servidor pode ser impactado durante a importação. Além disso, se o modelo for refrescado no serviço Power BI, o **acesso permitir a partir da** definição power BI só se aplica na escolha do Connect **live**.

5. Se solicitado, insira as suas credenciais de login. 

6. No **Navigator,** expanda o servidor, depois selecione o modelo ou perspetiva a que pretende ligar e, em seguida, clique em **Connect**. Clique num modelo ou perspetiva para mostrar todos os objetos para essa vista.

    O modelo abre no Power BI Desktop com um relatório em branco na vista do Relatório. A lista Campos apresenta todos os objetos de modelo não oculto. O estado de ligação é apresentado no canto inferior direito.

## <a name="connect-in-power-bi-service"></a>Ligar em Power BI (serviço)

1. Crie um ficheiro Power BI Desktop que tenha uma ligação ao vivo com o seu modelo no seu servidor.
2. No [Power BI,](https://powerbi.microsoft.com)clique em Obter**Ficheiros** **de Dados** > e, em seguida, localize e selecione o seu ficheiro .pbix.

## <a name="see-also"></a>Consulte também
[Ligue-se aos Serviços de Análise Azure](analysis-services-connect.md)   
[Bibliotecas de cliente](analysis-services-data-providers.md)

