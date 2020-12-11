---
title: Visão geral do controlo do controlo do espaço de trabalho da Sinapse
description: Este artigo descreve os mecanismos utilizados para controlar o acesso a um espaço de trabalho da Sinapse e os recursos e artefactos de código que contém.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 256fec97819cde0f6f62d59b34416c92e1edfd20
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109611"
---
# <a name="synapse-access-control"></a>Controlo de acesso sinapse 

Este artigo fornece uma visão geral dos mecanismos disponíveis para controlar o acesso aos recursos e dados do cálculo da Sinapse.  

## <a name="overview"></a>Descrição geral

A Sinapse fornece um sistema de controlo de acesso abrangente e fino, que integra: 
- **Funções Azure** para gestão de recursos e acesso a dados armazenados, 
- **Funções de sinapse** para gerir o acesso ao vivo ao código e à execução, 
- **Funções SQL** para acesso de plano de dados a dados em piscinas SQL, e 
- **Permissões de git** para controlo de código fonte, incluindo integração contínua e suporte de implementação.  

As funções de sinapse fornecem conjuntos de permissões que podem ser aplicadas em diferentes âmbitos. Esta granularidade facilita a concessão de acesso adequado a administradores, desenvolvedores, pessoal de segurança e operadores para calcular recursos e dados.

O controlo de acessos pode ser simplificado utilizando grupos de segurança alinhados com as funções de trabalho das pessoas.  Basta adicionar e remover os utilizadores dos grupos de segurança apropriados para gerir o acesso.

## <a name="access-control-elements"></a>Elementos de controlo de acesso

### <a name="creating-and-managing-synapse-compute-resources"></a>Criar e gerir recursos de computação synapse

As funções Azure são usadas para controlar a gestão de: 
- Piscinas SQL dedicadas, 
- Piscinas Apache Spark, e 
- Tempos de integração. 

Para *criar* estes recursos, precisa de ser proprietário ou colaborador da Azure no grupo de recursos.  Para *geri-los* uma vez criados, você precisa ser um Proprietário Azure ou Colaborador no grupo de recursos ou nos recursos individuais. 

### <a name="developing-and-executing-code-in-synapse"></a>Desenvolvimento e execução do código em Sinapse 

A Sinapse suporta dois modelos de desenvolvimento.

- **Sinapse desenvolvimento ao vivo.**  Desenvolve-se e depura código no Synapse Studio e depois **publica-o** para guardar e executar.  O serviço Synapse é a fonte da verdade para a edição e execução de códigos.  Qualquer obra inédita perde-se quando se fecha o Estúdio Synapse.  
- **Desenvolvimento habilitado a Git.** Desenvolves e depuras códigos no Synapse Studio e **cometes** alterações num ramo de trabalho de um git repo. O trabalho a partir de um ou mais balcões está integrado num ramo de colaboração, de onde o **publica** ao serviço.  O git repo é a fonte da verdade para a edição de código, enquanto o serviço é a fonte da verdade para a execução. As alterações devem ser comprometidas com o git repo ou publicadas no serviço antes de fechar o Synapse Studio. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2150100) sobre a utilização do Synapse Analytics com o Git.

Em ambos os modelos de desenvolvimento, qualquer utilizador com acesso ao Synapse Studio pode criar artefactos de código.  No entanto, precisa de permissões adicionais para publicar artefactos ao serviço, ler artefactos publicados, comprometer alterações ao Git, executar código e aceder a dados ligados protegidos por credenciais.

### <a name="synapse-roles"></a>Papéis de sinapse

As funções de sinapse são utilizadas para controlar o acesso ao serviço Synapse que lhe permite: 
- Lista de artefactos de código publicados, 
- Publicar artefactos de código, serviços ligados e definições de credenciais,
- Executar código ou oleodutos que utilizem recursos de computação synapse,
- Executar código ou oleodutos que acedam a dados ligados protegidos por credenciais,
- Ver saídas associadas a artefactos de código publicados,
- Monitorize o estado dos recursos computacional e veja os registos de tempo de execução.

As funções de sinapse podem ser atribuídas no âmbito do espaço de trabalho ou em âmbitos mais finos para limitar as permissões concedidas a recursos sinapses específicos.

### <a name="git-permissions"></a>Permissões de Git

Ao utilizar o desenvolvimento ativado por Git no modo Git, as permissões do Git controlam se pode ler e cometer alterações em artefactos de código, incluindo definições de serviço e credenciais ligadas.   
   
### <a name="accessing-data-in-sql"></a>Aceder a dados em SQL

