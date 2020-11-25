---
title: 'Azure AD Connect sync: Understanding the default configuration / Microsoft Docs'
description: Este artigo descreve a configuração padrão na sincronização Azure AD Connect.
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
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996632"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: entender a configuração predefinida
Este artigo explica as regras de configuração fora da caixa. Documenta as regras e como estas regras impactam a configuração. Também o percorre através da configuração padrão da sincronização Azure AD Connect. O objetivo é que o leitor compreenda como o modelo de configuração, chamado provisionamento declarativo, está a funcionar num exemplo do mundo real. Este artigo pressupõe que já instalou e configurar a sincronização Azure AD Connect utilizando o assistente de instalação.

Para compreender os detalhes do modelo de configuração, leia [a Compreensão Declarativa Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras fora da caixa desde as instalações até à Azure AD
As seguintes expressões podem ser encontradas na configuração fora da caixa.

### <a name="user-out-of-box-rules"></a>Regras fora da caixa do utilizador
Estas regras aplicam-se também ao tipo de objeto iNetOrgPerson.

Um objeto de utilizador deve satisfazer o seguinte para ser sincronizado:

* Deve ter uma fonte, Achor.
* Depois de o objeto ter sido criado em Azure AD, então a fonteAnchor não pode mudar. Se o valor for alterado no local, o objeto para de sincronizar até que a fonteAnchor seja alterada de volta para o seu valor anterior.
* Deve ter o atributo accountEnabled (userAccountControl) preenchido. Com um Ative Directory no local, este atributo está sempre presente e povoado.

Os seguintes objetos do utilizador **não** são sincronizados com a Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que muitos objetos fora da caixa no Ative Directory, como a conta de administrador incorporada, não estão sincronizados.
* `IsPresent([sAMAccountName]) = False`. Certifique-se de que os objetos do utilizador sem atributo sAMAccountName não estão sincronizados. Este caso só aconteceria praticamente num domínio atualizado a partir da NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço utilizada pela Azure AD Connect sincronizada e as suas versões anteriores.
* Não sincronize contas de Câmbio que não funcionariam no Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Não sincronize objetos que não funcionem no Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Este bitmask (&H21C07000) filtraria os seguintes objetos:
  * Pasta Pública ativada por correio (Em Pré-visualização a partir da versão 1.1.524.0)
  * Caixa de correio do assistente do sistema
  * Caixa de correio de correio de correio (Caixa de correio do sistema)
  * Universal Security Group (não se candidataria a um utilizador, mas está presente por razões antigas)
  * Grupo Não Universal (não se candidataria a um utilizador, mas está presente por razões antigas)
  * Plano de caixa de correio
  * Caixa de correio discovery
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize quaisquer objetos da vítima de replicação.

Aplicam-se as seguintes regras de atributos:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo SourceAnchor não é contribuído a partir de uma caixa de correio ligada. Presume-se que, se uma caixa de correio ligada tiver sido encontrada, a conta real é acompanhada mais tarde.
* Os atributos relacionados com a troca só são sincronizados se o **atributo mailNickName** tiver um valor.
* Quando há múltiplas florestas, os atributos são consumidos na seguinte ordem:
  1. Os atributos relacionados com a inscrição (por exemplo, userPrincipalName) são contribuídos da floresta com uma conta ativada.
  2. Os atributos que podem ser encontrados numa Exchange GAL (Global Address List) são contribuídos da floresta com uma caixa de correio de câmbio.
  3. Se não for encontrada nenhuma caixa de correio, então estes atributos podem vir de qualquer floresta.
  4. Os atributos relacionados com a troca (atributos técnicos não visíveis na GAL) são contribuídos da floresta onde `mailNickname ISNOTNULL` .
  5. Se existem múltiplas florestas que satisfaçam uma destas regras, então a ordem de criação (data/hora) dos Conectores (florestas) é usada para determinar que floresta contribui com os atributos. A primeira floresta ligada será a primeira floresta a sincronizar. 

### <a name="contact-out-of-box-rules"></a>Entrar em contato com regras fora da caixa
Um objeto de contacto deve satisfazer o seguinte para ser sincronizado:

* O contacto deve estar ativado por correio. Verifica-se com as seguintes regras:
  * `IsPresent([proxyAddresses]) = True)`. O atributo proxyAddresses deve ser povoado.
  * Um endereço de e-mail primário pode ser encontrado no atributo proxyAddresses ou no atributo de correio. A presença de um \@ é usada para verificar se o conteúdo é um endereço de e-mail. Uma destas duas regras tem de ser avaliada para a True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe uma entrada com "SMTP:" e se houver, pode ser encontrada uma \@ na corda?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. O atributo de correio é preenchido e, se for, pode \@ ser encontrado na cadeia?

Os seguintes objetos de contacto **não** são sincronizados com a Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que nenhum objeto de contacto marcado como crítico está sincronizado. Não deve ser nenhum com uma configuração padrão.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Estes objetos não funcionariam no Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize quaisquer objetos da vítima de replicação.

### <a name="group-out-of-box-rules"></a>Regras fora da caixa do grupo
Um objeto de grupo deve satisfazer o seguinte para ser sincronizado:

* Deve ter menos de 50.000 membros. Esta contagem é o número de membros no grupo no local.
  * Se tiver mais membros antes da sincronização começar pela primeira vez, o grupo não é sincronizado.
  * Se o número de membros crescer a partir de quando foi inicialmente criado, então quando atinge os 50.000 membros deixa de sincronizar até que a contagem de membros seja inferior a 50.000 novamente.
  * Nota: A contagem de 50.000 membros também é aplicada pela Azure AD. Não é possível sincronizar grupos com mais membros, mesmo que modifique ou remova esta regra.
* Se o grupo é um **Grupo de Distribuição,** então também deve estar ativado por correio. Consulte [as regras de contacto fora da caixa](#contact-out-of-box-rules) para esta regra é aplicada.

Os seguintes objetos de grupo **não** são sincronizados com Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que muitos objetos fora da caixa no Ative Directory, como o grupo de administradores incorporados, não estão sincronizados.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo legado usado por DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de Papéis.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize quaisquer objetos da vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regras fora da caixa do ForeignSecurityPrincipal
Os FSPs são unidos a \* "qualquer" ( ) objeto no metaverso. Na realidade, esta junção só acontece para utilizadores e grupos de segurança. Esta configuração garante que os membros transfronteiriços sejam resolvidos e representados corretamente em Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras fora da caixa de computador
Um objeto de computador deve satisfazer o seguinte para ser sincronizado:

* `userCertificate ISNOTNULL`. Apenas os computadores windows 10 preenchem este atributo. Todos os objetos de computador com um valor neste atributo são sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Compreender o cenário de regras fora da caixa
Neste exemplo, estamos a usar uma implantação com uma floresta de conta (A), uma floresta de recursos (R) e um diretório AD Azure.

![Imagem com descrição do cenário](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Nesta configuração, presume-se que existe uma conta ativada na floresta de conta e uma conta desativada na floresta de recursos com uma caixa de correio ligada.

O nosso objetivo com a configuração padrão é:

* Os atributos relacionados com a inscrição são sincronizados a partir da floresta com a conta ativada.
* Os atributos que podem ser encontrados na GAL (Global Address List) são sincronizados da floresta com a caixa de correio. Se não for encontrada nenhuma caixa de correio, qualquer outra floresta é utilizada.
* Se for encontrada uma caixa de correio ligada, a conta ativada ligada deve ser encontrada para que o objeto seja exportado para a Azure AD.

### <a name="synchronization-rule-editor"></a>Editor de Regras de Sincronização
A configuração pode ser visualizada e alterada com a ferramenta Synchronization Rules Editor (SRE) e um atalho para a presente pode ser encontrado no menu inicial.

![Ícone de editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

O SRE é uma ferramenta de kit de recursos e está instalado com sincronização Azure AD Connect. Para poder iniciá-lo, deve ser membro do grupo ADSyncAdmins. Quando começa, vê-se algo assim:

![Regras de sincronização Entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Neste painel, vê todas as Regras de Sincronização criadas para a sua configuração. Cada linha na tabela é uma Regra de Sincronização. À esquerda nos tipos de regras, os dois tipos diferentes estão listados: Entrada e Saída. Entrada e Saída é da vista do metaverso. Vai concentrar-se principalmente nas regras de entrada nesta visão geral. A lista real de Regras de Sincronização depende do esquema detetado em AD. Na imagem acima, a floresta de contas (fabrikamonline.com) não dispõe de quaisquer serviços, como o Exchange e o Lync, e não foram criadas regras de sincronização para estes serviços. No entanto, na floresta de recursos (res.fabrikamonline.com) encontra regras de sincronização para estes serviços. O conteúdo das regras é diferente consoante a versão detetada. Por exemplo, numa implantação com o Exchange 2013 há mais fluxos de atributos configurados do que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra da sincronização
Uma Regra de Sincronização é um objeto de configuração com um conjunto de atributos fluindo quando uma condição é satisfeita. Também é usado para descrever como um objeto num espaço de conector está relacionado com um objeto no metaverso, conhecido como **junção** ou **correspondência**. As Regras de Sincronização têm um valor de precedência que indica como se relacionam entre si. Uma Regra de Sincronização com um valor numérico mais baixo tem uma precedência maior e num conflito de fluxo de atributos, uma precedência superior ganha a resolução de conflitos.

Como exemplo, veja a Regra de Sincronização **Ad – User AccountEnabled**. Marque esta linha na SRE e **selecione Editar.**

Uma vez que esta regra é uma regra fora de caixa, recebes um aviso quando abres a regra. Não deve fazer [alterações às regras fora da caixa,](how-to-connect-sync-best-practices-changing-default-configuration.md)por isso, perguntam-lhe quais são as suas intenções. Neste caso, só quer ver a regra. Selecione **Não**.

![Aviso de Regras de Sincronização](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Uma Regra de Sincronização tem quatro secções de configuração: Descrição, filtro de scoping, regras de junção e transformações.

#### <a name="description"></a>Descrição
A primeira secção fornece informações básicas, como um nome e descrição.

![Separador de descrição no editor de regras de Sync](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Também encontra informações sobre o sistema ligado a que esta regra está relacionada, qual o tipo de objeto no sistema ligado a que se aplica, e o tipo de objeto metaverso. O tipo de metaverso é sempre pessoa, independentemente de o tipo de objeto de origem ser um utilizador, iNetOrgPerson ou contacto. O tipo de metaverso nunca deve mudar, pelo que é criado como um tipo genérico. O Tipo de Ligação pode ser definido para Juntar, StickyJoin ou Provision. Esta definição funciona em conjunto com a secção Regras de União e é coberta posteriormente.

Também pode ver que esta regra de sincronização é usada para sincronização de palavras-passe. Se um utilizador estiver no âmbito desta regra de sincronização, a palavra-passe é sincronizada de instalações para nuvem (assumindo que ativou a função de sincronização de palavra-passe).

#### <a name="scoping-filter"></a>Filtro de escotagem
A secção filtro de deteção é utilizada para configurar quando deve aplicar-se uma Regra de Sincronização. Uma vez que o nome da Regra de Sincronização que está a analisar indica que deve ser aplicado apenas para utilizadores ativados, o âmbito está configurado para que o utilizador de atributoS **ADControl** não tenha o conjunto bit 2. Quando o motor de sincronização encontra um utilizador em AD, aplica esta regra de sincronização quando **o utilizadorAccountControl** é definido para o valor decimal 512 (utilizador normal ativado). Não aplica a regra quando o utilizador tem **o userAccountControl** definido para 514 (utilizador normal desativado).

![Screenshot que mostra a secção "Filtrar o filtro" da janela "Editar a regra de sincronização de entrada".](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

O filtro de deteção tem Grupos e Cláusulas que podem ser aninhados. Todas as cláusulas dentro de um grupo devem ser satisfeitas para que uma Regra de Sincronização seja aplicada. Quando vários grupos são definidos, pelo menos um grupo deve ser satisfeito para que a regra se aplique. Ou seja, um OR lógico é avaliado entre grupos e um AND lógico é avaliado dentro de um grupo. Um exemplo desta configuração pode ser encontrado na regra de sincronização de saída **para a AAD – Group Join**. Existem vários grupos de filtros de sincronização, por exemplo, um para grupos de segurança e `securityEnabled EQUAL True` outro para grupos de distribuição `securityEnabled EQUAL False` ( ).

![Scoping tab in Sync rule editor](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Esta regra é utilizada para definir quais os grupos que devem ser a provisionados ao Azure AD. Os Grupos de Distribuição devem ser sincronizados com Azure AD, mas para grupos de segurança não é necessário um e-mail.

#### <a name="join-rules"></a>Aderir a regras
A terceira secção é usada para configurar como os objetos no espaço do conector se relacionam com objetos no metaverso. A regra que já analisou anteriormente não tem qualquer configuração para Regras de Junção, pelo que, em vez disso, vai olhar para **In a partir de AD – User Join**.

![Aderir ao separador regras no editor de regras do Sync](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

O conteúdo da regra de junção depende da opção de correspondência selecionada no assistente de instalação. Para uma regra de entrada, a avaliação começa com um objeto no espaço do conector de origem e cada grupo nas regras de junção é avaliado em sequência. Se um objeto de origem for avaliado para corresponder exatamente a um objeto no metaverso usando uma das regras de junção, os objetos são unidos. Se todas as regras tiverem sido avaliadas e não houver correspondência, o Link Type na página de descrição é utilizado. Se esta configuração estiver definida como **Provisão**, então um novo objeto é criado no alvo, o metaverso, se pelo menos um atributo nos critérios de adesão estiver presente (tem um valor). A disponibilização de um novo objeto ao metaverso também é conhecida como **para projetar** um objeto no metaverso.

As regras de junção só são avaliadas uma vez. Quando um objeto espacial de conector e um objeto metaverso são unidos, permanecem unidos enquanto o âmbito da Regra de Sincronização ainda estiver satisfeito.

Ao avaliar as Regras de Sincronização, apenas uma Regra de Sincronização com regras de junção definidas deve estar no âmbito. Se várias regras de sincronização com regras de junção forem encontradas para um objeto, um erro é lançado. Por esta razão, a melhor prática é ter apenas uma Regra de Sincronização com a junção definida quando várias Regras de Sincronização estão no âmbito de um objeto. Na configuração fora da caixa para a sincronização Azure AD Connect, estas regras podem ser encontradas olhando para o nome e encontrando aquelas com a palavra **Join** no final do nome. Uma Regra de Sincronização sem regras de junção definidas aplica-se os fluxos de atributos quando outra Regra de Sincronização juntou os objetos ou forenhou um novo objeto no alvo.

Se olhar para a imagem acima, pode ver que a regra está a tentar juntar **os objetosSID** com **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), que é o que esperamos numa topologia florestal de recursos de conta. Encontra-se a mesma regra em todas as florestas. O pressuposto é que cada floresta pode ser uma conta ou uma floresta de recursos. Esta configuração também funciona se tiver contas que vivem numa única floresta e não têm de ser unidas.

#### <a name="transformations"></a>Transformações
A secção de transformação define todos os fluxos de atributos que se aplicam ao objecto-alvo quando os objetos são unidos e o filtro de âmbito é satisfeito. Voltando ao In a partir de AD – Regra de Sincronização **Da Conta de UtilizadorEnabled** Synchronization, encontra as seguintes transformações:

![Separador de transformações em Sync rule editor](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Para contextualizar esta configuração, numa Account-Resource implantação florestal, espera-se que encontre uma conta ativada na floresta de conta e uma conta desativada na floresta de recursos com as configurações de Exchange e Lync. A Regra de Sincronização que está a analisar contém os atributos necessários para a entrada e estes atributos devem fluir da floresta onde existe uma conta ativada. Todos estes fluxos de atributos são montados numa Regra de Sincronização.

Uma transformação pode ter diferentes tipos: Constante, Direta e Expressão.

* Um fluxo constante flui sempre um valor codificado. No caso acima, define sempre o valor **Verdadeiro** no atributo metaverso denominado **accountEnabled**.
* Um fluxo direto flui sempre o valor do atributo na fonte para o atributo-alvo como-é.
* O terceiro tipo de fluxo é Expression e permite configurações mais avançadas.

O idioma de expressão é VBA (Visual Basic for Applications), pelo que as pessoas com experiência no Microsoft Office ou VBScript reconhecerão o formato. Os atributos são incluídos em parênteses quadrados, [atributoName]. Os nomes e nomes de funções do atributo são sensíveis a casos, mas o Editor de Regras de Sincronização avalia as expressões e fornece um aviso se a expressão não for válida. Todas as expressões são expressas numa única linha com funções aninhadas. Para mostrar o poder do idioma de configuração, aqui está o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

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

Consulte [a compreensão Expressões declarativas de provisionamento](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) para obter mais informações sobre a linguagem de expressão para fluxos de atributos.

### <a name="precedence"></a>Precedência
Já analisou algumas regras individuais de sincronização, mas as regras funcionam em conjunto na configuração. Em alguns casos, um valor de atributo é contribuído de várias regras de sincronização para o mesmo atributo alvo. Neste caso, a precedência do atributo é usada para determinar qual o atributo que vence. Como exemplo, olhe para a fonte de atributoAnchor. Este atributo é um atributo importante para poder entrar no Azure AD. Pode encontrar um fluxo de atributos para este atributo em duas regras de sincronização diferentes, **in from AD – User AccountEnabled** e **In from AD – User Common**. Devido à precedência da Regra da Sincronização, o atributo origemAnchor é contribuído a partir da floresta com uma conta ativada primeiro quando há vários objetos unidos ao objeto metaverso. Se não houver contas ativadas, o motor de sincronização utiliza a regra de sincronização catch-all **in a partir de AD – User Common**. Esta configuração garante que mesmo para contas desativadas, ainda existe uma fonteAnchor.

![Regras de sincronização Entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

A precedência das Regras de Sincronização é definida em grupo pelo assistente de instalação. Todas as regras de um grupo têm o mesmo nome, mas estão ligadas a diferentes diretórios conectados. O assistente de instalação dá a regra **In a partir de AD – User Join** precedência mais alta e itera sobre todos os diretórios de AD conectados. Em seguida, prossegue com os próximos grupos de regras numa ordem predefinida. Dentro de um grupo, as regras são adicionadas na ordem que os Conectores foram adicionados no assistente. Se for adicionado outro Conector através do assistente, as Regras de Sincronização são reordenadas e as regras do novo Conector são inseridas em último lugar em cada grupo.

### <a name="putting-it-all-together"></a>Juntar tudo
Agora sabemos o suficiente sobre as Regras de Sincronização para sermos capazes de entender como a configuração funciona com as diferentes Regras de Sincronização. Se olharmos para um utilizador e para os atributos que são contribuídos para o metaverso, as regras são aplicadas na seguinte ordem:

| Nome | Comentário |
|:--- |:--- |
| In from AD – User Join |Regra para unir objetos espaciais do conector com metaverso. |
| In a partir de AD – UserAccount Enabled |Atributos necessários para o sômin de S.A. e Microsoft 365. Queremos estes atributos a partir da conta ativada. |
| In from AD – User Common from Exchange |Atributos encontrados na Lista Global de Endereços. Assumimos que a qualidade dos dados é a melhor na floresta onde encontramos a caixa de correio do utilizador. |
| In from AD – User Common |Atributos encontrados na Lista Global de Endereços. Caso não tenhamos encontrado uma caixa de correio, qualquer outro objeto associado pode contribuir com o valor do atributo. |
| In from AD – User Exchange |Só existe se o Exchange tiver sido detetado. Flui todos os atributos de intercâmbio de infraestruturas. |
| In from AD – User Lync |Só existe se lync for detetado. Flui todas as infraestruturas que a Lync atribui. |

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração na [Compreensão declarativa.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Leia mais sobre a linguagem de expressão em [Compreensão Declarativas Expressões.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* Continuar a ler Como funciona a configuração fora da caixa na [compreensão dos utilizadores e dos contactos](concept-azure-ad-connect-sync-user-and-contacts.md)
* Veja como fazer uma alteração prática utilizando provisionamento declarativo em [Como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

