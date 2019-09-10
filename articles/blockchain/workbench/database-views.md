---
title: Exibições de banco de dados na visualização do Azure Blockchain Workbench
description: Visão geral das exibições do banco de dados SQL do Azure Blockchain Workbench Preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 7548d460d0d99642d11e4eb5755730400b509e94
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845034"
---
# <a name="database-views-in-azure-blockchain-workbench-preview"></a>Exibições de banco de dados na visualização do Azure Blockchain Workbench

A visualização do Azure Blockchain Workbench fornece dados de razões distribuídas para um banco de dados SQL DB *fora da cadeia* . O banco de dados fora da cadeia torna possível usar o SQL e as ferramentas existentes, como [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), para interagir com os dados do blockchain.

O Azure Blockchain Workbench fornece um conjunto de modos de exibição de banco de dados que fornecem acesso ao dado que será útil ao executar suas consultas. Essas exibições são muito desnormalizadas para facilitar a criação rápida de relatórios, análises e, de outra forma, consumir dados de blockchain com ferramentas existentes e sem a necessidade de treinar novamente a equipe do banco.

Esta seção inclui uma visão geral das exibições do banco de dados e dos próprios dados que elas contêm.

> [!NOTE]
> Qualquer uso direto de tabelas de banco de dados localizadas no banco de dados fora dessas exibições, embora possível, não tem suporte.
>

## <a name="vwapplication"></a>vwApplication

Esta exibição fornece detalhes sobre **os aplicativos** que foram carregados no Azure Blockchain Workbench.

| Name                             | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName                  | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDescription           | nvarchar(255) | Sim         | Uma descrição do aplicativo |
| ApplicationDisplayName           | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled               | bit           | Não          | Identifica se o aplicativo está habilitado no momento<br /> **Nota:** Embora um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dado. |
| UploadedDtTm                     | datetime2 (7)  | Não          | A data e a hora em que um contrato foi carregado |
| UploadedByUserId                 | int           | Não          | A ID do usuário que carregou o aplicativo |
| UploadedByUserExternalId         | nvarchar(255) | Não          | O identificador externo do usuário que carregou o aplicativo. Por padrão, essa ID é o usuário da Azure Active Directory para o consórcio.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Não          | Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado                         |
| UploadedByUserFirstName          | nvarchar(50)  | Sim         | O nome do usuário que carregou o contrato |
| UploadedByUserLastName           | nvarchar(50)  | Sim         | O último nome do usuário que carregou o contrato |
| UploadedByUserEmailAddress       | nvarchar(255) | Sim         | O endereço de email do usuário que carregou o contrato |

## <a name="vwapplicationrole"></a>vwApplicationRole

Esta exibição fornece detalhes sobre as funções que foram definidas nos aplicativos do Azure Blockchain Workbench.

Em um aplicativo de *transferência de ativos* , por exemplo, funções como *compradores* e *vendedores* podem ser definidas.

| Name                   | Type             | Pode ser nulo | Descrição                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Não          | Um identificador exclusivo para o aplicativo           |
| ApplicationName        | nvarchar(50)     | Não          | O nome do aplicativo                       |
| ApplicationDescription | nvarchar(255)    | Sim         | Uma descrição do aplicativo                  |
| ApplicationDisplayName | nvarchar(255)    | Não          | O nome a ser exibido em uma interface do usuário      |
| RoleId                 | int              | Não          | Um identificador exclusivo para uma função no aplicativo |
| RoleName               | nvarchar50)      | Não          | O nome da função                              |
| RoleDescription        | description(255) | Sim         | Uma descrição da função                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Essa exibição fornece detalhes sobre as funções que foram definidas nos aplicativos do Azure Blockchain Workbench e os usuários associados a elas.

Em um aplicativo de *transferência de ativos* , por exemplo, *John Smith* pode estar associado à função de *comprador* .

