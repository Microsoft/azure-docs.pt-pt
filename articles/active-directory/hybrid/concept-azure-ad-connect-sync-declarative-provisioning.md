---
title: 'Azure AD Connect: Compreensão do Provisionamento Declarativo Microsoft Docs'
description: Explica o modelo de configuração de provisionamento declarativo no Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60246362"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronização Azure AD Connect: Compreensão do Provisionamento Declarativo
Este tópico explica o modelo de configuração no Azure AD Connect. O modelo chama-se Disposição Declarativa e permite-lhe fazer uma mudança de configuração com facilidade. Muitas coisas descritas neste tópico são avançadas e não são necessárias para a maioria dos cenários do cliente.

## <a name="overview"></a>Descrição geral
O fornecimento declarativo é o processamento de objetos provenientes de um diretório ligado a fonte e determina como o objeto e os atributos devem ser transformados de uma fonte para um alvo. Um objeto é processado num gasoduto de sincronização e o gasoduto é o mesmo para as regras de entrada e saída. Uma regra de entrada é de um espaço de conector para o metaverso e uma regra de saída é do metaverso para um espaço de conector.

![Gasoduto sincronizado](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

O oleoduto tem vários módulos diferentes. Cada um é responsável por um conceito de sincronização de objetos.

![Gasoduto sincronizado](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Fonte, O objeto de origem
* [Âmbito,](#scope)encontra todas as regras de sincronização que estão no âmbito
* [Aderir](#join), Determina a relação entre o espaço do conector e o metaverso
* Transformar, calcular como os atributos devem ser transformados e fluir
* [Precedência](#precedence), Resolve contribuições contraditórias de atributos
* Alvo, O objeto alvo

## <a name="scope"></a>Âmbito
O módulo de âmbito está a avaliar um objeto e determina as regras que estão no âmbito e devem ser incluídas no processamento. Dependendo dos valores dos atributos no objeto, as diferentes regras de sincronização são avaliadas como estando no âmbito. Por exemplo, um utilizador deficiente sem caixa de correio Exchange tem regras diferentes das de um utilizador ativado com uma caixa de correio.  
![Âmbito](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

O âmbito é definido como grupos e cláusulas. As cláusulas estão dentro de um grupo. Um elógico e é usado entre todas as cláusulas de um grupo. Por exemplo, (departamento =IT And country = Dinamarca). Um OR lógico é usado entre grupos.

![Âmbito](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
O âmbito neste quadro deve ser lido como (departamento = IT And country = Dinamarca) OR (país=Suécia). Se o grupo 1 ou o grupo 2 forem avaliados como verdadeiros, então a regra está no âmbito.

O módulo de âmbito suporta as seguintes operações.

| Operação | Descrição |
| --- | --- |
| IGUAL, NÃO IGUAL |Uma comparação de cadeias que avalia se o valor é igual ao valor do atributo. Para atributos de vários valores, consulte ISIN e ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Uma comparação de cadeias que avalia se o valor é inferior ao valor no atributo. |
| CONTÉM, NÃO CONTÉM |Uma comparação de cadeias que avalia se o valor pode ser encontrado em algum lugar dentro do valor no atributo. |
| COMEÇA COM, NOTSTARTS WITH |Uma comparação de cadeias que avalia se o valor está no início do valor no atributo. |
| ENDSWITH, NOTENDSWITH |Uma comparação de cadeias que avalia se o valor está no final do valor no atributo. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Uma comparação de cadeias que avalia se o valor é maior do que o valor no atributo. |
| ISNULL, ISNOTNULL |Avalia se o atributo está ausente do objeto. Se o atributo não estiver presente e, portanto, nulo, então a regra está no âmbito. |
| ISIN, ISNOTIN |Avalia se o valor está presente no atributo definido. Esta operação é a variação multivalorizada de EQUAL e NOTEQUAL. O atributo é suposto ser um atributo multivalorizado e se o valor pode ser encontrado em qualquer um dos valores do atributo, então a regra está no âmbito. |
| ISBITSET, ISNOTBITSET |Avalia se uma parte em particular está definida. Por exemplo, pode ser usado para avaliar as bits no userAccountControl para ver se um utilizador está ativado ou desativado. |
| ISMEMBEROF, ISNOTMEMBEROF |O valor deve conter um DN para um grupo no espaço do conector. Se o objeto for membro do grupo especificado, a regra está no âmbito. |

## <a name="join"></a>Associar
O módulo de adesão no pipeline de sincronização é responsável por encontrar a relação entre o objeto na fonte e um objeto no alvo. Numa regra de entrada, esta relação seria um objeto num espaço de conector encontrar uma relação com um objeto no metaverso.  
![Junte-se entre cs e mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
O objetivo é ver se há um objeto já no metaverso, criado por outro Conector, deve ser associado. Por exemplo, numa floresta de recursos de conta, o utilizador da floresta de conta deve ser acompanhado pelo utilizador da floresta de recursos.

As juntas são usadas principalmente em regras de entrada para juntar objetos espaciais conector ao mesmo objeto metaverso.

As juntas são definidas como um ou mais grupos. Dentro de um grupo, tem cláusulas. Um elógico e é usado entre todas as cláusulas de um grupo. Um OR lógico é usado entre grupos. Os grupos são processados de cima para baixo. Quando um grupo encontra exatamente um fósforo com um objeto no alvo, então nenhuma outra regra de adesão é avaliada. Se for encontrado zero ou mais do que um objeto, o processamento continua para o próximo grupo de regras. Por esta razão, as regras devem ser criadas na ordem dos mais explícitos primeiro e mais confusos no final.  
![Aderir à definição](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
As juntas nesta imagem são processadas de cima para baixo. Primeiro, o gasoduto de sincronização vê se há uma correspondência no id do empregado. Caso contrário, a segunda regra vê se o nome da conta pode ser usado para unir os objetos. Se isso também não for compatível, a terceira e última regra é uma correspondência mais confusa utilizando o nome do utilizador.

Se todas as regras de adesão tiverem sido avaliadas e não houver exatamente uma correspondência, o **Link Type** na página **Descrição** é utilizado. Se esta opção for definida para **a Provision,** então é criado um novo objeto no alvo.  
![Provisão ou adesão](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Um objeto deve ter apenas uma regra de sincronização com regras de adesão no âmbito. Se houver várias regras de sincronização onde a adesão é definida, ocorre um erro. Precedência não é usada para resolver conflitos de adesão. Um objeto deve ter uma regra de adesão no âmbito para que os atributos fluam com a mesma direção de entrada/saída. Se precisar de fluir atribui a deentrada e saída ao mesmo objeto, deve ter uma regra de sincronização de entrada e de saída com a adesão.

A adesão à saída tem um comportamento especial quando tenta fornecer um objeto a um espaço de conector alvo. O atributo DN é usado para primeiro tentar uma inversão de marcha atrás. Se já houver um objeto no espaço do conector alvo com o mesmo DN, os objetos são unidos.

O módulo de adesão só é avaliado uma vez quando uma nova regra de sincronização entra em vigor. Quando um objeto se juntou, não é desaderido mesmo que os critérios de adesão já não estejam satisfeitos. Se quiser desjuntar-se de um objeto, a regra de sincronização que juntou os objetos deve ficar fora de alcance.

### <a name="metaverse-delete"></a>Aeliminar metaversos
Um objeto metaverso permanece enquanto houver uma regra de sincronização no âmbito com o Tipo de **Ligação** definido para **Provision** ou **StickyJoin**. Um StickyJoin é utilizado quando um Conector não é autorizado a fornecer um novo objeto ao metaverso, mas quando este se juntou, deve ser eliminado na fonte antes de o objeto metaverso ser eliminado.

Quando um objeto metaverso é eliminado, todos os objetos associados a uma regra de sincronização de saída marcada para **a disposição** são marcados para uma eliminação.

## <a name="transformations"></a>Transformações
As transformações são usadas para definir como os atributos devem fluir da fonte para o alvo. Os fluxos podem ter um dos **seguintes tipos**de fluxo: Direto, Constante ou Expressão. Um fluxo direto, flui um valor de atributo como é sem transformações adicionais. Um valor constante define o valor especificado. Uma expressão usa a linguagem de expressão de disposição declarativa para expressar como deve ser a transformação. Os detalhes para a linguagem de expressão podem ser encontrados no tópico de linguagem de [expressão declarativa de compreensão.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

![Provisão ou adesão](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

A caixa de verificação **Apply uma vez** define que o atributo só deve ser definido quando o objeto é inicialmente criado. Por exemplo, esta configuração pode ser usada para definir uma palavra-passe inicial para um novo objeto de utilizador.

### <a name="merging-attribute-values"></a>Fusão de valores de atributo
Nos fluxos de atributo saem-se para determinar se os atributos de vários valores devem ser fundidos a partir de vários Conectores diferentes. O valor predefinido é **Update**, que indica que a regra de sincronização com maior precedência deve ganhar.

![Tipos de fusão](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Há também **fusão** e **fusãoCaseInsensitive**. Estas opções permitem-lhe fundir valores de diferentes fontes. Por exemplo, pode ser usado para fundir o membro ou proxyAddresses atribuide de várias florestas diferentes. Quando utilizar esta opção, todas as regras de sincronização no âmbito de um objeto devem utilizar o mesmo tipo de fusão. Não é possível definir **a Atualização** de um Conector e **fundir-se** de outro. Se tentar, receberá um erro.

A diferença entre **a Fusão** e a **MergeCaseInsensitive** é como processar os valores de atributo duplicados. O motor sincronizado assegura que os valores duplicados não são inseridos no atributo-alvo. Com **a MergeCaseInsensitive,** duplicar valores com apenas uma diferença no caso de não estarem presentes. Por exemplo, não deveSMTP:bob@contoso.comver tantosmtp:bob@contoso.com" como " " no atributo alvo. **A fusão** está apenas a olhar para os valores exatos e múltiplos valores em que só há uma diferença no caso de estar presente.

A opção **Substituir** é a mesma que **a Atualização,** mas não é utilizada.

### <a name="control-the-attribute-flow-process"></a>Controlar o processo de fluxo de atributos
Quando várias regras de sincronização de entrada são configuradas para contribuir para o mesmo atributo metaverso, então a precedência é usada para determinar o vencedor. A regra de sincronização com maior precedência (valor numérico mais baixo) vai contribuir com o valor. O mesmo acontece com as regras de saída. A regra de sincronização com a maior precedência vence e contribui com o valor para o diretório conectado.

Em alguns casos, em vez de contribuir com um valor, a regra de sincronização deve determinar como devem ser as outras regras. Há alguns literais especiais usados para este caso.

Para as Regras de Sincronização de Entrada, o **NULO** literal pode ser usado para indicar que o fluxo não tem qualquer valor para contribuir. Outra regra com precedência mais baixa pode contribuir com um valor. Se nenhuma regra contribuiu com um valor, então o atributo metaverso é removido. Para uma regra de saída, se o **NULO** é o valor final depois de todas as regras de sincronização terem sido processadas, então o valor é removido no diretório conectado.

O **Autor** literal Null é semelhante ao **NULO,** mas com a diferença de que nenhuma regra de precedência inferior pode contribuir com um valor.

Um fluxo de atributo também pode usar **IgnoreThisFlow**. É semelhante ao NULO no sentido em que indica que não há nada a contribuir. A diferença é que não remove um valor já existente no alvo. É como se o fluxo de atributonunca tivesse estado lá.

Segue-se um exemplo:

In *out to AD - User Exchange híbrido* o seguinte fluxo pode ser encontrado:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Esta expressão deve ser lida como: se a caixa de correio do utilizador estiver localizada em Azure AD, então flua o atributo de Azure AD para AD. Caso contrário, não desloque nada para o Diretório Ativo. Neste caso, manteria o valor existente em AD.

### <a name="importedvalue"></a>ImportedValue
A função ImportedValue é diferente de todas as outras funções, uma vez que o nome do atributo deve ser incluído em cotações em vez de suportes quadrados:  
`ImportedValue("proxyAddresses")`.

Normalmente durante a sincronização um atributo usa o valor esperado, mesmo que ainda não tenha sido exportado ou tenha sido recebido um erro durante a exportação ("topo da torre"). Uma sincronização de entrada pressupõe que um atributo que ainda não chegou a um diretório conectado eventualmente chega-lhe. Em alguns casos, é importante apenas sincronizar um valor que foi confirmado pelo diretório conectado ("holograma e torre de importação delta").

Um exemplo desta função pode ser encontrado na regra de sincronização fora da caixa *em a partir de AD – User Common from Exchange*. Na Hybrid Exchange, o valor acrescentado pela Exchange online só deve ser sincronizado quando se confirmar que o valor foi exportado com sucesso:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedência
Quando várias regras de sincronização tentam contribuir com o mesmo valor de atributo para o alvo, o valor de precedência é usado para determinar o vencedor. A regra com maior precedência, valor numérico mais baixo, vai contribuir com o atributo num conflito.

![Tipos de fusão](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Esta encomenda pode ser usada para definir fluxos de atributos mais precisos para um pequeno subconjunto de objetos. Por exemplo, as regras fora da caixa certificam-se de que os atributos de uma conta ativada **(Conta de UtilizadorActivada**) têm precedência de outras contas.

A precedência pode ser definida entre Conectores. Isto permite que os Conectores com melhores dados contribuam primeiro com valores.

### <a name="multiple-objects-from-the-same-connector-space"></a>Múltiplos objetos do mesmo espaço do conector
Se tiver vários objetos no mesmo espaço de conector unidos ao mesmo objeto metaverso, a precedência deve ser ajustada. Se vários objetos estiverem no âmbito da mesma regra de sincronização, então o motor de sincronização não é capaz de determinar precedência. É ambíguo qual objeto de origem deve contribuir com o valor para o metaverso. Esta configuração é reportada como ambígua mesmo que os atributos na fonte tenham o mesmo valor.  
![Múltiplos objetos unidos ao mesmo objeto mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Para este cenário, é necessário alterar o âmbito das regras de sincronização para que os objetos de origem tenham diferentes regras de sincronização no âmbito. Isso permite-lhe definir precedências diferentes.  
![Múltiplos objetos unidos ao mesmo objeto mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre a linguagem de expressão na compreensão das expressões de [disposição declarativas.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* Veja como o provisionamento declarativo é usado fora da caixa na [compreensão da configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md).
* Veja como fazer uma alteração prática usando o fornecimento declarativo em [Como fazer uma alteração à configuração padrão](how-to-connect-sync-change-the-configuration.md).
* Continuar a ler como os utilizadores e contactos trabalham em conjunto na [Compreensão de Utilizadores e Contactos.](concept-azure-ad-connect-sync-user-and-contacts.md)

**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

**Tópicos de referência**

* [Sincronização azure AD Connect: Referência de funções](reference-connect-sync-functions-reference.md)
