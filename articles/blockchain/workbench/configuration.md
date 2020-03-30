---
title: Referência de metadados de configuração da bancada de trabalho Azure Blockchain
description: Visão geral da configuração de metadados de configuração da aplicação de pré-visualização da bancada Azure Blockchain.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 661e795f0e85f872b1072a8f641b8938115c5d7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252197"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referência de configuração da bancada de trabalho Azure Blockchain

As aplicações azure Blockchain Workbench são fluxos de trabalho multi-partidodefinidos por metadados de configuração e código de contrato inteligente. Os metadados de configuração definem os fluxos de trabalho de alto nível e o modelo de interação da aplicação blockchain. Os contratos inteligentes definem a lógica de negócio da aplicação blockchain. A Workbench utiliza a configuração e o código de contrato inteligente para gerar experiências de utilizador de aplicações blockchain.

Os metadados de configuração especificam as seguintes informações para cada aplicação blockchain:

* Nome e descrição da aplicação blockchain
* Funções únicas para utilizadores que possam atuar ou participar dentro da aplicação blockchain
* Um ou mais fluxos de trabalho. Cada fluxo de trabalho funciona como uma máquina estatal para controlar o fluxo da lógica empresarial. Os fluxos de trabalho podem ser independentes ou interagir uns com os outros.

Cada fluxo de trabalho definido especifica o seguinte:

* Nome e descrição do fluxo de trabalho
* Estados do fluxo de trabalho.  Cada estado é uma etapa no fluxo de controlo da lógica empresarial. 
* Ações de transição para o próximo Estado
* Funções de utilizador autorizadas a iniciar cada ação
* Contratos inteligentes que representam a lógica do negócio em ficheiros de código

## <a name="application"></a>Aplicação

Uma aplicação blockchain contém metadados de configuração, fluxos de trabalho e funções de utilizador que podem atuar ou participar dentro da aplicação.

