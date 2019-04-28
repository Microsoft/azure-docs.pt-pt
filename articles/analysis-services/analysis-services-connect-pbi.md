---
title: Ligar ao Azure Analysis Services com o Power BI | Documentos da Microsoft
description: Saiba como ligar a um servidor Azure Analysis Services com o Power BI.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cb93c29ae4cb8d365cd128c52ee726c10c94bc19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023579"
---
# <a name="connect-with-power-bi"></a>Ligar com o Power BI

Depois de ter criado um servidor no Azure e implementado um modelo de tabela, os utilizadores na sua organização estão prontos para se ligar e começar a explorar os dados. 

> [!TIP]
> Certifique-se de que utiliza a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ligar no Power BI Desktop

1. No Power BI Desktop, clique em **Obter Dados** > **Azure** > **Base de dados do Azure Analysis Services**.

2. Na **servidor**, introduza o nome do servidor. Certifique-se de que incluem o URL completo; Por exemplo, asazure://westcentralus.asazure.windows.net/advworks.

3. Na **base de dados**, se souber o nome da base de dados do modelo de tabela ou ponto de vista que pretende ligar, cole-o aqui. Caso contrário, pode deixar este campo em branco e selecione uma base de dados ou de uma perspetiva mais tarde.

4. Selecione uma opção de ligação e, em seguida, prima **Connect**. 

    Ambos **ligar em direto** e **importação** as opções são suportadas. No entanto, recomendamos que utilizar ligações em direto porque o modo de importação tem algumas limitações; nomeadamente, desempenho do servidor de pode ser afetado durante a importação. Também, se o modelo é seja atualizado no serviço Power BI, o **permitir o acesso a partir do Power BI** definição aplica-se apenas ao escolher **ligar em direto**.

5. Se lhe for pedido, introduza as credenciais de início de sessão. 

6. Na **Navigator**, expanda o servidor, em seguida, selecione o modelo ou o ponto de vista que pretende ligar a e, em seguida, clique em **Connect**. Clique num modelo ou uma perspetiva para mostrar todos os objetos para exibição.

    O modelo é aberto no Power BI Desktop com um relatório em branco na vista de relatório. A lista de campos mostra todos os objetos de modelo não oculto. O estado de ligação é apresentado no canto inferior direito.

## <a name="connect-in-power-bi-service"></a>Ligue-se no Power BI (serviço)

1. Crie um ficheiro do Power BI Desktop com uma ligação em direto ao seu modelo no seu servidor.
2. Na [Power BI](https://powerbi.microsoft.com), clique em **obter dados** > **ficheiros**e, em seguida, localize e selecione o seu ficheiro. pbix.

## <a name="see-also"></a>Consulte também
[Ligar ao Azure Analysis Services](analysis-services-connect.md)   
[Bibliotecas de cliente](analysis-services-data-providers.md)

