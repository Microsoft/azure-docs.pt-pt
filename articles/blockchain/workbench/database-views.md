---
title: Vistas de base de dados no Azure Blockchain Workbench
description: Descrição geral das vistas da base de dados de BD SQL do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 06b7fb678bc79203589cfa75e8afb457d6ed344f
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594326"
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Vistas de base de dados no Azure Blockchain Workbench

O Azure Blockchain Workbench fornece dados de livros razão distribuídos para um *fora da cadeia* base de dados SQL DB. A base de dados fora da cadeia torna possível utilizar o SQL e ferramentas existentes, como [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), para interagir com dados de blockchain.

O Azure Blockchain Workbench fornece um conjunto de vistas de base de dados que fornecem acesso a dados que serão úteis ao executar as suas consultas. Essas exibições são bastante desnormalizadas para que seja fácil obter rapidamente começar a criar relatórios, análises e caso contrário, consumir dados de blockchain com as ferramentas existentes e sem ter de voltar a preparar a equipe de base de dados.

Esta secção inclui uma visão geral das vistas da base de dados e os dados que nelas contidos.

> [!NOTE]
> Qualquer utilização direta das tabelas de base de dados encontrada na base de dados fora destas vistas, ainda que possível, não é suportada.
>

## <a name="vwapplication"></a>vwApplication

Esta vista fornece detalhes sobre **aplicativos** que tenha sido carregado para o Azure Blockchain Workbench.

| Name                             | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName                  | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDescription           | nvarchar(255) | Sim         | Uma descrição da aplicação |
| ApplicationDisplayName           | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled               | bit           | Não          | Identifica se o aplicativo está atualmente ativado<br /> **Nota:** Mesmo que um aplicativo pode ser refletido como desativado na base de dados, contratos de associados continuam a desenvolver o blockchain e dados sobre esses contratos permanecerem na base de dados. |
| UploadedDtTm                     | datetime2(7)  | Não          | A data e hora de que um contrato foi carregado |
| UploadedByUserId                 | int           | Não          | O ID do utilizador que carregou o aplicativo |
| UploadedByUserExternalId         | nvarchar(255) | Não          | O identificador externo para o utilizador que carregou o aplicativo. Por predefinição, este ID é o utilizador do Azure Active Directory para o consortium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Não          | Identifica o estado atual do processo para o utilizador de aprovisionamento. Os valores possíveis são: <br />0 – o utilizador tiver sido criado pela API<br />1 – a chave foi associada com o utilizador na base de dados<br />2 – o utilizador esteja totalmente aprovisionado                         |
| UploadedByUserFirstName          | nvarchar(50)  | Sim         | O primeiro nome do utilizador que carregou o contrato |
| UploadedByUserLastName           | nvarchar(50)  | Sim         | O apelido do utilizador que carregou o contrato |
| UploadedByUserEmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que carregou o contrato |

## <a name="vwapplicationrole"></a>vwApplicationRole

Esta vista fornece detalhes sobre as funções que foram definidas nas aplicações do Azure Blockchain Workbench.

Num *Asset transferência* aplicativo, por exemplo, funções, como *comprador* e *vendedor* poderão ser definidas em funções.

| Name                   | Type             | Pode ser nulo | Descrição                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Não          | Um identificador exclusivo para a aplicação           |
| ApplicationName        | nvarchar(50)     | Não          | O nome da aplicação                       |
| ApplicationDescription | nvarchar(255)    | Sim         | Uma descrição da aplicação                  |
| ApplicationDisplayName | nvarchar(255)    | Não          | O nome a apresentar numa interface de utilizador      |
| RoleId                 | int              | Não          | Um identificador exclusivo para uma função na aplicação |
| RoleName               | nvarchar50)      | Não          | O nome da função                              |
| RoleDescription        | description(255) | Sim         | Uma descrição da função                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Esta vista fornece detalhes sobre as funções que foram definidas em aplicações do Azure Blockchain Workbench e os utilizadores associados a eles.

