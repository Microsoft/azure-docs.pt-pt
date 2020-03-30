---
title: Vistas da base de dados da bancada de trabalho Azure Blockchain
description: Visão geral das vistas disponíveis da base de dados Azure Blockchain Workbench Preview SQL DB.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325977"
---
# <a name="azure-blockchain-workbench-database-views"></a>Vistas da base de dados da bancada de trabalho Azure Blockchain

A Pré-visualização da bancada azure Blockchain fornece dados de livros distribuídos para uma base de dados SQL DB *fora da cadeia.* A base de dados off-chain permite a utilização de SQL e ferramentas existentes, como o [SQL Server Management Studio,](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)para interagir com dados blockchain.

A Azure Blockchain Workbench fornece um conjunto de visualizações de bases de dados que fornecem acesso a dados que serão úteis na execução das suas consultas. Estas vistas são fortemente desnormalizadas para facilitar o início rápido de relatórios de construção, análises e, de outro modo, consumir dados blockchain com ferramentas existentes e sem ter que reconverter pessoal da base de dados.

Esta secção inclui uma visão geral das vistas da base de dados e dos dados que contêm.

> [!NOTE]
> Qualquer utilização direta de tabelas de bases de dados encontradas na base de dados fora destes pontos de vista, embora possível, não é suportada.
>

## <a name="vwapplication"></a>vwApplication

Esta visão fornece detalhes sobre **aplicações** que foram enviadas para a bancada de trabalho Azure Blockchain.

| Nome                             | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                    | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                  | nvarchar(50)  | Não          | O nome da aplicação |
| Descrição da aplicação           | nvarchar(255) | Sim         | Uma descrição do pedido |
| Nome de exibição de aplicações           | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada               | bit           | Não          | Identifica se a aplicação está ativada atualmente<br /> **Nota:** Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| DtTTm carregado                     | data2(7)  | Não          | A data e hora em que um contrato foi carregado |
| UploadedByUserId                 | int           | Não          | A identificação do utilizador que fez o upload da aplicação |
| UploadedByUserExternalId         | nvarchar(255) | Não          | O identificador externo para o utilizador que fez o upload da aplicação. Por predefinição, este ID é o utilizador do Diretório Ativo Azure para o consórcio.                                                                                                |
| Estatuto de Provisionamento de Utilizadores enviados | int           | Não          | Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 – Foi associada uma chave ao utilizador na base de dados<br />2 – O utilizador está totalmente provisionado                         |
| UploadedByUserFirstName          | nvarchar(50)  | Sim         | O primeiro nome do utilizador que fez o upload do contrato |
| UploadbyByUserLastName           | nvarchar(50)  | Sim         | O último nome do utilizador que fez o upload do contrato |
| UploadbyUserEmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que fez o upload do contrato |

## <a name="vwapplicationrole"></a>vwApplicationRole

Esta visão fornece detalhes sobre as funções que foram definidas nas aplicações da bancada azure Blockchain.

Numa aplicação *de Transferência de Ativos,* por exemplo, podem ser definidas funções como *funções de Comprador* e *Vendedor.*

| Nome                   | Tipo             | Pode ser Nulo | Descrição                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationID          | int              | Não          | Um identificador único para a aplicação           |
| ApplicationName        | nvarchar(50)     | Não          | O nome da aplicação                       |
| Descrição da aplicação | nvarchar(255)    | Sim         | Uma descrição do pedido                  |
| Nome de exibição de aplicações | nvarchar(255)    | Não          | O nome a ser exibido numa interface de utilizador      |
| Roleid                 | int              | Não          | Um identificador único para um papel na aplicação |
| RoleName               | nvarchar50)      | Não          | O nome do papel                              |
| Descrição de papéis        | descrição(255) | Sim         | Uma descrição do papel                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Esta visão fornece detalhes sobre as funções que foram definidas nas aplicações da Bancada de Trabalho Azure Blockchain e nos utilizadores associados.

