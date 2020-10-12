---
title: Vistas da base de dados Azure Blockchain Workbench
description: Visão geral das vistas disponíveis da base de dados de pré-estrada Azure Blockchain Workbench Preview SQL DB.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e715bd2297c1e051ae92b15d73c945c9e80c3d94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87003326"
---
# <a name="azure-blockchain-workbench-database-views"></a>Vistas da base de dados Azure Blockchain Workbench

A Azure Blockchain Workbench Preview fornece dados de livros distribuídos a uma base de dados DB SQL *off-chain.* A base de dados off-chain permite utilizar SQL e ferramentas existentes, como [o SQL Server Management Studio,](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)para interagir com os dados blockchain.

O Azure Blockchain Workbench fornece um conjunto de pontos de vista de base de dados que fornecem acesso a dados que serão úteis na realização das suas consultas. Estas opiniões são fortemente desnormalizadas para facilitar a rápida construção de relatórios, análises e de outra forma consumir dados blockchain com ferramentas existentes e sem ter que reconverter o pessoal da base de dados.

Esta secção inclui uma visão geral das vistas da base de dados e dos dados que contêm.

> [!NOTE]
> Qualquer utilização direta de tabelas de bases de dados encontradas na base de dados fora destas vistas, embora possível, não é suportada.
>

## <a name="vwapplication"></a>vwApplication

Esta vista fornece detalhes sobre aplicações que foram **enviadas** para a Azure Blockchain Workbench.

| Nome                             | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                    | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                  | nvarchar(50)  | Não          | O nome da aplicação |
| AplicaçãoDescrição           | nvarchar(255) | Sim         | Uma descrição do pedido |
| Nome de Aplicaçõesdisplay           | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled               | bit           | Não          | Identifica se o pedido está atualmente ativado<br /> **Nota:** Embora uma aplicação possa ser refletida como deficiente na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| UploadEdDtM                     | data 2(7)  | Não          | A data e a hora de um contrato foi enviado |
| UploadedByUserId                 | int           | Não          | O ID do utilizador que carregou a aplicação |
| UploadedByUserExternalId         | nvarchar(255) | Não          | O identificador externo para o utilizador que fez o upload da aplicação. Por padrão, este ID é o utilizador do Diretório Ativo Azure para o consórcio.                                                                                                |
| UploadEdByUserProvisioningStatus | int           | Não          | Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 - Foi associada uma chave ao utilizador na base de dados<br />2 - O utilizador está totalmente a provisionado                         |
| UploadedByUserFirstName          | nvarchar(50)  | Sim         | O primeiro nome do utilizador que carregou o contrato |
| UploadedByUserLastName           | nvarchar(50)  | Sim         | O último nome do utilizador que carregou o contrato |
| UploadedByUserEmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que fez o upload do contrato |

## <a name="vwapplicationrole"></a>vwApplicationRole

Esta visão fornece detalhes sobre as funções que foram definidas nas aplicações da Azure Blockchain Workbench.

Numa aplicação *de Transferência de Ativos,* por exemplo, podem ser definidas funções como As funções *de Comprador* e *Vendedor.*

| Nome                   | Tipo             | Pode ser nulo | Descrição                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationID          | int              | Não          | Um identificador único para a aplicação           |
| ApplicationName        | nvarchar(50)     | Não          | O nome da aplicação                       |
| AplicaçãoDescrição | nvarchar(255)    | Sim         | Uma descrição do pedido                  |
| Nome de Aplicaçõesdisplay | nvarchar(255)    | Não          | O nome a ser exibido numa interface de utilizador      |
| RoleId                 | int              | Não          | Um identificador único para um papel na aplicação |
| RoleName               | nvarchar50)      | Não          | O nome do papel                              |
| Descrição do papel        | descrição(255) | Sim         | Uma descrição do papel                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Esta visão fornece detalhes sobre as funções que foram definidas nas aplicações Azure Blockchain Workbench e os utilizadores associados às suas.

Numa aplicação *de Transferência de Ativos,* por exemplo, *John Smith* pode estar associado ao papel *de Comprador.*