Num *Asset transferir* aplicativo, por exemplo, *John Smith* podem ser associados a *comprador* função.

| Name                       | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Não          | Um identificador exclusivo para a aplicação                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Não          | O nome da aplicação                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Sim         | Uma descrição da aplicação                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Não          | Um identificador exclusivo para uma função na aplicação                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Não          | O nome da função                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Sim         | Uma descrição da função                                                                                                                                                                                                             |
| UserId                     | int           | Não          | O ID do utilizador associado à função |
| UserExternalId             | nvarchar(255) | Não          | O identificador externo para o utilizador que está associado com a função. Por predefinição, este ID é o utilizador do Azure Active Directory para o consortium.                                                                     |
| UserProvisioningStatus     | int           | Não          | Identifica o estado atual do processo para o utilizador de aprovisionamento. Os valores possíveis são: <br />0 – o utilizador tiver sido criado pela API<br />1 – a chave foi associada com o utilizador na base de dados<br />2 – o utilizador esteja totalmente aprovisionado |
| UserFirstName              | nvarchar(50)  | Sim         | O primeiro nome do utilizador que está associado com a função |
| UserLastName               | nvarchar(255) | Sim         | O apelido do utilizador que está associado com a função |
| UserEmailAddress           | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que está associado com a função |

## <a name="vwconnectionuser"></a>vwConnectionUser

Esta vista fornece detalhes sobre as conexões definidas no Azure Blockchain Workbench e os utilizadores associados a eles. Para cada ligação, esta vista contém os seguintes dados:

-   Detalhes de contabilidade associada
-   Informações de utilizador associado

| Name                     | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Não          | O identificador exclusivo para uma ligação no Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Não          | O url do ponto final de uma ligação |
| ConnectionFundingAccount | nvarchar(255) | Sim         | A conta de financiamento associada com uma ligação, se aplicável |
| LedgerId                 | int           | Não          | O identificador exclusivo de um livro razão |
| LedgerName               | nvarchar(50)  | Não          | O nome do razão |
| LedgerDisplayName        | nvarchar(255) | Não          | O nome do razão a apresentar na interface do Usuário |
| UserId                   | int           | Não          | O ID do utilizador associado à ligação |
| UserExternalId           | nvarchar(255) | Não          | O identificador externo para o utilizador que está associado com a ligação. Por predefinição, este ID é o utilizador do Azure Active Directory para o consortium. |
| UserProvisioningStatus   | int           | Não          |Identifica o estado atual do processo para o utilizador de aprovisionamento. Os valores possíveis são: <br />0 – o utilizador tiver sido criado pela API<br />1 – a chave foi associada com o utilizador na base de dados<br />2 – o utilizador esteja totalmente aprovisionado |
| UserFirstName            | nvarchar(50)  | Sim         | O primeiro nome do utilizador que está associado com a ligação |
| UserLastName             | nvarchar(255) | Sim         | O apelido do utilizador que está associado com a ligação |
| UserEmailAddress         | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que está associado com a ligação |

## <a name="vwcontract"></a>vwContract

Esta vista fornece detalhes sobre os contratos implementados. Para cada contrato, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Implementação de contabilidade associada para a função
-   Detalhes para o utilizador que iniciou a ação
-   Detalhes relacionados com o bloco de blockchain e transação