| Name                       | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Não          | Um identificador exclusivo para o aplicativo                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Não          | O nome do aplicativo                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Sim         | Uma descrição do aplicativo                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Não          | Um identificador exclusivo para uma função no aplicativo                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Não          | O nome da função                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Sim         | Uma descrição da função                                                                                                                                                                                                             |
| UserId                     | int           | Não          | A ID do usuário associado à função |
| Userexternalsourceid             | nvarchar(255) | Não          | O identificador externo para o usuário que está associado à função. Por padrão, essa ID é o usuário da Azure Active Directory para o consórcio.                                                                     |
| UserProvisioningStatus     | int           | Não          | Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado |
| UserFirstName              | nvarchar(50)  | Sim         | O nome do usuário que está associado à função |
| UserLastName               | nvarchar(255) | Sim         | O último nome do usuário que está associado à função |
| UserEmailAddress           | nvarchar(255) | Sim         | O endereço de email do usuário que está associado à função |

## <a name="vwconnectionuser"></a>vwConnectionUser

Esta exibição fornece detalhes sobre as conexões definidas no Azure Blockchain Workbench e os usuários associados a elas. Para cada conexão, essa exibição contém os seguintes dados:

-   Detalhes do razão associado
-   Informações do usuário associado

| Name                     | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| connectionId             | int           | Não          | O identificador exclusivo para uma conexão no Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Não          | A URL do ponto de extremidade para uma conexão |
| ConnectionFundingAccount | nvarchar(255) | Sim         | A conta de financiamento associada a uma conexão, se aplicável |
| LedgerId                 | int           | Não          | O identificador exclusivo de um razão |
| LedgerName               | nvarchar(50)  | Não          | O nome do razão |
| LedgerDisplayName        | nvarchar(255) | Não          | O nome do razão a ser exibido na interface do usuário |
| UserId                   | int           | Não          | A ID do usuário associado à conexão |
| Userexternalsourceid           | nvarchar(255) | Não          | O identificador externo para o usuário que está associado à conexão. Por padrão, essa ID é o usuário da Azure Active Directory para o consórcio. |
| UserProvisioningStatus   | int           | Não          |Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado |
| UserFirstName            | nvarchar(50)  | Sim         | O nome do usuário que está associado à conexão |
| UserLastName             | nvarchar(255) | Sim         | O último nome do usuário que está associado à conexão |
| UserEmailAddress         | nvarchar(255) | Sim         | O endereço de email do usuário que está associado à conexão |

## <a name="vwcontract"></a>vwContract

Esta exibição fornece detalhes sobre os contratos implantados. Para cada contrato, essa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Implementação do razão associada para a função
-   Detalhes do usuário que iniciou a ação
-   Detalhes relacionados ao bloco e à transação do blockchain

