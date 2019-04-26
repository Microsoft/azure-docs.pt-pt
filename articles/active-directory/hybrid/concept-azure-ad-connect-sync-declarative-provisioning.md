---
title: 'Azure AD Connect: Noções básicas sobre o aprovisionamento declarativo | Documentos da Microsoft'
description: Explica o modelo de configuração de aprovisionamento declarativo no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 543c1a6706f794b81c4f93fc6fff3a61ed3fb9e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246362"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronização do Azure AD Connect: Compreender o Aprovisionamento Declarativo
Este tópico explica o modelo de configuração no Azure AD Connect. O modelo é chamado de aprovisionamento declarativo e permite-lhe efetuar uma configuração alterar com facilidade. Muitas coisas descritas neste tópico são avançadas e não é necessário para a maioria dos cenários de clientes.

## <a name="overview"></a>Descrição geral
Aprovisionamento declarativo está a processar objetos proveniente de um diretório ligado de origem e determina como o objeto e os atributos devem ser transformados de uma origem para um destino. Um objeto é processado num pipeline de sincronização e o pipeline é o mesmo para regras de entrada e saídas. Uma regra de entrada é proveniente de um espaço conector para o metaverso e é uma regra de saída do metaverse para um espaço conector.

![Pipeline de sincronização](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

O pipeline tem vários módulos diferentes. Cada um deles é responsável por um conceito na sincronização de objetos.

![Pipeline de sincronização](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Origem, o objeto de origem
* [Âmbito](#scope), encontrar todas as regras de sincronização que se encontrem no âmbito
* [Junte-se a](#join), determina a relação entre o espaço conector e de metaverso
* Transformação, Calculates como atributos devem ser transformados e o flow
* [Precedência](#precedence), resolve em conflito contribuições de atributo
* Destino, o objeto de destino

## <a name="scope"></a>Âmbito
O módulo de âmbito está a avaliar um objeto e determina as regras que estão no âmbito e devem ser incluídas no processamento. Dependendo dos valores de atributos no objeto, as regras de sincronização diferentes são avaliadas no âmbito. Por exemplo, um utilizador desativado com nenhuma caixa de correio do Exchange tem regras diferentes do que um utilizador ativado com uma caixa de correio.  
![Âmbito](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

O âmbito é definido como grupos e as cláusulas. As cláusulas estão dentro de um grupo. Um lógico e é utilizado entre todas as cláusulas num grupo. Por exemplo, (departamento = TI e país = Dinamarca). OU é utilizada entre grupos.

![Âmbito](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
O âmbito nesta imagem deve ser lida como (departamento = TI e país = Dinamarca) ou (país = Suécia). Se o grupo 1 ou 2 do grupo é avaliado como true, em seguida, a regra está no âmbito.

O módulo de âmbito suporta as seguintes operações.

| Operação | Descrição |
| --- | --- |
| EQUAL, NOTEQUAL |Comparação de cadeia de caracteres que avalia se o valor é igual ao valor no atributo. Para atributos com múltiplos valores, consulte ISIN e ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Uma comparação de cadeia de caracteres que avalia se o valor for less than do valor no atributo. |
| CONTAINS, NOTCONTAINS |Comparação de cadeia de caracteres que avalia se o valor pode ser encontrado em algum lugar dentro do valor no atributo. |
| STARTSWITH, NOTSTARTSWITH |Comparação de cadeia de caracteres que avalia se o valor é no início do valor no atributo. |
| ENDSWITH, NOTENDSWITH |Comparação de cadeia de caracteres que avalia se o valor é no fim do valor no atributo. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Comparação de cadeia de caracteres que avalia se o valor for superior do valor no atributo. |
| ISNULL, ISNOTNULL |Avalia se o atributo estiver ausente do objeto. Se o atributo não estiver presente e, portanto, null, em seguida, a regra está no âmbito. |
| ISIN, ISNOTIN |Avalia se o valor estiver presente no atributo definido. Esta operação é a variação com múltiplos valor de igual e NOTEQUAL. O atributo deve para ser um atributo com múltiplos valor e se o valor pode ser encontrado em qualquer um dos valores de atributo, em seguida, a regra está no âmbito. |
| ISBITSET, ISNOTBITSET |Avalia se um bit particular está definido. Por exemplo, pode ser utilizado para avaliar o bits na userAccountControl para ver se um utilizador está ativado ou desativado. |
| ISMEMBEROF, ISNOTMEMBEROF |O valor deve conter um DN a um grupo no espaço conector. Se o objeto for um membro do grupo especificado, a regra está no âmbito. |

## <a name="join"></a>Associar
O módulo de associação no pipeline de sincronização é responsável pela localização a relação entre o objeto na origem e um objeto no destino. Numa regra de entrada, esta relação seria um objeto num espaço conector encontrar uma relação a um objeto no metaverso.  
![Junte-se entre cs e mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
O objetivo é ver que se existe um objeto já no metaverso, criado por outro conector, deve ser associado com. Por exemplo, numa floresta de recursos da conta de utilizador da floresta de contas deve ser associado com o utilizador da floresta de recursos.

As associações são usadas principalmente em regras de entrada para associar objetos de espaço conector em conjunto para o mesmo objeto de metaverso.

As associações são definidas como um ou mais grupos. Dentro de um grupo, tem cláusulas. Um lógico e é utilizado entre todas as cláusulas num grupo. OU é utilizada entre grupos. Os grupos são processados por ordem de cima para baixo. Quando um grupo encontrou exatamente uma correspondência com um objeto no destino, não há outras regras de associação são avaliadas. Se zero ou mais de um objeto for encontrado, o processamento continua ao seguinte grupo de regras. Por esse motivo, as regras devem ser criada na ordem mais explícito primeiro e mais difusa no final.  
![Junte-se a definição](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
A associa nesta imagem é processada de cima para baixo. Em primeiro lugar, o pipeline de sincronização vê se existe uma correspondência no employeeID. Caso contrário, a segunda regra verifica se o nome da conta pode ser utilizado para associar os objetos em conjunto. Se não for uma correspondência seja, a terceira e última regra é uma correspondência difusa mais usando o nome de utilizador.

Se todas as regras de associação que foram avaliadas e exatamente não existe uma correspondência, o **tipo de ligação** sobre o **Descrição** página é usada. Se esta opção estiver definida como **aprovisionar**, em seguida, é criado um novo objeto no destino.  
![Aprovisionar ou associação](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Um objeto só deve ter uma regra de sincronização único com regras de associação no âmbito. Se existirem várias regras de sincronização, em que a associação é definida, ocorrerá um erro. Precedência não é utilizada para resolver conflitos de associação. Um objeto tem de ter uma regra de associação no âmbito de atributos para o fluxo com a mesma direção de entrada/saída. Se precisar de atributos de fluxo de entrada e saída para o mesmo objeto, tem de ter uma entrada e uma regra de sincronização de saída com a associação.

Associação de saída tem um comportamento especial quando tentar aprovisionar um objeto para um espaço de conector de destino. O atributo de DN é usado para experimentar primeiro uma associação de inverso. Se já existir um objeto no espaço de conector de destino com o DN do mesmo, os objetos estão associados.

O módulo de associação é avaliado apenas uma vez, quando uma nova regra de sincronização é fornecido para o escopo. Quando tiver associado um objeto, não é disjoining, mesmo se os critérios de associação já não é satisfeita. Se desejar anular adesão um objeto, tem de aceder a regra de sincronização que os objetos associados a um fora do escopo.

### <a name="metaverse-delete"></a>Eliminação de Metaverso
Um objeto de metaverso permanece, desde que não existe uma regra de sincronização no âmbito com **tipo de ligação** definida como **aprovisionar** ou **StickyJoin**. Um StickyJoin é utilizado quando um conector não tem permissão para aprovisionar um novo objeto para o metaverso, mas quando ele foi associado, tem de ser eliminado na origem antes do objeto de metaverso é eliminado.

Quando um objeto de metaverso é eliminado, todos os objetos associados uma regra de sincronização de saída marcado para **aprovisionar** estão marcadas para uma exclusão.

## <a name="transformations"></a>Transformações
As transformações são usadas para definir a forma como os atributos devem fluir da origem para o destino. Os fluxos podem ter um dos seguintes **tipos de fluxo**: Direct, constante ou expressão. Um fluxo em direto, fluxos de um valor de atributo como-é com nenhuma transformação adicionais. Um valor constante define o valor especificado. Uma expressão usa a linguagem de expressão de aprovisionamento declarativa para expressar a forma como deve ser a transformação. Os detalhes para a linguagem de expressão podem ser encontrados na [Noções básicas sobre a linguagem de expressão de aprovisionamento declarativa](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) tópico.

![Aprovisionar ou associação](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

O **aplicam-se de uma vez** caixa de verificação define que o atributo só deve ser definido quando o objeto é criado inicialmente. Por exemplo, esta configuração pode ser utilizada para definir uma palavra-passe inicial para um novo objeto de utilizador.

### <a name="merging-attribute-values"></a>Intercalação de valores de atributo
Os fluxos de atributos é uma definição para determinar se os atributos com múltiplos valores devem ser intercalados de vários conectores diferentes. O valor predefinido é **atualização**, que indica que a regra de sincronização com precedência máxima deve ganhar.

![Tipos de intercalação](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Também há **intercalar** e **MergeCaseInsensitive**. Estas opções permitem-lhe valores de diferentes origens de intercalação. Por exemplo, ele pode ser usado para intercalar o atributo de membro ou proxyAddresses de várias florestas diferentes. Quando utilizar esta opção, todas as regras de sincronização no âmbito de um objeto tem de utilizar o mesmo tipo de intercalação. Não é possível definir **atualização** de um conector e **intercalar** de outro. Se tentar, receberá um erro.

A diferença entre **intercalar** e **MergeCaseInsensitive** é como processar os valores de atributos duplicados. O motor de sincronização torna-se de que valores duplicados não são inseridos no atributo de destino. Com o **MergeCaseInsensitive**, valores com apenas uma diferença de duplicados, no caso de não vão estar presentes. Por exemplo, não verá ambos "SMTP:bob@contoso.com"e"smtp:bob@contoso.com" no atributo de destino. **Intercalar** é apenas a ver os valores exatos e vários valores onde há apenas uma diferença no caso pode estar presente.

A opção **substitua** é igual a **atualização**, mas não é utilizada.

### <a name="control-the-attribute-flow-process"></a>Controlar o processo de fluxo de atributo
Quando várias regras de sincronização de entrada são configuradas para contribuir para o mesmo atributo de metaverso, precedência é utilizada para determinar o vencedor. A regra de sincronização com precedência máxima (menor valor numérico) irá contribuir com o valor. O mesmo ocorre para regras de saída. A sincronização da regra com o wins de precedência mais altos e contribuir com o valor para o diretório conectado.

Em alguns casos, em vez de contribuir com um valor, a regra de sincronização deve determinar como outras regras devem se comportar. Existem alguns literais especiais utilizados para este caso.

Para regras de sincronização de entrada, o literal **nulo** pode ser usado para indicar que o fluxo não tem nenhum valor para contribuir. Outra regra com precedência inferior pode contribuir com um valor. Se nenhuma regra contribuiu com um valor, em seguida, o atributo do metaverso é removido. Para uma regra de saída, se **nulo** é o valor final depois de todas as regras de sincronização foram processadas, o valor é removido no diretório conectado.

O literal **AuthoritativeNull** é semelhante à **nulo** , mas com a diferença que não existem regras de precedência inferior podem contribuir com um valor.

Também pode utilizar um fluxo de atributos **IgnoreThisFlow**. É semelhante a NULL no sentido de que ele indica que não há nada para contribuir. A diferença é que não remove um valor já existente no destino. É como o fluxo de atributos nunca foi lá.

Segue-se um exemplo:

Na *expansão para AD - híbrida do Exchange do utilizador* o fluxo seguinte pode ser encontrado:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Esta expressão deve ser lida como: se a caixa de correio do utilizador está localizada no Azure AD, fluxo, em seguida, o atributo do Azure AD para o AD. Caso contrário, não fluxo nada para o Active Directory. Neste caso, ele seria manter o valor existente no AD.

### <a name="importedvalue"></a>ImportedValue
A função ImportedValue é diferente de todas as outras funções, uma vez que o nome de atributo deve estar entre aspas, em vez de parênteses Retos:  
`ImportedValue("proxyAddresses")`.

Normalmente, durante a sincronização um atributo utiliza o valor esperado, mesmo que ainda não foram exportado ainda ou foi recebido um erro durante a exportação ("início do Torre"). Uma sincronização de entrada parte do princípio de que um atributo que ainda não tenha atingido, eventualmente, um diretório conectado chegue até ele. Em alguns casos, é importante sincronizar apenas um valor que foi confirmado pelo diretório conectado ("hologram e delta importar Torre").

Um exemplo dessa função pode ser encontrado na regra de sincronização de out-of-box *do AD – comuns de usuário do Exchange*. No Exchange híbrido, o valor adicionado pelo Exchange online só deve ser sincronizado quando este foi confirmado que o valor foi exportado com êxito:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedência
Quando tentarem várias regras de sincronização contribuir com o mesmo valor de atributo para o destino, é utilizado o valor de precedência para determinar o vencedor. A regra com precedência máxima, o menor valor numérico, irá contribuir com o atributo num conflito.

![Tipos de intercalação](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Esta ordem pode ser usado para definir fluxos de atributos mais precisos para um pequeno subconjunto de objetos. Por exemplo, certifique-se de que atributos a partir de uma conta ativada fora-da-caixa-regras (**utilizador AccountEnabled**) têm precedência de outras contas.

Precedência pode ser definida entre conectores. Isso permite que conectores com dados melhor para contribuir com valores em primeiro lugar.

### <a name="multiple-objects-from-the-same-connector-space"></a>Vários objetos do mesmo espaço conector
Se tiver vários objetos no mesmo espaço conector associado ao mesmo objeto de metaverso, tem de ser ajustada precedência. Se vários objetos estiverem no âmbito da mesma regra de sincronização, o motor de sincronização não é capaz de determinar a precedência. É ambíguo qual objeto de origem deve contribuir com o valor para o metaverso. Esta configuração é comunicada como ambígua, mesmo que os atributos na origem com o mesmo valor.  
![Vários objetos associados ao mesmo objeto de mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Para este cenário, terá de alterar o âmbito das regras de sincronização para que os objetos de origem tem regras de sincronização diferentes no âmbito. Que permite-lhe definir a precedência diferente.  
![Vários objetos associados ao mesmo objeto de mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a linguagem de expressão no [Noções básicas sobre expressões de aprovisionamento declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Veja como declarativa de aprovisionamento é utilizado out-of-box na [entender a configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md).
* Veja como fazer uma alteração prática com o aprovisionamento declarativo [como fazer uma alteração à configuração padrão](how-to-connect-sync-change-the-configuration.md).
* Continuar a ler como funcionam em conjunto os utilizadores e contactos [entender utilizadores e contactos](concept-azure-ad-connect-sync-user-and-contacts.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

**Tópicos de referência**

* [Sincronização do Azure AD Connect: Referência das funções](reference-connect-sync-functions-reference.md)