Numa aplicação de Transferência de *Ativos,* por exemplo, *John Smith* pode estar associado à função *Comprador.*

| Nome                       | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID              | int           | Não          | Um identificador único para a aplicação                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Não          | O nome da aplicação                                                                                                                                                                                                           |
| Descrição da aplicação     | nvarchar(255) | Sim         | Uma descrição do pedido                                                                                                                                                                                                      |
| Nome de exibição de aplicações     | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador                                                                                                                                                                                          |
| AplicaçãoRoleid          | int           | Não          | Um identificador único para um papel na aplicação                                                                                                                                                                                     |
| Nome de RoleName de Aplicação        | nvarchar50)   | Não          | O nome do papel                                                                                                                                                                                                                  |
| Descrição da roleta de aplicações | nvarchar(255) | Sim         | Uma descrição do papel                                                                                                                                                                                                             |
| IDUtilizador                     | int           | Não          | A identificação do utilizador associada ao papel |
| UserExternalId             | nvarchar(255) | Não          | O identificador externo para o utilizador que está associado à função. Por predefinição, este ID é o utilizador do Diretório Ativo Azure para o consórcio.                                                                     |
| Estatuto de Provisionamento de Utilizadores     | int           | Não          | Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 – Foi associada uma chave ao utilizador na base de dados<br />2 – O utilizador está totalmente provisionado |
| UserFirstName              | nvarchar(50)  | Sim         | O primeiro nome do utilizador que está associado ao papel |
| UserLastName               | nvarchar(255) | Sim         | O último nome do utilizador que está associado ao papel |
| Endereço de email do utilizador           | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que está associado ao papel |

## <a name="vwconnectionuser"></a>vwConnectionUser

Esta visão fornece detalhes sobre as ligações definidas na Bancada de Trabalho Azure Blockchain e os utilizadores associados a elas. Para cada ligação, esta visão contém os seguintes dados:

-   Detalhes do livro-razão associados
-   Informações de utilizadores associados

| Nome                     | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ligação             | int           | Não          | O identificador único para uma ligação na bancada azure blockchain |
| ConnectionEndpointUrl    | nvarchar(50)  | Não          | A url final point para uma ligação |
| ConnectionFundingAccount | nvarchar(255) | Sim         | A conta de financiamento associada a uma ligação, se aplicável |
| Ledgerid                 | int           | Não          | O identificador único para um livro-razão |
| Nome de livro               | nvarchar(50)  | Não          | O nome do livro-razão |
| Nome de visualização de livros        | nvarchar(255) | Não          | O nome do livro-razão para exibir na UI |
| IDUtilizador                   | int           | Não          | A identificação do utilizador associada à ligação |
| UserExternalId           | nvarchar(255) | Não          | O identificador externo para o utilizador que esteja associado à ligação. Por predefinição, este ID é o utilizador do Diretório Ativo Azure para o consórcio. |
| Estatuto de Provisionamento de Utilizadores   | int           | Não          |Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 – Foi associada uma chave ao utilizador na base de dados<br />2 – O utilizador está totalmente provisionado |
| UserFirstName            | nvarchar(50)  | Sim         | O primeiro nome do utilizador que está associado à ligação |
| UserLastName             | nvarchar(255) | Sim         | O último nome do utilizador que está associado à ligação |
| Endereço de email do utilizador         | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que está associado à ligação |

## <a name="vwcontract"></a>vwContrato

Esta visão fornece detalhes sobre contratos implementados. Para cada contrato, esta visão contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Implementação do livro-razão associado para a função
-   Detalhes para o utilizador que iniciou a ação
-   Detalhes relacionados com o bloco blockchain e transação