| Nome                       | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID              | int           | Não          | Um identificador único para a aplicação                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Não          | O nome da aplicação                                                                                                                                                                                                           |
| AplicaçãoDescrição     | nvarchar(255) | Sim         | Uma descrição do pedido                                                                                                                                                                                                      |
| Nome de Aplicaçõesdisplay     | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Não          | Um identificador único para um papel na aplicação                                                                                                                                                                                     |
| Nome de aplicativorole        | nvarchar50)   | Não          | O nome do papel                                                                                                                                                                                                                  |
| Descrição do AplicativoRoleDescription | nvarchar(255) | Sim         | Uma descrição do papel                                                                                                                                                                                                             |
| IDUtilizador                     | int           | Não          | O ID do utilizador associado ao papel |
| UserExternalId             | nvarchar(255) | Não          | O identificador externo para o utilizador que está associado ao papel. Por padrão, este ID é o utilizador do Diretório Ativo Azure para o consórcio.                                                                     |
| UserProvisioningStatus     | int           | Não          | Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 - Foi associada uma chave ao utilizador na base de dados<br />2 - O utilizador está totalmente a provisionado |
| Nome userFirst              | nvarchar(50)  | Sim         | O primeiro nome do utilizador que está associado ao papel |
| Nome do UtilizadorLastName               | nvarchar(255) | Sim         | O último nome do utilizador que está associado ao papel |
| UserEmailAddress           | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que está associado à função |

## <a name="vwconnectionuser"></a>vwConnectionUser

Esta vista fornece detalhes sobre as ligações definidas na Azure Blockchain Workbench e os utilizadores associados a elas. Para cada ligação, esta vista contém os seguintes dados:

-   Detalhes do livro de contabilidade associados
-   Informações associadas ao utilizador

| Nome                     | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Não          | O identificador único para uma ligação na Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Não          | O url de ponto final para uma ligação |
| Contagem de financiamento de conexão | nvarchar(255) | Sim         | A conta de financiamento associada a uma ligação, se aplicável |
| LedgerId                 | int           | Não          | O identificador único para um livro-razão |
| Nome ledger               | nvarchar(50)  | Não          | O nome do livro-razão |
| Nome de LedgerDisplay        | nvarchar(255) | Não          | O nome do livro-razão para exibir na UI |
| IDUtilizador                   | int           | Não          | O ID do utilizador associado à ligação |
| UserExternalId           | nvarchar(255) | Não          | O identificador externo para o utilizador que está associado à ligação. Por padrão, este ID é o utilizador do Diretório Ativo Azure para o consórcio. |
| UserProvisioningStatus   | int           | Não          |Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 - Foi associada uma chave ao utilizador na base de dados<br />2 - O utilizador está totalmente a provisionado |
| Nome userFirst            | nvarchar(50)  | Sim         | O primeiro nome do utilizador que está associado à ligação |
| Nome do UtilizadorLastName             | nvarchar(255) | Sim         | O último nome do utilizador que está associado à ligação |
| UserEmailAddress         | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que está associado à ligação |

## <a name="vwcontract"></a>vwContract

Esta vista fornece detalhes sobre contratos implementados. Para cada contrato, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Implementação de contabilidade associada para a função
-   Detalhes para o utilizador que iniciou a ação
-   Detalhes relacionados com o bloco blockchain e transação

