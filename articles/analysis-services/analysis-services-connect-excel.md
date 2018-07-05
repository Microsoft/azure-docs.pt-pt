---
title: Ligar ao Azure Analysis Services com o Excel | Documentos da Microsoft
description: Saiba como ligar a um servidor Azure Analysis Services com o Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 807496584acb3f93fccd3495de005792b769b37f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443005"
---
# <a name="connect-with-excel"></a>Ligar com o Excel

Depois de ter um servidor criado e implementado um modelo de tabela, os clientes podem ligar e começar a explorar os dados. 

## <a name="before-you-begin"></a>Antes de começar
A conta, que inicie sessão com têm de pertencer a uma função de base de dados de modelo com, pelo menos, permissões de leitura. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador). 

## <a name="connect-in-excel"></a>Ligue-se no Excel

A ligação a um servidor no Excel é suportada ao utilizar obter dados no Excel 2016. Não é suportada a ligar-se com o Assistente de importação de tabelas no Power Pivot. 

**Para ligar-se no Excel 2016**

1. No Excel 2016, sobre o **dados** Friso, clique em **obter dados externos** > **de outras origens** > **do Analysis Services** .

2. No Assistente de ligação de dados, no **nome do servidor**, introduza o nome de servidor, incluindo o protocolo e URI. Por exemplo, asazure://westcentralus.asazure.windows.net/advworks. Em seguida, na **credenciais de início de sessão**, selecione **utilize o seguinte nome de utilizador e palavra-passe**e, em seguida, escreva o nome de utilizador organizacional, por exemplo nancy@adventureworks.come a palavra-passe.

    > [!IMPORTANT]
    > Se iniciar sessão com um Microsoft Account, Live ID, Yahoo, Gmail, etc., ou tem de iniciar sessão com a autenticação multifator, deixe o campo de palavra-passe em branco. Lhe for pedida uma palavra-passe depois de clicar em seguinte. 

    ![Ligar a partir do início de sessão do Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Na **selecionar base de dados e tabela**, selecione a base de dados e o modelo ou perspetiva e, em seguida, clique em **concluir**.
   
    ![Ligar a partir de modelo selecione do Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Consulte também
[Bibliotecas de cliente](analysis-services-data-providers.md)   
[Gerir o seu servidor](analysis-services-manage.md)     


