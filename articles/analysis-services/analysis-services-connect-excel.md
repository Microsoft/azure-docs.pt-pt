---
title: Ligue-se aos Serviços de Análise Azure com o Excel ! Microsoft Docs
description: Saiba como ligar-se a um servidor Azure Analysis Services utilizando o Excel. Uma vez conectados, os utilizadores podem criar Tabelas Demômutárias para explorar dados.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 123e271ae1b83603d599b9ef0381e25b3c963def
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361562"
---
# <a name="connect-with-excel"></a>Ligar com o Excel

Uma vez criado um servidor e implantado um modelo tabular, os clientes podem conectar-se e começar a explorar dados. 

## <a name="before-you-begin"></a>Antes de começar

A conta com que insinou deve pertencer a uma função de base de dados modelo com, pelo menos, permissões de leitura. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador). 

## <a name="connect-in-excel"></a>Conecte-se no Excel

A ligação a um servidor no Excel é suportada utilizando o Get Data no Excel 2016 e mais tarde. A ligação utilizando o Assistente de Tabela de Importação no Pivô de Potência não é suportada. 

1. No Excel, na fita **de dados,** clique em **Obter Dados Externos**  >  **de Outras Fontes**dos  >  **Serviços de Análise**.

2. No Assistente de Ligação de Dados, em **nome do Servidor,** insira o nome do servidor, incluindo o protocolo e o URI. Por exemplo, asazure://westcentralus.asazure.windows.net/advworks. Em seguida, nas **credenciais de Logon**, selecione **Use o nome de utilizador e palavra-passe seguintes**, e, em seguida, digite o nome de utilizador organizacional, por exemplo , e a nancy@adventureworks.com palavra-passe.

    > [!IMPORTANT]
    > Se iniciar sedutação com uma Conta Microsoft, ID ao Vivo, Yahoo, Gmail, etc., ou se tiver de iniciar sôm entrada com autenticação multi-factor, deixe o campo de palavra-passe em branco. É solicitado uma palavra-passe depois de clicar em Seguinte. 

    ![Conecte-se a partir do início do Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Na **Base de Dados e Tabela Seletivas,** selecione a base de dados e o modelo ou perspetiva e, em seguida, clique em **Terminar**.
   
    ![Conecte-se a partir do modelo de seleção do Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Ver também

[Bibliotecas de clientes](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[Gerencie o seu servidor](analysis-services-manage.md)     


