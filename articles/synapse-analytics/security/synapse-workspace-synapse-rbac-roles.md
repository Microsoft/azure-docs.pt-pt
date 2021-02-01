---
title: Funções RBAC do Synapse
description: Este artigo descreve os papéis de Sinaapse RBAC incorporados
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 1ffbb5579ea19d7d608dd9c9d600342cd89d371c
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225079"
---
# <a name="synapse-rbac-roles"></a>Papéis de Sinapse RBAC

O artigo descreve as funções de RBAC da Sinapse incorporadas, as permissões que concedem e os âmbitos em que podem ser utilizados.  

## <a name="whats-changed-since-the-preview"></a>O que mudou desde a pré-estreia?
Para os utilizadores familiarizados com as funções DE RBAC da Sinapse fornecidas durante a pré-visualização, aplicam-se as seguintes alterações:
- Workspace Admin é renomeado **Administrador da Sinapse**
- Apache Spark Admin é renomeado **Synapse Apache Spark Administrator** e tem permissão para ver todos os artefactos de código publicados, incluindo scripts SQL.  Esta função já não dá permissão para utilizar o ESPAÇO DE TRABALHO MSI, que requer a função de Utilizador Credencial De Sinapse.  Esta permissão é necessária para executar gasodutos. 
- O SQL Admin é renomeado **Administrador Sinaapse SQL** e tem permissão para ver todos os artefactos de código publicados, incluindo cadernos spark e empregos.  Esta função já não dá permissão para utilizar o ESPAÇO DE TRABALHO MSI, que requer a função de Utilizador Credencial De Sinapse. Esta permissão é necessária para executar gasodutos.
- **São introduzidas novas funções de RBAC de sinapse** mais finas que se concentram no apoio ao desenvolvimento e às personalidades de operações em vez de tempos de análise específicos.  
- **São introduzidos novos âmbitos de nível inferior** para várias funções.  Estes âmbitos permitem que as funções sejam restritas a recursos ou objetos específicos.

>[!Note]
>As **novas funções de RBAC da Sinapse e os âmbitos de nível inferior estão atualmente em pré-visualização.**  É encorajado a utilizar estas novas funções e âmbitos, que são totalmente apoiados, e a fornecer feedback sobre a sua utilização.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Funções e âmbitos e âmbitos de RBAC de Sinapse incorporados

A tabela a seguir descreve as funções incorporadas e os âmbitos em que podem ser utilizados.

>[!Note]
> Os utilizadores com qualquer papel DE RBAC da Synapse em qualquer âmbito têm automaticamente a função de Utilizador Synapse no âmbito do espaço de trabalho. 

