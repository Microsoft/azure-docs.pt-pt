---
title: Referência de configuração do Azure Blockchain Workbench
description: O Azure Blockchain Workbench aplicação descrição geral da configuração.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/08/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: b45cd84322279d7f81cc3f047f72a75a6d898bc6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452696"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referência de configuração do Azure Blockchain Workbench

 As aplicações do Azure Blockchain Workbench são fluxos de trabalho de múltiplos intervenientes definidos por metadados de configuração e o código de contrato inteligente. Metadados de configuração definem os fluxos de trabalho de alto nível e o modelo de interação da aplicação de blockchain. Contratos inteligentes definem a lógica de negócio da aplicação de blockchain. O Workbench utiliza a configuração e o código de contrato inteligente para gerar as experiências de utilizador de aplicação de blockchain.

Metadados de configuração especifica as seguintes informações para cada aplicação de blockchain:

* Nome e descrição da aplicação de blockchain
* Funções exclusivas para os utilizadores que podem agir ou tem participação na aplicação de blockchain
* Um ou mais fluxos de trabalho. Cada fluxo de trabalho funciona como uma máquina de estado para controlar o fluxo de lógica de negócios. Fluxos de trabalho podem ser independente ou interagir entre si.

Cada fluxo de trabalho definido Especifica o seguinte:

* Nome e uma descrição do fluxo de trabalho
* Estados do fluxo de trabalho.  Cada Estado é uma fase no fluxo de controlo a lógica de negócios. 
* Ações a efetuar a transição para o próximo estado
* Permitida para iniciar cada ação de funções de utilizador
* Contratos inteligentes que representam a lógica de negócios em arquivos de código

## <a name="application"></a>Aplicação

Uma aplicação de blockchain contém funções de metadados, fluxos de trabalho e usuário de configuração que podem agir ou tem participação na aplicação.

