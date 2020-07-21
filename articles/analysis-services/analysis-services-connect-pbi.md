---
title: Ligue-se aos Serviços de Análise Azure com o Power BI ; Microsoft Docs
description: Saiba como ligar-se a um servidor Azure Analysis Services utilizando o Power BI. Uma vez conectados, os utilizadores podem explorar dados de modelos.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f79ff9af1272ca908c7a5ba35fbaa1e8bb87fc50
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506715"
---
# <a name="connect-with-power-bi"></a>Ligar com o Power BI

Depois de criar um servidor em Azure, e implementar um modelo tabular para o mesmo, os utilizadores da sua organização estão prontos para se conectarem e começarem a explorar dados. 

> [!TIP]
> Certifique-se de que utiliza a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ligar no Power BI Desktop

1. No Power BI Desktop, clique em **Obter dados**  >  **Azure**  >  **Azure Analysis Services**.

2. No **Servidor,** insira o nome do servidor. Certifique-se de incluir o URL completo; por exemplo, asazure://westcentralus.asazure.windows.net/advworks.

3. Em **Base de Dados,** se souber o nome da base de dados ou perspetiva do modelo tabular a que pretende ligar, cole-a aqui. Caso contrário, pode deixar este campo em branco e selecionar uma base de dados ou perspetiva mais tarde.

4. Selecione uma opção de ligação e, em seguida, prima **Connect**. 

    As opções **Connect live** e **Import** são suportadas. No entanto, recomendamos que utilize ligações vivas porque o modo de importação tem algumas limitações; mais notavelmente, o desempenho do servidor pode ser impactado durante a importação. Além disso, se o modelo for atualizado no serviço Power BI, o **acesso a partir da** definição Power BI só se aplica ao escolher o Connect **live**.

5. Se solicitado, insira as suas credenciais de login. 

   > [!NOTE]
   > As contas de código de acesso único (OTP) não são suportadas. 

6. No **Navigator,** expanda o servidor e, em seguida, selecione o modelo ou perspetiva a que pretende ligar e, em seguida, clique em **Connect**. Clique num modelo ou perspetiva para mostrar todos os objetos para essa vista.

    O modelo abre no Power BI Desktop com um relatório em branco na vista do Relatório. A lista Campos apresenta todos os objetos de modelo não oculto. O estado de ligação é apresentado no canto inferior direito.

## <a name="connect-in-power-bi-service"></a>Ligar em Power BI (serviço)

1. Crie um ficheiro Power BI Desktop que tenha uma ligação ao vivo com o seu modelo no seu servidor.
2. No [Power BI,](https://powerbi.microsoft.com)clique em Obter Ficheiros **de**  >  **Dados**e, em seguida, localize e selecione o seu ficheiro .pbix.

## <a name="see-also"></a>Veja também
[Ligar-se aos Serviços de Análise Azure](analysis-services-connect.md)   
[Bibliotecas de cliente](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)