|Função |Permissões|Âmbitos|
|---|---|-----|
|Administrador da Sinapse  |Acesso total da Sinapse a piscinas SQL sem servidor, piscinas Apache Spark e tempos de integração.  Inclui criar, ler, atualizar e eliminar o acesso a todos os artefactos de código publicados.  Inclui permissões de Operador compute, Gestor de Dados Ligados e Credenciais do Utilizador na credencial de identidade do sistema de trabalho.  Inclui a atribuição de funções de RBAC de Sinaapse. Além do Administrador da Sinapse, os Proprietários do Azure também podem atribuir funções de Sinapse RBAC. As permissões Azure são necessárias para criar, eliminar e gerir recursos de computação. </br></br>_Pode ler e escrever artefactos </br> Pode fazer todas as ações em atividades de Spark. </br> Pode ver registos de piscina spark </br> Pode ver portátil e saída de pipeline </br> guardados Pode usar os segredos armazenados por serviços ou credenciais </br> ligados Pode ligar-se a pontos finais sem servidor SQL com SQL `db_datareader` , , e `db_datawriter` `connect` `grant` permissões Podem atribuir e revogar </br> funções DE RBAC synapse no âmbito atual_|Área de trabalho </br> Piscina de faíscas<br/>Runtime de integração </br>Serviço ligado</br>Credencial |
|Administrador de faíscas Apache Synapse</br>|Acesso total da Sinapse às Piscinas Apache Spark.  Criar, ler, atualizar e apagar o acesso às definições de emprego, cadernos e suas saídas publicados da Spark, e a bibliotecas, serviços e credenciais ligados.  Inclui acesso de leitura a todos os outros artefactos de código publicados. Não inclui permissão para usar credenciais e executar oleodutos. Não inclui conceder acesso. </br></br>_Pode fazer todas as ações em artefactos de </br> faíscas Pode fazer todas as ações em atividades de Spark_|Área de trabalho</br>Piscina de faíscas|
|Administrador Sinapse SQL|Acesso total da Sinapse a piscinas SQL sem servidor.  Criar, ler, atualizar e apagar o acesso a scripts, credenciais e serviços SQL publicados.  Inclui acesso de leitura a todos os outros artefactos de código publicados.  Não inclui permissão para usar credenciais e executar oleodutos. Não inclui conceder acesso. </br></br>*Pode fazer todas as ações em scripts SQL <br/> Pode ligar-se a pontos finais sem servidor SQL com `db_datareader` SQL, , e `db_datawriter` `connect` `grant` permissões*|Área de trabalho|
|Colaborador da Sinapse|Acesso total da Sinapse a piscinas SQL sem servidor, piscinas Apache Spark, tempos de integração.  Inclui criar, ler, atualizar e eliminar o acesso a todos os artefactos de código publicados e suas saídas, incluindo credenciais e serviços ligados.  Inclui permissões do operador de computação. Não inclui permissão para usar credenciais e executar oleodutos. Não inclui conceder acesso. </br></br>_Pode ler e escrever artefactos </br> Pode ver cadernos guardados e saída de pipeline </br> Pode fazer todas as ações em atividades spark </br> pode ver troncos de piscina spark_|Área de trabalho </br> Piscina de faíscas<br/> Runtime de integração|
|Editora de Artefactos Da Sinapse|Criar, ler, atualizar e apagar o acesso aos artefactos de código publicados e suas saídas. Não inclui permissão para executar códigos ou oleodutos, ou para conceder acesso. </br></br>_Pode ler artefactos publicados e publicar artefactos </br> Pode ver cadernos guardados, trabalho de faísca e saída de gasoduto_|Área de trabalho
|Utilizador de artefactos de sinapse|Leia o acesso aos artefactos de código publicados e suas saídas. Pode criar novos artefactos mas não pode publicar alterações ou executar código sem permissões adicionais.|Área de trabalho
|Operador de Computação De Sinapse |Submeta trabalhos de faíscas e cadernos e veja registos.  Inclui o cancelamento de empregos Spark submetidos por qualquer utilizador. Requer permissões de credenciais adicionais no sistema de trabalho para executar gasodutos, ver corridas de gasodutos e saídas. </br></br>_Pode submeter e cancelar empregos, incluindo empregos submetidos por outros </br> Pode ver registos de piscina spark_|Área de trabalho</br>Piscina de faíscas</br>Runtime de integração|
|Utilizador credencial de sinapse|Utilização em tempo de execução e configuração de segredos dentro de credenciais e serviços ligados em atividades como o pipeline runs. Para executar os gasodutos, esta função é necessária, traçada para a identidade do sistema de espaço de trabalho. </br></br>_Atenção a uma credencial, permite o acesso aos dados através de um serviço ligado protegido pela credencial (também requer permissão de utilização de cálculo) </br> Permite a execução de gasodutos protegidos pela credencial de identidade do sistema de trabalho (com permissão adicional de utilização de computador)_|Área de trabalho </br>Serviço Ligado</br>Credencial
|Gestor de dados ligado à Sinapse|Criação e gestão de pontos finais privados geridos, serviços ligados e credenciais. Pode criar pontos finais privados geridos que utilizam serviços ligados protegidos por credenciais|Área de trabalho|
|Utilizador da Sinapse|Liste e veja detalhes de piscinas SQL, piscinas Apache Spark, tempos de integração e serviços e credenciais ligados publicados. Não inclui outros artefactos de código publicados.  Pode criar novos artefactos mas não pode ser executado ou publicado sem permissões adicionais.</br></br>_Pode listar e ler piscinas Spark, tempos de integração._|Espaço de trabalho, piscina spark</br>Serviço ligado </br>Credencial|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Sinapse papéis RBAC e as ações que permitem

>[!Note]
>- Todas as ações listadas nas tabelas abaixo estão prefixadas, "Microsoft.Synapse/..."</br>
>- Todos os artefactos lidos, escritos e apagados são no que diz respeito aos artefactos publicados no serviço ao vivo.  Estas permissões não afetam o acesso a artefactos num repo Git ligado.  

A tabela que se segue lista as funções incorporadas e as ações/permissões que cada um suporta.