| Campo | Descrição | Necessário |
|-------|-------------|:--------:|
| ApplicationName | Nome único da aplicação. O contrato inteligente correspondente deve utilizar o mesmo Nome de **Aplicação** para a classe contratual aplicável.  | Sim |
| DisplayName | Nome de exibição amigável da aplicação. | Sim |
| Descrição | Descrição do pedido. | Não |
| AplicaçõesRoles | Coleção de Papéis de [Aplicações](#application-roles). Funções de utilizador que possam atuar ou participar dentro da aplicação.  | Sim |
| Fluxos de trabalho | Coleção de Fluxos de [Trabalho.](#workflows) Cada fluxo de trabalho funciona como uma máquina estatal para controlar o fluxo da lógica empresarial. | Sim |

Por exemplo, consulte o exemplo do [ficheiro de configuração](#configuration-file-example).

## <a name="workflows"></a>Fluxos de trabalho

A lógica empresarial de uma aplicação pode ser modelada como uma máquina estatal onde tomar uma ação faz com que o fluxo da lógica empresarial se mova de um Estado para outro. Um fluxo de trabalho é uma coleção de tais estados e ações. Cada fluxo de trabalho consiste em um ou mais contratos inteligentes, que representam a lógica do negócio em ficheiros de código. Um contrato executável é um exemplo de fluxo de trabalho.

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Nome | Nome único do fluxo de trabalho. O contrato inteligente correspondente deve utilizar o mesmo **nome** para a classe contratual aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável do fluxo de trabalho. | Sim | 255 |
| Descrição | Descrição do fluxo de trabalho. | Não | 255 |
| Iniciadores | Coleção de Papéis de [Aplicações](#application-roles). Funções que são atribuídas a utilizadores autorizados a criar contratos no fluxo de trabalho. | Sim | |
| Início do Estado | Nome do estado inicial do fluxo de trabalho. | Sim | |
| Propriedades | Recolha de [identificadores.](#identifiers) Representa dados que podem ser lidos fora da cadeia ou visualizados numa ferramenta de experiência do utilizador. | Sim | |
| Construtor | Define os parâmetros de entrada para criar uma instância do fluxo de trabalho. | Sim | |
| Funções | Uma coleção de funções que podem ser [executadas](#functions) no fluxo de trabalho. | Sim | |
| Estados | Uma coleção de [estados](#states)de fluxo de trabalho. | Sim | |

Por exemplo, consulte o exemplo do [ficheiro de configuração](#configuration-file-example).

## <a name="type"></a>Tipo

Tipos de dados suportados.

| Tipo | Descrição |
|-------|-------------|
| address  | Tipo de endereço blockchain, como *contratos* ou *utilizadores.* |
| array    | Um único nível de inteiro, bool, dinheiro ou tempo. As matrizes podem ser estáticas ou dinâmicas. Utilize o **ElementType** para especificar o tipo de dados dos elementos dentro da matriz. Ver [configuração](#example-configuration-of-type-array)de exemplo . |
| bool     | Tipo de dados booleanos. |
| contrato | Endereço de contrato tipo. |
| enum     | Conjunto enumerado de valores nomeados. Ao utilizar o tipo enum, também especifica uma lista de EnumValues. Cada valor está limitado a 255 caracteres. Os caracteres de valor válido incluem letras maiúsculas e minúsculas (A-Z, a-z) e números (0-9). Consulte a [configuração do exemplo e a utilização na Solidez](#example-configuration-of-type-enum). |
| int      | Tipo de dados inteiros. |
| dinheiro    | Tipo de dados de dinheiro. |
| state    | Estado de fluxo de trabalho. |
| string  | Tipo de dados String. 4000 caracteres no máximo. Ver [configuração](#example-configuration-of-type-string)de exemplo . |
| utilizador     | Endereço do utilizador do tipo. |
| hora     | Tipo de dados do tempo. |
|`[ Application Role Name ]`| Qualquer nome especificado na função de candidatura. Limita os utilizadores a serem desse tipo de função. |

### <a name="example-configuration-of-type-array"></a>Configuração de exemplo de matriz de tipo

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Usando uma propriedade de tipo matriz

Se definir uma propriedade como matriz de tipo na configuração, você precisa incluir uma função de obter explícita para devolver a propriedade pública do tipo matriz em Solidez. Por exemplo:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Configuração de exemplo da cadeia de tipo

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Configuração do exemplo do tipo enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Utilização do tipo de enumeração na Solidez

Uma vez definido um enum na configuração, pode utilizar tipos de enumeração na Solidez. Por exemplo, pode definir um enum chamado PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

A lista de cordas tem de coincidir entre a configuração e o contrato inteligente para ser válida e consistente na bancada de trabalho blockchain.

Exemplo de atribuição:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Exemplo de parâmetro de função: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Construtor

Define os parâmetros de entrada para um caso de fluxo de trabalho.

| Campo | Descrição | Necessário |
|-------|-------------|:--------:|
| Parâmetros | Recolha de [identificadores necessários](#identifiers) para iniciar um contrato inteligente. | Sim |

### <a name="constructor-example"></a>Exemplo de construtor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Funções

Define funções que podem ser executadas no fluxo de trabalho.

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Nome | O nome único da função. O contrato inteligente correspondente deve usar o mesmo **nome** para a função aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável da função. | Sim | 255 |
| Descrição | Descrição da função | Não | 255 |
| Parâmetros | Recolha de [identificadores correspondentes](#identifiers) aos parâmetros da função. | Sim | |

### <a name="functions-example"></a>Exemplo de funções

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Estados

Uma coleção de estados únicos dentro de um fluxo de trabalho. Cada estado capta um passo no fluxo de controlo da lógica empresarial. 

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Nome | Nome único do estado. O contrato inteligente correspondente deve utilizar o mesmo **nome** para o estado aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável do estado. | Sim | 255 |
| Descrição | Descrição do estado. | Não | 255 |
| PercentComplete | Um valor inteiro apresentado na interface de utilizador da Bancada de Trabalho blockchain para mostrar o progresso dentro do fluxo de controlo da lógica empresarial. | Sim | |
| Estilo | Dica visual indicando se o Estado representa um estado de sucesso ou fracasso. Existem dois valores válidos: `Success` ou `Failure`. | Sim | |
| Transições | Recolha de [transições](#transitions) disponíveis do estado atual para o próximo conjunto de estados. | Não | |

### <a name="states-example"></a>Exemplo de estados

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transições

Ações disponíveis para o próximo estado. Uma ou mais funções de utilizador podem executar uma ação em cada estado, onde uma ação pode transitar um estado para outro estado no fluxo de trabalho. 

| Campo | Descrição | Necessário |
|-------|-------------|:--------:|
| Papéis Permitidos | Lista de funções de candidaturas autorizadas a iniciar a transição. Todos os utilizadores da função especificada poderão ser capazes de executar a ação. | Não |
| PermitidoSPapéis | A lista das funções de utilizador participantes ou especificadas no contrato inteligente permitiu iniciar a transição. As funções por exemplo são definidas em **Propriedades** dentro dos fluxos de trabalho. AllowedInstanceRoles representa um utilizador que participa num caso de contrato inteligente. AllowedInstanceRoles dá-lhe a capacidade de restringir a tomada de uma ação para um papel de utilizador em caso de contrato.  Por exemplo, só pode querer permitir que o utilizador que criou o contrato (InstanceOwner) possa rescindir em vez de todos os utilizadores do tipo de função (Proprietário) se tiver especificado a função em AllowedRoles. | Não |
| DisplayName | Nome amigável da transição. | Sim |
| Descrição | Descrição da transição. | Não |
| Função | O nome da função para iniciar a transição. | Sim |
| Próximos Estados | Uma coleção de potenciais próximos estados após uma transição bem sucedida. | Sim |

### <a name="transitions-example"></a>Exemplo de transições

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Funções da aplicação

As funções de aplicação definem um conjunto de funções que podem ser atribuídas aos utilizadores que pretendam atuar ou participar dentro da aplicação. As funções de aplicação podem ser utilizadas para restringir ações e participação dentro da aplicação blockchain e dos fluxos de trabalho correspondentes. 

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Nome | O nome único do papel de candidatura. O contrato inteligente correspondente deve utilizar o mesmo **nome** para o papel aplicável. Os nomes do tipo base estão reservados. Não é possível nomear uma função de candidatura com o mesmo nome de [Tipo](#type)| Sim | 50 |
| Descrição | Descrição da função de candidatura. | Não | 255 |

### <a name="application-roles-example"></a>Exemplo de funções de aplicação

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identificadores

Os identificadores representam uma recolha de informação usada para descrever propriedades de fluxo de trabalho, construtores e parâmetros de função. 

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Nome | O nome único da propriedade ou parâmetro. O contrato inteligente correspondente deve usar o mesmo **nome** para a propriedade ou parâmetro aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável para a propriedade ou parâmetro. | Sim | 255 |
| Descrição | Descrição da propriedade ou parâmetro. | Não | 255 |
| Tipo | Tipo [de dados](#type)de propriedade. | Sim |

### <a name="identifiers-example"></a>Exemplo de identificadores

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Exemplo de ficheiro de configuração

A transferência de ativos é um cenário inteligente de contrato para a compra e venda de ativos de alto valor, que requerem um inspetor e avaliador. Os vendedores podem listar os seus ativos instantaneamente, instantaneamente, um contrato inteligente de transferência de ativos. Os compradores podem fazer ofertas tomando uma ação sobre o contrato inteligente, e outras partes podem tomar ações para inspecionar ou avaliar o ativo. Uma vez que o ativo seja marcado tanto inspecionado como avaliado, o comprador e o vendedor confirmarão a venda novamente antes de o contrato estar concluído. Em cada ponto do processo, todos os participantes têm visibilidade no estado do contrato à medida que este é atualizado. 

Para mais informações, incluindo os ficheiros de código, consulte a [amostra de transferência de ativos para a Bancada de Trabalho da Blockchain Azure](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

O seguinte ficheiro de configuração destina-se à amostra de transferência de ativos:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

