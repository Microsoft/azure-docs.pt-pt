---
title: Referência de configuração do Azure Blockchain Workbench
description: Visão geral da configuração do aplicativo Azure Blockchain Workbench Preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 1c737106b47b95fcc6d1abdadc81398a3bc9256d
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845108"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referência de configuração do Azure Blockchain Workbench

Os aplicativos do Azure Blockchain Workbench são fluxos de trabalho de várias partes definidos por metadados de configuração e código de contrato inteligente. Os metadados de configuração definem os fluxos de trabalho de alto nível e o modelo de interação do aplicativo blockchain. Os contratos inteligentes definem a lógica de negócios do aplicativo blockchain. O Workbench usa o código de configuração e de contrato inteligente para gerar experiências do usuário do aplicativo blockchain.

Metadados de configuração especifica as seguintes informações para cada aplicativo blockchain:

* Nome e descrição do aplicativo blockchain
* Funções exclusivas para usuários que podem agir ou participar dentro do aplicativo blockchain
* Um ou mais fluxos de trabalho. Cada fluxo de trabalho atua como um computador de estado para controlar o fluxo da lógica de negócios. Os fluxos de trabalho podem ser independentes ou interagir entre si.

Cada fluxo de trabalho definido especifica o seguinte:

* Nome e descrição do fluxo de trabalho
* Estados do fluxo de trabalho.  Cada Estado é um estágio no fluxo de controle da lógica de negócios. 
* Ações para fazer a transição para o próximo estado
* Funções de usuário com permissão para iniciar cada ação
* Contratos inteligentes que representam a lógica de negócios em arquivos de código

## <a name="application"></a>Aplicação

Um aplicativo blockchain contém metadados de configuração, fluxos de trabalho e funções de usuário que podem agir ou participar dentro do aplicativo.