Função|Ações
--|--
Administrador da Sinapse|espaços de trabalho/leitura</br>espaços de trabalho/funçãoAssinsignments/write, delete</br>espaços de trabalho/managedPrivateEndpoint/write, delete</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/integraçãoAstimes/useCompute/ação</br>espaços de trabalho/artefactos/leitura</br>espaços de trabalho/cadernos/escrever, excluir</br>espaços de trabalho/sparkJobDefinitions/write, delete</br>espaços de trabalho/sqlScripts/write, delete</br>espaços de trabalho/dataFlows/write, delete</br>espaços de trabalho/gasodutos/escrever, excluir</br>espaços de trabalho/gatilhos/escrever, excluir</br>espaços de trabalho/conjuntos de dados/escrever, excluir</br>espaços de trabalho/bibliotecas/escrever, excluir</br>espaços de trabalho/linkedServices/write, delete</br>espaços de trabalho/credenciais/escrever, excluir</br>espaços de trabalho/cadernos/visualizaçãoOutputs/ação</br>espaços de trabalho/gasodutos/viewOutputs/ação</br>espaços de trabalho/linkedServices/useSecret/action</br>espaços de trabalho/credenciais/useSecret/ação|
|Administrador de faíscas Apache Synapse|espaços de trabalho/leitura</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/cadernos/visualizaçãoOutputs/ação</br>espaços de trabalho/artefactos/leitura</br>espaços de trabalho/cadernos/escrever, excluir</br>espaços de trabalho/sparkJobDefinitions/write, delete</br>espaços de trabalho/bibliotecas/escrever, excluir</br>espaços de trabalho/linkedServices/write, delete</br>espaços de trabalho/credenciais/escrever, excluir|
|Administrador Sinapse SQL|espaços de trabalho/leitura</br>espaços de trabalho/artefactos/leitura</br>espaços de trabalho/sqlScripts/write, delete</br>espaços de trabalho/linkedServices/write, delete</br>espaços de trabalho/credenciais/escrever, excluir|
|Colaborador da Sinapse|espaços de trabalho/leitura</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/integraçãoAstimes/useCompute/ação</br>espaços de trabalho/integraçãoRuntimes/viewLogs/ação</br>espaços de trabalho/artefactos/leitura</br>espaços de trabalho/cadernos/escrever, excluir</br>espaços de trabalho/sparkJobDefinitions/write, delete</br>espaços de trabalho/sqlScripts/write, delete</br>espaços de trabalho/dataFlows/write, delete</br>espaços de trabalho/gasodutos/escrever, excluir</br>espaços de trabalho/gatilhos/escrever, excluir</br>espaços de trabalho/conjuntos de dados/escrever, excluir</br>espaços de trabalho/bibliotecas/escrever, excluir</br>espaços de trabalho/linkedServices/write, delete</br>espaços de trabalho/credenciais/escrever, excluir</br>espaços de trabalho/cadernos/visualizaçãoOutputs/ação</br>espaços de trabalho/gasodutos/viewOutputs/ação|
|Editora de Artefactos Da Sinapse|espaços de trabalho/leitura</br>espaços de trabalho/artefactos/leitura</br>espaços de trabalho/cadernos/escrever, excluir</br>espaços de trabalho/sparkJobDefinitions/write, delete</br>espaços de trabalho/sqlScripts/write, delete</br>espaços de trabalho/dataFlows/write, delete</br>espaços de trabalho/gasodutos/escrever, excluir</br>espaços de trabalho/gatilhos/escrever, excluir</br>espaços de trabalho/conjuntos de dados/escrever, excluir</br>espaços de trabalho/bibliotecas/escrever, excluir</br>espaços de trabalho/linkedServices/write, delete</br>espaços de trabalho/credenciais/escrever, excluir</br>espaços de trabalho/cadernos/visualizaçãoOutputs/ação</br>espaços de trabalho/gasodutos/viewOutputs/ação|
|Utilizador de artefactos de sinapse|espaços de trabalho/leitura</br>espaços de trabalho/artefactos/leitura</br>espaços de trabalho/cadernos/visualizaçãoOutputs/ação</br>espaços de trabalho/gasodutos/viewOutputs/ação|
|Operador de Computação De Sinapse |espaços de trabalho/leitura</br>espaços de trabalho/bigDataPools/useCompute/ação</br>espaços de trabalho/bigDataPools/viewLogs/ação</br>espaços de trabalho/integraçãoAstimes/useCompute/ação</br>espaços de trabalho/integraçãoRuntimes/viewLogs/ação|
|Utilizador credencial de sinapse|espaços de trabalho/leitura</br>espaços de trabalho/linkedServices/useSecret/action</br>espaços de trabalho/credenciais/useSecret/ação|
|Gestor de dados ligado à Sinapse|espaços de trabalho/leitura</br>espaços de trabalho/managedPrivateEndpoint/write, delete</br>espaços de trabalho/linkedServices/write, delete</br>espaços de trabalho/credenciais/escrever, excluir|
|Utilizador da Sinapse|espaços de trabalho/leitura|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Sinapse ações do RBAC e as funções que lhes permitem

