---
title: Azure Attestation reivindica gramática de regra
description: Detalhes sobre as reivindicações políticas da Azure Attestation e regras de reivindicação.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91285423"
---
# <a name="claim-and-claim-rules"></a>Regras de reclamação e reivindicação

Para compreender a gramática das regras de reivindicação, tem primeiro de compreender as alegações de política de atestado.

## <a name="claim"></a>Afirmação

Uma reclamação é um conjunto de propriedades agrupadas para fornecer informações relevantes. Para a Azure Attestation, uma reclamação contém as seguintes propriedades:

- **tipo**: Um valor de corda que representa o tipo da reclamação.
- **valor**: Um valor booleano, inteiro ou de corda que representa o valor da reclamação.
- **valorType**: O tipo de dados da informação armazenada na propriedade de valor. Os tipos suportados são String, Inteiro e Boolean. Se não for definido, o valor predefinido será "String".
- **emitente**: Informações relativas ao emitente da reclamação. O emitente será um dos seguintes tipos:
  - **AttestationService**: Algumas reclamações são disponibilizadas ao autor da apólice pela Azure Attestation, que pode ser usada pelo autor da política de atestado para elaborar a política adequada.
  - **AttestationPolicy**: A política (tal como definida pelo administrador) em si pode adicionar reclamações aos elementos de prova recebidos durante o processamento. O emitente neste caso está definido para "AttestationPolicy".
  - **CustomClaim**: O attestor (cliente) também pode adicionar reclamações adicionais às provas de atestado. O emitente neste caso está definido para "CustomClaim".

Se não for definido. o valor predefinido será "CustomClaim".

## <a name="claim-rule"></a>Regra de Reclamação

O conjunto de reclamações recebidas é utilizado pelo motor de política para calcular o resultado do atestado. Uma regra de reclamação é um conjunto de condições usadas para validar os pedidos de entrada e tomar a ação definida.

```
Conditions list => Action (Claim)
```

A avaliação do Azure Attestation de uma regra de reclamação envolve os seguintes passos:

- Se a lista de condições não estiver presente, execute a ação com reivindicação especificada 
- Caso contrário, avalie as condições da lista de condições.
- Se a lista de condições avaliar falsamente, pare. Caso contrário, prossiga.

As condições de uma regra de reclamação são utilizadas para determinar se a ação tem de ser executada. A lista de condições é uma sequência de condições que são separadas pelo operador "&&".

A lista de condições é estruturada como:

```
Condition && Condition && ...
```

A condição é estruturada como:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

A lista de condições é composta por condições individuais em vários imóveis de uma reclamação. Uma condição pode ter um identificador opcional, que pode ser usado para remeter a reclamação/s que satisfaz a circunstância. Esta referência pode ser utilizada nas outras condições ou na ação da mesma regra.

Por exemplo

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

Seguem-se os operadores que podem ser utilizados para verificar as condições:

| Tipo de valor | Operações Apoiadas |
|--|--|
| Número inteiro | == (iguais), \! = (não igual), <= (inferior ou igual), < (menos que), >= (maior ou igual), > (maior do que) |
| String | == (igual), \! = (não igual) |
| Booleano | == (igual), \! = (não igual) |

Lista de avaliação das condições:

- A presença do operador "&&" implica que uma lista de condições só é avaliada como verdadeira se todas as condições da lista forem avaliadas como verdadeiras.
- Uma condição representa critérios de filtragem no conjunto de reclamações. Diz-se que a condição em si é para avaliar a verdade se houver pelo menos uma reivindicação que satisfaça a condição.
- Diz-se que satisfaz o critério de filtragem representado pela condição se cada uma das suas propriedades satisfizer as condições de propriedade de reclamação correspondentes presentes na condição.  

O conjunto de ações que são permitidas numa política são descritos abaixo.

| Verbo de ação | Description | Secções políticas às quais estas se aplicam |
|--|--|--|
| () | O conjunto de reclamações recebidas pode ser utilizado para calcular **as regras de emissão**. Não aceita qualquer reivindicação como parâmetro | **regras de autorização** |
| negar() | O conjunto de reclamações recebidas não deve ser utilizado para calcular **as regras de emissão** Não aceita qualquer reclamação como parâmetro | **regras de autorização** |
| adicionar(reivindicação) | Acrescenta a reclamação ao conjunto de reclamações recebidas. Qualquer reclamação adicionada ao conjunto de reclamações recebidas estará disponível para as regras de reclamação subsequentes. |**regras de autorização,** **regras de emissão** |
| emissão (reclamação) | Acrescenta a reclamação ao conjunto de reclamações recebidas e de saída | **regras de emissão** |
| problema de emissão (reivindicação) | Adiciona a reclamação ao conjunto de reclamações de entrada e propriedade | **regras de emissão**

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)