| Campo | Description | Requerido |
|-------|-------------|:--------:|
| ApplicationName | Nome de aplicativo exclusivo. O contrato inteligente correspondente deve usar o mesmo **ApplicationName** para a classe de contrato aplicável.  | Sim |
| DisplayName | Nome de exibição amigável do aplicativo. | Sim |
| Description | Descrição do aplicativo. | Não |
| applicationRoles | Coleção de [ApplicationRoles](#application-roles). Funções de usuário que podem agir ou participar dentro do aplicativo.  | Sim |
| Workflows | Coleção de [fluxos de trabalho](#workflows). Cada fluxo de trabalho atua como um computador de estado para controlar o fluxo da lógica de negócios. | Sim |

Para obter um exemplo, consulte [exemplo de arquivo de configuração](#configuration-file-example).

## <a name="workflows"></a>Workflows

A lógica de negócios de um aplicativo pode ser modelada como um computador de estado em que tomar uma ação faz com que o fluxo da lógica comercial se mova de um estado para outro. Um fluxo de trabalho é uma coleção de tais Estados e ações. Cada fluxo de trabalho consiste em um ou mais contratos inteligentes, que representam a lógica de negócios em arquivos de código. Um contrato executável é uma instância de um fluxo de trabalho.

| Campo | Description | Requerido | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | Nome exclusivo do fluxo de trabalho. O contrato inteligente correspondente deve usar o mesmo **nome** para a classe de contrato aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável do fluxo de trabalho. | Sim | 255 |
| Description | Descrição do fluxo de trabalho. | Não | 255 |
| Iniciadores | Coleção de [ApplicationRoles](#application-roles). Funções atribuídas a usuários que estão autorizados a criar contratos no fluxo de trabalho. | Sim | |
| Iniciarstate | Nome do estado inicial do fluxo de trabalho. | Sim | |
| properties | Coleção de [identificadores](#identifiers). Representa os dados que podem ser lidos fora da cadeia ou visualizados em uma ferramenta de experiência do usuário. | Sim | |
| Qu | Define os parâmetros de entrada para criar uma instância do fluxo de trabalho. | Sim | |
| Functions | Uma coleção de [funções](#functions) que podem ser executadas no fluxo de trabalho. | Sim | |
| States | Uma coleção de [Estados](#states)de fluxo de trabalho. | Sim | |

Para obter um exemplo, consulte [exemplo de arquivo de configuração](#configuration-file-example).

## <a name="type"></a>Type

Tipos de dados com suporte.

| Type | Description |
|-------|-------------|
| endereço  | Tipo de endereço Blockchain, como *contratos* ou *usuários*. |
| array    | Matriz de nível único do tipo inteiro, bool, dinheiro ou tempo. As matrizes podem ser estáticas ou dinâmicas. Use **ElementType** para especificar o tipo de dados dos elementos dentro da matriz. Consulte [exemplo de configuração](#example-configuration-of-type-array). |
| bool     | Tipo de dados booliano. |
| Contrato | Endereço do tipo contrato. |
| Enum     | Conjunto enumerado de valores nomeados. Ao usar o tipo de enumeração, você também especifica uma lista de EnumValues. Cada valor é limitado a 255 caracteres. Os caracteres de valor válidos incluem letras maiúsculas e minúsculas (A-Z, a-z) e números (0-9). Consulte [exemplo de configuração e uso em solidez](#example-configuration-of-type-enum). |
| int      | Tipo de dados Integer. |
| money    | Tipo de dados Money. |
| state    | Estado do fluxo de trabalho. |
| Cadeia de caracteres  | Tipo de dados de cadeia de caracteres. máximo de 4000 caracteres. Consulte [exemplo de configuração](#example-configuration-of-type-string). |
| Utilizador     | Endereço do tipo usuário. |
| time     | Tipo de dados time. |
|`[ Application Role Name ]`| Qualquer nome especificado na função de aplicativo. Limita os usuários a serem desse tipo de função. |

### <a name="example-configuration-of-type-array"></a>Exemplo de configuração do tipo array

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

#### <a name="using-a-property-of-type-array"></a>Usando uma propriedade do tipo array

Se você definir uma propriedade como matriz de tipo na configuração, precisará incluir uma função Get explícita para retornar a propriedade pública do tipo de matriz em solidez. Por exemplo:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Exemplo de configuração do tipo cadeia de caracteres

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

### <a name="example-configuration-of-type-enum"></a>Exemplo de configuração do tipo enum

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

#### <a name="using-enumeration-type-in-solidity"></a>Usando tipo de enumeração em solidez

Depois que um enum é definido na configuração, você pode usar tipos de enumeração em solidez. Por exemplo, você pode definir uma enumeração chamada PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

A lista de cadeias de caracteres precisa fazer a correspondência entre a configuração e o contrato inteligente para serem declarações válidas e consistentes no Blockchain Workbench.

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

## <a name="constructor"></a>Qu

Define os parâmetros de entrada para uma instância de um fluxo de trabalho.

| Campo | Description | Requerido |
|-------|-------------|:--------:|
| Parâmetros | Coleção de [identificadores](#identifiers) necessários para iniciar um contrato inteligente. | Sim |

### <a name="constructor-example"></a>Exemplo de Construtor

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

## <a name="functions"></a>Functions

Define as funções que podem ser executadas no fluxo de trabalho.

| Campo | Description | Requerido | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | O nome exclusivo da função. O contrato inteligente correspondente deve usar o mesmo **nome** para a função aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável da função. | Sim | 255 |
| Description | Descrição da função | Não | 255 |
| Parâmetros | Coleção de [identificadores](#identifiers) correspondentes aos parâmetros da função. | Sim | |

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

## <a name="states"></a>States

Uma coleção de Estados exclusivos em um fluxo de trabalho. Cada estado captura uma etapa no fluxo de controle da lógica de negócios. 

| Campo | Description | Requerido | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | Nome exclusivo do estado. O contrato inteligente correspondente deve usar o mesmo **nome** para o estado aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável do estado. | Sim | 255 |
| Description | Descrição do estado. | Não | 255 |
| PercentComplete | Um valor inteiro exibido na interface do usuário do Blockchain Workbench para mostrar o progresso dentro do fluxo de controle da lógica de negócios. | Sim | |
| Estilo | Dica visual que indica se o estado representa um estado de êxito ou falha. Há dois valores válidos: `Success` ou. `Failure` | Sim | |
| Transições | Coleção de [transições](#transitions) disponíveis do estado atual para o próximo conjunto de Estados. | Não | |

### <a name="states-example"></a>Exemplo de Estados

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

Ações disponíveis para o próximo estado. Uma ou mais funções de usuário podem executar uma ação em cada Estado, em que uma ação pode fazer a transição de um estado para outro Estado no fluxo de trabalho. 

| Campo | Description | Requerido |
|-------|-------------|:--------:|
| AllowedRoles | Lista de funções de aplicativos com permissão para iniciar a transição. Todos os usuários da função especificada podem ser capazes de executar a ação. | Não |
| AllowedInstanceRoles | Lista de funções de usuário participantes ou especificadas no contrato inteligente com permissão para iniciar a transição. As funções de instância são definidas em **Propriedades** em fluxos de trabalho. AllowedInstanceRoles representam um usuário que participa de uma instância de um contrato inteligente. O AllowedInstanceRoles permite restringir a execução de uma ação a uma função de usuário em uma instância de contrato.  Por exemplo, você pode querer permitir que o usuário que criou o contrato (InstanceOwner) seja capaz de terminar em vez de todos os usuários no tipo de função (proprietário) se você especificou a função em AllowedRoles. | Não |
| DisplayName | Nome de exibição amigável da transição. | Sim |
| Description | Descrição da transição. | Não |
| Função | O nome da função para iniciar a transição. | Sim |
| NextStates | Uma coleção de possíveis próximos Estados após uma transição bem-sucedida. | Sim |

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

As funções de aplicativo definem um conjunto de funções que podem ser atribuídas a usuários que desejam agir ou participar dentro do aplicativo. As funções de aplicativo podem ser usadas para restringir ações e participação no aplicativo blockchain e nos fluxos de trabalho correspondentes. 

| Campo | Description | Requerido | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | O nome exclusivo da função de aplicativo. O contrato inteligente correspondente deve usar o mesmo **nome** para a função aplicável. Os nomes de tipo base são reservados. Você não pode nomear uma função de aplicativo com o mesmo nome que o [tipo](#type)| Sim | 50 |
| Description | Descrição da função de aplicativo. | Não | 255 |

### <a name="application-roles-example"></a>Exemplo de funções de aplicativo

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

Os identificadores representam uma coleção de informações usadas para descrever Propriedades de fluxo de trabalho, Construtor e parâmetros de função. 

| Campo | Description | Requerido | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | O nome exclusivo da propriedade ou do parâmetro. O contrato inteligente correspondente deve usar o mesmo **nome** para a propriedade ou o parâmetro aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável para a propriedade ou parâmetro. | Sim | 255 |
| Description | Descrição da propriedade ou do parâmetro. | Não | 255 |

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

## <a name="configuration-file-example"></a>Exemplo de arquivo de configuração

A transferência de ativos é um cenário de contrato inteligente para compra e venda de ativos de valor alto, que exigem um inspetor e avaliador. Os vendedores podem listar seus ativos instanciando um contrato inteligente de transferência de ativos. Os compradores podem fazer ofertas realizando uma ação no contrato inteligente, e outras partes podem tomar medidas para inspecionar ou avaliar o ativo. Depois que o ativo for marcado como inspecionado e avaliado, o comprador e o vendedor confirmarão a venda novamente antes que o contrato seja definido como concluído. Em cada ponto do processo, todos os participantes têm visibilidade do estado do contrato conforme ele é atualizado. 

Para obter mais informações, incluindo os arquivos de código, consulte [exemplo de transferência de ativos para o Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

O arquivo de configuração a seguir é para o exemplo de transferência de ativos:

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