| Nome                                     | Tipo           | Pode ser Nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ligação                             | int            | Não          | O identificador único para uma ligação na bancada azure blockchain.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Não          | A url final point para uma ligação |
| ConnectionFundingAccount                 | nvarchar(255)  | Sim         | A conta de financiamento associada a uma ligação, se aplicável |
| Ledgerid                                 | int            | Não          | O identificador único para um livro-razão |
| Nome de livro                               | nvarchar(50)   | Não          | O nome do livro-razão |
| Nome de visualização de livros                        | nvarchar(255)  | Não          | O nome do livro-razão para exibir na UI |
| ApplicationID                            | int            | Não          | Um identificador único para a aplicação |
| ApplicationName                          | nvarchar (50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações                   | nvarchar (255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada                       | bit            | Não          | Identifica se a aplicação está ativada.<br /> **Nota:** Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados.  |
| Fluxo de trabalhoId                               | int            | Não          | Um identificador único para o fluxo de trabalho associado a um contrato |
| WorkflowName                             | nvarchar(50)   | Não          | O nome do fluxo de trabalho associado a um contrato |
| WorkflowDisplayName                      | nvarchar(255)  | Não          | O nome do fluxo de trabalho associado ao contrato apresentado na interface do utilizador |
| Fluxo de TrabalhoDescrição                      | nvarchar(255)  | Sim         | A descrição do fluxo de trabalho associado a um contrato |
| Contractcodeid                           | int            | Não          | Um identificador único para o código contratual associado ao contrato |
| Nome de Ficheiro sintetiza                         | int            | Não          | O nome do ficheiro que contém o código de contrato inteligente para este fluxo de trabalho. |
| ContractUploadedDtTm                     | int            | Não          | A data e hora em que o código do contrato foi carregado |
| Contractid                               | int            | Não          | O identificador único para o contrato |
| Estatuto de Prestação de Contratos               | int            | Não          | Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro-razão<br />2 – O contrato foi implementado com sucesso para o livro de contabilidade<br />3 ou 4 - O contrato não foi implementado para o livro de contabilidade<br />5 - O contrato foi implementado com sucesso para o livro de contabilidade <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retroceder na versão atual, a visualização **vwContractV0** está disponível que apenas suporta os valores 0 a 2. |
| Identificador ContractLedger                 | nvarchar (255) |             | O endereço de e-mail do utilizador que implementou o contrato |
| ContractDeployedByUserId                 | int            | Não          | Um identificador externo para o utilizador que implementou o contrato. Por predefinição, este ID é o guia que representa o ID do Diretório Ativo Azure para o utilizador.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Não          | Um identificador externo para o utilizador que implementou o contrato. Por predefinição, este ID é o guia que representa o ID do Diretório Ativo Azure para o utilizador.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Não          | Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – o utilizador foi criado pela API<br />1 – Foi associada uma chave ao utilizador na base de dados <br />2 – O utilizador está totalmente provisionado                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Sim         | O primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName           | nvarchar(255)  | Sim         | O último nome do utilizador que implementou o contrato |
| ContratouByUserEmailAddress       | nvarchar(255)  | Sim         | O endereço de e-mail do utilizador que implementou o contrato |

## <a name="vwcontractaction"></a>vwContractAction

Esta opinião representa a maioria das informações relacionadas com as ações tomadas nos contratos e destina-se a facilitar facilmente cenários comuns de reporte. Para cada ação tomada, esta visão contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Função de contrato inteligente associado e definição de parâmetro
-   Implementação do livro-razão associado para a função
-   Valores de exemplo específicos previstos para parâmetros
-   Detalhes para o utilizador que iniciou a ação
-   Detalhes relacionados com o bloco blockchain e transação

| Nome                                     | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                            | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                          | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações                   | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada                       | bit           | Não          | Este campo identifica se a aplicação está ativada. Nota – Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados.                                                  |
| Fluxo de trabalhoId                               | int           | Não          | Um identificador único para um fluxo de trabalho |
| WorkflowName                             | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                      | nvarchar(255) | Não          | O nome do fluxo de trabalho para exibir numa interface de utilizador |
| Fluxo de TrabalhoDescrição                      | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| Contractid                               | int           | Não          | Um identificador único para o contrato |
| Estatuto de Prestação de Contratos               | int           | Não          | Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro-razão<br />2 – O contrato foi implementado com sucesso para o livro de contabilidade<br />3 ou 4 - O contrato não foi implementado para o livro de contabilidade<br />5 - O contrato foi implementado com sucesso para o livro de contabilidade <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retroceder na versão atual, a visualização **vwContractActionV0** está disponível que apenas suporta os valores 0 a 2. |
| Contractcodeid                           | int           | Não          | Um identificador único para a implementação do código do contrato |
| Identificador ContractLedger                 | nvarchar(255) | Sim         | Um identificador único associado à versão implementada de um contrato inteligente para um livro-razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId                 | int           | Não          | O identificador único do utilizador que implementou o contrato |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Sim         | Primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName           | nvarchar(255) | Sim         | Último nome do utilizador que implementou o contrato |
| ContractDeployedByUserExternalId         | nvarchar(255) | Não          | Identificador externo do utilizador que implementou o contrato. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Directory.                                                                                                                                                |
| ContratouByUserEmailAddress       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementou o contrato |
| WorkflowFunctionId                       | int           | Não          | Um identificador único para uma função de fluxo de trabalho |
| Nome função de fluxo de trabalho                     | nvarchar(50)  | Não          | O nome da função |
| WorkflowFunctionDisplayName              | nvarchar(255) | Não          | O nome de uma função a ser exibida na interface do utilizador |
| Função workflowDescrição              | nvarchar(255) | Não          | A descrição da função |
| ContractActionId                         | int           | Não          | O identificador único para uma ação contratual |
| ContractActionProvisioningStatus         | int           | Não          | Identifica o estado atual do processo de provisionamento para a ação contratual. Os valores possíveis são: <br />0 – A ação contratual foi criada pela API na base de dados<br />1 - A ação contratual foi enviada para o livro de contabilidade<br />2 – A ação contratual foi implementada com sucesso para o livro de contabilidade<br />3 ou 4 - O contrato não foi implementado para o livro de contabilidade<br />5 - O contrato foi implementado com sucesso para o livro de contabilidade <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retroceder na versão atual, a visualização **vwContractActionV0** está disponível que apenas suporta os valores 0 a 2. |
| ContractActionTimestamp                  | data(2,7) | Não          | O carimbo temporal da ação contratual |
| ContractActionExecutedByUserId           | int           | Não          | Identificador único do utilizador que executou a ação contratual |
| ContractActionExecutedByUserFirstName    | int           | Sim         | Primeiro nome do utilizador que executou a ação contratual |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Sim         | Apelido do utilizador que executou a ação contratual |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Sim         | Identificador externo do utilizador que executou a ação contratual. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Directory. |
| ContractActionExecuteedByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que executou a ação contratual |
| Função de circulaçãoParameterId              | int           | Não          | Um identificador único para um parâmetro da função |
| WorkflowFunctionMeterName            | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do utilizador |
| Função de fluxo de trabalhoParâmetroDataTypeId      | int           | Não          | O identificador único para o tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| ContractActionParameterValue             | nvarchar(255) | Não          | O valor para o parâmetro armazenado no contrato inteligente |
| BlockHash                                | nvarchar(255) | Sim         | O hash do quarteirão |
| Número de blocos                              | int           | Sim         | O número do bloco no livro-razão |
| Carimbo de Tempo de Bloqueio                           | data(2,7) | Sim         | O carimbo de tempo do bloco |
| TransacçãoId                            | int           | Não          | Um identificador único para a transação |
| Transações A partir de                          | nvarchar(255) | Sim         | A parte que originou a transação |
| Transações                            | nvarchar(255) | Sim         | O partido que foi transacionado com |
| TransacçõesHash                          | nvarchar(255) | Sim         | O hash de uma transação |
| TransacçõesIsWorkbenchTransaction        | bit           | Sim         | Um pouco que identifica se a transação é uma transação azure Blockchain Workbench |
| TransaçõesEstatuto            | int           | Sim         | Identifica o estado atual do processo de provisionamento para a operação. Os valores possíveis são: <br />0 – A transação foi criada pela API na base de dados<br />1 - A transação foi enviada para o livro-razão<br />2 – A transação foi implementada com sucesso para o livro de contabilidade                 |
| TransacçãoValor                         | decimal(32,2) | Sim         | O valor da transação |

## <a name="vwcontractproperty"></a>vwContractProperty

Esta visão representa a maioria das informações relacionadas com propriedades associadas a um contrato e destina-se a facilitar facilmente cenários comuns de reporte. Para cada imóvel tomado, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes para o utilizador que implementou o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associado
-   Valores específicos de exemplo para propriedades
-   Detalhes para a propriedade do Estado do contrato

| Nome                               | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                    | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações             | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada                 | bit           | Não          | Identifica se a aplicação está ativada.<br />**Nota:** Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados.                      |
| Fluxo de trabalhoId                         | int           | Não          | O identificador único para o fluxo de trabalho |
| WorkflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome do fluxo de trabalho apresentado na interface do utilizador |
| Fluxo de TrabalhoDescrição                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| Contractid                         | int           | Não          | O identificador único para o contrato |
| Estatuto de Prestação de Contratos         | int           | Não          | Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro-razão<br />2 – O contrato foi implementado com sucesso para o livro de contabilidade<br />3 ou 4 - O contrato não foi implementado para o livro de contabilidade<br />5 - O contrato foi implementado com sucesso para o livro de contabilidade <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retroceder na versão atual, a visualização **vwContractPropertyV0** está disponível que apenas suporta os valores 0 a 2. |
| Contractcodeid                     | int           | Não          | Um identificador único para a implementação do código do contrato |
| Identificador ContractLedger           | nvarchar(255) | Sim         | Um identificador único associado à versão implementada de um contrato inteligente para um livro-razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId           | int           | Não          | O identificador único do utilizador que implementou o contrato |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Último nome do utilizador que implementou o contrato |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do utilizador que implementou o contrato. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Diretório |
| ContratouByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementou o contrato |
| WorkflowPropertyId                 | int           |             | Um identificador único para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | A identificação do tipo de dados do imóvel |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados do imóvel |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade workflow |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome de exibição da propriedade workflow |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | Uma descrição da propriedade |
| ContractPropertyValue              | nvarchar(255) | Não          | O valor de um imóvel no contrato |
| Nome do Estado                          | nvarchar(50)  | Sim         | Se esta propriedade contém o estado do contrato, é o nome de exibição para o estado. Se não estiver associado ao Estado, o valor será nulo. |
| Nome de eexposição de Estado                   | nvarchar(255) | Não          | Se esta propriedade contém o estado, é o nome de exibição para o estado. Se não estiver associado ao Estado, o valor será nulo. |
| Valor do Estado                         | nvarchar(255) | Sim         | Se esta propriedade contém o estado, é o valor do Estado. Se não estiver associado ao Estado, o valor será nulo. |

## <a name="vwcontractstate"></a>vwContractState

Esta visão representa a maioria das informações relacionadas com o estado de um contrato específico e destina-se a facilitar facilmente cenários comuns de reporte. Cada registo, nesta opinião, contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes para o utilizador que implementou o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associado
-   Detalhes para a propriedade do Estado do contrato

| Nome                               | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                    | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações             | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada                 | bit           | Não          | Identifica se a aplicação está ativada.<br />**Nota:** Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| Fluxo de trabalhoId                         | int           | Não          | Um identificador único para o fluxo de trabalho |
| WorkflowName                       | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar(255) | Não          | O nome apresentado na interface do utilizador |
| Fluxo de TrabalhoDescrição                | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| ContractledgerImplementationId     | nvarchar(255) | Sim         | Um identificador único associado à versão implementada de um contrato inteligente para um livro-razão distribuído específico. Por exemplo, Ethereum. |
| Contractid                         | int           | Não          | Um identificador único para o contrato |
| Estatuto de Prestação de Contratos         | int           | Não          |Identifica o estado atual do processo de provisionamento do contrato. Os valores possíveis são: <br />0 – O contrato foi criado pela API na base de dados<br />1 - O contrato foi enviado para o livro-razão<br />2 – O contrato foi implementado com sucesso para o livro de contabilidade<br />3 ou 4 - O contrato não foi implementado para o livro de contabilidade<br />5 - O contrato foi implementado com sucesso para o livro de contabilidade <br /><br />Começando pela versão 1.5, os valores 0 a 5 são suportados. Para retroceder na versão atual, a visualização **vwContractStateV0** está disponível que apenas suporta os valores 0 a 2. |
| Ligação                       | int           | Não          | Um identificador único para a instância blockchain o fluxo de trabalho é implantado para |
| Contractcodeid                     | int           | Não          | Um identificador único para a implementação do código do contrato |
| ContractDeployedByUserId           | int           | Não          | Identificador único do utilizador que implementou o contrato |
| ContractDeployedByUserExternalId   | nvarchar(255) | Não          | Identificador externo do utilizador que implementou o contrato. Por padrão, este ID é o guia que representa a sua identidade no consórcio Azure Ative Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sim         | Primeiro nome do utilizador que implementou o contrato |
| ContractDeployedByUserLastName     | nvarchar(255) | Sim         | Último nome do utilizador que implementou o contrato |
| ContratouByUserEmailAddress | nvarchar(255) | Sim         | O endereço de e-mail do utilizador que implementou o contrato |
| WorkflowPropertyId                 | int           | Não          | Um identificador único para uma propriedade de fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não          | A identificação do tipo de dados da propriedade workflow |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Não          | O nome do tipo de dados da propriedade workflow |
| WorkflowPropertyName               | nvarchar(50)  | Não          | O nome da propriedade workflow |
| WorkflowPropertyDisplayName        | nvarchar(255) | Não          | O nome de exibição da propriedade para mostrar em um UI |
| WorkflowPropertyDescription        | nvarchar(255) | Sim         | A descrição do imóvel |
| ContractPropertyValue              | nvarchar(255) | Não          | O valor para um imóvel armazenado no contrato |
| Nome do Estado                          | nvarchar(50)  | Sim         | Se esta propriedade contém o estado, é o nome de exibição para o estado. Se não estiver associado ao Estado, o valor será nulo. |
| Nome de eexposição de Estado                   | nvarchar(255) | Não          | Se esta propriedade contém o estado, é o nome de exibição para o estado. Se não estiver associado ao Estado, o valor será nulo. |
| Valor do Estado                         | nvarchar(255) | Sim         | Se esta propriedade contém o estado, é o valor do Estado. Se não estiver associado ao Estado, o valor será nulo. |

## <a name="vwuser"></a>vwUser

Esta visão fornece detalhes sobre os membros do consórcio que estão dispostos a usar a bancada de trabalho Azure Blockchain. Por predefinição, os dados são povoados através do fornecimento inicial do utilizador.

| Nome               | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Não          | Um identificador único para um utilizador |
| Id externo         | nvarchar(255) | Não          | Um identificador externo para um utilizador. Por predefinição, este ID é o guia que representa o ID do Diretório Ativo Azure para o utilizador. |
| Estatuto de Provisionamento | int           | Não          |Identifica o estado atual do processo de provisionamento para o utilizador. Os valores possíveis são: <br />0 – O utilizador foi criado pela API<br />1 – Foi associada uma chave ao utilizador na base de dados<br />2 – O utilizador está totalmente provisionado |
| FirstName          | nvarchar(50)  | Sim         | O primeiro nome do utilizador |
| LastName           | nvarchar(50)  | Sim         | O último nome do utilizador |
| Endereço de e-mail       | nvarchar(255) | Sim         | O endereço de e-mail do utilizador |

## <a name="vwworkflow"></a>vwWorkflow

Esta visão representa os detalhes dos metadados de fluxo de trabalho, bem como as funções e parâmetros do fluxo de trabalho. Projetado para reportar, também contém metadados sobre a aplicação associada ao fluxo de trabalho. Esta visão contém dados de várias tabelas subjacentes para facilitar a comunicação sobre fluxos de trabalho. Para cada fluxo de trabalho, esta visão contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Fluxo de trabalho associado inicia mainformações estatais

| Nome                              | Tipo          | Pode ser Nulo | Descrição                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                     | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                   | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações            | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada                | bit           | Não          | Identifica se a aplicação está ativada |
| Fluxo de trabalhoId                        | int           | Sim         | Um identificador único para um fluxo de trabalho |
| WorkflowName                      | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName               | nvarchar(255) | Não          | O nome apresentado na interface do utilizador |
| Fluxo de TrabalhoDescrição               | nvarchar(255) | Sim         | A descrição do fluxo de trabalho. |
| WorkflowConstructorFunctionId     | int           | Não          | O identificador da função de fluxo de trabalho que serve de construtor para o fluxo de trabalho |
| Fluxo de TrabalhoStartStateId              | int           | Não          | Um identificador único para o Estado |
| WorkflowStartStateName            | nvarchar(50)  | Não          | O nome do estado |
| WorkflowStartStateDisplayName     | nvarchar(255) | Não          | O nome a ser exibido na interface do utilizador para o estado |
| WorkflowStartStateDescription     | nvarchar(255) | Sim         | Uma descrição do estado do fluxo de trabalho |
| WorkflowStartStateStyle           | nvarchar(50)  | Sim         | Este valor identifica a percentagem completa que o fluxo de trabalho é quando neste estado |
| Fluxo de TrabalhoStartStateValue           | int           | Não          | O valor do Estado |
| Fluxo de trabalhoStartStatePercentComplete | int           | Não          | Uma descrição de texto que fornece uma dica aos clientes sobre como tornar este estado na UI. Estados apoiados incluem *Sucesso* e *Fracasso* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Esta visão representa os detalhes dos metadados de fluxo de trabalho, bem como as funções e parâmetros do fluxo de trabalho. Projetado para reportar, também contém metadados sobre a aplicação associada ao fluxo de trabalho. Esta visão contém dados de várias tabelas subjacentes para facilitar a comunicação sobre fluxos de trabalho. Para cada função de fluxo de trabalho, esta visão contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes da função workflow

| Nome                                 | Tipo          | Pode ser Nulo | Descrição                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationID                        | int           | Não          | Um identificador único para a aplicação |
| ApplicationName                      | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações               | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada                   | bit           | Não          | Identifica se a aplicação está ativada |
| Fluxo de trabalhoId                           | int           | Não          | Um identificador único para um fluxo de trabalho |
| WorkflowName                         | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName                  | nvarchar(255) | Não          | O nome do fluxo de trabalho apresentado na interface do utilizador |
| Fluxo de TrabalhoDescrição                  | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowFunctionId                   | int           | Não          | Um identificador único para uma função |
| Nome função de fluxo de trabalho                 | nvarchar(50)  | Sim         | O nome da função |
| WorkflowFunctionDisplayName          | nvarchar(255) | Não          | O nome de uma função a ser exibida na interface do utilizador |
| Função workflowDescrição          | nvarchar(255) | Sim         | A descrição da função de fluxo de trabalho |
| WorkflowFunctionIsConstructor        | bit           | Não          | Identifica se a função de fluxo de trabalho é o construtor para o fluxo de trabalho |
| Função de circulaçãoParameterId          | int           | Não          | Um identificador único para um parâmetro de uma função |
| WorkflowFunctionMeterName        | nvarchar(50)  | Não          | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Não          | O nome de um parâmetro de função a ser exibido na interface do utilizador |
| Função de fluxo de trabalhoParâmetroDataTypeId  | int           | Não          | Um identificador único para o tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Não          | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Esta vista representa as propriedades definidas para um fluxo de trabalho. Para cada imóvel, esta vista contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Detalhes da propriedade workflow

| Nome                         | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | Não          | Um identificador único para a aplicação |
| ApplicationName              | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações       | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| Aplicação Habilitada           | bit           | Não          | Identifica se a aplicação está ativada.<br />**Nota:** Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| Fluxo de trabalhoId                   | int           | Não          | Um identificador único para o fluxo de trabalho |
| WorkflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome a ser apresentado para o fluxo de trabalho numa interface de utilizador |
| Fluxo de TrabalhoDescrição          | nvarchar(255) | Sim         | Uma descrição do fluxo de trabalho |
| WorkflowPropertyID           | int           | Não          | Um identificador único para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyName         | nvarchar(50)  | Não          | O nome da propriedade |
| WorkflowPropertyDescription  | nvarchar(255) | Sim         | Uma descrição da propriedade |
| WorkflowPropertyDisplayName  | nvarchar(255) | Não          | O nome a ser exibido numa interface de utilizador |
| WorkflowPropertyWorkflowId   | int           | Não          | A identificação do fluxo de trabalho a que esta propriedade está associada |
| WorkflowPropertyDataTypeId   | int           | Não          | A identificação do tipo de dados definido para a propriedade |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Não          | O nome do tipo de dados definido para a propriedade |
| WorkflowPropertyIsState      | bit           | Não          | Este campo identifica se esta propriedade de fluxo de trabalho contém o estado do fluxo de trabalho |

## <a name="vwworkflowstate"></a>vwWorkflowState

Esta vista representa as propriedades associadas a um fluxo de trabalho. Para cada contrato, esta visão contém os seguintes dados:

-   Definição de aplicação associada
-   Definição de fluxo de trabalho associado
-   Informação do Estado do fluxo de trabalho

| Nome                         | Tipo          | Pode ser Nulo | Descrição                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | Não          | Um identificador único para a aplicação |
| ApplicationName              | nvarchar(50)  | Não          | O nome da aplicação |
| Nome de exibição de aplicações       | nvarchar(255) | Não          | Uma descrição do pedido |
| Aplicação Habilitada           | bit           | Não          | Identifica se a aplicação está ativada.<br />**Nota:** Embora uma aplicação possa ser refletida como desativada na base de dados, os contratos associados permanecem na blockchain e os dados sobre esses contratos permanecem na base de dados. |
| Fluxo de trabalhoId                   | int           | Não          | O identificador único para o fluxo de trabalho |
| WorkflowName                 | nvarchar(50)  | Não          | O nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar(255) | Não          | O nome apresentado na interface do utilizador para o fluxo de trabalho |
| Fluxo de TrabalhoDescrição          | nvarchar(255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowStateID              | int           | Não          | O identificador único para o Estado |
| WorkflowStateName            | nvarchar(50)  | Não          | O nome do estado |
| WorkflowStateDisplayName     | nvarchar(255) | Não          | O nome a ser exibido na interface do utilizador para o estado |
| WorkflowStateDescription     | nvarchar(255) | Sim         | Uma descrição do estado do fluxo de trabalho |
| Fluxo de trabalhoStatePercentComplete | int           | Não          | Este valor identifica a percentagem completa que o fluxo de trabalho é quando neste estado |
| WorkflowStateValue           | nvarchar(50)  | Não          | Valor do Estado |
| WorkflowStateStyle           | nvarchar(50)  | Não          | Uma descrição de texto que fornece uma dica aos clientes sobre como tornar este estado na UI. Estados apoiados incluem *Sucesso* e *Fracasso* |
