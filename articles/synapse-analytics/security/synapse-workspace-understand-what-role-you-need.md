---
title: Compreender as funções necessárias para executar tarefas comuns na Sinapse
description: Este artigo descreve quais os papéis rbac sinapses incorporados para realizar tarefas específicas
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: aadc8e817eb2b5de856ac73cfd010b48d0531bfc
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523501"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Compreender as funções necessárias para executar tarefas comuns na Sinapse

Este artigo irá ajudá-lo a entender quais os papéis de Synapse RBAC (controle de acesso baseado em funções) ou Azure RBAC que precisa para fazer o trabalho no Synapse Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Resumo do controlo de acesso do Estúdio de Sinaapse e do fluxo de trabalho 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Aceder ao Synapse Studio e ver o seu conteúdo

- Você pode abrir o Synapse Studio e ver detalhes do espaço de trabalho e listar qualquer um dos seus recursos Azure (piscinas SQL, piscinas spark ou tempos de integração) se lhe for atribuído qualquer papel de RBAC da Synapse ou tiver o papel de Proprietário, Colaborador ou Leitor no espaço de trabalho.

### <a name="resource-management"></a>Gestão de recursos

- Você pode criar piscinas SQL, piscinas Apache Spark e tempos de integração se você é um Proprietário Azure ou Colaborador no espaço de trabalho.
- Você pode parar ou escalar uma piscina SQL dedicada, configurar uma piscina Spark ou um tempo de execução de integração se você é um Proprietário ou Colaborador Azure no espaço de trabalho ou esse recurso.

### <a name="viewing-and-editing-code-artifacts"></a>Artefactos de código de visualização e edição

- Com acesso ao Synapse Studio, pode criar novos artefactos de código, tais como scripts SQL, cadernos, trabalhos de faíscas, serviços ligados, oleodutos, fluxos de dados, gatilhos e credenciais.  (Estes artefactos podem ser publicados ou guardados com permissões adicionais.)  
- Se é um Utilizador de Artefactos De Sinapse, Editor de Artefactos De Sinapse, Colaborador de Sinapse ou Administrador de Sinapse pode listar, abrir e editar artefactos de código já publicados.

### <a name="executing-your-code"></a>Executando o seu código

- Pode executar scripts SQL em piscinas SQL se tiver as permissões SQL necessárias definidas nas piscinas SQL.  
- Você pode executar cadernos e trabalhos de spark se você tiver permissões do Operador de Computação Synapse no espaço de trabalho ou piscinas apache spark específicas.  
- Com permissões do Operador compute no espaço de trabalho ou tempos de integração específicos, e permissões de credenciais apropriadas, pode executar oleodutos.

### <a name="monitoring-and-managing-execution"></a>Monitorização e gestão da execução
- Você pode rever o estado de execução de cadernos e empregos em piscinas Apache Spark se você é um Utilizador Da Sinapse.
- Pode rever registos e cancelar trabalhos de execução e oleodutos se for um Operador de Computação Synapse no espaço de trabalho ou para uma piscina ou oleoduto específico.  

### <a name="publishing-and-saving-your-code"></a>Publicar e guardar o seu código

- Pode publicar artefactos de código novos ou atualizados ao serviço se for um Editor de Artefactos Da Sinapse, Colaborador de Sinapse ou Administrador de Sinapse. 
- Você pode entregar artefactos de código a um ramo de trabalho de um repositório git se o espaço de trabalho for habilitado para Git e você tiver permissões de Git. Com Git habilitado, a publicação só é permitida a partir do ramo de colaboração.
- Se fechar o Synapse Studio sem publicar ou comprometer alterações aos artefactos de código, então essas alterações serão perdidas.


## <a name="tasks-and-required-roles"></a>Tarefas e funções necessárias

O quadro abaixo lista tarefas comuns e para cada tarefa, as funções DE RBAC de Sinapse ou Azure RBAC necessárias.  

>[!Note]
>- O Administrador da Sinapse não está listado para cada tarefa, a menos que seja a única função que forneça a permissão necessária.  Um Administrador da Sinapse pode executar todas as tarefas habilitados por outras funções do RBAC da Sinapse.</br>
>- Todas as funções DE Sinaapse RBAC em qualquer âmbito fornecem-lhe permissões do Utilizador Synapse no espaço de trabalho
>- Todas as permissões/ações do RBAC da Synapse mostradas na tabela estão pré-fixas Microsoft/Synapse/workspaces/... </br>