| Name                                     | Type           | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| connectionId                             | int            | Não          | O identificador exclusivo para uma conexão no Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Não          | A URL do ponto de extremidade para uma conexão |
| ConnectionFundingAccount                 | nvarchar(255)  | Sim         | A conta de financiamento associada a uma conexão, se aplicável |
| LedgerId                                 | int            | Não          | O identificador exclusivo de um razão |
| LedgerName                               | nvarchar(50)   | Não          | O nome do razão |
| LedgerDisplayName                        | nvarchar(255)  | Não          | O nome do razão a ser exibido na interface do usuário |
| ApplicationId                            | int            | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName                          | nvarchar (50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName                   | nvarchar (255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                       | bit            | Não          | Identifica se o aplicativo está habilitado no momento.<br /> **Nota:** Embora um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dado.  |
| WorkflowId                               | int            | Não          | Um identificador exclusivo para o fluxo de trabalho associado a um contrato |
| workflowName                             | nvarchar(50)   | Não          | O nome do fluxo de trabalho associado a um contrato |
| WorkflowDisplayName                      | nvarchar(255)  | Não          | O nome do fluxo de trabalho associado ao contrato exibido na interface do usuário |
| WorkflowDescription                      | nvarchar(255)  | Sim         | A descrição do fluxo de trabalho associado a um contrato |
| ContractCodeId                           | int            | Não          | Um identificador exclusivo para o código de contrato associado ao contrato |
| ContractFileName                         | int            | Não          | O nome do arquivo que contém o código do contrato inteligente para este fluxo de trabalho. |
| ContractUploadedDtTm                     | int            | Não          | A data e a hora em que o código do contrato foi carregado |
| contractId                               | int            | Não          | O identificador exclusivo do contrato |
| ContractProvisioningStatus               | int            | Não          | Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito no razão<br />3 ou 4-o contrato falhou ao ser implantado no razão<br />5-o contrato foi implantado com êxito no razão <br /><br />A partir da versão 1,5, há suporte para os valores de 0 a 5. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractV0** está disponível e só dá suporte a valores de 0 a 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | O endereço de email do usuário que implantou o contrato |
| ContractDeployedByUserId                 | int            | Não          | Um identificador externo para o usuário que implantou o contrato. Por padrão, essa ID é o GUID que representa a ID de Azure Active Directory para o usuário.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Não          | Um identificador externo para o usuário que implantou o contrato. Por padrão, essa ID é o GUID que representa a ID de Azure Active Directory para o usuário.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Não          | Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi associada ao usuário no banco de dados <br />2 – o usuário está totalmente provisionado                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Sim         | O nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName           | nvarchar(255)  | Sim         | O último nome do usuário que implantou o contrato |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Sim         | O endereço de email do usuário que implantou o contrato |

## <a name="vwcontractaction"></a>vwContractAction

Essa exibição representa a maioria das informações relacionadas às ações executadas em contratos e é projetada para facilitar prontamente cenários comuns de relatórios. Para cada ação executada, essa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Definição de parâmetro e função de contrato inteligente associada
-   Implementação do razão associada para a função
-   Valores de instância específicos fornecidos para parâmetros
-   Detalhes do usuário que iniciou a ação
-   Detalhes relacionados ao bloco e à transação do blockchain

| Name                                     | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName                          | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName                   | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                       | bit           | Não          | Este campo identifica se o aplicativo está habilitado no momento. Observação – mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecerão no blockchain e os dados sobre esses contratos permanecerão no banco de dado.                                                  |
| WorkflowId                               | int           | Não          | Um identificador exclusivo para um fluxo de trabalho |
| workflowName                             | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                      | nvarchar(255) | Não          | O nome do fluxo de trabalho a ser exibido em uma interface do usuário |
| WorkflowDescription                      | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| contractId                               | int           | Não          | Um identificador exclusivo para o contrato |
| ContractProvisioningStatus               | int           | Não          | Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito no razão<br />3 ou 4-o contrato falhou ao ser implantado no razão<br />5-o contrato foi implantado com êxito no razão <br /><br />A partir da versão 1,5, há suporte para os valores de 0 a 5. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractActionV0** está disponível e só dá suporte a valores de 0 a 2. |
| ContractCodeId                           | int           | Não          | Um identificador exclusivo para a implementação do código do contrato |
| ContractLedgerIdentifier                 | nvarchar(255) | Sim         | Um identificador exclusivo associado à versão implantada de um contrato inteligente para um razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId                 | int           | Não          | O identificador exclusivo do usuário que implantou o contrato |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Sim         | Nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName           | nvarchar(255) | Sim         | Sobrenome do usuário que implantou o contrato |
| ContractDeployedByUserExternalId         | nvarchar(255) | Não          | Identificador externo do usuário que implantou o contrato. Por padrão, essa ID é o GUID que representa sua identidade no Azure Active Directory do consórcio.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Sim         | O endereço de email do usuário que implantou o contrato |
| WorkflowFunctionId                       | int           | Não          | Um identificador exclusivo para uma função de fluxo de trabalho |
| WorkflowFunctionName                     | nvarchar(50)  | Não          | O nome da função |
| WorkflowFunctionDisplayName              | nvarchar(255) | Não          | O nome de uma função a ser exibida na interface do usuário |
| WorkflowFunctionDescription              | nvarchar(255) | Não          | A descrição da função |
| ContractActionId                         | int           | Não          | O identificador exclusivo para uma ação de contrato |
| ContractActionProvisioningStatus         | int           | Não          | Identifica o status atual do processo de provisionamento para a ação de contrato. Os valores possíveis são: <br />0 – a ação de contrato foi criada pela API no banco de dados<br />1 – a ação de contrato foi enviada para o razão<br />2 – a ação de contrato foi implantada com êxito no razão<br />3 ou 4-o contrato falhou ao ser implantado no razão<br />5-o contrato foi implantado com êxito no razão <br /><br />A partir da versão 1,5, há suporte para os valores de 0 a 5. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractActionV0** está disponível e só dá suporte a valores de 0 a 2. |
| ContractActionTimestamp                  | DateTime (2, 7) | Não          | O carimbo de data/hora da ação do contrato |
| ContractActionExecutedByUserId           | int           | Não          | Identificador exclusivo do usuário que executou a ação de contrato |
| ContractActionExecutedByUserFirstName    | int           | Sim         | Nome do usuário que executou a ação de contrato |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Sim         | Sobrenome do usuário que executou a ação de contrato |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Sim         | Identificador externo do usuário que executou a ação de contrato. Por padrão, essa ID é o GUID que representa sua identidade no Azure Active Directory do consórcio. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de email do usuário que executou a ação de contrato |
| WorkflowFunctionParameterId              | int           | Não          | Um identificador exclusivo para um parâmetro da função |
| WorkflowFunctionParameterName            | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do usuário |
| WorkflowFunctionParameterDataTypeId      | int           | Não          | O identificador exclusivo para o tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| ContractActionParameterValue             | nvarchar(255) | Não          | O valor do parâmetro armazenado no contrato inteligente |
| blockHash                                | nvarchar(255) | Sim         | O hash do bloco |
| blockNumber                              | int           | Sim         | O número do bloco no razão |
| blockTimestamp                           | DateTime (2, 7) | Sim         | O carimbo de data/hora do bloco |
| TransactionId                            | int           | Não          | Um identificador exclusivo para a transação |
| TransactionFrom                          | nvarchar(255) | Sim         | A parte que originou a transação |
| TransactionTo                            | nvarchar(255) | Sim         | A parte que foi transacionada com |
| transactionHash                          | nvarchar(255) | Sim         | O hash de uma transação |
| TransactionIsWorkbenchTransaction        | bit           | Sim         | Um bit que identifica se a transação é uma transação do Azure Blockchain Workbench |
| TransactionProvisioningStatus            | int           | Sim         | Identifica o status atual do processo de provisionamento para a transação. Os valores possíveis são: <br />0 – a transação foi criada pela API no banco de dados<br />1 – a transação foi enviada para o razão<br />2 – a transação foi implantada com êxito no razão                 |
| Transação                         | decimal (32, 2) | Sim         | O valor da transação |

## <a name="vwcontractproperty"></a>vwContractProperty

Essa exibição representa a maioria das informações relacionadas às propriedades associadas a um contrato e é projetada para facilitar prontamente cenários comuns de relatórios. Para cada propriedade obtida, essa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Detalhes do usuário que implantou o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associada
-   Valores de instância específicos para propriedades
-   Detalhes da propriedade de estado do contrato

| Name                               | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName                    | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName             | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                 | bit           | Não          | Identifica se o aplicativo está habilitado no momento.<br />**Nota:** Embora um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dado.                      |
| WorkflowId                         | int           | Não          | O identificador exclusivo do fluxo de trabalho |
| workflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome do fluxo de trabalho exibido na interface do usuário |
| WorkflowDescription                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| contractId                         | int           | Não          | O identificador exclusivo do contrato |
| ContractProvisioningStatus         | int           | Não          | Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito no razão<br />3 ou 4-o contrato falhou ao ser implantado no razão<br />5-o contrato foi implantado com êxito no razão <br /><br />A partir da versão 1,5, há suporte para os valores de 0 a 5. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractPropertyV0** está disponível e só dá suporte a valores de 0 a 2. |
| ContractCodeId                     | int           | Não          | Um identificador exclusivo para a implementação do código do contrato |
| ContractLedgerIdentifier           | nvarchar(255) | Sim         | Um identificador exclusivo associado à versão implantada de um contrato inteligente para um razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId           | int           | Não          | O identificador exclusivo do usuário que implantou o contrato |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Sobrenome do usuário que implantou o contrato |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do usuário que implantou o contrato. Por padrão, essa ID é o GUID que representa sua identidade no consórcio Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de email do usuário que implantou o contrato |
| workflowPropertyId                 | int           |             | Um identificador exclusivo para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | A ID do tipo de dados da propriedade |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados da propriedade |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade de fluxo de trabalho |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome de exibição da propriedade de fluxo de trabalho |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | Uma descrição da propriedade |
| ContractPropertyValue              | nvarchar(255) | Não          | O valor de uma propriedade no contrato |
| StateName                          | nvarchar(50)  | Sim         | Se essa propriedade contiver o estado do contrato, será o nome de exibição do estado. Se não estiver associado ao estado, o valor será NULL. |
| StateDisplayName                   | nvarchar(255) | Não          | Se essa propriedade contiver o estado, será o nome de exibição do estado. Se não estiver associado ao estado, o valor será NULL. |
| Estadovalue                         | nvarchar(255) | Sim         | Se essa propriedade contiver o estado, será o valor de estado. Se não estiver associado ao estado, o valor será NULL. |

## <a name="vwcontractstate"></a>vwContractState

Essa exibição representa a maioria das informações relacionadas ao estado de um contrato específico e é projetada para facilitar prontamente cenários comuns de relatórios. Cada registro nessa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Detalhes do usuário que implantou o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associada
-   Detalhes da propriedade de estado do contrato

| Name                               | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName                    | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName             | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                 | bit           | Não          | Identifica se o aplicativo está habilitado no momento.<br />**Nota:** Embora um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dado. |
| WorkflowId                         | int           | Não          | Um identificador exclusivo para o fluxo de trabalho |
| workflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome exibido na interface do usuário |
| WorkflowDescription                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractLedgerImplementationId     | nvarchar(255) | Sim         | Um identificador exclusivo associado à versão implantada de um contrato inteligente para um razão distribuído específico. Por exemplo, Ethereum. |
| contractId                         | int           | Não          | Um identificador exclusivo para o contrato |
| ContractProvisioningStatus         | int           | Não          |Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito no razão<br />3 ou 4-o contrato falhou ao ser implantado no razão<br />5-o contrato foi implantado com êxito no razão <br /><br />A partir da versão 1,5, há suporte para os valores de 0 a 5. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractStateV0** está disponível e só dá suporte a valores de 0 a 2. |
| connectionId                       | int           | Não          | Um identificador exclusivo para a instância de blockchain em que o fluxo de trabalho é implantado |
| ContractCodeId                     | int           | Não          | Um identificador exclusivo para a implementação do código do contrato |
| ContractDeployedByUserId           | int           | Não          | Identificador exclusivo do usuário que implantou o contrato |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do usuário que implantou o contrato. Por padrão, essa ID é o GUID que representa sua identidade no Azure Active Directory do consórcio. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Sobrenome do usuário que implantou o contrato |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de email do usuário que implantou o contrato |
| workflowPropertyId                 | int           | Não          | Um identificador exclusivo para uma propriedade de fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | A ID do tipo de dados da propriedade de fluxo de trabalho |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados da propriedade de fluxo de trabalho |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade de fluxo de trabalho |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome de exibição da propriedade a ser mostrada em uma interface do usuário |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | A descrição da propriedade |
| ContractPropertyValue              | nvarchar(255) | Não          | O valor de uma propriedade armazenada no contrato |
| StateName                          | nvarchar(50)  | Sim         | Se essa propriedade contiver o estado, ela será o nome de exibição do estado. Se não estiver associado ao estado, o valor será NULL. |
| StateDisplayName                   | nvarchar(255) | Não          | Se essa propriedade contiver o estado, será o nome de exibição do estado. Se não estiver associado ao estado, o valor será NULL. |
| Estadovalue                         | nvarchar(255) | Sim         | Se essa propriedade contiver o estado, será o valor de estado. Se não estiver associado ao estado, o valor será NULL. |

## <a name="vwuser"></a>vwUser

Esta exibição fornece detalhes sobre os membros do Consortium que são provisionados para usar o Azure Blockchain Workbench. Por padrão, os dados são preenchidos por meio do provisionamento inicial do usuário.

| Name               | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                 | int           | Não          | Um identificador exclusivo para um usuário |
| externalID         | nvarchar(255) | Não          | Um identificador externo para um usuário. Por padrão, essa ID é o GUID que representa a ID de Azure Active Directory para o usuário. |
| provisioningStatus | int           | Não          |Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado |
| FirstName          | nvarchar(50)  | Sim         | O nome do usuário |
| LastName           | nvarchar(50)  | Sim         | O último nome do usuário |
| EmailAddress       | nvarchar(255) | Sim         | O endereço de email do usuário |

## <a name="vwworkflow"></a>vwWorkflow

Essa exibição representa os detalhes principais metadados do fluxo de trabalho, bem como as funções e os parâmetros do fluxo de trabalho. Projetado para emissão de relatórios, ele também contém metadados sobre o aplicativo associado ao fluxo de trabalho. Esta exibição contém dados de várias tabelas subjacentes para facilitar o relatório de fluxos de trabalho. Para cada fluxo de trabalho, essa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Informações do estado de início do fluxo de trabalho associado

| Name                              | Type          | Pode ser nulo | Descrição                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName                   | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName            | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                | bit           | Não          | Identifica se o aplicativo está habilitado |
| WorkflowId                        | int           | Sim         | Um identificador exclusivo para um fluxo de trabalho |
| workflowName                      | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName               | nvarchar(255) | Não          | O nome exibido na interface do usuário |
| WorkflowDescription               | nvarchar(255) | Sim         | A descrição do fluxo de trabalho. |
| WorkflowConstructorFunctionId     | int           | Não          | O identificador da função de fluxo de trabalho que serve como o construtor para o fluxo de trabalho |
| WorkflowStartStateId              | int           | Não          | Um identificador exclusivo para o estado |
| WorkflowStartStateName            | nvarchar(50)  | Não          | O nome do estado |
| WorkflowStartStateDisplayName     | nvarchar(255) | Não          | O nome a ser exibido na interface do usuário para o estado |
| WorkflowStartStateDescription     | nvarchar(255) | Sim         | Uma descrição do estado do fluxo de trabalho |
| WorkflowStartStateStyle           | nvarchar(50)  | Sim         | Esse valor identifica a porcentagem concluída em que o fluxo de trabalho é quando está nesse estado |
| WorkflowStartStateValue           | int           | Não          | O valor do estado |
| WorkflowStartStatePercentComplete | int           | Não          | Uma descrição de texto que fornece uma dica aos clientes sobre como renderizar esse estado na interface do usuário. Os Estados com suporte incluem *êxito* e *falha* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Essa exibição representa os detalhes principais metadados do fluxo de trabalho, bem como as funções e os parâmetros do fluxo de trabalho. Projetado para emissão de relatórios, ele também contém metadados sobre o aplicativo associado ao fluxo de trabalho. Esta exibição contém dados de várias tabelas subjacentes para facilitar o relatório de fluxos de trabalho. Para cada função de fluxo de trabalho, essa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Detalhes da função de fluxo de trabalho

| Name                                 | Type          | Pode ser nulo | Descrição                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName                      | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName               | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                   | bit           | Não          | Identifica se o aplicativo está habilitado |
| WorkflowId                           | int           | Não          | Um identificador exclusivo para um fluxo de trabalho |
| workflowName                         | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                  | nvarchar(255) | Não          | O nome do fluxo de trabalho exibido na interface do usuário |
| WorkflowDescription                  | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowFunctionId                   | int           | Não          | Um identificador exclusivo para uma função |
| WorkflowFunctionName                 | nvarchar(50)  | Sim         | O nome da função |
| WorkflowFunctionDisplayName          | nvarchar(255) | Não          | O nome de uma função a ser exibida na interface do usuário |
| WorkflowFunctionDescription          | nvarchar(255) | Sim         | A descrição da função de fluxo de trabalho |
| WorkflowFunctionIsConstructor        | bit           | Não          | Identifica se a função de fluxo de trabalho é o construtor do fluxo de trabalho |
| WorkflowFunctionParameterId          | int           | Não          | Um identificador exclusivo para um parâmetro de uma função |
| WorkflowFunctionParameterName        | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do usuário |
| WorkflowFunctionParameterDataTypeId  | int           | Não          | Um identificador exclusivo para o tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Essa exibição representa as propriedades definidas para um fluxo de trabalho. Para cada propriedade, essa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Detalhes da propriedade de fluxo de trabalho

| Name                         | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName              | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName       | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled           | bit           | Não          | Identifica se o aplicativo está habilitado no momento.<br />**Nota:** Embora um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dado. |
| WorkflowId                   | int           | Não          | Um identificador exclusivo para o fluxo de trabalho |
| workflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome a ser exibido para o fluxo de trabalho em uma interface do usuário |
| WorkflowDescription          | nvarchar(255) | Sim         | Uma descrição do fluxo de trabalho |
| WorkflowPropertyID           | int           | Não          | Um identificador exclusivo para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyName         | nvarchar(50)  | Não          | O nome da propriedade |
| WorkflowPropertyDescription  | nvarchar(255) | Sim         | Uma descrição da propriedade |
| WorkflowPropertyDisplayName  | nvarchar(255) | Não          | O nome a ser exibido em uma interface do usuário |
| WorkflowPropertyWorkflowId   | int           | Não          | A ID do fluxo de trabalho ao qual esta propriedade está associada |
| WorkflowPropertyDataTypeId   | int           | Não          | A ID do tipo de dados definido para a propriedade |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Não          | O nome do tipo de dados definido para a propriedade |
| WorkflowPropertyIsState      | bit           | Não          | Este campo identifica se essa propriedade de fluxo de trabalho contém o estado do fluxo de trabalho |

## <a name="vwworkflowstate"></a>vwWorkflowState

Essa exibição representa as propriedades associadas a um fluxo de trabalho. Para cada contrato, essa exibição contém os seguintes dados:

-   Definição de aplicativo associada
-   Definição de fluxo de trabalho associada
-   Informações de estado do fluxo de trabalho

| Name                         | Type          | Pode ser nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Não          | Um identificador exclusivo para o aplicativo |
| ApplicationName              | nvarchar(50)  | Não          | O nome do aplicativo |
| ApplicationDisplayName       | nvarchar(255) | Não          | Uma descrição do aplicativo |
| ApplicationEnabled           | bit           | Não          | Identifica se o aplicativo está habilitado no momento.<br />**Nota:** Embora um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dado. |
| WorkflowId                   | int           | Não          | O identificador exclusivo do fluxo de trabalho |
| workflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome exibido na interface do usuário para o fluxo de trabalho |
| WorkflowDescription          | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowStateID              | int           | Não          | O identificador exclusivo para o estado |
| WorkflowStateName            | nvarchar(50)  | Não          | O nome do estado |
| WorkflowStateDisplayName     | nvarchar(255) | Não          | O nome a ser exibido na interface do usuário para o estado |
| WorkflowStateDescription     | nvarchar(255) | Sim         | Uma descrição do estado do fluxo de trabalho |
| WorkflowStatePercentComplete | int           | Não          | Esse valor identifica a porcentagem concluída em que o fluxo de trabalho é quando está nesse estado |
| WorkflowStateValue           | nvarchar(50)  | Não          | Valor do estado |
| WorkflowStateStyle           | nvarchar(50)  | Não          | Uma descrição de texto que fornece uma dica aos clientes sobre como renderizar esse estado na interface do usuário. Os Estados com suporte incluem *êxito* e *falha* |