A tabela que se segue enumera as ações da Sinapse e as funções incorporadas que permitem estas ações:

Ação|Função
--|--
espaços de trabalho/leitura|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Administrador Sinapse SQL</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse</br>Utilizador de artefactos de sinapse</br>Operador de Computação De Sinapse </br>Utilizador credencial de sinapse</br>Gestor de dados ligado à Sinapse</br>Utilizador da Sinapse 
espaços de trabalho/funçãoAssinsignments/write, delete|Administrador da Sinapse
espaços de trabalho/managedPrivateEndpoint/write, delete|Administrador da Sinapse</br>Gestor de dados ligado à Sinapse
espaços de trabalho/bigDataPools/useCompute/ação|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Colaborador da Sinapse</br>Operador de Computação De Sinapse 
espaços de trabalho/bigDataPools/viewLogs/ação|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Colaborador da Sinapse</br>Operador de Computação De Sinapse 
espaços de trabalho/integraçãoAstimes/useCompute/ação|Administrador da Sinapse</br>Colaborador da Sinapse</br>Operador de Computação De Sinapse 
espaços de trabalho/artefactos/leitura|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Administrador Sinapse SQL</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse</br>Utilizador de artefactos de sinapse
espaços de trabalho/cadernos/escrever, excluir|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/sparkJobDefinitions/write, delete|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/sqlScripts/write, delete|Administrador da Sinapse</br>Administrador Sinapse SQL</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/dataFlows/write, delete|Administrador da Sinapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/gasodutos/escrever, excluir|Administrador da Sinapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/gatilhos/escrever, excluir|Administrador da Sinapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/conjuntos de dados/escrever, excluir|Administrador da Sinapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/bibliotecas/escrever, excluir|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse
espaços de trabalho/linkedServices/write, delete|Administrador da Sinapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse</br>Gestor de dados ligado à Sinapse
espaços de trabalho/credenciais/escrever, excluir|Administrador da Sinapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse</br>Gestor de dados ligado à Sinapse
espaços de trabalho/cadernos/visualizaçãoOutputs/ação|Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse</br>Utilizador de artefactos de sinapse
espaços de trabalho/gasodutos/viewOutputs/ação|Administrador da Sinapse</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse</br>Utilizador de artefactos de sinapse
espaços de trabalho/linkedServices/useSecret/action|Administrador da Sinapse</br>Utilizador credencial de sinapse
espaços de trabalho/credenciais/useSecret/ação|Administrador da Sinapse</br>Utilizador credencial de sinapse

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Âmbitos de Sinapse RBAC e as suas funções apoiadas

O quadro abaixo lista os âmbitos do RBAC synapse e as funções que podem ser atribuídas em cada âmbito. 

>[!note]
>Para criar ou eliminar um objeto tem de ter permissões num âmbito de nível superior.

Âmbito|Funções
--|--
Área de trabalho |Administrador da Sinapse</br>Administrador de faíscas Apache Synapse</br>Administrador Sinapse SQL</br>Colaborador da Sinapse</br>Editora de Artefactos Da Sinapse</br>Utilizador de artefactos de sinapse</br>Operador de Computação De Sinapse </br>Utilizador credencial de sinapse</br>Gestor de dados ligado à Sinapse</br>Utilizador da Sinapse
Piscina de faíscas Apache | Administrador da Sinapse </br>Colaborador da Sinapse </br> Operador de Computação De Sinapse
Runtime de integração | Administrador da Sinapse </br>Colaborador da Sinapse </br> Operador de Computação De Sinapse
Serviço ligado |Administrador da Sinapse </br>Utilizador credencial de sinapse
Credencial |Administrador da Sinapse </br>Utilizador credencial de sinapse
 
>[!note]
>Todos os papéis e ações de artefactos são traçados ao nível do espaço de trabalho. 

## <a name="next-steps"></a>Passos seguintes

Saiba [como rever as atribuições de funções do SYNAPSE RBAC](./how-to-review-synapse-rbac-role-assignments.md) para um espaço de trabalho.

Saiba [como atribuir funções de Sinapse RBAC](./how-to-manage-synapse-rbac-role-assignments.md)