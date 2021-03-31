---
title: Permissões de Catálogo (pré-visualização)
description: Este artigo apresenta uma visão geral de como configurar Role-Based Controlo de Acesso (RBAC) na Alçada Azure
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610374"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Controlo de acesso baseado em funções no Plano de Dados da Azure Purview

Este artigo descreve como Role-Based Access Control (RBAC) é implementado no Plano de [Dados](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)da Azure Purview.

> [!IMPORTANT]
> O principal que criou uma conta Desídura é automaticamente dado todas as permissões de data plane, independentemente das funções de data plane em que possam ou não estar. Para que qualquer outro utilizador faça qualquer coisa em Azure Purview, eles têm que estar em pelo menos uma das funções de Data Plane pré-definidas.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>As funções pré-definidas do Plano de Dados da Azure Purview

A Azure Purview define um conjunto de funções pré-definidas do Plano de Dados que podem ser usadas para controlar quem pode aceder ao que, em Azure Purview. Essas funções são:

* **Função de leitor de dados de visão** - Tem acesso ao portal Desresvisão e pode ler todos os conteúdos em Azure Purview, exceto para encadernações de digitalização
* **Função de Curador de Dados** - Tem acesso ao portal Desreserva e pode ler todos os conteúdos em Azure Purview, exceto para encadernações de digitalização, pode editar informações sobre ativos, editar definições de classificação e termos glossários, e pode aplicar classificações e termos glossários a ativos.
* **Função de Administrador de Fonte de Dados** - Não tem acesso ao Portal da Alças (o utilizador também precisa de estar nas funções de Leitor de Dados ou Curador de Dados) e pode gerir todos os aspetos da digitalização dos dados em Azure Purview, mas não tem acesso lido ou escrito a conteúdos em Azure Purview para além dos relacionados com a digitalização.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Compreender como usar as funções do Plano de Dados da Azure Purview

Quando uma Conta Azure Purview for criada, o criador será tratado como se estivesse tanto no Curador de Dados de Purview como em Funções de Administrador de Fonte de Dados de Purview. No entanto, essas funções não lhe são atribuídas no arquivo de funções. O Azure Purview reconhece que o principal é o criador da conta e concede-lhe estas capacidades com base na sua identidade.

Todos os outros utilizadores só podem utilizar a Conta do Azure Purview se tiverem, pelo menos, uma destas funções. Isto significa que quando uma Conta Azure Purview é criada, ninguém além do criador pode aceder à conta ou usar as suas APIs até que sejam colocadas numa ou mais das funções definidas anteriores.

Por favor, note que a função de Administrador de Fonte de Dados de Purview tem dois cenários suportados. O primeiro cenário é para utilizadores que já estejam a receber leitores de dados ou curadores de dados de purview que também precisam de ser capazes de criar digitalizações. Esses utilizadores precisam de estar em duas funções, pelo menos um do Purview Data Reader ou do Purview Data Curator, bem como ser colocado na Função de Administrador de Fonte de Dados de Purview.

O outro cenário para o Administrador de Fonte de Dados da Purview é para processos programáticos, como os principais do serviço, que precisam de ser capazes de configurar e monitorizar as verificações, mas não devem ter acesso a nenhum dos dados do catálogo.

Este cenário pode ser implementado colocando o principal de serviço na Função de Administrador de Fonte de Dados de Purview sem ser colocado em nenhuma das outras duas funções. O diretor não terá acesso ao Portal da Alçapão, mas isso é ok. porque é um diretor programático e só comunica através de APIs.

## <a name="putting-users-into-roles"></a>Colocar os utilizadores em funções

Assim, a primeira ordem de trabalhos depois de criar uma conta Azure Purview é atribuir pessoas a estas funções.

A tarefa é gerida através [do RBAC da Azure.](../role-based-access-control/overview.md)

Apenas duas funções de avião de controlo incorporado no Azure podem atribuir funções aos utilizadores, são proprietários ou administradores de acesso ao utilizador. Assim, para colocar as pessoas nestas funções para Azure Purview, é preciso encontrar alguém que seja proprietário ou administrador de acesso ao utilizador ou tornar-se um só.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Um exemplo de atribuir alguém a um papel

1. Vá https://portal.azure.com e navegue para a sua Conta Azure Purview
1. No lado esquerdo clique em "Controlo de acesso (IAM)"
1. Em seguida, siga as instruções gerais dadas [aqui](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)

## <a name="role-definitions-and-actions"></a>Definições e ações de papel

Um papel é definido como uma coleção de ações. Consulte [aqui](../role-based-access-control/role-definitions.md) mais informações sobre como os papéis são definidos. E veja [aqui](../role-based-access-control/built-in-roles.md) as definições de papel para os papéis de Azure Purview.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Ser adicionado a um papel de plano de dados numa conta de azure

Se quiser ter acesso a uma Conta Azure Purview para que possa utilizar o seu estúdio ou chamar as suas APIs, tem de ser adicionado a uma Função de Plano de Plano de Dados Azure. As únicas pessoas que podem fazê-lo são aqueles que são Proprietários ou Administradores de Acesso ao Utilizador na Conta Azure Purview. Para a maioria dos utilizadores, o próximo passo é encontrar um administrador local que possa ajudá-lo a encontrar as pessoas certas que lhe podem dar acesso.

Para os utilizadores que tenham acesso ao [portal Azure](https://portal.azure.com) da sua empresa podem consultar a conta Azure Purview que pretendem aderir, clicar no separador "Controlo de Acesso (IAM)" e ver quem são os Proprietários ou Administradores de Acesso ao Utilizador (UAAs). Mas note que em alguns casos, grupos de Diretório Ativo Azure ou Diretores de Serviço podem ser usados como Proprietários ou UAAs, caso em que pode não ser possível contactá-los diretamente. Em vez disso, temos de encontrar um administrador para ajudar.

## <a name="who-should-be-assigned-to-what-role"></a>Quem deve ser designado para que papel?

|Cenário de utilizador|Função apropriada|
|-------------|-----------------|
|Só preciso de encontrar bens, não quero editar nada.|Função de leitor de dados de competência|
|Preciso editar informações sobre ativos, colocar classificações neles, associá-los a entradas glossárias, etc.|Função de curador de dados de visão|
|Preciso editar o glossário ou configurar novas definições de classificação|Função de curador de dados de visão|
|O diretor de serviço da minha aplicação precisa de empurrar os dados para a Azure Purview|Função de curador de dados de visão|
|Preciso de configurar os exames através do Estúdio De Purview.|Função de administrador de fonte de dados de visão de competência mais pelo menos uma das funções de leitor de dados de Purview ou de Provedor de Dados|
|Preciso de permitir que um Diretor de Serviço ou outra identidade programática crie e monitorize as sondagens em Azure Purview sem permitir que a identidade programática aceda à informação do catálogo |Função de administrador de fonte de dados de competência|
|Preciso colocar os utilizadores em funções no Azure Purview | Proprietário ou Administrador de Acesso ao Utilizador |

Para obter mais informações sobre como adicionar um principal de segurança a uma função, consulte [Quickstart: Create a Azure Purview account](create-catalog-portal.md) .

## <a name="next-steps"></a>Passos seguintes

* [Informações de dados](concept-insights.md)