| Campo | Descrição | Necessário |
|-------|-------------|:--------:|
| ApplicationName | Nome de aplicação único. O contrato inteligente correspondente tem de utilizar o mesmo **ApplicationName** para a classe de contrato aplicável.  | Sim |
| Nome a Apresentar | Nome a apresentar amigável do aplicativo. | Sim |
| Descrição | Descrição da aplicação. | Não |
| ApplicationRoles | Coleção de [ApplicationRoles](#application-roles). Funções de utilizador que podem agir ou tem participação na aplicação.  | Sim |
| Fluxos de trabalho | Coleção de [fluxos de trabalho](#workflows). Cada fluxo de trabalho funciona como uma máquina de estado para controlar o fluxo de lógica de negócios. | Sim |

Por exemplo, veja [exemplo de ficheiro de configuração](#configuration-file-example).

## <a name="workflows"></a>Fluxos de trabalho

Lógica de negócios de uma aplicação pode ser modelada como uma máquina de Estado onde realizar uma ação faz com que o fluxo da lógica de negócios para mover de um Estado para outro. Um fluxo de trabalho é uma coleção desses Estados e ações. Cada fluxo de trabalho consiste num ou mais contratos inteligentes, que representam a lógica de negócios em arquivos de código. Um contrato de executável é uma instância de um fluxo de trabalho.

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | Nome do fluxo de trabalho exclusivo. O contrato inteligente correspondente tem de utilizar o mesmo **nome** para a classe de contrato aplicável. | Sim | 50 |
| Nome a Apresentar | Nome a apresentar amigável do fluxo de trabalho. | Sim | 255 |
| Descrição | Descrição do fluxo de trabalho. | Não | 255 |
| Iniciadores | Coleção de [ApplicationRoles](#application-roles). Funções que estão atribuídas a utilizadores que estão autorizados a criar contratos no fluxo de trabalho. | Sim | |
| StartState | Nome do Estado inicial do fluxo de trabalho. | Sim | |
| Propriedades | Coleção de [identificadores](#identifiers). Representa dados que podem ser lidas fora da cadeia ou visualizadas num usuário experiência de ferramenta. | Sim | |
| Construtor | Define os parâmetros de entrada para a criação de uma instância do fluxo de trabalho. | Sim | |
| Funções | Uma coleção de [funções](#functions) que pode ser executado no fluxo de trabalho. | Sim | |
| Estados | Uma coleção de fluxo de trabalho [Estados](#states). | Sim | |

Por exemplo, veja [exemplo de ficheiro de configuração](#configuration-file-example).

## <a name="type"></a>Type

Tipos de dados suportados.

| Type | Descrição |
|-------|-------------|
| endereço  | Blockchain como tipo, de endereço *contratos* ou *utilizadores*. |
| array    | Matriz de nível único do tipo número inteiro, booleano, dinheiro ou tempo. As matrizes podem ser estáticos ou dinâmicos. Uso **ElementType** para especificar o tipo de dados de elementos dentro da matriz. Ver [exemplo de configuração](#example-configuration-of-type-array). |
| booleano     | Tipo de dados booleano. |
| contrato | Endereço do contrato de tipo. |
| Enum     | Enumerados conjunto de valores nomeados. Ao usar o tipo de enumeração, também especificar uma lista de EnumValues. Cada valor é limitado a 255 carateres. Carateres de valor válido incluem superior e letras minúsculas (A-Z, a-z) as letras e números (0-9). Ver [exemplo de configuração e utilização na solidez](#example-configuration-of-type-enum). |
| int      | Tipo de dados de número inteiro. |
| dinheiro    | Tipo de dados de dinheiro. |
| state    | Estado do fluxo de trabalho. |
| string  | Tipo de dados de cadeia de caracteres. máximo de caracteres de 4000. Ver [exemplo de configuração](#example-configuration-of-type-string). |
| Utilizador     | Endereço do tipo de utilizador. |
| hora     | Tipo de dados de tempo. |
|`[ Application Role Name ]`| Qualquer nome especificado na função de aplicação. Limita os utilizadores sejam desse tipo de função. |

### <a name="example-configuration-of-type-array"></a>Exemplo de configuração do tipo matriz

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

Se definir uma propriedade como tipo de matriz na configuração, precisa incluir uma função de get explícita para devolver a propriedade pública do tipo de matriz no solidez. Por exemplo:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Exemplo de configuração do tipo string

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

### <a name="example-configuration-of-type-enum"></a>Exemplo de configuração de enumeração de tipo

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

#### <a name="using-enumeration-type-in-solidity"></a>Com o tipo de enumeração no solidez

Depois de uma enumeração está definida na configuração, pode usar tipos de enumeração no solidez. Por exemplo, pode definir um enum chamado PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

A lista de cadeias de caracteres tem de corresponder entre a configuração e o contrato inteligente para ser declarações válidas e consistentes na bancada de trabalho de Blockchain.

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

Define os parâmetros de entrada para uma instância de um fluxo de trabalho.

| Campo | Descrição | Necessário |
|-------|-------------|:--------:|
| Parâmetros | Coleção de [identificadores](#identifiers) tem de iniciar um contrato inteligente. | Sim |

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

Define as funções que podem ser executadas no fluxo de trabalho.

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | O nome exclusivo da função. O contrato inteligente correspondente tem de utilizar o mesmo **nome** para a função aplicável. | Sim | 50 |
| Nome a Apresentar | Nome a apresentar amigável da função. | Sim | 255 |
| Descrição | Descrição da função | Não | 255 |
| Parâmetros | Coleção de [identificadores](#identifiers) correspondente para os parâmetros da função. | Sim | |

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

Uma coleção de Estados exclusivos dentro de um fluxo de trabalho. Cada Estado captura um passo no fluxo de controlo a lógica de negócios. 

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | Nome exclusivo do Estado. O contrato inteligente correspondente tem de utilizar o mesmo **nome** para o estado de aplicável. | Sim | 50 |
| Nome a Apresentar | Nome a apresentar amigável do Estado. | Sim | 255 |
| Descrição | Descrição do Estado. | Não | 255 |
| PercentComplete | Um valor inteiro exibido na interface de utilizador do Blockchain Workbench para mostrar o progresso do fluxo de controle de lógica comercial. | Sim | |
| Estilo | Sugestão visual, que indica se o estado representa um Estado de êxito ou falha. Existem dois valores válidos: `Success` ou `Failure`. | Sim | |
| Transições | Coleção de disponíveis [transições](#transitions) do estado atual para o próximo conjunto de Estados. | Não | |

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

Ações disponíveis para o próximo estado. Uma ou mais funções de utilizador podem efetuar uma ação em cada Estado, onde uma ação pode passar um Estado para outro Estado no fluxo de trabalho. 

| Campo | Descrição | Necessário |
|-------|-------------|:--------:|
| AllowedRoles | Lista de aplicações de funções de permissão para iniciar a transição. Todos os utilizadores da função especificada podem ser capazes de executar a ação. | Não |
| AllowedInstanceRoles | Lista de funções de utilizador a participar ou ser especificada no contrato de inteligente autorizado a iniciar a transição. Funções de instância são definidas no **propriedades** dentro de fluxos de trabalho. AllowedInstanceRoles representar um utilizador de participar de uma instância de um contrato de inteligente. AllowedInstanceRoles dão-lhe a capacidade para restringir a realização de uma ação para uma função de utilizador numa instância de contrato.  Por exemplo, apenas poderá permitir que o utilizador que criou o contrato (InstanceOwner) para conseguir terminar em vez de todos os utilizadores no tipo de função (proprietário), se especificou a função no AllowedRoles. | Não |
| Nome a Apresentar | Nome a apresentar amigável da transição. | Sim |
| Descrição | Descrição da transição. | Não |
| Função | O nome da função para iniciar a transição. | Sim |
| NextStates | Uma coleção de Estados seguintes potenciais após uma transição com êxito. | Sim |

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

Funções da aplicação definem um conjunto de funções que podem ser atribuídos a utilizadores que pretendem atuar ou tem participação na aplicação. Funções de aplicação podem ser utilizadas para restringir as ações e participação no blockchain aplicações e fluxos de trabalho correspondentes. 

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | O nome exclusivo da função de aplicação. O contrato inteligente correspondente tem de utilizar o mesmo **nome** para a função aplicável. Nomes de tipo de base são reservados. Não é possível nomear uma função de aplicação com o mesmo nome que [tipo](#type)| Sim | 50 |
| Descrição | Descrição da função de aplicação. | Não | 255 |

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

Identificadores representam uma coleção de informações utilizadas para descrever as propriedades de fluxo de trabalho e parâmetros da função de construtor. 

| Campo | Descrição | Necessário | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| Name | O nome exclusivo da propriedade ou parâmetro. O contrato inteligente correspondente tem de utilizar o mesmo **nome** para a propriedade aplicável ou o parâmetro. | Sim | 50 |
| Nome a Apresentar | Nome a apresentar amigável para a propriedade ou um parâmetro. | Sim | 255 |
| Descrição | Descrição da propriedade ou parâmetro. | Não | 255 |

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

Transferência de recurso é um cenário de contrato inteligente para a compra e venda de ativos de valor elevado, que requerem um inspetor e appraiser. Os vendedores podem listar os seus ativos pela Instanciação de um contrato de inteligentes de transferência de recurso. Compradores podem tornar ofertas ao realizar uma ação no contrato inteligente e outras partes podem tomar ações para inspecionar ou avaliar o ativo. Assim que o elemento é marcado como ambos inspecionadas e appraised, o comprador e vendedor confirma a venda novamente antes do contrato é definido para concluir. Em cada momento no processo, todos os participantes tenham visibilidade sobre o estado do contrato de como ela é atualizada. 

Para obter mais informações, incluindo os arquivos de código, consulte [exemplo de transferência de recurso para o Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

É o seguinte ficheiro de configuração para o exemplo de transferência de recurso:

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
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

