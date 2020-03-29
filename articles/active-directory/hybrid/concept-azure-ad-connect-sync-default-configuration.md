---
title: 'Sincronização Azure AD Connect: Compreender a configuração padrão / Microsoft Docs'
description: Este artigo descreve a configuração predefinida no sincronizado Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548871"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: entender a configuração predefinida
Este artigo explica as regras de configuração fora da caixa. Documenta as regras e como estas regras impactam a configuração. Também o acompanha através da configuração padrão do sincronizado Azure AD Connect. O objetivo é que o leitor compreenda como o modelo de configuração, chamado disposição declarativa, está a funcionar num exemplo real. Este artigo pressupõe que já instalou e configurou a sincronização Azure AD Connect utilizando o assistente de instalação.

Para compreender os detalhes do modelo de configuração, leia [o Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras fora da caixa do local para a AD Azure
As seguintes expressões podem ser encontradas na configuração fora da caixa.

### <a name="user-out-of-box-rules"></a>Regras do utilizador fora da caixa
Estas regras aplicam-se também ao tipo de objeto iNetOrgPerson.

Um objeto de utilizador deve satisfazer o seguinte a sincronizar:

* Deve ter uma fonte Anchor.
* Depois de o objeto ter sido criado em Azure AD, então a fonte Anchor não pode mudar. Se o valor for alterado no local, o objeto para de sincronizar até que a fonteAnchor seja alterada de volta para o seu valor anterior.
* Deve ter o atributo da contaEnabled (userAccountControl) povoado. Com um Diretório Ativo no local, este atributo está sempre presente e povoado.

Os seguintes objetos de utilizador **não** são sincronizados com a AD Azure:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que muitos objetos fora da caixa no Diretório Ativo, como a conta de administrador incorporado, não são sincronizados.
* `IsPresent([sAMAccountName]) = False`. Certifique-se de que os objetos do utilizador sem atributo sAMAccountName não estão sincronizados. Este caso só aconteceria praticamente num domínio atualizado a partir de NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço utilizada pelo sincronia Azure AD Connect e as suas versões anteriores.
* Não sincronize contas de Câmbio que não funcionem no Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Não sincronize objetos que não funcionem no Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Esta máscara bitmask (&H21C07000) filtraria os seguintes objetos:
  * Pasta Pública ativada por correio (Em Pré-visualização a partir da versão 1.1.524.0)
  * Caixa de correio assistente de sistema
  * Caixa de correio (Caixa de Correio do Sistema)
  * Universal Security Group (não se candidataria a um utilizador, mas está presente por razões antigas)
  * Grupo Não Universal (não se candidataria a um utilizador, mas está presente por razões antigas)
  * Plano caixa de correio
  * Discovery Mailbox
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize quaisquer objetos da vítima de replicação.

Aplicam-se as seguintes regras de atributo:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo sourceAnchor não é contribuído a partir de uma caixa de correio ligada. Presume-se que, se for encontrada uma caixa de correio ligada, a conta real é aderida mais tarde.
* Os atributos relacionados com a troca só são sincronizados se o **maile nickname** tiver um valor.
* Quando existem múltiplas florestas, os atributos são consumidos na seguinte ordem:
  1. Os atributos relacionados com o insessão (por exemplo, userPrincipalName) são contribuídos da floresta com uma conta ativada.
  2. Os atributos que podem ser encontrados numa Exchange GAL (Global Address List) são contribuídos da floresta com uma Caixa de Correio saem.
  3. Se não for encontrada nenhuma caixa de correio, então estes atributos podem vir de qualquer floresta.
  4. Os atributos relacionados com a troca (atributos `mailNickname ISNOTNULL`técnicos não visíveis na GAL) são contribuídos a partir da floresta onde .
  5. Se existem múltiplas florestas que satisfaçam uma destas regras, então a ordem de criação (data/hora) dos Conectores (florestas) é usada para determinar que floresta contribui com os atributos. A primeira floresta ligada será a primeira floresta a sincronizar. 

### <a name="contact-out-of-box-rules"></a>Contacte regras fora da caixa
Um objeto de contacto deve satisfazer o seguinte a sincronizar:

* O contacto deve ser ativado por correio. Verifica-se com as seguintes regras:
  * `IsPresent([proxyAddresses]) = True)`. O atributo proxyAddresses deve ser povoado.
  * Um endereço de e-mail primário pode ser encontrado no atributo proxyAddresss ou no atributo de correio. A presença \@ de um é usada para verificar se o conteúdo é um endereço de e-mail. Uma destas duas regras deve ser avaliada para true.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Há alguma entrada com "SMTP:" e se \@ houver, pode ser encontrado um na corda?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. O atributo do correio é povoado \@ e, se for, pode um ser encontrado na corda?

Os seguintes objetos de contacto **não** são sincronizados com a AD Azure:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que não estão sincronizados objetos de contacto marcados como críticos. Não deve ser qualquer com uma configuração padrão.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Estes objetos não funcionariam no Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize quaisquer objetos da vítima de replicação.

### <a name="group-out-of-box-rules"></a>Regras fora da caixa de grupo
Um objeto de grupo deve satisfazer o seguinte a sincronizar:

* Deve ter menos de 50.000 membros. Esta contagem é o número de membros do grupo no local.
  * Se tiver mais membros antes da sincronização começar pela primeira vez, o grupo não está sincronizado.
  * Se o número de membros crescer a partir do momento em que foi criado inicialmente, então quando atinge os 50.000 membros deixa de sincronizar até que a contagem de membros seja inferior a 50.000 novamente.
  * Nota: A contagem de 50.000 membros também é aplicada pela Azure AD. Não é capaz de sincronizar grupos com mais membros, mesmo que modifique ou remova esta regra.
* Se o grupo é um Grupo de **Distribuição,** então também deve ser ativado por correio. Consulte [as regras de contacto fora da caixa](#contact-out-of-box-rules) para esta regra.

Os seguintes objetos de grupo **não** são sincronizados com a AD Azure:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que muitos objetos fora da caixa em Diretório Ativo, como o grupo de administradores incorporados, não são sincronizados.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo legado usado pelo DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de Papéis.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize quaisquer objetos da vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regras fora da caixa do ForeignSecurityPrincipal
Os FSPs juntam-se a\*"qualquer" ( ) objeto no metaverso. Na realidade, esta adesão só acontece para utilizadores e grupos de segurança. Esta configuração garante que as adesões cross-forest sejam resolvidas e representadas corretamente em Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras fora da caixa de computador
Um objeto de computador deve satisfazer o seguinte a sincronizar:

* `userCertificate ISNOTNULL`. Apenas os computadores windows 10 povoam este atributo. Todos os objetos de computador com um valor neste atributo são sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Compreender o cenário de regras fora da caixa
Neste exemplo, estamos a utilizar uma implantação com uma floresta de uma conta (A), uma floresta de recursos (R) e um diretório Azure AD.

![Imagem com descrição do cenário](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Nesta configuração, presume-se que existe uma conta ativada na floresta de conta e uma conta desativada na floresta de recursos com uma caixa de correio ligada.

O nosso objetivo com a configuração padrão é:

* Os atributos relacionados com o insessão são sincronizados a partir da floresta com a conta ativada.
* Os atributos que podem ser encontrados na GAL (Global Address List) são sincronizados a partir da floresta com a caixa de correio. Se não for encontrada nenhuma caixa de correio, qualquer outra floresta é usada.
* Se for encontrada uma caixa de correio ligada, a conta ativada ligada deve ser encontrada para que o objeto seja exportado para a AD Azure.

### <a name="synchronization-rule-editor"></a>Editor de Regras de Sincronização
A configuração pode ser visualizada e alterada com a ferramenta Synchronization Rules Editor (SRE) e um atalho para ele pode ser encontrado no menu inicial.

![Ícone do editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

O SRE é uma ferramenta de kit de recursos e está instalado com sincronização Azure AD Connect. Para poder iniciá-lo, deve ser membro do grupo ADSyncAdmins. Quando começa, vê-se algo assim:

![Regras de sincronização Inbound](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Neste painel, você vê todas as Regras de Sincronização criadas para a sua configuração. Cada linha na tabela é uma Regra de Sincronização. À esquerda sob os tipos de regra, os dois tipos diferentes estão listados: Entrada e Saída. A entrada e a saída são da vista do metaverso. Vai concentrar-se principalmente nas regras de entrada nesta visão geral. A lista real de Regras de Sincronização depende do esquema detetado em AD. Na imagem acima, a floresta de conta (fabrikamonline.com) não dispõe de quaisquer serviços, como o Exchange e o Lync, e não foram criadas regras de sincronização para estes serviços. No entanto, na floresta de recursos (res.fabrikamonline.com) encontra regras de sincronização para estes serviços. O conteúdo das regras é diferente dependendo da versão detetada. Por exemplo, numa implantação com o Exchange 2013 existem mais fluxos de atributos configurados do que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra de sincronização
Uma Regra de Sincronização é um objeto de configuração com um conjunto de atributos fluindo quando uma condição é satisfeita. Também é usado para descrever como um objeto num espaço de conector está relacionado com um objeto no metaverso, conhecido como **unir** ou **combinar**. As Regras de Sincronização têm um valor de precedência indicando como se relacionam entre si. Uma Regra de Sincronização com um valor numérico mais baixo tem uma precedência maior e num conflito de fluxo de atributos, maior precedência ganha a resolução de conflitos.

Como exemplo, veja a Regra de Sincronização em AD – Conta de **UtilizadorActivada**. Marque esta linha no SRE e **selecione Editar**.

Como esta regra é uma regra fora da caixa, recebes um aviso quando abres a regra. Não deve fazer [alterações às regras fora da caixa,](how-to-connect-sync-best-practices-changing-default-configuration.md)por isso é-lhe perguntado quais são as suas intenções. Neste caso, só quer ver a regra. Selecione **Não**.

![Aviso de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Uma Regra de Sincronização tem quatro secções de configuração: Descrição, filtro de deteção, regras de adesão e transformações.

#### <a name="description"></a>Descrição
A primeira secção fornece informações básicas, tais como um nome e descrição.

![Separador de descrição no editor de regras de Sync](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Também encontra informações sobre o sistema conectado a que se refere esta regra, a que tipo de objeto no sistema conectado se aplica e ao tipo de objeto metaverso. O tipo de objeto metaverso é sempre pessoa independentemente de o tipo de objeto de origem ser um utilizador, iNetOrgPerson ou contacto. O tipo de objeto metaverso nunca deve mudar, pelo que é criado como um tipo genérico. O Link Type pode ser definido para aderir, StickyJoin ou Provision. Este cenário funciona em conjunto com a secção Regras de União e é abordado posteriormente.

Também pode ver que esta regra de sincronização é usada para sincronização de palavras-passe. Se um utilizador estiver no âmbito desta regra de sincronização, a palavra-passe é sincronizada desde as instalações até à nuvem (assumindo que ativou a função de sincronização de palavra-passe).

#### <a name="scoping-filter"></a>Filtro de deteção
A secção filtro de deteção é utilizada para configurar quando uma regra de sincronização deve ser aplicada. Uma vez que o nome da Regra de Sincronização que está a analisar indica que só deve ser aplicado para utilizadores ativados, o âmbito está configurado para que o utilizador atributo AD **AccountControl** não tenha o conjunto bit 2. Quando o motor de sincronização encontra um utilizador em AD, aplica esta regra de sincronização quando o **utilizadorAccountControl** é definido para o valor decimal 512 (utilizador normal ativado). Não aplica a regra quando o utilizador tem o **utilizador AccountControl** definido para 514 (utilizador normal desativado).

![Scoping tab no editor de regras sync](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

O filtro de deteção tem Grupos e Cláusulas que podem ser aninhados. Todas as cláusulas dentro de um grupo devem estar satisfeitas para que uma Regra de Sincronização seja aplicável. Quando vários grupos são definidos, pelo menos um grupo deve estar satisfeito para que a regra se aplique. Ou seja, um OR lógico é avaliado entre grupos e um elógico e é avaliado dentro de um grupo. Um exemplo desta configuração pode ser encontrado na regra de sincronização de saída **para AAD – Group Join**. Existem vários grupos de filtros de sincronização, por exemplo um para grupos de segurança (`securityEnabled EQUAL True`) e um para grupos de distribuição ().`securityEnabled EQUAL False`

![Scoping tab no editor de regras sync](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Esta regra é utilizada para definir quais os grupos que devem ser provisionados à AD Azure. Os Grupos de Distribuição devem ser enviados por correio para serem sincronizados com a AD Azure, mas para grupos de segurança não é necessário um e-mail.

#### <a name="join-rules"></a>Aderir às regras
A terceira secção é usada para configurar como os objetos no espaço do conector se relacionam com objetos no metaverso. A regra que analisou anteriormente não tem qualquer configuração para Regras de Adesão, por isso, em vez disso, vai supor **in from AD – User Join**.

![Junte-se ao separador de regras no editor de regras sync](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

O conteúdo da regra de adesão depende da opção de correspondência selecionada no assistente de instalação. Para uma regra de entrada, a avaliação começa com um objeto no espaço do conector de origem e cada grupo nas regras de adesão é avaliado em sequência. Se um objeto de origem for avaliado para combinar exatamente com um objeto no metaverso usando uma das regras de adesão, os objetos são unidos. Se todas as regras tiverem sido avaliadas e não houver correspondência, então o Link Type na página de descrição é utilizado. Se esta configuração for definida para **a Provision**, então um novo objeto é criado no alvo, o metaverso, se pelo menos um atributo nos critérios de adesão estiver presente (tem um valor). Para fornecer um novo objeto ao metaverso também é conhecido como **projetar** um objeto para o metaverso.

As regras de adesão só são avaliadas uma vez. Quando um objeto espacial conector e um objeto metaverso são unidos, permanecem unidos enquanto o âmbito da Regra de Sincronização ainda estiver satisfeito.

Ao avaliar as Regras de Sincronização, apenas uma Regra de Sincronização com regras de adesão definidas deve estar no âmbito. Se várias Regras de Sincronização com regras de adesão forem encontradas para um objeto, um erro é lançado. Por esta razão, a melhor prática é ter apenas uma Regra de Sincronização com a adesão definida quando várias Regras de Sincronização estão no âmbito de um objeto. Na configuração fora da caixa para o sincronizado Azure AD Connect, estas regras podem ser encontradas olhando para o nome e encontrando as que têm a palavra **Junte-se** no final do nome. Uma Regra de Sincronização sem quaisquer regras de adesão definidas aplica os fluxos de atributo quando outra Regra de Sincronização uniu os objetos ou forreum novo objeto no alvo.

Se olhar para a imagem acima, pode ver que a regra está a tentar juntar o **objectSID** com **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), que é o que esperamos numa topologia florestal de recursos de conta. Encontras a mesma regra em todas as florestas. O pressuposto é que cada floresta pode ser uma conta ou uma floresta de recursos. Esta configuração também funciona se tiver contas que vivem numa única floresta e não têm de ser unidas.

#### <a name="transformations"></a>Transformações
A secção de transformação define todos os fluxos de atributos que se aplicam ao objeto-alvo quando os objetos são unidos e o filtro de âmbito é satisfeito. Voltando ao **In a partir de AD – Regra de** Sincronização da Conta de Utilizador, encontra as seguintes transformações:

![Guia de transformações no editor de regras sync](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Para contextualizar esta configuração, numa implantação florestal de Recursos de Conta, espera-se que encontre uma conta ativada na floresta de conta e uma conta desativada na floresta de recursos com configurações de Exchange e Lync. A Regra de Sincronização que está a analisar contém os atributos necessários para o início de sessão e estes atributos devem fluir da floresta onde existe uma conta ativada. Todos estes fluxos de atributos são reunidos numa regra de sincronização.

Uma transformação pode ter diferentes tipos: Constante, Direto e Expressão.

* Um fluxo constante sempre flui um valor codificado. No caso acima, define sempre o valor **Verdadeiro** no atributo metaverso denominado **contaEnabled**.
* Um fluxo direto flui sempre o valor do atributo na fonte para o atributo-alvo como é.
* O terceiro tipo de fluxo é expressão e permite configurações mais avançadas.

O idioma de expressão é VBA (Visual Basic for Applications), para que as pessoas com experiência no Microsoft Office ou VBScript reconheçam o formato. Os atributos são incluídos em parênteses quadrados, [nome de atributo]. Os nomes de atributos e os nomes de funções são sensíveis aos casos, mas o Editor de Regras de Sincronização avalia as expressões e fornece um aviso se a expressão não for válida. Todas as expressões são expressas numa única linha com funções aninhadas. Para mostrar o poder da linguagem de configuração, aqui está o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

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

Consulte a compreensão das expressões de [provisionamento declarativa para](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) obter mais informações sobre a linguagem de expressão para fluxos de atributos.

### <a name="precedence"></a>Precedência
Já analisou algumas regras individuais de sincronização, mas as regras funcionam em conjunto na configuração. Em alguns casos, um valor de atributo é contribuído de múltiplas regras de sincronização para o mesmo atributo-alvo. Neste caso, a precedência do atributo é usada para determinar que atributo ganha. Como exemplo, olhe para a fonte do atributoAnchor. Este atributo é um atributo importante para poder entrar na Azure AD. Pode encontrar um fluxo de atributo para este atributo em duas diferentes Regras de Sincronização, em AD – Conta de **UtilizadorActivada** e **Dentro de AD – Utilizador Comum**. Devido à prevalência da regra de sincronização, o atributo sourceAnchor é contribuído da floresta com uma conta ativada primeiro quando há vários objetos unidos ao objeto metaverso. Se não houver contas ativadas, então o motor de sincronização utiliza a regra de sincronização **catch-all in from AD – User Common**. Esta configuração garante que mesmo para contas que estão desativadas, ainda existe uma fonteAnchor.

![Regras de sincronização Inbound](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

A precedência das Regras de Sincronização é definida em grupos pelo assistente de instalação. Todas as regras de um grupo têm o mesmo nome, mas estão ligadas a diferentes diretórios conectados. O assistente de instalação dá a regra **In from AD – User Join** a maior precedência e iterates sobre todos os diretórios ad's conectados. Prossegue então com os próximos grupos de regras numa ordem predefinida. Dentro de um grupo, as regras são adicionadas na ordem em que os Conectores foram adicionados no assistente. Se outro Conector for adicionado através do assistente, as Regras de Sincronização são reordenadas e as regras do novo Connector são inseridas em último lugar em cada grupo.

### <a name="putting-it-all-together"></a>Juntar tudo
Sabemos agora o suficiente sobre regras de sincronização para sermos capazes de entender como a configuração funciona com as diferentes Regras de Sincronização. Se olhar para um utilizador e para os atributos que são contribuídos para o metaverso, as regras são aplicadas na seguinte ordem:

| Nome | Comentário |
|:--- |:--- |
| Em AD – User Join |Regra para juntar objetos espaciais de conector com metaverso. |
| Em AD – UserAccount Habilitado |Atributos necessários para a inscrição no Azure AD e office 365. Queremos estes atributos a partir da conta ativada. |
| Em AD – User Common from Exchange |Atributos encontrados na Lista de Endereços Globais. Assumimos que a qualidade dos dados é melhor na floresta onde encontramos a caixa de correio do utilizador. |
| Em AD – User Common |Atributos encontrados na Lista de Endereços Globais. Caso não encontremos uma caixa de correio, qualquer outro objeto unido pode contribuir com o valor do atributo. |
| Em AD – Permuta de Utilizadores |Só existe se o Exchange tiver sido detetado. Flui todos os atributos de troca de infraestruturas. |
| Em AD – User Lync |Só existe se o Lync tiver sido detetado. Flui todas as infraestruturas atribuiis lync. |

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o modelo de configuração em [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão na compreensão das expressões de [disposição declarativas.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* Continuar a ler Como funciona a configuração fora da caixa em [Compreender utilizadores e Contactos](concept-azure-ad-connect-sync-user-and-contacts.md)
* Veja como fazer uma alteração prática usando o fornecimento declarativo em [Como fazer uma alteração à configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

