---
title: Controlo de acesso baseado em funções da Sinapse
description: Um artigo que explica o controlo de acesso baseado em funções no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105152"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>O que é o controlo de acesso baseado em funções da Synapse (RBAC)?

O RBAC synapse alarga as capacidades do [Azure RBAC](../../role-based-access-control/overview.md) para espaços de trabalho da Sinapse e seus conteúdos. 

O Azure RBAC é usado para gerir quem pode criar, atualizar ou apagar o espaço de trabalho synapse e suas piscinas SQL, piscinas Apache Spark e tempos de integração.

O RBAC de sinapse é usado para gerir quem pode:
- Publicar artefactos de código e listar ou aceder a artefactos de código publicados, 
- Execute o código nas piscinas Apaches Spark e nos tempos de integração,
- Serviços de acesso (dados) protegidos por credenciais 
- Monitorize ou cancele a execução de empregos, reveja a produção de emprego e os registos de execução.  

>[!Note]
>Embora o SYNAPSe RBAC seja usado para gerir o acesso a scripts SQL publicados, ele fornece apenas controlo de acesso limitado a piscinas SQL sem servidor e _não_ é usado para controlar o acesso a piscinas SQL dedicadas.  O acesso às piscinas SQL é controlado principalmente através da segurança SQL.

## <a name="what-can-i-do-with-synapse-rbac"></a>O que posso fazer com o SYNAPSE RBAC?

Aqui estão alguns exemplos do que pode fazer com o SYNAPSE RBAC:
  - Permitir que um utilizador publique alterações feitas aos cadernos e empregos da Apache Spark para o serviço ao vivo.
  - Permitir que um utilizador corra e cancele cadernos e faísca empregos numa piscina específica do Apache Spark.
  - Permitir que um utilizador utilize credenciais específicas para que possa executar oleodutos protegidos pela identidade do sistema de trabalho e dados de acesso em serviços ligados protegidos com credenciais. 
  - Permitir que um administrador gere, monitorize e cancele a execução de emprego em Piscinas de Faíscas específicas.    

## <a name="how-synapse-rbac-works"></a>Como funciona o SYNAPSE RBAC
Tal como o Azure RBAC, o SYNAPSe RBAC trabalha criando atribuições de papéis. Uma atribuição de função consiste em três elementos: um principal de segurança, uma definição de função e um âmbito.  

### <a name="security-principals"></a>Principados de Segurança

Um _diretor de segurança_ é um utilizador, grupo, diretor de serviço ou identidade gerida.

### <a name="roles"></a>Funções
 
Um _papel_ é uma coleção de permissões ou ações que podem ser realizadas em tipos específicos de recursos ou tipos de artefactos.

A Sinapse fornece papéis incorporados que definem coleções de ações que correspondem às necessidades de diferentes personalidades:
- Os administradores podem ter acesso total para criar e configurar um espaço de trabalho 
- Os desenvolvedores podem criar, atualizar e depurar scripts SQL, cadernos, oleodutos e fluxos de dados, mas não ser capazes de publicar ou executar este código em recursos/dados de cálculo de produção
- Os operadores podem monitorizar e gerir o estado do sistema, a execução de aplicações e os registos de revisão, sem acesso ao código ou às saídas da execução.
- O pessoal de segurança pode gerir e configurar pontos finais sem ter acesso a códigos, recursos de computação ou dados.

[Saiba mais](./synapse-workspace-synapse-rbac-roles.md) sobre os papéis da Sinapse incorporada. 

### <a name="scopes"></a>Âmbitos

Um _âmbito_ define os recursos ou artefactos a que o acesso se aplica.  A Sinapse suporta âmbitos hierárquicos.  As permissões concedidas num âmbito de nível superior são herdadas por objetos a um nível mais baixo.  No SYNAPSE RBAC, o âmbito de alto nível é um espaço de trabalho.  Atribuir uma função com âmbito de espaço de trabalho concede permissões a todos os objetos aplicáveis no espaço de trabalho.  

Os âmbitos suportados atuais dentro de um espaço de trabalho são: Piscina Apache Spark, tempo de integração, serviço ligado e credencial. 

O acesso a artefactos de código é concedido com âmbito de espaço de trabalho.  A concessão de acesso a coleções de artefactos dentro de um espaço de trabalho será suportada num lançamento posterior.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Resolução de atribuições de funções para determinar permissões

Uma atribuição de funções concede ao diretor as permissões definidas pelo papel no âmbito especificado.

Synapse RBAC é um modelo aditivo como Azure RBAC. As funções múltiplas podem ser atribuídas a um único principal e em diferentes âmbitos. Ao calcular as permissões de um principal de segurança, o sistema considera todas as funções atribuídas ao principal e a grupos que direta ou indiretamente incluem o principal.  Considera ainda o âmbito de cada atribuição na determinação das permissões que se aplicam.  

## <a name="enforcing-assigned-permissions"></a>Aplicação das permissões atribuídas

No Synapse Studio, botões ou opções específicos podem ser acinzentados ou um erro de permissões pode ser devolvido ao tentar uma ação se não tiver as permissões necessárias. 

Se um botão ou opção for desativado, pairar sobre o botão ou opção mostra uma ponta de ferramenta com a permissão necessária.  Contacte um administrador da Sinapse para atribuir uma função que conceda a permissão necessária. Pode ver os papéis que fornecem ações específicas [aqui.](./synapse-workspace-synapse-rbac-roles.md)

## <a name="who-can-assign-synapse-rbac-roles"></a>Quem pode atribuir papéis de Sinaapse RBAC?

Apenas um administrador da Sinapse pode atribuir funções de Sinapse RBAC.  Um administrador de sinapse ao nível do espaço de trabalho pode conceder acesso em qualquer âmbito.  Um administrador da Sinapse num âmbito de nível inferior só pode conceder acesso nesse âmbito. 

Quando um novo espaço de trabalho é criado, o criador recebe automaticamente o papel de Administrador synapse no âmbito do espaço de trabalho.   

## <a name="where-do-i-manage-synapse-rbac"></a>Onde é que eu manco o SYNAPSE RBAC?

O RBAC synapse é gerido a partir de dentro do Synapse Studio usando as ferramentas de controlo de acesso no hub Manage. 

## <a name="next-steps"></a>Passos seguintes

Compreenda os [papéis de RBAC da Sinapse](./synapse-workspace-synapse-rbac-roles.md)incorporados.

Saiba [como rever as atribuições de funções do SYNAPSE RBAC](./how-to-review-synapse-rbac-role-assignments.md) para um espaço de trabalho.

Saiba [como atribuir funções de Sinapse RBAC](./how-to-manage-synapse-rbac-role-assignments.md)