| Nome                                     | Tipo           | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Não          | O identificador único para uma ligação na Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Não          | O url de ponto final para uma ligação |
| Contagem de financiamento de conexão                 | nvarchar(255)  | Sim         | A conta de financiamento associada a uma ligação, se aplicável |
| LedgerId                                 | int            | Não          | O identificador único para um livro-razão |
| Nome ledger                               | nvarchar(50)   | Não          | O nome do livro-razão |
| Nome de LedgerDisplay                        | nvarchar(255)  | Não          | O nome do livro-razão para exibir na UI |
| ApplicationID                            | int            | Não          | Um identificador único para a aplicação |
| ApplicationName                          | nvarchar (50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay                   | nvarchar (255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled                       | bit            | Não          | Identifica se o pedido está atualmente ativado.<br /> **Nota:** Embora uma aplicação possa ser refletida como deficiente na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados.  |
| WorkflowId                               | int            | Não          | Um identificador único para o fluxo de trabalho associado a um contrato |
| WorkflowName                             | nvarchar(50)   | Não          | O nome do fluxo de trabalho associado a um contrato |
| WorkflowDisplayName                      | nvarchar(255)  | Não          | O nome do fluxo de trabalho associado ao contrato apresentado na interface do utilizador |
| FuncionaçãoDescriminação                      | nvarchar(255)  | Sim         | A descrição do fluxo de trabalho associado a um contrato |
| ContractCodeId                           | int            | Não          | Um identificador único para o código contratual associado ao contrato |
| Nome do ContractFile                         | int            | Não          | O nome do ficheiro que contém o código de contrato inteligente para este fluxo de trabalho. |
| ContractUploadedDtm                     | int            | Não          | A data e a hora em que o código do contrato foi carregado |
| ContractId                               | int            | Não          | O identificador único para o contrato |
| ContractProvisioningStatus               | int            | Não          | Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro de contabilidade<br />2 - O contrato foi implementado com sucesso no livro de contabilidade<br />3 ou 4 - O contrato não foi implantado no livro-razão<br />5 - O contrato foi implementado com sucesso no livro-razão <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retrocompatibilidade na versão atual, veja que o **VWContractV0** está disponível e que suporta apenas os valores 0 a 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | O endereço de e-mail do utilizador que implementou o contrato |
| ContractDeployedByUserId                 | int            | Não          | Um identificador externo para o utilizador que implementou o contrato. Por predefinição, este ID é o guia que representa o ID do Diretório Ativo Azure para o utilizador.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Não          | Um identificador externo para o utilizador que implementou o contrato. Por predefinição, este ID é o guia que representa o ID do Diretório Ativo Azure para o utilizador.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Não          | Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – o utilizador foi criado pela API<br />1 - Foi associada uma chave ao utilizador na base de dados <br />2 - O utilizador está totalmente a provisionado                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Sim         | O primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName           | nvarchar(255)  | Sim         | O último nome do utilizador que implementou o contrato |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Sim         | O endereço de e-mail do utilizador que implementou o contrato |

## <a name="vwcontractaction"></a>vwContractAction

Este ponto de vista representa a maioria das informações relacionadas com as ações tomadas sobre contratos e destina-se a facilitar facilmente cenários comuns de reporte. Para cada ação tomada, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Função de contrato inteligente associada e definição de parâmetro
-   Implementação de contabilidade associada para a função
-   Valores de instância específicos previstos para parâmetros
-   Detalhes para o utilizador que iniciou a ação
-   Detalhes relacionados com o bloco blockchain e transação

| Nome                                     | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                            | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                          | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay                   | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled                       | bit           | Não          | Este campo identifica se a aplicação está ativada atualmente. Nota – Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados.                                                  |
| WorkflowId                               | int           | Não          | Um identificador único para um fluxo de trabalho |
| WorkflowName                             | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                      | nvarchar(255) | Não          | O nome do fluxo de trabalho para exibir numa interface de utilizador |
| FuncionaçãoDescriminação                      | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractId                               | int           | Não          | Um identificador único para o contrato |
| ContractProvisioningStatus               | int           | Não          | Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro de contabilidade<br />2 - O contrato foi implementado com sucesso no livro de contabilidade<br />3 ou 4 - O contrato não foi implantado no livro-razão<br />5 - O contrato foi implementado com sucesso no livro-razão <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retrocompatibilidade na versão atual, veja **que o VWContractActionV0** está disponível que suporta apenas os valores 0 a 2. |
| ContractCodeId                           | int           | Não          | Um identificador único para a implementação do código do contrato |
| ContractLedgerIdentifier                 | nvarchar(255) | Sim         | Um identificador único associado à versão implementada de um contrato inteligente para um livro de contabilidade distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId                 | int           | Não          | O identificador único do utilizador que implementou o contrato |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Sim         | Primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName           | nvarchar(255) | Sim         | Último nome do utilizador que implementou o contrato |
| ContractDeployedByUserExternalId         | nvarchar(255) | Não          | Identificador externo do utilizador que implementou o contrato. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementou o contrato |
| WorkflowFunctionId                       | int           | Não          | Um identificador único para uma função de fluxo de trabalho |
| WorkflowSucação Desagraçado                     | nvarchar(50)  | Não          | O nome da função |
| WorkflowFunctionDisplayName              | nvarchar(255) | Não          | O nome de uma função a ser exibida na interface do utilizador |
| WorkflowDiscrição de funcionação              | nvarchar(255) | Não          | A descrição da função |
| ContractActionId                         | int           | Não          | O identificador único para uma ação contratual |
| ContractActionProvisioningStatus         | int           | Não          | Identifica o estado atual do processo de provisionamento para a ação contratual. Os valores possíveis são: <br />0 – A ação contratual foi criada pela API na base de dados<br />1 - A ação contratual foi enviada para o livro de contabilidade<br />2 - A ação contratual foi implementada com sucesso no livro-razão<br />3 ou 4 - O contrato não foi implantado no livro-razão<br />5 - O contrato foi implementado com sucesso no livro-razão <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retrocompatibilidade na versão atual, veja **que o VWContractActionV0** está disponível que suporta apenas os valores 0 a 2. |
| ContractActionTimestamp                  | datata(2,7) | Não          | O tempotad da ação contratual |
| ContractActionExecutedByUserId           | int           | Não          | Identificador único do utilizador que executou a ação contratual |
| ContractActionExecutedByUserFirstName    | int           | Sim         | Primeiro nome do utilizador que executou a ação contratual |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Sim         | Último nome do utilizador que executou a ação contratual |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Sim         | Identificador externo do utilizador que executou a ação contratual. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que executou a ação contratual |
| WorkflowFunctionParameterId              | int           | Não          | Um identificador único para um parâmetro da função |
| WorkflowSfístame de Parametrómetro            | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do utilizador |
| Fluxo de trabalhoFunctionParameterDataTypeId      | int           | Não          | O identificador único para o tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| ContractActionParameterValue             | nvarchar(255) | Não          | O valor para o parâmetro armazenado no contrato inteligente |
| BlockHash                                | nvarchar(255) | Sim         | O haxixe do bloco |
| BlockNumber                              | int           | Sim         | O número do bloco no livro de contabilidade |
| BlockTimestamp                           | datata(2,7) | Sim         | O carimbo do tempo do quarteirão |
| TransactionId                            | int           | Não          | Um identificador único para a transação |
| TransaçõesD                          | nvarchar(255) | Sim         | A parte que originou a transação |
| TransaçãoTo                            | nvarchar(255) | Sim         | O partido que foi transacionado com |
| TransactionHash                          | nvarchar(255) | Sim         | O haxixe de uma transação |
| TransacçãoIsWorkbenchTransacções        | bit           | Sim         | Um pouco que identifica se a transação é uma transação Azure Blockchain Workbench |
| TransacçãoProvisioningStatus            | int           | Sim         | Identifica o estado atual do processo de provisionamento para a transação. Os valores possíveis são: <br />0 – A transação foi criada pela API na base de dados<br />1 - A transação foi enviada para o livro-razão<br />2 - A transação foi implementada com sucesso no livro-razão                 |
| TransacçãoValue                         | decimal (32,2) | Sim         | O valor da transação |

## <a name="vwcontractproperty"></a>vwContractProperty

Esta visão representa a maioria das informações relacionadas com propriedades associadas a um contrato e destina-se a facilitar facilmente cenários comuns de reporte. Para cada propriedade tomada, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes para o utilizador que implementou o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associado
-   Valores de instância específica para propriedades
-   Detalhes para a propriedade do Estado do contrato

| Nome                               | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                    | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay             | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled                 | bit           | Não          | Identifica se o pedido está atualmente ativado.<br />**Nota:** Embora uma aplicação possa ser refletida como deficiente na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados.                      |
| WorkflowId                         | int           | Não          | O identificador único para o fluxo de trabalho |
| WorkflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome do fluxo de trabalho exibido na interface do utilizador |
| FuncionaçãoDescriminação                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractId                         | int           | Não          | O identificador único para o contrato |
| ContractProvisioningStatus         | int           | Não          | Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro de contabilidade<br />2 - O contrato foi implementado com sucesso no livro de contabilidade<br />3 ou 4 - O contrato não foi implantado no livro-razão<br />5 - O contrato foi implementado com sucesso no livro-razão <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retrocompatibilidade na versão atual, veja que o **VWContractPropertyV0** está disponível que suporta apenas os valores 0 a 2. |
| ContractCodeId                     | int           | Não          | Um identificador único para a implementação do código do contrato |
| ContractLedgerIdentifier           | nvarchar(255) | Sim         | Um identificador único associado à versão implementada de um contrato inteligente para um livro de contabilidade distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId           | int           | Não          | O identificador único do utilizador que implementou o contrato |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Último nome do utilizador que implementou o contrato |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do utilizador que implementou o contrato. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementou o contrato |
| WorkflowPropertyId                 | int           |             | Um identificador único para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | O ID do tipo de dados da propriedade |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados do imóvel |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade workflow |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome de exibição da propriedade workflow |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | Uma descrição da propriedade |
| ContratoPropertyValue              | nvarchar(255) | Não          | O valor de um imóvel no contrato |
| Nome do Estado                          | nvarchar(50)  | Sim         | Se esta propriedade contiver o estado do contrato, é o nome de exposição para o estado. Se não estiver associado ao Estado, o valor será nulo. |
| Nome de Estadodisplay                   | nvarchar(255) | Não          | Se esta propriedade contiver o estado, é o nome de exibição do estado. Se não estiver associado ao Estado, o valor será nulo. |
| Estado Devasso                         | nvarchar(255) | Sim         | Se esta propriedade contiver o estado, é o valor do Estado. Se não estiver associado ao Estado, o valor será nulo. |

## <a name="vwcontractstate"></a>vwContractState

Esta opinião representa a maioria das informações relacionadas com o estado de um contrato específico e destina-se a facilitar facilmente cenários comuns de reporte. Cada registo nesta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes para o utilizador que implementou o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associado
-   Detalhes para a propriedade do Estado do contrato

| Nome                               | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                    | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay             | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled                 | bit           | Não          | Identifica se o pedido está atualmente ativado.<br />**Nota:** Embora uma aplicação possa ser refletida como deficiente na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| WorkflowId                         | int           | Não          | Um identificador único para o fluxo de trabalho |
| WorkflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome apresentado na interface do utilizador |
| FuncionaçãoDescriminação                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractLedgerImplementationId     | nvarchar(255) | Sim         | Um identificador único associado à versão implementada de um contrato inteligente para um livro de contabilidade distribuído específico. Por exemplo, Ethereum. |
| ContractId                         | int           | Não          | Um identificador único para o contrato |
| ContractProvisioningStatus         | int           | Não          |Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro de contabilidade<br />2 - O contrato foi implementado com sucesso no livro de contabilidade<br />3 ou 4 - O contrato não foi implantado no livro-razão<br />5 - O contrato foi implementado com sucesso no livro-razão <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retrocompatibilidade na versão atual, veja que o **VWContractStateV0** está disponível que apenas suporta os valores 0 a 2. |
| ConnectionId                       | int           | Não          | Um identificador único para a instância blockchain o fluxo de trabalho é implementado para |
| ContractCodeId                     | int           | Não          | Um identificador único para a implementação do código do contrato |
| ContractDeployedByUserId           | int           | Não          | Identificador único do utilizador que implementou o contrato |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do utilizador que implementou o contrato. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Último nome do utilizador que implementou o contrato |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementou o contrato |
| WorkflowPropertyId                 | int           | Não          | Um identificador único para uma propriedade de fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | O ID do tipo de dados da propriedade do fluxo de trabalho |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados da propriedade do fluxo de trabalho |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade workflow |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome de exibição da propriedade para mostrar em uma UI |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | A descrição do imóvel |
| ContratoPropertyValue              | nvarchar(255) | Não          | O valor de um imóvel armazenado no contrato |
| Nome do Estado                          | nvarchar(50)  | Sim         | Se esta propriedade contiver o estado, é o nome de exibição do estado. Se não estiver associado ao Estado, o valor será nulo. |
| Nome de Estadodisplay                   | nvarchar(255) | Não          | Se esta propriedade contiver o estado, é o nome de exibição do estado. Se não estiver associado ao Estado, o valor será nulo. |
| Estado Devasso                         | nvarchar(255) | Sim         | Se esta propriedade contiver o estado, é o valor do Estado. Se não estiver associado ao Estado, o valor será nulo. |

## <a name="vwuser"></a>vwUser

Esta visão fornece detalhes sobre os membros do consórcio que estão dispostos a usar a Azure Blockchain Workbench. Por predefinição, os dados são preenchidos através do fornecimento inicial do utilizador.

| Nome               | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Não          | Um identificador único para um utilizador |
| EXTERNALID         | nvarchar(255) | Não          | Um identificador externo para um utilizador. Por predefinição, este ID é o guia que representa o ID do Diretório Ativo Azure para o utilizador. |
| ProvisioningStatus | int           | Não          |Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 - Foi associada uma chave ao utilizador na base de dados<br />2 - O utilizador está totalmente a provisionado |
| FirstName          | nvarchar(50)  | Sim         | O primeiro nome do utilizador |
| LastName           | nvarchar(50)  | Sim         | O último nome do utilizador |
| EmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador |

## <a name="vwworkflow"></a>vwWorkflow

Esta visão representa os metadados de fluxo de trabalho de núcleo de detalhes, bem como as funções e parâmetros do fluxo de trabalho. Projetado para reportagem, também contém metadados sobre a aplicação associada ao fluxo de trabalho. Esta visão contém dados de várias tabelas subjacentes para facilitar a comunicação sobre fluxos de trabalho. Para cada fluxo de trabalho, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Fluxo de trabalho associado inicia informações estatais

| Nome                              | Tipo          | Pode ser nulo | Descrição                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                     | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                   | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay            | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled                | bit           | Não          | Identifica se a aplicação está ativada |
| WorkflowId                        | int           | Sim         | Um identificador único para um fluxo de trabalho |
| WorkflowName                      | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName               | nvarchar(255) | Não          | O nome apresentado na interface do utilizador |
| FuncionaçãoDescriminação               | nvarchar(255) | Sim         | A descrição do fluxo de trabalho. |
| WorkflowConstructorFunctionId     | int           | Não          | O identificador da função de fluxo de trabalho que serve de construtor para o fluxo de trabalho |
| WorkflowStartStateId              | int           | Não          | Um identificador único para o estado |
| WorkflowStartStateName            | nvarchar(50)  | Não          | O nome do estado |
| WorkflowStartStateDisplayName     | nvarchar(255) | Não          | O nome a ser exibido na interface de utilizador para o estado |
| WorkflowStartStateDescription     | nvarchar(255) | Sim         | Uma descrição do estado de fluxo de trabalho |
| WorkflowStartStateStyle           | nvarchar(50)  | Sim         | Este valor identifica a percentagem completa que o fluxo de trabalho é quando neste estado |
| WorkflowStartStateValue           | int           | Não          | O valor do Estado |
| WorkflowStartStatePercentComplete | int           | Não          | Uma descrição de texto que fornece uma dica aos clientes sobre como tornar este estado na UI. Estados apoiados incluem *sucesso* e *fracasso* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Esta visão representa os metadados de fluxo de trabalho de núcleo de detalhes, bem como as funções e parâmetros do fluxo de trabalho. Projetado para reportagem, também contém metadados sobre a aplicação associada ao fluxo de trabalho. Esta visão contém dados de várias tabelas subjacentes para facilitar a comunicação sobre fluxos de trabalho. Para cada função de fluxo de trabalho, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes da função de fluxo de trabalho

| Nome                                 | Tipo          | Pode ser nulo | Descrição                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationID                        | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                      | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay               | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled                   | bit           | Não          | Identifica se a aplicação está ativada |
| WorkflowId                           | int           | Não          | Um identificador único para um fluxo de trabalho |
| WorkflowName                         | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                  | nvarchar(255) | Não          | O nome do fluxo de trabalho exibido na interface do utilizador |
| FuncionaçãoDescriminação                  | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowFunctionId                   | int           | Não          | Um identificador único para uma função |
| WorkflowSucação Desagraçado                 | nvarchar(50)  | Sim         | O nome da função |
| WorkflowFunctionDisplayName          | nvarchar(255) | Não          | O nome de uma função a ser exibida na interface do utilizador |
| WorkflowDiscrição de funcionação          | nvarchar(255) | Sim         | A descrição da função de fluxo de trabalho |
| WorkflowFunctionIsConstructor        | bit           | Não          | Identifica se a função de fluxo de trabalho é o construtor para o fluxo de trabalho |
| WorkflowFunctionParameterId          | int           | Não          | Um identificador único para um parâmetro de uma função |
| WorkflowSfístame de Parametrómetro        | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do utilizador |
| Fluxo de trabalhoFunctionParameterDataTypeId  | int           | Não          | Um identificador único para o tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Esta vista representa as propriedades definidas para um fluxo de trabalho. Para cada propriedade, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes da propriedade do fluxo de trabalho

| Nome                         | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | Não          | Um identificador único para a aplicação |
| ApplicationName              | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay       | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| ApplicationEnabled           | bit           | Não          | Identifica se o pedido está atualmente ativado.<br />**Nota:** Embora uma aplicação possa ser refletida como deficiente na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| WorkflowId                   | int           | Não          | Um identificador único para o fluxo de trabalho |
| WorkflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome a ser apresentado para o fluxo de trabalho numa interface de utilizador |
| FuncionaçãoDescriminação          | nvarchar(255) | Sim         | Uma descrição do fluxo de trabalho |
| WorkflowPropertyID           | int           | Não          | Um identificador único para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyName         | nvarchar(50)  | Não          | O nome da propriedade |
| WorkflowPropertyDescription  | nvarchar(255) | Sim         | Uma descrição da propriedade |
| WorkflowPropertyDisplayName  | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| WorkflowPropertyWorkflowId   | int           | Não          | O ID do fluxo de trabalho ao qual esta propriedade está associada |
| WorkflowPropertyDataTypeId   | int           | Não          | O ID do tipo de dados definido para o imóvel |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Não          | O nome do tipo de dados definido para o imóvel |
| WorkflowPropertyIsState      | bit           | Não          | Este campo identifica se esta propriedade de fluxo de trabalho contém o estado do fluxo de trabalho |

## <a name="vwworkflowstate"></a>vwWorkflowState

Esta vista representa as propriedades associadas a um fluxo de trabalho. Para cada contrato, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Informações do estado do fluxo de trabalho

| Nome                         | Tipo          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | Não          | Um identificador único para a aplicação |
| ApplicationName              | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de Aplicaçõesdisplay       | nvarchar(255) | Não          | Uma descrição do pedido |
| ApplicationEnabled           | bit           | Não          | Identifica se o pedido está atualmente ativado.<br />**Nota:** Embora uma aplicação possa ser refletida como deficiente na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| WorkflowId                   | int           | Não          | O identificador único para o fluxo de trabalho |
| WorkflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome apresentado na interface do utilizador para o fluxo de trabalho |
| FuncionaçãoDescriminação          | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowStateID              | int           | Não          | O identificador único para o estado |
| WorkflowName            | nvarchar(50)  | Não          | O nome do estado |
| WorkflowStateDisplayName     | nvarchar(255) | Não          | O nome a ser exibido na interface de utilizador para o estado |
| WorkflowDescription     | nvarchar(255) | Sim         | Uma descrição do estado de fluxo de trabalho |
| WorkflowStatePercentComplete | int           | Não          | Este valor identifica a percentagem completa que o fluxo de trabalho é quando neste estado |
| WorkflowStateValue           | nvarchar(50)  | Não          | Valor do Estado |
| WorkflowStateStyle           | nvarchar(50)  | Não          | Uma descrição de texto que fornece uma dica aos clientes sobre como tornar este estado na UI. Estados apoiados incluem *sucesso* e *fracasso* |