| Name                                     | Type           | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Não          | O identificador exclusivo para uma ligação no Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Não          | O url do ponto final de uma ligação |
| ConnectionFundingAccount                 | nvarchar(255)  | Sim         | A conta de financiamento associada com uma ligação, se aplicável |
| LedgerId                                 | int            | Não          | O identificador exclusivo de um livro razão |
| LedgerName                               | nvarchar(50)   | Não          | O nome do razão |
| LedgerDisplayName                        | nvarchar(255)  | Não          | O nome do razão a apresentar na interface do Usuário |
| ApplicationId                            | int            | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName                          | nvarchar (50)  | Não          | O nome da aplicação |
| ApplicationDisplayName                   | nvarchar (255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled                       | bit            | Não          | Identifica se o aplicativo está atualmente ativado.<br /> **Nota:** Mesmo que um aplicativo pode ser refletido como desativado na base de dados, contratos de associados continuam a desenvolver o blockchain e dados sobre esses contratos permanecerem na base de dados.  |
| WorkflowId                               | int            | Não          | Um identificador exclusivo para o fluxo de trabalho associado com um contrato |
| WorkflowName                             | nvarchar(50)   | Não          | O nome do fluxo de trabalho associado com um contrato |
| WorkflowDisplayName                      | nvarchar(255)  | Não          | O nome do fluxo de trabalho associado com o contrato exibido na interface do usuário |
| WorkflowDescription                      | nvarchar(255)  | Sim         | A descrição do fluxo de trabalho associado com um contrato |
| ContractCodeId                           | int            | Não          | Um identificador exclusivo para o código de contrato associado o contrato |
| ContractFileName                         | int            | Não          | O nome do ficheiro que contém o código de contrato inteligente para este fluxo de trabalho. |
| ContractUploadedDtTm                     | int            | Não          | A data e hora, que o código de contrato foi carregado |
| ContractId                               | int            | Não          | O identificador exclusivo para o contrato |
| ContractProvisioningStatus               | int            | Não          | Identifica o estado atual do processo de aprovisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API na base de dados<br />1 – o contrato foi enviado ao razão<br />2 – o contrato foi implementado com êxito ao razão<br />3 ou 4 - o contrato de falha para implementação em razão<br />5 - o contrato foi implementado com êxito ao razão <br /><br />A partir da versão 1.5, são suportados valores de 0 a 5. Para efeitos compatibilidade na versão atual, modo de exibição **vwContractV0** está disponível-se de que apenas suporta valores 2 por meio de 0. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | O endereço de e-mail do utilizador que implementaram o contrato |
| ContractDeployedByUserId                 | int            | Não          | Um identificador externo para o utilizador que implementaram o contrato. Por predefinição, este ID é o guid que representa a ID do Azure Active Directory para o utilizador.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Não          | Um identificador externo para o utilizador que implementado o contrato. Por predefinição, este ID é o guid que representa a ID do Azure Active Directory para o utilizador.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Não          | Identifica o estado atual do processo de aprovisionamento para o utilizador. Os valores possíveis são: <br />0 – o utilizador tiver sido criado pela API<br />1 – a chave foi associada com o utilizador na base de dados <br />2 – o utilizador esteja totalmente aprovisionado                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Sim         | O primeiro nome do utilizador que implementaram o contrato |
| ContractDeployedByUserLastName           | nvarchar(255)  | Sim         | O apelido do utilizador que implementaram o contrato |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Sim         | O endereço de e-mail do utilizador que implementaram o contrato |

## <a name="vwcontractaction"></a>vwContractAction

Esta vista representa a maioria das informações relacionadas com ações executadas nos contratos e foi desenvolvida para facilitar prontamente cenários comuns de geração de relatórios. Para cada ação executada, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Associado à definição de função e o parâmetro de contrato inteligente
-   Implementação de contabilidade associada para a função
-   Valores de instância específica que forneceu para os parâmetros
-   Detalhes para o utilizador que iniciou a ação
-   Detalhes relacionados com o bloco de blockchain e transação

| Name                                     | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName                          | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDisplayName                   | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled                       | bit           | Não          | Este campo identifica se o aplicativo está atualmente ativado. Tenha em atenção – mesmo que um aplicativo pode ser refletido como desativado na base de dados, contratos de associados permanecem a desenvolver o blockchain e os dados sobre esses contratos permanecem na base de dados.                                                  |
| WorkflowId                               | int           | Não          | Um identificador exclusivo para um fluxo de trabalho |
| WorkflowName                             | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                      | nvarchar(255) | Não          | O nome do fluxo de trabalho para exibir numa interface de utilizador |
| WorkflowDescription                      | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractId                               | int           | Não          | Um identificador exclusivo para o contrato |
| ContractProvisioningStatus               | int           | Não          | Identifica o estado atual do processo de aprovisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API na base de dados<br />1 – o contrato foi enviado ao razão<br />2 – o contrato foi implementado com êxito ao razão<br />3 ou 4 - o contrato de falha para implementação em razão<br />5 - o contrato foi implementado com êxito ao razão <br /><br />A partir da versão 1.5, são suportados valores de 0 a 5. Para efeitos compatibilidade na versão atual, modo de exibição **vwContractActionV0** está disponível-se de que apenas suporta valores 2 por meio de 0. |
| ContractCodeId                           | int           | Não          | Um identificador exclusivo para a implementação de código do contrato |
| ContractLedgerIdentifier                 | nvarchar(255) | Sim         | Um identificador exclusivo associado com a versão implementada de um contrato inteligente para um livro razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId                 | int           | Não          | O identificador exclusivo do utilizador que o contrato de implementadas |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Sim         | Nome próprio do utilizador que implementaram o contrato |
| ContractDeployedByUserLastName           | nvarchar(255) | Sim         | Apelido do utilizador que implementaram o contrato |
| ContractDeployedByUserExternalId         | nvarchar(255) | Não          | Identificador externo do utilizador que implementaram o contrato. Por predefinição, este ID é o guid que representa a identidade no consortium do Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementaram o contrato |
| WorkflowFunctionId                       | int           | Não          | Um identificador exclusivo para uma função de fluxo de trabalho |
| WorkflowFunctionName                     | nvarchar(50)  | Não          | O nome da função |
| WorkflowFunctionDisplayName              | nvarchar(255) | Não          | O nome de uma função a ser exibido na interface do usuário |
| WorkflowFunctionDescription              | nvarchar(255) | Não          | A descrição da função |
| ContractActionId                         | int           | Não          | O identificador exclusivo para uma ação de contrato |
| ContractActionProvisioningStatus         | int           | Não          | Identifica o estado atual do processo de aprovisionamento para a ação de contrato. Os valores possíveis são: <br />0 – a ação de contrato ter sido criada pela API na base de dados<br />1 – a ação de contrato foi enviada ao razão<br />2 – a ação de contrato foi implementada com êxito ao razão<br />3 ou 4 - o contrato de falha para implementação em razão<br />5 - o contrato foi implementado com êxito ao razão <br /><br />A partir da versão 1.5, são suportados valores de 0 a 5. Para efeitos compatibilidade na versão atual, modo de exibição **vwContractActionV0** está disponível-se de que apenas suporta valores 2 por meio de 0. |
| ContractActionTimestamp                  | datetime(2,7) | Não          | O carimbo de hora da ação de contrato |
| ContractActionExecutedByUserId           | int           | Não          | Identificador exclusivo do utilizador que executou a ação de contrato |
| ContractActionExecutedByUserFirstName    | int           | Sim         | Nome próprio do utilizador que executou a ação de contrato |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Sim         | Apelido do utilizador que executou a ação de contrato |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Sim         | Identificador externo do utilizador que executou a ação de contrato. Por predefinição, este ID é o guid que representa a identidade no consortium do Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que executou a ação de contrato |
| WorkflowFunctionParameterId              | int           | Não          | Um identificador exclusivo para um parâmetro da função |
| WorkflowFunctionParameterName            | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do usuário |
| WorkflowFunctionParameterDataTypeId      | int           | Não          | O identificador exclusivo para o tipo de dados associado um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| ContractActionParameterValue             | nvarchar(255) | Não          | O valor para o parâmetro armazenado no contrato de inteligente |
| BlockHash                                | nvarchar(255) | Sim         | O hash do bloco |
| BlockNumber                              | int           | Sim         | O número de blocos no razão |
| BlockTimestamp                           | datetime(2,7) | Sim         | O carimbo de data / hora do bloco |
| TransactionId                            | int           | Não          | Um identificador exclusivo para a transação |
| TransactionFrom                          | nvarchar(255) | Sim         | A parte que originou a transação |
| TransactionTo                            | nvarchar(255) | Sim         | A parte que foi transacionada com |
| TransactionHash                          | nvarchar(255) | Sim         | O hash de uma transação |
| TransactionIsWorkbenchTransaction        | bit           | Sim         | Um pouco que identifica se a transação é uma transação de Azure Blockchain Workbench |
| TransactionProvisioningStatus            | int           | Sim         | Identifica o estado atual do processo de aprovisionamento para a transação. Os valores possíveis são: <br />0 – a transação foi criada pela API na base de dados<br />1 – a transação foi enviada ao razão<br />2 – a transação foi implementada com êxito ao razão                 |
| TransactionValue                         | decimal(32,2) | Sim         | O valor da transação |

## <a name="vwcontractproperty"></a>vwContractProperty

Esta vista representa a maioria das informações relacionadas com a propriedades associadas a um contrato e foi desenvolvida para facilitar prontamente cenários comuns de geração de relatórios. Para cada propriedade tomada, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Detalhes para o utilizador que implementaram o fluxo de trabalho
-   Associado à definição de propriedade de contrato inteligente
-   Valores de instância específica para propriedades
-   Detalhes para a propriedade de estado do contrato

| Name                               | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName                    | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDisplayName             | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled                 | bit           | Não          | Identifica se o aplicativo está atualmente ativado.<br />**Nota:** Mesmo que um aplicativo pode ser refletido como desativado na base de dados, contratos de associados continuam a desenvolver o blockchain e dados sobre esses contratos permanecerem na base de dados.                      |
| WorkflowId                         | int           | Não          | O identificador exclusivo para o fluxo de trabalho |
| WorkflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome do fluxo de trabalho exibido na interface do usuário |
| WorkflowDescription                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractId                         | int           | Não          | O identificador exclusivo para o contrato |
| ContractProvisioningStatus         | int           | Não          | Identifica o estado atual do processo de aprovisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API na base de dados<br />1 – o contrato foi enviado ao razão<br />2 – o contrato foi implementado com êxito ao razão<br />3 ou 4 - o contrato de falha para implementação em razão<br />5 - o contrato foi implementado com êxito ao razão <br /><br />A partir da versão 1.5, são suportados valores de 0 a 5. Para efeitos compatibilidade na versão atual, modo de exibição **vwContractPropertyV0** está disponível-se de que apenas suporta valores 2 por meio de 0. |
| ContractCodeId                     | int           | Não          | Um identificador exclusivo para a implementação de código do contrato |
| ContractLedgerIdentifier           | nvarchar(255) | Sim         | Um identificador exclusivo associado com a versão implementada de um contrato inteligente para um livro razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId           | int           | Não          | O identificador exclusivo do utilizador que o contrato de implementadas |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Nome próprio do utilizador que implementaram o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Apelido do utilizador que implementaram o contrato |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do utilizador que implementaram o contrato. Por predefinição, este ID é o guid que representa a identidade no consortium do Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementaram o contrato |
| WorkflowPropertyId                 | int           |             | Um identificador exclusivo para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | O ID do tipo de dados da propriedade |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados da propriedade |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade de fluxo de trabalho |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome a apresentar da propriedade de fluxo de trabalho |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | Uma descrição da propriedade |
| ContractPropertyValue              | nvarchar(255) | Não          | O valor para uma propriedade no contrato |
| StateName                          | nvarchar(50)  | Sim         | Se esta propriedade contém o estado do contrato, é o nome a apresentar para o estado. Se não for associado com o estado, o valor será nulo. |
| StateDisplayName                   | nvarchar(255) | Não          | Se esta propriedade contém o estado, é o nome a apresentar para o estado. Se não for associado com o estado, o valor será nulo. |
| StateValue                         | nvarchar(255) | Sim         | Se esta propriedade contém o estado, é o valor de estado. Se não for associado com o estado, o valor será nulo. |

## <a name="vwcontractstate"></a>vwContractState

Esta vista representa a maioria das informações relacionadas com o estado de um contrato específico e foi desenvolvida para facilitar prontamente cenários comuns de geração de relatórios. Cada registo nesta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Detalhes para o utilizador que implementaram o fluxo de trabalho
-   Associado à definição de propriedade de contrato inteligente
-   Detalhes para a propriedade de estado do contrato

| Name                               | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName                    | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDisplayName             | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled                 | bit           | Não          | Identifica se o aplicativo está atualmente ativado.<br />**Nota:** Mesmo que um aplicativo pode ser refletido como desativado na base de dados, contratos de associados continuam a desenvolver o blockchain e dados sobre esses contratos permanecerem na base de dados. |
| WorkflowId                         | int           | Não          | Um identificador exclusivo para o fluxo de trabalho |
| WorkflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome apresentado na interface do usuário |
| WorkflowDescription                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractLedgerImplementationId     | nvarchar(255) | Sim         | Um identificador exclusivo associado com a versão implementada de um contrato inteligente para um livro razão distribuído específico. Por exemplo, Ethereum. |
| ContractId                         | int           | Não          | Um identificador exclusivo para o contrato |
| ContractProvisioningStatus         | int           | Não          |Identifica o estado atual do processo de aprovisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API na base de dados<br />1 – o contrato foi enviado ao razão<br />2 – o contrato foi implementado com êxito ao razão<br />3 ou 4 - o contrato de falha para implementação em razão<br />5 - o contrato foi implementado com êxito ao razão <br /><br />A partir da versão 1.5, são suportados valores de 0 a 5. Para efeitos compatibilidade na versão atual, modo de exibição **vwContractStateV0** está disponível-se de que apenas suporta valores 2 por meio de 0. |
| ConnectionId                       | int           | Não          | Um identificador exclusivo para o fluxo de trabalho é implementado para a instância de blockchain |
| ContractCodeId                     | int           | Não          | Um identificador exclusivo para a implementação de código do contrato |
| ContractDeployedByUserId           | int           | Não          | Identificador exclusivo do utilizador que o contrato de implementadas |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do utilizador que implementaram o contrato. Por predefinição, este ID é o guid que representa a identidade no consortium do Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Nome próprio do utilizador que implementaram o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Apelido do utilizador que implementaram o contrato |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementaram o contrato |
| WorkflowPropertyId                 | int           | Não          | Um identificador exclusivo para uma propriedade de fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | O ID do tipo de dados da propriedade de fluxo de trabalho |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados da propriedade de fluxo de trabalho |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade de fluxo de trabalho |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome a apresentar da propriedade a mostrar numa interface do Usuário |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | A descrição da propriedade |
| ContractPropertyValue              | nvarchar(255) | Não          | O valor para uma propriedade armazenado no contrato |
| StateName                          | nvarchar(50)  | Sim         | Se esta propriedade contém o estado, ele o nome a apresentar para o estado. Se não for associado com o estado, o valor será nulo. |
| StateDisplayName                   | nvarchar(255) | Não          | Se esta propriedade contém o estado, é o nome a apresentar para o estado. Se não for associado com o estado, o valor será nulo. |
| StateValue                         | nvarchar(255) | Sim         | Se esta propriedade contém o estado, é o valor de estado. Se não for associado com o estado, o valor será nulo. |

## <a name="vwuser"></a>vwUser

Esta vista fornece detalhes sobre os membros de consórcio aprovisionadas para utilizar o Azure Blockchain Workbench. Por predefinição, os dados serem preenchidos por meio de provisionamento inicial do utilizador.

| Name               | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Não          | Um identificador exclusivo para um utilizador |
| externalID         | nvarchar(255) | Não          | Um identificador externo para um utilizador. Por predefinição, este ID é o guid que representa a ID do Azure Active Directory para o utilizador. |
| ProvisioningStatus | int           | Não          |Identifica o estado atual do processo para o utilizador de aprovisionamento. Os valores possíveis são: <br />0 – o utilizador tiver sido criado pela API<br />1 – a chave foi associada com o utilizador na base de dados<br />2 – o utilizador esteja totalmente aprovisionado |
| FirstName          | nvarchar(50)  | Sim         | O primeiro nome do utilizador |
| LastName           | nvarchar(50)  | Sim         | O apelido do utilizador |
| EmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador |

## <a name="vwworkflow"></a>vwWorkflow

Esta vista representa os parâmetros e as funções do fluxo de trabalho, bem como metadados de fluxo de trabalho de núcleo de detalhes. Concebido para relatórios, também contém metadados sobre a aplicação associada com o fluxo de trabalho. Esta vista contém dados de várias tabelas subjacentes para facilitar a geração de relatórios em fluxos de trabalho. Para cada fluxo de trabalho, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Informações de estado de início do fluxo de trabalho de associados

| Name                              | Type          | Pode ser nulo | Descrição                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName                   | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDisplayName            | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled                | bit           | Não          | Identifica se o aplicativo está ativado |
| WorkflowId                        | int           | Sim         | Um identificador exclusivo para um fluxo de trabalho |
| WorkflowName                      | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName               | nvarchar(255) | Não          | O nome apresentado na interface do usuário |
| WorkflowDescription               | nvarchar(255) | Sim         | A descrição do fluxo de trabalho. |
| WorkflowConstructorFunctionId     | int           | Não          | O identificador da função de fluxo de trabalho que serve como o construtor para o fluxo de trabalho |
| WorkflowStartStateId              | int           | Não          | Um identificador exclusivo para o Estado |
| WorkflowStartStateName            | nvarchar(50)  | Não          | O nome do Estado |
| WorkflowStartStateDisplayName     | nvarchar(255) | Não          | O nome a apresentar na interface do usuário para o Estado |
| WorkflowStartStateDescription     | nvarchar(255) | Sim         | Uma descrição do Estado do fluxo de trabalho |
| WorkflowStartStateStyle           | nvarchar(50)  | Sim         | Este valor identifica a percentagem de conclusão de que o fluxo de trabalho está quando neste Estado |
| WorkflowStartStateValue           | int           | Não          | O valor do Estado |
| WorkflowStartStatePercentComplete | int           | Não          | Uma descrição de texto que fornece uma dica para os clientes sobre como processar esse Estado na interface do Usuário. Estados suportados incluem *sucesso* e *falha* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Esta vista representa os parâmetros e as funções do fluxo de trabalho, bem como metadados de fluxo de trabalho de núcleo de detalhes. Concebido para relatórios, também contém metadados sobre a aplicação associada com o fluxo de trabalho. Esta vista contém dados de várias tabelas subjacentes para facilitar a geração de relatórios em fluxos de trabalho. Para cada função de fluxo de trabalho, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Detalhes da função de fluxo de trabalho

| Name                                 | Type          | Pode ser nulo | Descrição                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName                      | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDisplayName               | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled                   | bit           | Não          | Identifica se o aplicativo está ativado |
| WorkflowId                           | int           | Não          | Um identificador exclusivo para um fluxo de trabalho |
| WorkflowName                         | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                  | nvarchar(255) | Não          | O nome do fluxo de trabalho exibido na interface do usuário |
| WorkflowDescription                  | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowFunctionId                   | int           | Não          | Um identificador exclusivo para uma função |
| WorkflowFunctionName                 | nvarchar(50)  | Sim         | O nome da função |
| WorkflowFunctionDisplayName          | nvarchar(255) | Não          | O nome de uma função a ser exibido na interface do usuário |
| WorkflowFunctionDescription          | nvarchar(255) | Sim         | A descrição da função de fluxo de trabalho |
| WorkflowFunctionIsConstructor        | bit           | Não          | Identifica se a função de fluxo de trabalho é o construtor para o fluxo de trabalho |
| WorkflowFunctionParameterId          | int           | Não          | Um identificador exclusivo para um parâmetro de uma função |
| WorkflowFunctionParameterName        | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do usuário |
| WorkflowFunctionParameterDataTypeId  | int           | Não          | Um identificador exclusivo para o tipo de dados associado um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Esta vista representa as propriedades definidas para um fluxo de trabalho. Para cada propriedade, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Detalhes de propriedade do fluxo de trabalho

| Name                         | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName              | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDisplayName       | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| ApplicationEnabled           | bit           | Não          | Identifica se o aplicativo está atualmente ativado.<br />**Nota:** Mesmo que um aplicativo pode ser refletido como desativado na base de dados, contratos de associados continuam a desenvolver o blockchain e dados sobre esses contratos permanecerem na base de dados. |
| WorkflowId                   | int           | Não          | Um identificador exclusivo para o fluxo de trabalho |
| WorkflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome a apresentar para o fluxo de trabalho numa interface de utilizador |
| WorkflowDescription          | nvarchar(255) | Sim         | Uma descrição do fluxo de trabalho |
| WorkflowPropertyID           | int           | Não          | Um identificador exclusivo para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyName         | nvarchar(50)  | Não          | O nome da propriedade |
| WorkflowPropertyDescription  | nvarchar(255) | Sim         | Uma descrição da propriedade |
| WorkflowPropertyDisplayName  | nvarchar(255) | Não          | O nome a apresentar numa interface de utilizador |
| WorkflowPropertyWorkflowId   | int           | Não          | O ID do fluxo de trabalho a que esta propriedade está associada |
| WorkflowPropertyDataTypeId   | int           | Não          | O ID do tipo de dados definido para a propriedade |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Não          | O nome do tipo de dados definido para a propriedade |
| WorkflowPropertyIsState      | bit           | Não          | Este campo identifica se esta propriedade de fluxo de trabalho contém o estado do fluxo de trabalho |

## <a name="vwworkflowstate"></a>vwWorkflowState

Esta vista representa as propriedades associadas um fluxo de trabalho. Para cada contrato, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associada
-   Informações de estado do fluxo de trabalho

| Name                         | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Não          | Um identificador exclusivo para a aplicação |
| ApplicationName              | nvarchar(50)  | Não          | O nome da aplicação |
| ApplicationDisplayName       | nvarchar(255) | Não          | Uma descrição da aplicação |
| ApplicationEnabled           | bit           | Não          | Identifica se o aplicativo está atualmente ativado.<br />**Nota:** Mesmo que um aplicativo pode ser refletido como desativado na base de dados, contratos de associados continuam a desenvolver o blockchain e dados sobre esses contratos permanecerem na base de dados. |
| WorkflowId                   | int           | Não          | O identificador exclusivo para o fluxo de trabalho |
| WorkflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome apresentado na interface do usuário para o fluxo de trabalho |
| WorkflowDescription          | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowStateID              | int           | Não          | O identificador exclusivo para o Estado |
| WorkflowStateName            | nvarchar(50)  | Não          | O nome do Estado |
| WorkflowStateDisplayName     | nvarchar(255) | Não          | O nome a apresentar na interface do usuário para o Estado |
| WorkflowStateDescription     | nvarchar(255) | Sim         | Uma descrição do Estado do fluxo de trabalho |
| WorkflowStatePercentComplete | int           | Não          | Este valor identifica a percentagem de conclusão de que o fluxo de trabalho está quando neste Estado |
| WorkflowStateValue           | nvarchar(50)  | Não          | Valor do Estado |
| WorkflowStateStyle           | nvarchar(50)  | Não          | Uma descrição de texto que fornece uma dica para os clientes sobre como processar esse Estado na interface do Usuário. Estados suportados incluem *sucesso* e *falha* |
