---
title: Conectar-se a Azure Analysis Services com o Excel | Microsoft Docs
description: Saiba como se conectar a um servidor de Azure Analysis Services usando o Excel. Uma vez conectado, os usuários podem criar tabelas dinâmicas para explorar dados.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb76accfef94327386d0ff58d003782a9fe3b0d2
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573141"
---
# <a name="connect-with-excel"></a>Ligar com o Excel

Depois de criar um servidor e implantar um modelo de tabela nele, os clientes podem se conectar e começar a explorar os dados. 

## <a name="before-you-begin"></a>Antes de começar

A conta com a qual você entra deve pertencer a uma função de banco de dados modelo com pelo menos permissões de leitura. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador). 

## <a name="connect-in-excel"></a>Conectar no Excel

Há suporte para a conexão com um servidor no Excel usando obter dados no Excel 2016 e versões posteriores. Não há suporte para a conexão usando o assistente para importar tabela no Power Pivot. 

1. No Excel, na faixa de tipos de **dados** , clique em **obter dados externos** > **de outras fontes** > **de Analysis Services**.

2. No assistente de conexão de dados, em **nome do servidor**, insira o nome do servidor, incluindo protocolo e URI. Por exemplo, asazure://westcentralus.asazure.windows.net/advworks. Em seguida, em **credenciais de logon**, selecione **usar o seguinte nome de usuário e senha**e, em seguida, digite o nome de usuário organizacional, por exemplo nancy@adventureworks.come senha.

    > [!IMPORTANT]
    > Se você entrar com uma conta da Microsoft, Live ID, Yahoo, Gmail, etc., ou precisar entrar com a autenticação multifator, deixe o campo de senha em branco. Você será solicitado a fornecer uma senha depois de clicar em Avançar. 

    ![Conectar a partir do logon do Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Em **Selecionar Banco de dados e tabela**, selecione o banco de dados e o modelo ou a perspectiva e clique em **concluir**.
   
    ![Conectar do Excel selecionar modelo](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Consulte também

[Bibliotecas de cliente](analysis-services-data-providers.md)   
[Gerenciar seu servidor](analysis-services-manage.md)     


