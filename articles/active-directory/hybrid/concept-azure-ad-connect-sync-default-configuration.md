---
title: 'Sincronização de Azure AD Connect: Noções básicas sobre a configuração padrão | Microsoft Docs'
description: Este artigo descreve a configuração padrão no Azure AD Connect sincronização.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2886b842aab81732beec0fdd7957aab8e2b4f5e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548871"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: entender a configuração predefinida
Este artigo explica as regras de configuração integradas. Ele documenta as regras e como essas regras afetam a configuração. Ele também orienta você pela configuração padrão de sincronização de Azure AD Connect. O objetivo é que o leitor entenda como o modelo de configuração, chamado provisionamento declarativo, está funcionando em um exemplo do mundo real. Este artigo pressupõe que você já instalou e configurou Azure AD Connect sincronização usando o assistente de instalação.

Para entender os detalhes do modelo de configuração, leia [noções básicas sobre provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras prontas para uso do local para o Azure AD
As expressões a seguir podem ser encontradas na configuração pronta para uso.

### <a name="user-out-of-box-rules"></a>Regras prontas para uso do usuário
Essas regras também se aplicam ao tipo de objeto iNetOrgPerson.

Um objeto de usuário deve atender ao seguinte para ser sincronizado:

* Deve ter um sourceAnchor.
* Depois que o objeto tiver sido criado no Azure AD, sourceAnchor não poderá ser alterado. Se o valor for alterado no local, o objeto parará de sincronizar até que o sourceAnchor seja alterado para seu valor anterior.
* O atributo accountEnabled (userAccountControl) deve ser preenchido. Com um Active Directory local, esse atributo está sempre presente e populado.

Os seguintes objetos de usuário **não** são sincronizados com o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que muitos objetos prontos para uso no Active Directory, como a conta interna de administrador, não estejam sincronizados.
* `IsPresent([sAMAccountName]) = False`. Verifique se os objetos de usuário sem nenhum atributo sAMAccountName não estão sincronizados. Esse caso praticamente aconteceria em um domínio atualizado do NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço usada pelo Azure AD Connect a sincronização e suas versões anteriores.
* Não sincronize as contas do Exchange que não funcionariam no Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Não sincronize objetos que não funcionariam no Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Esse bitmask (& H21C07000) filtraria os seguintes objetos:
  * Pasta pública habilitada para email (em visualização a partir da versão 1.1.524.0)
  * Caixa de correio do Atendente do Sistema
  * Caixa de correio do banco de dados caixa de correio (caixa de correio
  * Grupo de segurança universal (não se aplica a um usuário, mas está presente por motivos herdados)
  * Grupo não universal (não se aplica a um usuário, mas está presente por motivos herdados)
  * Plano de caixa de correio
  * Caixa de correio de descoberta
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objeto de vítima de replicação.

As seguintes regras de atributo se aplicam:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo sourceAnchor não é contribuído por uma caixa de correio vinculada. Supõe-se que, se uma caixa de correio vinculada tiver sido encontrada, a conta real será unida mais tarde.
* Os atributos relacionados ao Exchange só serão sincronizados se o atributo **mailNickName** tiver um valor.
* Quando há várias florestas, os atributos são consumidos na seguinte ordem:
  1. Os atributos relacionados à entrada (por exemplo, userPrincipalName) são contribuídos da floresta com uma conta habilitada.
  2. Os atributos que podem ser encontrados em uma GAL do Exchange (lista de endereços global) são contribuídos da floresta com uma caixa de correio do Exchange.
  3. Se nenhuma caixa de correio puder ser encontrada, esses atributos poderão vir de qualquer floresta.
  4. Os atributos relacionados ao Exchange (atributos técnicos não visíveis na GAL) são contribuídos da floresta em que `mailNickname ISNOTNULL`.
  5. Se houver várias florestas que satisfaçam uma dessas regras, a ordem de criação (data/hora) dos conectores (florestas) será usada para determinar qual floresta contribui com os atributos. A primeira floresta conectada será a primeira floresta a ser sincronizada. 

### <a name="contact-out-of-box-rules"></a>Regras prontas para contato
Um objeto de contato deve atender ao seguinte para ser sincronizado:

* O contato deve ser habilitado para email. Ele é verificado com as seguintes regras:
  * `IsPresent([proxyAddresses]) = True)`. O atributo proxyAddresses deve ser preenchido.
  * Um endereço de email primário pode ser encontrado no atributo proxyAddresses ou no atributo mail. A presença de uma \@ é usada para verificar se o conteúdo é um endereço de email. Uma dessas duas regras deve ser avaliada como true.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Há uma entrada com "SMTP:" e, se houver, pode ser encontrada uma \@ na cadeia de caracteres?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. O atributo de email está preenchido e, se for, pode ser encontrado um \@ na cadeia de caracteres?

Os seguintes objetos de contato **não** estão sincronizados com o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que nenhum objeto de contato marcado como crítico seja sincronizado. Não deve ser qualquer um com uma configuração padrão.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Esses objetos não funcionariam no Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objeto de vítima de replicação.

### <a name="group-out-of-box-rules"></a>Regras integradas de grupo
Um objeto de grupo deve atender ao seguinte para ser sincronizado:

* Deve ter menos de 50.000 membros. Essa contagem é o número de membros no grupo local.
  * Se tiver mais membros antes de a sincronização iniciar pela primeira vez, o grupo não será sincronizado.
  * Se o número de membros aumentar de quando ele foi criado inicialmente, quando ele atingir 50.000 membros, ele parará de sincronizar até que a contagem de associação seja inferior a 50.000 novamente.
  * Observação: a contagem de associação 50.000 também é imposta pelo Azure AD. Você não poderá sincronizar grupos com mais membros mesmo se modificar ou remover essa regra.
* Se o grupo for um **grupo de distribuição**, ele também deverá ser habilitado para email. Veja [as regras de contato prontas para uso](#contact-out-of-box-rules) desta regra são impostas.

Os seguintes objetos de grupo **não** estão sincronizados com o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Verifique se muitos objetos prontos para uso no Active Directory, como o grupo interno de administradores, não estão sincronizados.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo herdado usado pelo DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de funções.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objeto de vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal regras prontas para uso
FSPs são Unidos a um objeto "any" (\*) no metaverso. Na realidade, essa junção ocorre apenas para usuários e grupos de segurança. Essa configuração garante que as associações entre florestas sejam resolvidas e representadas corretamente no Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras prontas para uso do computador
Um objeto de computador deve atender ao seguinte para ser sincronizado:

* `userCertificate ISNOTNULL`. Somente computadores Windows 10 preenchem esse atributo. Todos os objetos de computador com um valor nesse atributo são sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Compreendendo o cenário de regras prontas para uso
Neste exemplo, estamos usando uma implantação com uma floresta de conta (A), uma floresta de recursos (R) e um diretório do Azure AD.

![Imagem com descrição do cenário](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Nessa configuração, supõe-se que há uma conta habilitada na floresta da conta e uma conta desabilitada na floresta de recursos com uma caixa de correio vinculada.

Nossa meta com a configuração padrão é:

* Os atributos relacionados à entrada são sincronizados da floresta com a conta habilitada.
* Os atributos que podem ser encontrados na GAL (lista de endereços global) são sincronizados da floresta com a caixa de correio. Se nenhuma caixa de correio puder ser encontrada, qualquer outra floresta será usada.
* Se uma caixa de correio vinculada for encontrada, a conta habilitada vinculada deverá ser encontrada para o objeto a ser exportado para o Azure AD.

### <a name="synchronization-rule-editor"></a>Editor de regras de sincronização
A configuração pode ser exibida e alterada com o SRE (editor de regras de sincronização de ferramentas) e um atalho para ele pode ser encontrado no menu iniciar.

![Ícone do editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

O SRE é uma ferramenta do kit de recursos e é instalado com Azure AD Connect sincronização. Para poder iniciá-lo, você deve ser um membro do grupo ADSyncAdmins. Quando ele for iniciado, você verá algo assim:

![Regras de sincronização de entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Nesse painel, você verá todas as regras de sincronização criadas para sua configuração. Cada linha na tabela é uma regra de sincronização. À esquerda em tipos de regra, os dois tipos diferentes são listados: entrada e saída. Entrada e saída é da exibição do metaverso. Você será focalizado principalmente nas regras de entrada nesta visão geral. A lista real de regras de sincronização depende do esquema detectado no AD. Na imagem acima, a floresta da conta (fabrikamonline.com) não tem nenhum serviço, como o Exchange e o Lync, e nenhuma regra de sincronização foi criada para esses serviços. No entanto, na floresta de recursos (res.fabrikamonline.com), você encontra regras de sincronização para esses serviços. O conteúdo das regras é diferente dependendo da versão detectada. Por exemplo, em uma implantação com o Exchange 2013, há mais fluxos de atributos configurados do que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra de Sincronização
Uma regra de sincronização é um objeto de configuração com um conjunto de atributos que fluem quando uma condição é satisfeita. Ele também é usado para descrever como um objeto em um espaço conector está relacionado a um objeto no metaverso, conhecido como **junção** ou **correspondência**. As regras de sincronização têm um valor de precedência indicando como elas se relacionam entre si. Uma regra de sincronização com um valor numérico inferior tem uma precedência mais alta e em um conflito de fluxo de atributo, maior precedência vence a resolução de conflitos.

Como exemplo, examine a regra de sincronização **em do AD – usuário AccountEnabled**. Marque esta linha no SRE e selecione **Editar**.

Como essa regra é uma regra pronta para uso, você receberá um aviso ao abrir a regra. Você não deve fazer nenhuma [alteração nas regras prontas para uso](how-to-connect-sync-best-practices-changing-default-configuration.md), portanto, você é perguntado quais são suas intenções. Nesse caso, você só deseja exibir a regra. Selecione **Não**.

![Aviso de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Uma regra de sincronização tem quatro seções de configuração: descrição, filtro de escopo, regras de junção e transformações.

#### <a name="description"></a>Descrição
A primeira seção fornece informações básicas, como um nome e uma descrição.

![Guia Descrição no editor de regra de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Você também encontrará informações sobre a qual sistema conectado essa regra está relacionada, a qual tipo de objeto no sistema conectado ele se aplica e o tipo de objeto de metaverso. O tipo de objeto do metaverso é sempre uma pessoa, independentemente de o tipo de objeto de origem ser um usuário, iNetOrgPerson ou contato. O tipo de objeto do metaverso nunca deve ser alterado para que seja criado como um tipo genérico. O tipo de link pode ser definido como join, StickyJoin ou Provision. Essa configuração funciona junto com a seção regras de junção e é abordada posteriormente.

Você também pode ver que essa regra de sincronização é usada para sincronização de senha. Se um usuário estiver no escopo dessa regra de sincronização, a senha será sincronizada do local para a nuvem (supondo que você tenha habilitado o recurso de sincronização de senha).

#### <a name="scoping-filter"></a>Filtro de escopo
A seção filtro de escopo é usada para configurar quando uma regra de sincronização deve ser aplicada. Como o nome da regra de sincronização que você está vendo indica que ela só deve ser aplicada a usuários habilitados, o escopo é configurado para que o atributo do AD **userAccountControl** não deva ter o bit 2 definido. Quando o mecanismo de sincronização localizar um usuário no AD, ele aplicará essa regra de sincronização quando **userAccountControl** estiver definido como o valor decimal 512 (usuário normal habilitado). Ele não aplica a regra quando o usuário tem **userAccountControl** definido como 514 (usuário normal desabilitado).

![Guia escopo no editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

O filtro de escopo tem grupos e cláusulas que podem ser aninhados. Todas as cláusulas dentro de um grupo devem ser satisfeitas para que uma regra de sincronização seja aplicada. Quando vários grupos são definidos, pelo menos um grupo deve ser satisfeito para que a regra seja aplicada. OU seja, um OR lógico é avaliado entre grupos e um e lógico é avaliado dentro de um grupo. Um exemplo dessa configuração pode ser encontrado na regra de sincronização de saída **para o AAD – junção de grupo**. Há vários grupos de filtro de sincronização, por exemplo, um para grupos de segurança (`securityEnabled EQUAL True`) e outro para grupos de distribuição (`securityEnabled EQUAL False`).

![Guia escopo no editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Essa regra é usada para definir quais grupos devem ser provisionados no Azure AD. Os grupos de distribuição devem ser habilitados para email para serem sincronizados com o Azure AD, mas para grupos de segurança, um email não é necessário.

#### <a name="join-rules"></a>Regras de junção
A terceira seção é usada para configurar como os objetos no espaço do conector se relacionam com objetos no metaverso. A regra que você examinou anteriormente não tem nenhuma configuração para regras de junção; portanto, você vai examinar **em AD – ingresso no usuário**.

![Guia regras de junção no editor de regra de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

O conteúdo da regra de junção depende da opção de correspondência selecionada no assistente de instalação. Para uma regra de entrada, a avaliação começa com um objeto no espaço do conector de origem e cada grupo nas regras de junção é avaliado em sequência. Se um objeto de origem for avaliado para corresponder exatamente a um objeto no metaverso usando uma das regras de junção, os objetos serão Unidos. Se todas as regras tiverem sido avaliadas e não houver correspondência, o tipo de link na página descrição será usado. Se essa configuração for definida como **provisionar**, um novo objeto será criado no destino, o metaverso, se pelo menos um atributo nos critérios de junção estiver presente (tem um valor). Para provisionar um novo objeto para o metaverso também é conhecido como **projetar** um objeto para o metaverso.

As regras de junção são avaliadas apenas uma vez. Quando um objeto de espaço do conector e um objeto de metaverso são ingressados, eles permanecem Unidos, desde que o escopo da regra de sincronização ainda esteja satisfeito.

Ao avaliar as regras de sincronização, somente uma regra de sincronização com regras de junção definidas deve estar no escopo. Se várias regras de sincronização com regras de junção forem encontradas para um objeto, um erro será gerado. Por esse motivo, a prática recomendada é ter apenas uma regra de sincronização com junção definida quando várias regras de sincronização estiverem no escopo de um objeto. Na configuração pronta para uso para Azure AD Connect sincronização, essas regras podem ser encontradas examinando o nome e localizando-as com a palavra **Join** no final do nome. Uma regra de sincronização sem nenhuma regra de junção definida aplica os fluxos de atributo quando outra regra de sincronização ingressou nos objetos ou provisionou um novo objeto no destino.

Se você olhar a imagem acima, poderá ver que a regra está tentando unir **objectSid** com **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), que é o que esperamos em uma topologia de floresta de recurso de conta. Você encontra a mesma regra em todas as florestas. A suposição é que cada floresta possa ser uma floresta de conta ou de recurso. Essa configuração também funcionará se você tiver contas que residem em uma única floresta e não precisam ser Unidas.

#### <a name="transformations"></a>Transformações
A seção de transformação define todos os fluxos de atributo que se aplicam ao objeto de destino quando os objetos são ingressados e o filtro de escopo é satisfeito. Voltando à regra de sincronização do **ad – usuário AccountEnabled** , você encontrará as seguintes transformações:

![Guia transformações no editor de regra de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Para colocar essa configuração no contexto, em uma implantação de floresta de recurso de conta, é esperado encontrar uma conta habilitada na floresta da conta e uma conta desabilitada na floresta de recursos com as configurações do Exchange e do Lync. A regra de sincronização que você está examinando contém os atributos necessários para entrar e esses atributos devem fluir da floresta onde há uma conta habilitada. Todos esses fluxos de atributo são agrupados em uma regra de sincronização.

Uma transformação pode ter tipos diferentes: constante, direta e expressão.

* Um fluxo constante sempre flui um valor codificado. No caso acima, ele sempre define o valor **true** no atributo de metaverso chamado **accountEnabled**.
* Um fluxo direto sempre flui o valor do atributo na origem para o atributo de destino como está.
* O terceiro tipo de fluxo é Expression e permite configurações mais avançadas.

A linguagem de expressão é VBA (Visual Basic for Applications), de modo que as pessoas com experiência em Microsoft Office ou VBScript reconhecerão o formato. Os atributos são colocados entre colchetes, [attributeName]. Nomes de atributo e nomes de função diferenciam maiúsculas de minúsculas, mas o editor de regras de sincronização avaliará as expressões e fornecerá um aviso se a expressão não for válida. Todas as expressões são expressas em uma única linha com funções aninhadas. Para mostrar o poder da linguagem de configuração, aqui está o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Consulte [noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) para obter mais informações sobre a linguagem de expressão para fluxos de atributos.

### <a name="precedence"></a>Precedência
Agora você examinou algumas regras de sincronização individuais, mas as regras funcionam juntas na configuração. Em alguns casos, um valor de atributo é contribuído de várias regras de sincronização para o mesmo atributo de destino. Nesse caso, a precedência de atributo é usada para determinar qual atributo vence. Como exemplo, examine o atributo sourceAnchor. Esse atributo é um atributo importante para poder entrar no Azure AD. Você pode encontrar um fluxo de atributos para esse atributo em duas regras de sincronização diferentes, **em do AD – usuário AccountEnabled** e **entrada do AD – usuário comum**. Devido à precedência da regra de sincronização, o atributo sourceAnchor é contribuído da floresta com uma conta habilitada primeiro quando há vários objetos ingressados no objeto de metaverso. Se não houver contas habilitadas, o mecanismo de sincronização usará a regra de sincronização capturar tudo **no do AD – usuário comum**. Essa configuração garante que, mesmo para as contas que estão desabilitadas, ainda há um sourceAnchor.

![Regras de sincronização de entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

A precedência para regras de sincronização é definida em grupos pelo assistente de instalação. Todas as regras em um grupo têm o mesmo nome, mas elas estão conectadas a diferentes diretórios conectados. O assistente de instalação fornece a regra no AD – a precedência mais alta **da junção do usuário** e itera em todos os diretórios do AD conectados. Em seguida, ele continua com os próximos grupos de regras em uma ordem predefinida. Dentro de um grupo, as regras são adicionadas na ordem em que os conectores foram adicionados no assistente. Se outro conector for adicionado por meio do assistente, as regras de sincronização serão reordenadas e as regras do novo conector serão inseridas por último em cada grupo.

### <a name="putting-it-all-together"></a>Juntar tudo
Agora sabemos o suficiente sobre as regras de sincronização para poder entender como a configuração funciona com as diferentes regras de sincronização. Se você examinar um usuário e os atributos que são contribuídos para o metaverso, as regras serão aplicadas na seguinte ordem:

| Nome | Comentário |
|:--- |:--- |
| Entrada do AD – ingresso no usuário |Regra para unir objetos de espaço de conector com metaverso. |
| Entrada do AD – USERACCOUNT habilitada |Atributos necessários para entrar no Azure AD e no Office 365. Queremos esses atributos da conta habilitada. |
| Entrada do AD – usuário comum do Exchange |Atributos encontrados na lista de endereços global. Supomos que a qualidade dos dados é a melhor na floresta onde encontramos a caixa de correio do usuário. |
| Entrada do AD – usuário comum |Atributos encontrados na lista de endereços global. Caso não encontremos uma caixa de correio, qualquer outro objeto Unido poderá contribuir com o valor do atributo. |
| Entrada do AD – usuário Exchange |Existe somente se o Exchange foi detectado. Ele flui todos os atributos de troca de infraestrutura. |
| Entrada do AD – usuário Lync |Existe somente se o Lync foi detectado. Ele flui todos os atributos do Lync de infraestrutura. |

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração em [noções básicas sobre provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão em [noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Continue lendo como a configuração pronta para uso funciona na compreensão de [usuários e contatos](concept-azure-ad-connect-sync-user-and-contacts.md)
* Veja como fazer uma alteração prática usando o provisionamento declarativo em [como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de visão geral**

* [Sincronização de Azure AD Connect: entender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

