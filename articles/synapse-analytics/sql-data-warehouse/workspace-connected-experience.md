---
title: Habilitando funcionalidades de espaço de trabalho synapse em uma piscina SQL dedicada (anteriormente SQL DW)
description: Este documento descreve como um cliente pode aceder e usar o seu caso autónomo SQL DW existente no espaço de trabalho.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694619"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Habilitando funcionalidades de espaço de trabalho synapse em uma piscina SQL dedicada existente (anteriormente SQL DW)

Todos os clientes do armazém de dados SQL podem agora aceder e utilizar uma caixa de PISCINA SQL (anteriormente SQL DW) existente através do Synapse Studio e Workspace, sem afetar a automação, conexões ou ferramentas. Este artigo explica como um cliente Azure Synapse Analytics existente pode construir e expandir a sua solução analítica existente, tirando partido das novas capacidades ricas em recursos agora disponíveis através do espaço de trabalho synapse e estúdio.   

## <a name="experience"></a>Experiência
 
Agora que o espaço de trabalho da Synapse é GA, uma nova capacidade está disponível na secção de visão geral do Portal DW que lhe permite criar um espaço de trabalho Synapse para as suas instâncias existentes de piscina SQL (anteriormente SQL DW). Esta nova capacidade permitir-lhe-á ligar o servidor lógico que hospeda as suas instâncias de armazém de dados existentes a um novo espaço de trabalho da Synapse. A ligação garante que todos os armazéns de dados alojados nesse servidor são acessíveis a partir do Workspace e Studio e podem ser usados em conjunto com os serviços de parceiros Synapse (piscina SQL sem servidor, piscina Apache Spark e ADF). Pode começar a aceder e utilizar os seus recursos assim que os passos de provisionamento estiverem concluídos e a ligação ao espaço de trabalho recém-criado.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Espaço de trabalho da Sinapse conectado":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Utilizando o espaço de trabalho da Synapse e as funcionalidades do Estúdio para aceder e utilizar uma piscina SQL dedicada (anteriormente SQL DW)
 
As seguintes informações aplicar-se-ão quando utilizar um SQL DW dedicado (anteriormente SQL DW) com as funcionalidades do espaço de trabalho Synapse ativadas: 
- **Capacidades SQL** Todas as capacidades SQL permanecerão com o servidor SQL lógico após a ativação da função de espaço de trabalho Synapse. O acesso ao servidor através do fornecedor de recursos SQL continuará a ser possível depois de o espaço de trabalho ter sido ativado. Todas as funções de gestão podem ser iniciadas através do espaço de trabalho e a operação terá lugar no Servidor SQL Lógico que hospeda as suas piscinas SQL. Nenhuma automatização, ferramentas ou ligações existentes serão quebradas ou interrompidas quando um espaço de trabalho estiver ativado.  
- **Movimento de recursos**  Iniciar uma mudança de recursos num Servidor com a funcionalidade de espaço de trabalho Synapse ativada fará com que a ligação entre o servidor e o espaço de trabalho seja avariada e deixará de poder aceder às instâncias existentes da piscina SQL (anteriormente SQL DW) a partir do espaço de trabalho. Para garantir a manutenção da ligação, recomenda-se que ambos os recursos permaneçam dentro do mesmo grupo de subscrição e recursos. 
- **Monitorização** Os pedidos SQL submetidos através do Synapse Studio num espaço de trabalho habilitado a piscina SQL (anteriormente SQL DW) podem ser vistos no centro do Monitor. Para todas as outras atividades de monitorização, você pode ir ao portal Azure dedicada à monitorização da piscina SQL (anteriormente SQL DW). 
- **Controlos de segurança** e **acesso** Tal como acima indicado, todas as funções de gestão para o seu servidor SQL e as caixas SQL dedicadas (anteriormente SQL DW) continuarão a residir no servidor lógico SQL. Estas funções incluem, gestão de regras de firewall, definição do administrador AD AD do servidor, e todo o controlo de acesso para os dados na sua piscina de SQL dedicada (anteriormente SQL DW). Devem ser tomadas as seguintes medidas para garantir que a sua piscina SQL dedicada (anteriormente SQL DW) esteja acessível e possa ser utilizada através do espaço de trabalho synapse. As associações de funções do espaço de trabalho não dão aos utilizadores permissões aos dados em casos dedicados de piscina SQL (anteriormente SQL DW). Siga as suas políticas normais de [autenticação SQL](sql-data-warehouse-authentication.md) para garantir que os utilizadores possam aceder às instâncias dedicadas da piscina SQL (anteriormente SQL DW) no servidor lógico. Se o servidor anfitrião da piscina SQL (anteriormente SQL DW) tiver uma identidade gerida já lhe atribuída, o nome desta identidade gerida será o mesmo que o do Workspace Identidade gerida que é automaticamente criada para suportar os serviços de parceiros do Workspace (por exemplo, oleodutos ADF).  Duas identidades geridas com o mesmo nome podem existir num Cenário conectado. As identidades geridas podem ser distinguidas pelos seus IDs de objeto AD Azure, a funcionalidade para criar utilizadores SQL usando IDs de objeto está para breve.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > O espaço de trabalho conectado Synapse Studio apresentará os nomes de piscinas dedicadas com base nas permissões que o utilizador tem em Azure. Os objetos debaixo das piscinas não estarão acessíveis se o utilizador não tiver permissões nas piscinas SQL. 

- **Segurança da rede** Se o espaço de trabalho synapse que ativou na sua piscina SQL (anteriormente SQL DW) estiver ativado para proteção contra infiltração de dados. Crie uma ligação de ponto final privado gerida do espaço de trabalho para o servidor lógico SQL. Aprove o pedido de ligação de ponto final privado para permitir comunicações entre o servidor e o espaço de trabalho.
- **Estúdio** Piscinas SQL no **centro de dados** Um espaço de trabalho habilitado para piscina SQL (anteriormente SQL DW) pode ser identificado através da ponta da ferramenta no centro de dados. 
- **Criação de uma nova piscina SQL dedicada (anteriormente SQL DW)** Novas piscinas SQL dedicadas podem ser criadas através do espaço de trabalho synapse e studio depois que a funcionalidade de espaço de trabalho foi ativada e o fornecimento de uma nova piscina será realizado no servidor lógico SQL. Os novos recursos aparecerão no portal e no Estúdio quando o provisionamento estiver concluído.      

## <a name="next-steps"></a>Passos seguintes
Ativar [as funcionalidades do espaço de trabalho synapse](workspace-connected-create.md) na sua piscina SQL dedicada existente (anteriormente SQL DW)
