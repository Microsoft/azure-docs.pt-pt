---
title: Controlo de acesso baseado em funções - Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como configurar o controlo de acesso baseado em funções para os seus projetos de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 4eea87d7b5ef543551b789ddc6b045358fa98631
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826938"
---
# <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

A Custom Vision suporta o controlo de acesso baseado em funções Azure (Azure RBAC), um sistema de autorização para gerir o acesso individual aos recursos Azure. Utilizando o RBAC, atribui diferentes membros da equipa diferentes níveis de permissões para os seus projetos de Visão Personalizada. Para obter mais informações sobre o RBAC, consulte a documentação do [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Adicionar atribuição de função ao recurso Visão Personalizada

O Azure RBAC pode ser atribuído a um recurso de Visão Personalizada. Para conceder acesso a um recurso Azure, adicione uma atribuição de funções.
1. No [portal do Azure](https://ms.portal.azure.com/), selecione **Todos os serviços**. 
1. Em seguida, selecione os **Serviços Cognitivos**e navegue para o seu recurso específico de treino de visão personalizada.
   > [!NOTE]
   > Também pode configurar o RBAC para grupos de recursos inteiros, subscrições ou grupos de gestão. Faça-o selecionando o nível de âmbito pretendido e, em seguida, navegando para o item pretendido (por exemplo, selecionando **grupos de Recursos** e, em seguida, clicando no seu grupo de recursos pretendido).
1. Selecione **o controlo de acesso (IAM)** no painel de navegação esquerdo.
1. Selecione o **separador atribuições de funções** para visualizar as atribuições de funções para este âmbito.
1. Selecione **Add**  ->  **Adicionar Ação de função**.
1. Na **Role** lista de drop-down role, selecione uma função que pretende adicionar.
1. Na lista **Select,** selecione um utilizador, grupo, principal de serviço ou identidade gerida. Se não vir o principal de segurança da lista, pode escrever a caixa Select para pesquisar nomes de exibição, endereços de e-mail e identificadores de objetos.
1. Selecione **Guardar** para atribuir a função.

Dentro de poucos minutos, o alvo será atribuído à função selecionada no âmbito selecionado.

## <a name="custom-vision-role-types"></a>Tipos de funções de visão personalizada

Utilize a seguinte tabela para determinar as necessidades de acesso aos seus recursos de Visão Personalizada.

|Função  |Permissões  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Acesso total aos projetos, incluindo a capacidade de criar, editar ou excluir um projeto.        |
|`Cognitive Services Custom Vision Trainer`     | Acesso total, exceto a capacidade de criar ou excluir um projeto. Os formadores podem ver e editar projetos e treinar, publicar, não publicar ou exportar os modelos.        |
|`Cognitive Services Custom Vision Labeler`     | Capacidade de carregar, editar ou apagar imagens de treino e criar, adicionar, remover ou eliminar tags. Os labelers podem ver projetos, mas não podem atualizar nada além de imagens de treino e tags.         |
|`Cognitive Services Custom Vision Deployment`     | Capacidade de publicar, não publicar ou exportar os modelos. Os deployers podem ver projetos mas não podem atualizar um projeto, imagens de formação ou tags.        |
|`Cognitive Services Custom Vision Reader`     | Capacidade de visualizar projetos. Os leitores não podem fazer alterações.        |