Tarefa (quero...) |Papel (preciso ser...)|Permissão/ação do RBAC de Sinapse
--|--|--
|Abrir o Estúdio Synapse em um espaço de trabalho|Utilizador da Sinapse, ou|leitura
| |Proprietário, Colaborador ou Leitor do Azure Proprietário, Colaborador ou Leitor no espaço de trabalho|nenhum
|Listar piscinas SQL, piscinas Apache Spark, tempos de integração e aceder aos seus detalhes de configuração|Utilizador da Sinapse, ou|leitura|
||Proprietário, Colaborador ou Leitor do Azure Proprietário, Colaborador ou Leitor no espaço de trabalho|nenhum
|Lista de serviços ligados, credenciais, pontos finais privados geridos|Utilizador da Sinapse|leitura
**Conjuntos de SQL**||
Crie uma piscina SQL dedicada ou uma piscina SQL sem servidor|Proprietário ou Colaborador Azure no espaço de trabalho|nenhum
Gerir (pausa, escala ou apagar) uma piscina SQL dedicada|Proprietário ou Colaborador Azure na piscina ou espaço de trabalho SQL|nenhum
Criar um script SQL</br>|Utilizador da Sinapse, ou </br>Proprietário ou Colaborador da Azure no espaço de trabalho, </br>*São necessárias permissões SQL adicionais para executar um script SQL*.|
Liste e abra qualquer script SQL publicado| Utilizador de Artefactos De Sinapse, Editor de Artefactos, Colaborador da Sinapse|artefactos/leitura
Executar um script SQL em uma piscina SQL sem servidor|Permissões SQL na piscina (concedidas automaticamente a um administrador da Sinapse)|nenhum
Executar um script SQL em uma piscina DE SQL dedicada|Requer permissões SQL na piscina|nenhum
Publique um novo script SQL atualizado ou eliminado|Editor de Artefactos Da Sinapse, Colaborador da Sinapse|sqlScripts/write, delete
Comprometa alterações a um script SQL a um git repo|Requer permissões de Git no repo|
Atribuir a Administração Ative No espaço de trabalho (através de propriedades de espaço de trabalho no Portal Azure)|Proprietário ou Colaborador Azure no espaço de trabalho |
**Piscinas apache spark**||
Criar uma piscina Apache Spark|Proprietário ou Colaborador Azure no espaço de trabalho|
Monitore aplicações Apache Spark| Utilizador da Sinapse|leitura
Ver os registos para o caderno e execução de emprego |Operador de Computação De Sinapse|
Cancele qualquer caderno ou trabalho de faísca em execução numa piscina Apache Spark|Operador de computação de sinapse na piscina Apache Spark.|bigDataPools/useCompute
Criar um caderno ou definição de trabalho|Utilizador sinapse ou proprietário do Azure, colaborador ou leitor no espaço de trabalho</br> *Permissões adicionais são necessárias para executar, publicar ou salvar*|leitura
Listar e abrir um caderno publicado ou definição de emprego, incluindo a revisão de saídas guardadas|Utilizador de Artefactos De Sinapse, Editor de Artefactos De Sinapse, Colaborador de Sinapse no espaço de trabalho|artefactos/leitura
Executar um caderno e rever a sua saída|Administrador de faíscas Da Sinapse Apache, Operador de Cálculo De Sinapse na piscina selecionada apache spark|bigDataPools/useCompute 
Publicar ou excluir um caderno ou definição de emprego (incluindo saída) para o serviço|Editor de Artefactos no espaço de trabalho, Administrador de Faíscas Apache Synapse|cadernos/escrever, excluir
Comprometer alterações a um caderno ou definição de emprego para o ramo de trabalho git|Permissões de Git|nenhum
**Pipelines, Tempos de Integração, Fluxos de Dados, Conjuntos de Dados e Gatilhos**||
Criar, atualizar ou eliminar um tempo de execução de integração|Proprietário ou Colaborador Azure no espaço de trabalho|
Estado do tempo de funcionação da integração do monitor|Utilizador da Sinapse|ler, gasodutos/visualizaroutputs
Revisão do gasoduto corre|Editor de Artefactos da Sinapse/Colaborador da Sinapse|ler, gasodutos/visualizaroutputs 
Criar um pipeline |Utilizador da Sinapse </br>[**_em consideração + Utilizador Credencial De Sinapse no WorkspaceSystemIdentity_* _]</br>_Additional permissões são necessárias para publicar ou guardar*|ler, credenciais/UseSecret/ação
Criar um fluxo de dados, conjunto de dados ou gatilho |Utilizador da Sinapse</br>*Permissões adicionais são necessárias para publicar ou salvar*|leitura
Listar e abrir um oleoduto publicado |Utilizador de artefactos de sinapse | artefactos/leitura
Dados do conjunto de dados de pré-visualização|Utilizador de Sinapse + Utilizador Credencial De Sinapse na WorkspaceSystemIdentity| 
Depurar um oleoduto utilizando o tempo de funcionação padrão da integração|Utilizador de Sinapse + Utilizador Credencial De Sinapse na credencial de entidade WorkspaceSystemId|ler, </br>credenciais/usoSecret
Criar um gatilho, incluindo o gatilho agora|Utilizador de Sinapse + Utilizador Credencial De Sinapse na WorkspaceSystemIdentity|ler, credenciais/usoTere/ação
Copiar dados utilizando a ferramenta Dados de Cópia|Utilizador de Sinapse + Utilizador credencial de sinapse na identidade do sistema de espaço de trabalho|ler, credenciais/usoTere/ação
Ingerir dados (usando um horário)|Autor de Sinapse + Utilizador Credencial De Sinapse na Identidade do Sistema de Espaço de Trabalho|ler, credenciais/usoTere/ação
Publicar um novo, atualizado ou eliminado pipeline, fluxo de dados ou gatilho para o serviço|Sinapse Artifact Publisher no espaço de trabalho|gasodutos/escrever, eliminar</br>fluxos de dados escrever, excluir</br>gatilhos/escrever, eliminar
Publicar um novo fluxo de dados, conjunto de dados atualizado ou eliminado|Editor de artefactos no espaço de trabalho|gatilhos/escrever, eliminar
Guardar (cometer) alterações em oleodutos, fluxos de dados, conjuntos de dados, gatilhos para o repo Git |Permissões de Git|nenhum 
**Serviços ligados**||
Criar um serviço ligado (inclui a atribuição de uma credencial)|Utilizador da Sinapse</br>*Permissões adicionais são necessárias para executar, publicar ou salvar*|leitura
Listar e abrir um serviço de ligação publicado|Utilizador de artefactos de sinapse|linkedServices/write, delete  
Ligação de teste num serviço ligado garantido por uma credencial|Utilizador de Sinapse e Utilizador credencial de sinapse|credenciais/usoTere/ação|
Publicar um serviço ligado|Editora de Artefactos Da Sinapse|linkedServices/write, delete
Guardar (comprometer) definições de serviço ligadas ao git repo|Permissões de Git|nenhum
**Gestão de acesso**||
Rever atribuições de funções do RBAC da Synapse em qualquer âmbito|Utilizador da Sinapse|leitura
Atribuir e remover atribuições de funções de RBAC da Synapse para utilizadores, grupos e diretores de serviços| Administrador de Sinapse no espaço de trabalho ou num âmbito específico de item do espaço de trabalho|papelAssignments/write, delete
Criar ou remover o acesso do RBAC do Synapse a artefactos de código|Administrador de Sinapse no âmbito do espaço de trabalho|papelAssignments/write, delete   

>[!Note]
>Os utilizadores convidados de outro inquilino não podem rever, adicionar ou alterar atribuições de funções, independentemente do papel que lhes tenha sido atribuído. 

## <a name="next-steps"></a>Passos seguintes

Saiba [como rever as atribuições de funções do SYNAPSE RBAC](./how-to-review-synapse-rbac-role-assignments.md)

Saiba [como gerir as atribuições de funções do SYNAPC.](./how-to-manage-synapse-rbac-role-assignments.md) 