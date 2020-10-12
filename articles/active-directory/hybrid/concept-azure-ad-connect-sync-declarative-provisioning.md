---
title: 'Azure AD Connect: Understanding Declarative Provisioning / Microsoft Docs'
description: Explica o modelo de configuração declarativo de provisionamento no Azure AD Connect.
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
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313625"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect sync: Understanding Declarative Provisioning
Este tópico explica o modelo de configuração no Azure AD Connect. O modelo chama-se Provisionamento Declarativo e permite-lhe fazer uma alteração de configuração com facilidade. Muitas coisas descritas neste tópico são avançadas e não são necessárias para a maioria dos cenários do cliente.

## <a name="overview"></a>Descrição geral
O provisionamento declarativo é processar objetos vindos de um diretório ligado à fonte e determina como o objeto e os atributos devem ser transformados de uma fonte para um alvo. Um objeto é processado num gasoduto de sincronização e o gasoduto é o mesmo para regras de entrada e saída. Uma regra de entrada é de um espaço de conector para o metaverso e uma regra de saída é do metaverso para um espaço de conector.

![Diagrama que mostra um exemplo de pipeline de sincronização.](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

O oleoduto tem vários módulos diferentes. Cada um é responsável por um conceito de sincronização de objetos.

![Diagrama que mostra os módulos na conduta.](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Fonte, O objeto de origem
* [Âmbito](#scope), Encontra todas as regras de sincronização que estão no âmbito
* [Junte-se,](#join)Determine a relação entre o espaço do conector e o metaverso
* Transforme, Calcule como os atributos devem ser transformados e fluir
* [Precedência](#precedence), Resolve contribuições de atributos contraditórios
* Alvo, o objeto alvo

## <a name="scope"></a>Âmbito
O módulo de âmbito está a avaliar um objeto e determina as regras que estão no âmbito e devem ser incluídas no processamento. Dependendo dos valores de atributos no objeto, diferentes regras de sincronização são avaliadas para estarem no âmbito. Por exemplo, um utilizador desativado sem caixa de correio Exchange tem regras diferentes das de um utilizador ativado com uma caixa de correio.  
![Diagrama que mostra o módulo de âmbito para um objeto.](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

O âmbito é definido como grupos e cláusulas. As cláusulas estão dentro de um grupo. Um E lógico é usado entre todas as cláusulas de um grupo. Por exemplo, (departamento =IT AND country = Dinamarca). Um OR lógico é usado entre grupos.

![Âmbito](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
O âmbito desta imagem deve ser lido como (departamento = IT AND country = Dinamarca) OR (country=Suécia). Se um dos grupos 1 ou 2 for avaliado como verdadeiro, então a regra está no âmbito.

O módulo de âmbito suporta as seguintes operações.

| Operação | Descrição |
| --- | --- |
| IGUAL, NOTAQUAL |Uma cadeia compara que avalia se o valor é igual ao valor no atributo. Para obter atributos multi-valorizados, consulte ISIN e ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Uma cadeia compara que avalia se o valor é inferior ao valor no atributo. |
| CONTÉM, NOTCONTAINS |Uma cadeia compara que avalia se o valor pode ser encontrado em algum lugar dentro do valor no atributo. |
| STARTWITH, NOTSTARTSWITH |Uma cadeia compara que avalia se o valor está no início do valor no atributo. |
| ENDSWITH, NOTENDWITH |Uma cadeia compara que avalia se o valor está no final do valor no atributo. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Uma cadeia compara que avalia se o valor é maior do que o valor no atributo. |
| ISNULL |Avalia se o atributo está ausente do objeto. Se o atributo não estiver presente e, portanto, nulo, então a regra está no âmbito. |
| ISIN |Avalia se o valor está presente no atributo definido. Esta operação é a variação multi-valorizada de EQUAL e NOTEQUAL. O atributo é suposto ser um atributo multi-valor e se o valor pode ser encontrado em qualquer um dos valores do atributo, então a regra está no âmbito. |
| ISBITSET, ISNOTBITSET |Avalia se um determinado bit é definido. Por exemplo, pode ser usado para avaliar os bits no userAccountControl para ver se um utilizador está ativado ou desativado. |
| ISMEMBEROF, ISNOTMEMBEROF |O valor deve conter um DN para um grupo no espaço do conector. Se o objeto for um membro do grupo especificado, a regra está no âmbito. |

## <a name="join"></a>Associar
O módulo de junção no pipeline de sincronização é responsável por encontrar a relação entre o objeto na fonte e um objeto no alvo. Numa regra de entrada, esta relação seria um objeto num espaço de conector encontrando uma relação com um objeto no metaverso.  
![Junte-se entre cs e mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
O objetivo é ver se já há algum objeto no metaverso, criado por outro Conector, que deve ser associado. Por exemplo, numa floresta de recursos de conta, o utilizador da floresta de conta deve ser acompanhado pelo utilizador da floresta de recursos.

As juntas são usadas principalmente em regras de entrada para juntar objetos espaciais de conector ao mesmo objeto metaverso.

As junções são definidas como um ou mais grupos. Dentro de um grupo, tens cláusulas. Um E lógico é usado entre todas as cláusulas de um grupo. Um OR lógico é usado entre grupos. Os grupos são processados de cima para baixo. Quando um grupo encontra exatamente um fósforo com um objeto no alvo, então nenhuma outra regra de junção é avaliada. Se for encontrado zero ou mais de um objeto, o processamento continua para o próximo grupo de regras. Por esta razão, as regras devem ser criadas na ordem do mais explícito primeiro e mais confuso no final.  
![Aderir à definição](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
As juntas nesta imagem são processadas de cima para baixo. Primeiro, o pipeline de sincronização vê se há uma correspondência no STAFF dos empregados. Caso contrário, a segunda regra verifica se o nome da conta pode ser usado para juntar os objetos. Se isso também não for compatível, a terceira e última regra é uma correspondência mais confusa usando o nome do utilizador.

Se todas as regras de junção tiverem sido avaliadas e não houver exatamente uma correspondência, o **Tipo de Link** na página **Descrição** é utilizado. Se esta opção for definida como **Provisão,** então é criado um novo objeto no alvo.  
![Screenshot que mostra o menu suspenso "Link Type" aberto.](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Um objeto deve ter apenas uma regra de sincronização única com regras de junção no âmbito. Se houver várias regras de sincronização onde a junção é definida, ocorre um erro. A precedência não é usada para resolver conflitos. Um objeto deve ter uma regra de união no âmbito para que os atributos fluam com a mesma direção de entrada/saída. Se precisar de fluir atributos tanto de entrada como de saída para o mesmo objeto, deve ter uma regra de entrada e sincronização de saída com união.

A união de saída tem um comportamento especial quando tenta providenciar um objeto para um espaço de conector alvo. O atributo DN é usado para primeiro tentar uma junção inversa. Se já houver um objeto no espaço do conector alvo com o mesmo DN, os objetos são unidos.

O módulo de junção só é avaliado uma vez quando uma nova regra de sincronização entra em vigor. Quando um objeto se juntou, não se despreomia mesmo que os critérios de adesão já não estivessem satisfeitos. Se quiser desacoplar um objeto, a regra de sincronização que uniu os objetos deve ficar fora de alcance.

### <a name="metaverse-delete"></a>Metaverso apagar
Um objeto metaverso permanece enquanto houver uma regra de sincronização no âmbito com **o Tipo de Ligação** definido para **Provision** ou **StickyJoin**. Um StickyJoin é utilizado quando um Conector não é autorizado a forneça um novo objeto ao metaverso, mas quando se junta, deve ser eliminado na fonte antes de o objeto metaverso ser eliminado.

Quando um objeto metaverso é eliminado, todos os objetos associados a uma regra de sincronização de saída marcada para **a disposição** estão marcados para uma eliminação.

## <a name="transformations"></a>Transformações
As transformações são usadas para definir como os atributos devem fluir da fonte para o alvo. Os fluxos podem ter um dos **seguintes tipos**de fluxo : Direto, Constante ou Expressão. Um fluxo direto, flui um valor de atributo como-é sem transformações adicionais. Um valor constante define o valor especificado. Uma expressão usa a linguagem de expressão declarativa para expressar como deve ser a transformação. Os detalhes para a linguagem de expressão podem ser encontrados no [tópico declarativo declarativo da linguagem de expressão.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

![Provisão ou aderião](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

A Caixa de verificação **Aplicar uma vez** que o atributo só deve ser definido quando o objeto for inicialmente criado. Por exemplo, esta configuração pode ser usada para definir uma palavra-passe inicial para um novo objeto de utilizador.

### <a name="merging-attribute-values"></a>Valores de atributos de fusão
Nos fluxos de atributos existe uma definição para determinar se os atributos multi-valor devem ser fundidos de vários Conectores diferentes. O valor predefinido é **Update**, o que indica que a regra de sincronização com maior precedência deve ganhar.

![Screenshot que mostra a secção "Adicionar transformações" com o menu "Merge Types" aberto.](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Há também **Merge** **e MergeCaseInsensitive**. Estas opções permitem fundir valores de diferentes fontes. Por exemplo, pode ser usado para fundir o membro ou proxyAddresses atributo de várias florestas diferentes. Quando utilizar esta opção, todas as regras de sincronização no âmbito de um objeto devem utilizar o mesmo tipo de fusão. Não é possível definir **Atualização** a partir de um Conector e **fundir-se** a partir de outro. Se tentar, receberá um erro.

A diferença entre **a** **MergeCaseInsensitive** é como processar valores de atributos duplicados. O motor de sincronização garante que os valores duplicados não são inseridos no atributo alvo. Com **a MergeCaseInsensitive,** os valores duplicados com apenas uma diferença no caso de não estarem presentes. Por exemplo, não deve ver os dois " SMTP:bob@contoso.com e " " no atributo smtp:bob@contoso.com alvo. **A fusão** está apenas a olhar para os valores exatos e múltiplos valores onde só há uma diferença no caso de estar presente.

A opção **Substituir** é a mesma que **a Atualização,** mas não é utilizada.

### <a name="control-the-attribute-flow-process"></a>Controlar o processo de fluxo de atributos
Quando várias regras de sincronização de entrada são configuradas para contribuir para o mesmo atributo metaverso, então a precedência é usada para determinar o vencedor. A regra de sincronização com maior precedência (menor valor numérico) vai contribuir com o valor. O mesmo acontece com as regras de saída. A regra de sincronização com a maior precedência vence e contribui para o valor para o diretório conectado.

Em alguns casos, em vez de contribuir com um valor, a regra da sincronização deve determinar como outras regras devem comportar-se. Há alguns literais especiais usados para este caso.

Para as Regras de Sincronização de Entrada, o **NUL** literal pode ser usado para indicar que o fluxo não tem valor para contribuir. Outra regra com menor precedência pode contribuir com um valor. Se nenhuma regra contribuiu com um valor, então o atributo metaverso é removido. Para uma regra de saída, se **o NU** É o valor final depois de todas as regras de sincronização terem sido processadas, então o valor é removido no diretório ligado.

O **literativo** literal é semelhante ao **NULL,** mas com a diferença de que nenhuma regra de precedência mais baixa pode contribuir com um valor.

Um fluxo de atributos também pode usar **IgnoreThisFlow**. É semelhante ao NULO no sentido em que indica que não há nada a contribuir. A diferença é que não remove um valor já existente no alvo. É como se o fluxo de atributos nunca tivesse estado lá.

Segue-se um exemplo:

In *out to AD - User Exchange híbrido* o seguinte fluxo pode ser encontrado:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Esta expressão deve ser lida como: se a caixa de correio do utilizador estiver localizada em Azure AD, em seguida, fluir o atributo de Azure AD para AD. Caso contrário, não desacorda nada para o Ative Directory. Neste caso, manteria o valor existente em AD.

### <a name="importedvalue"></a>Valor Importado
A função ImportedValue é diferente de todas as outras funções, uma vez que o nome do atributo deve ser incluído em orçamentos em vez de parênteses quadrados:  
`ImportedValue("proxyAddresses")`.

Normalmente durante a sincronização, um atributo utiliza o valor esperado, mesmo que ainda não tenha sido exportado ou tenha sido recebido um erro durante a exportação ("topo da torre"). Uma sincronização de entrada pressupõe que um atributo que ainda não chegou a um diretório conectado eventualmente o alcança. Em alguns casos, é importante apenas sincronizar um valor que tenha sido confirmado pelo diretório conectado ("holograma e torre de importação delta").

Um exemplo desta função pode ser encontrado na regra de sincronização fora de caixa *a partir de AD – Utilizador Comum de Troca.* Na Hybrid Exchange, o valor acrescentado por Exchange online só deve ser sincronizado quando se confirmar que o valor foi exportado com sucesso:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedência
Quando várias regras de sincronização tentam contribuir com o mesmo valor de atributo para o alvo, o valor de precedência é usado para determinar o vencedor. A regra com maior precedência, o menor valor numérico, vai contribuir com o atributo num conflito.

![Tipos de fusão](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Esta encomenda pode ser usada para definir fluxos de atributos mais precisos para um pequeno subconjunto de objetos. Por exemplo, as regras fora de caixa certificam-se de que os atributos de uma conta ativada **(User AccountEnabled)** têm precedência de outras contas.

A precedência pode ser definida entre conectores. Isto permite que os Conectores com melhores dados contribuam primeiro com valores.

### <a name="multiple-objects-from-the-same-connector-space"></a>Vários objetos do mesmo espaço de conector
Se tiver vários objetos no mesmo espaço de conector ligados ao mesmo objeto metaverso, a precedência deve ser ajustada. Se vários objetos estiverem no âmbito da mesma regra de sincronização, então o motor de sincronização não é capaz de determinar a precedência. É ambíguo qual objeto de origem deve contribuir com o valor para o metaverso. Esta configuração é reportada como ambígua mesmo que os atributos na fonte tenham o mesmo valor.  
![Diagrama que mostra múltiplos objetos unidos ao mesmo objeto mv com uma sobreposição transparente de X vermelho. ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Para este cenário, é necessário alterar o âmbito das regras de sincronização para que os objetos de origem tenham diferentes regras de sincronização no âmbito. Isso permite-lhe definir diferentes precedências.  
![Vários objetos unidos ao mesmo objeto mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre a linguagem de expressão em [Compreensão Declarativas Expressões.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* Veja como é utilizada a provisão declarativa fora da caixa na [compreensão da configuração padrão](concept-azure-ad-connect-sync-default-configuration.md).
* Veja como fazer uma alteração prática utilizando provisionamento declarativo em [Como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md).
* Continuar a ler como os utilizadores e os contactos funcionam em conjunto na [Compreensão dos Utilizadores e Contactos.](concept-azure-ad-connect-sync-user-and-contacts.md)

**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

**Tópicos de referência**

* [Azure AD Connect Sync: Funções Referência](reference-connect-sync-functions-reference.md)
