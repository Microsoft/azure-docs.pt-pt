---
title: Ligue-se aos Serviços de Análise Azure com o Excel Microsoft Docs
description: Aprenda a ligar-se a um servidor de Serviços de Análise Azure utilizando o Excel. Uma vez conectados, os utilizadores podem criar Tabelas Dinâmicas para explorar dados.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b5c46c8b85af59efaf611300e2ab7129b3ef3cde
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411375"
---
# <a name="connect-with-excel"></a>Ligar com o Excel

Uma vez criado um servidor, e implementado um modelo tabular para ele, os clientes podem conectar-se e começar a explorar dados. 

## <a name="before-you-begin"></a>Antes de começar

A conta com a sua inscrição deve pertencer a uma função de base de dados modelo com pelo menos permissões de leitura. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador). 

## <a name="connect-in-excel"></a>Conecte-se em Excel

A ligação a um servidor no Excel é suportada através da utilização do Get Data no Excel 2016 e posteriormente. A ligação utilizando o Assistente da Tabela de Importação no Power Pivot não é suportada. 

1. No Excel, na fita **Data,** clique em **Obter Dados Externos** > **de Outras Fontes** > **dos Serviços de Análise**.

2. No Assistente de Ligação de Dados, no **nome do Servidor,** introduza o nome do servidor incluindo protocolo e URI. Por exemplo, asazure://westcentralus.asazure.windows.net/advworks. Em seguida, nas **credenciais de Início**de Sessão , selecione Utilize nancy@adventureworks.como seguinte Nome de Utilizador e **Palavra-passe,** e, em seguida, digite o nome de utilizador organizacional, por exemplo , e palavra-passe.

    > [!IMPORTANT]
    > Se iniciar sessão com uma Conta Microsoft, ID ao vivo, Yahoo, Gmail, etc., ou se for obrigado a iniciar sessão com a autenticação de vários fatores, deixe o campo de palavra-passe em branco. É solicitado a uma senha depois de clicar em Seguinte. 

    ![Conecte-se a partir do logon excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Na Base de **Dados e Tabela Select,** selecione a base de dados e o modelo ou perspetiva e, em seguida, clique em **Terminar**.
   
    ![Conecte-se a partir do modelo selecionado do Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Consulte também

[Bibliotecas de clientes](analysis-services-data-providers.md)   
[Gerir o seu servidor](analysis-services-manage.md)     