Ao trabalhar com piscinas SQL dedicadas e sem servidor, o acesso a um plano de dados é controlado através de permissões SQL. 

O criador de um espaço de trabalho é designado como o Ative Directory Admin no espaço de trabalho.  Após a criação, esta função pode ser atribuída a um utilizador diferente ou a um grupo de segurança no portal Azure.

**Piscinas SQL sem servidor**: Os administradores de sinapse são `db_owner` `DBO` concedidos () permissões na piscina SQL sem servidor, 'Built-in'. Para garantir a outros utilizadores o acesso a piscinas SQL sem servidor, os administradores da Synapse precisam de executar scripts SQL em cada piscina sem servidor.  

**Piscinas SQL dedicadas**: A permissão do Diretório Ativo é concedida ao criador do espaço de trabalho e ao espaço de trabalho MSI.  A permissão para aceder a piscinas SQL dedicadas não é concedida automaticamente. Para conceder a outros utilizadores ou grupos acesso a piscinas SQL dedicadas, o Ative Directory Admin deve executar scripts SQL contra cada piscina SQL dedicada.

Veja [como configurar o Synapse Access Control](./how-to-set-up-access-control.md) para exemplos de scripts SQL para conceder permissões SQL em piscinas SQL.  

 ### <a name="accessing-system-managed-data-in-storage"></a>Aceder a dados geridos pelo sistema no armazenamento

As piscinas SQL sem servidor e as tabelas Apache Spark armazenam os seus dados num contentor ADLS Gen2 associado ao espaço de trabalho As bibliotecas Apache Spark instaladas pelo utilizador também são geridas na mesma conta de armazenamento.  Para permitir estes casos de utilização, os utilizadores e o espaço de trabalho MSI devem ter acesso ao **armazenamento de dados blob de armazenamento** a este recipiente de armazenamento ADLS Gen2.  

## <a name="using-security-groups-as-a-best-practice"></a>Usar os grupos de segurança como uma boa prática

Para simplificar a gestão do controlo de acesso, pode utilizar grupos de segurança para atribuir funções a indivíduos e grupos. Grupos de segurança podem ser criados para espelhar personalidades ou funções de trabalho na sua organização que precisam de acesso a recursos ou artefactos da Sinapse.  Estes grupos de segurança baseados em persona podem então ser atribuídos um ou mais papéis Azure, funções de Sinapse, permissões SQL ou Permissões Git. Com grupos de segurança bem escolhidos, é fácil atribuir a um utilizador as permissões necessárias adicionando-as ao grupo de segurança apropriado. 

>[!Note]
>Se utilizar grupos de segurança para gerir o acesso, existe latência adicional introduzida pelo Azure Ative Directory antes que as alterações entrem em vigor. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Controlo de acesso no Synapse Studio

O Synapse Studio comportar-se-á de forma diferente com base nas suas permissões e no modo atual:
- Modo ao vivo da **sinapse:** O Synapse Studio irá impedi-lo de ver conteúdo publicado, publicar conteúdos ou tomar outras ações se não tiver a permissão necessária.  Em alguns casos, será impedido de criar artefactos de código que não pode usar ou guardar. 
- **Modo git:** Se tiver permissões de Git que lhe permitem comprometer alterações na sucursal atual, então a ação de compromisso será permitida mesmo que não tenha permissão para publicar alterações no serviço ao vivo.  

Em alguns casos, é permitido criar artefactos de código mesmo sem permissão para publicar ou cometer.  Isto permite-lhe executar código (com as permissões de execução necessárias). [Saiba mais](./synapse-workspace-understand-what-role-you-need.md) sobre os papéis necessários para tarefas comuns. 

Se uma funcionalidade for desativada no Synapse Studio, uma ponta de ferramenta indicará a permissão necessária.  Utilize o guia de [funções do SYNAPSE RBAC](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) para ver qual a função necessária para fornecer a permissão em falta.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Sinaapse RBAC](./synapse-workspace-synapse-rbac.md)
- Saiba mais sobre [os papéis do SYNAPSE RBAC](./synapse-workspace-synapse-rbac-roles.md)
- [Saiba como configurar o controlo de acesso](./how-to-set-up-access-control.md) para um espaço de trabalho synapse utilizando grupos de segurança.
- [Saiba como rever atribuições de funções de SYNAPSE RBAC](./how-to-review-synapse-rbac-role-assignments.md)
- Saiba [como gerir atribuições de funções do SYNAPC](./how-to-manage-synapse-rbac-role-assignments.md)