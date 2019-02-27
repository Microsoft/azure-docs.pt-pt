---
title: 'Sincronização do Azure AD Connect: Entender a configuração predefinida | Documentos da Microsoft'
description: Este artigo descreve a configuração predefinida do Azure AD Connect.
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
ms.openlocfilehash: 06f51f56de29d5e598ea74b39352d3c15bf7b375
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880636"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: Entender a configuração predefinida
Este artigo explica as regras de configuração de out-of-box. Ele documenta as regras e como estas regras afetam a configuração. Ele também explica a configuração predefinida de sincronização do Azure AD Connect. O objetivo é que o leitor compreende como o modelo de configuração, com o nome aprovisionamento declarativo, está a funcionar num exemplo real. Este artigo pressupõe que já instalou e configurar a sincronização do Azure AD Connect utilizando o Assistente de instalação.

Para entender os detalhes do modelo de configuração, leia [aprovisionamento declarativo da compreensão](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras de Out-of-box do local para o Azure AD
As seguintes expressões podem ser encontradas na configuração de out-of-box.

### <a name="user-out-of-box-rules"></a>Regras de out-of-box do utilizador
Estas regras também se aplicam ao tipo de objeto iNetOrgPerson.

Um objeto de utilizador tem de satisfazer o seguinte para ser sincronizados:

* Tem de ter um sourceAnchor.
* Depois do objeto tiver sido criado no Azure AD, não é possível alterar a sourceAnchor. Se o valor for alterado no local, o objeto para sincronização até que o sourceAnchor mudará de volta para o valor anterior.
* Deve ter o atributo accountEnabled (userAccountControl) preenchida. Com um diretório de Active Directory no local, este atributo é sempre presente e preenchida.

Os seguintes objetos de utilizador são **não** sincronizados com o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se vários objetos de out-of-box no Active Directory, tal como a conta de administrador incorporada, não estão sincronizados.
* `IsPresent([sAMAccountName]) = False`. Certifique-se de que os objetos de utilizador com nenhum atributo sAMAccountName não estão sincronizados. Neste caso, apenas praticamente aconteceria num domínio atualizado a partir do NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço utilizada do Azure AD Connect e suas versões anteriores.
* Não sincronize contas do Exchange que não funcionariam no Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Não sincronize objetos que não funcionariam no Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Esta máscara de bits (& H21C07000) seriam filtrar os seguintes objetos:
  * Capacidade de correio de pasta pública (em pré-visualização a partir da versão 1.1.524.0)
  * Caixa de correio do atendente do sistema
  * Caixa de correio do banco de dados de caixa de correio (caixa de correio do sistema)
  * Grupo de segurança universal (não se aplicam para um utilizador, mas está presente por motivos de herança)
  * Grupo de não-Universal (não se aplicam para um utilizador, mas está presente por motivos de herança)
  * Plano de caixa de correio
  * Caixa de correio de deteção
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize a quaisquer objetos de vítima de replicação.

As seguintes regras de atributo aplicam-se:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo sourceAnchor não é contribuiu de uma caixa de correio ligada. Pressupõe-se que se foi encontrada uma caixa de correio ligada, a conta real está associada mais tarde.
* Exchange relacionados com os atributos são sincronizados de apenas se o atributo **mailNickName** tem um valor.
* Quando existem várias florestas, atributos são consumidos pela seguinte ordem:
  1. Atributos relacionados com o início de sessão (por exemplo, userPrincipalName) são fornecidos da floresta com uma conta ativada.
  2. Atributos que podem ser encontrados num Exchange de GAL (lista de endereços Global) são fornecidos da floresta com uma caixa de correio do Exchange.
  3. Se nenhuma caixa de correio pode ser encontrada, em seguida, estes atributos podem vir de qualquer floresta.
  4. Exchange relacionados com atributos (atributos técnicos não é visíveis na GAL) são fornecidos da floresta onde `mailNickname ISNOTNULL`.
  5. Se existirem várias florestas que satisfazem estas regras, em seguida, a ordem de criação (data/hora) dos conectores (florestas) é utilizada para determinar quais florestas contribui os atributos.

### <a name="contact-out-of-box-rules"></a>Entre em contato com regras de out-of-box
Um objeto de contato deve satisfazer o seguinte para ser sincronizados:

* O contacto tem de ser habilitados para email. É verificado com as seguintes regras:
  * `IsPresent([proxyAddresses]) = True)`. O atributo proxyAddresses deve ser preenchido.
  * Um endereço de e-mail principal pode ser encontrado no atributo proxyAddresses do ou o atributo de correio. A presença de um \@ é usado para verificar que o conteúdo é um endereço de e-mail. Estas duas regras têm de ser avaliada como True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe uma entrada com "SMTP:" e se existir, pode uma \@ ser encontrado na cadeia de caracteres?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. É o atributo de correio preenchido e se for, pode uma \@ ser encontrado na cadeia de caracteres?

São os seguintes objetos de contatos **não** sincronizados com o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que não existem objetos de contato marcados como críticos estão sincronizados. Não deve ser qualquer um com uma configuração predefinida.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Estes objetos não funcionavam no Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize a quaisquer objetos de vítima de replicação.

### <a name="group-out-of-box-rules"></a>Grupo de regras de out-of-box
Um objeto de grupo tem de satisfazer o seguinte para ser sincronizados:

* Tem de ter menos de 50.000 membros. Esta contagem é o número de membros do grupo no local.
  * Se tiver mais membros antes de sincronização é iniciada pela primeira vez, o grupo não está sincronizado.
  * Se o número de membros crescer desde quando foi inicialmente criado, em seguida, ao chegar 50.000 membros deixa de sincronização até que a contagem de associação é inferior a 50 000 novamente.
  * Nota: A contagem de associação de 50 000 também é imposta pelo Azure AD. Não é possível sincronizar grupos com mais membros do mesmo modificar ou remover esta regra.
* Se o grupo é um **grupo de distribuição**, também tem de ser com capacidade de correio. Ver [entre em contato com regras de out-of-box](#contact-out-of-box-rules) para esta regra é aplicada.

Os seguintes objetos de grupo são **não** sincronizados com o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se vários objetos de out-of-box no Active Directory, como o grupo de administradores internos, não estão sincronizados.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo de legado utilizado pelo DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de funções.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize a quaisquer objetos de vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regras de out-of-box ForeignSecurityPrincipal
FSPs estão associados a "qualquer" (\*) objeto no metaverso. Na realidade, esta associação acontece apenas para utilizadores e grupos de segurança. Esta configuração garante que as associações de múltiplas florestas são resolvidas e representadas corretamente no Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras de out-of-box do computador
Um objeto de computador tem de satisfazer o seguinte para ser sincronizados:

* `userCertificate ISNOTNULL`. Apenas os computadores Windows 10 preencher este atributo. Todos os objetos de computador com um valor neste atributo são sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Compreender o cenário de regras de out-of-box
Neste exemplo, estamos a utilizar uma implementação com uma floresta de conta (A), uma floresta de recursos (R) e um diretório do Azure AD.

![Com a descrição do cenário de imagens](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Nesta configuração, presume-se que existe uma conta ativada na floresta de conta e uma conta desabilitada na floresta de recursos com uma caixa de correio ligada.

O nosso objetivo com a configuração predefinida é:

* Atributos relacionados ao início de sessão são sincronizados a partir da floresta com a conta ativada.
* Atributos que podem ser encontrados na GAL (lista de endereços Global) são sincronizados a partir da floresta com a caixa de correio. Se nenhuma caixa de correio pode ser encontrada, é utilizada qualquer outra floresta.
* Se for encontrada uma caixa de correio ligada, a conta ativada ligada deve estar presente para o objeto a ser exportados para o Azure AD.

### <a name="synchronization-rule-editor"></a>Editor de regras de sincronização
A configuração pode ser visualizada e alterada com a ferramenta de Editor de regras de sincronização (SRE) e um atalho para ele pode ser encontrado no menu Iniciar.

![Ícone de Editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

O SRE é uma ferramenta de kit de recursos e ele é instalado com a sincronização do Azure AD Connect. Ser capaz de iniciá-lo, tem de ser membro do grupo ADSyncAdmins. Quando é iniciado, verá algo parecido com isto:

![Regras de sincronização de entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Neste painel, verá todas as regras de sincronização criado para a sua configuração. Cada linha na tabela é uma regra de sincronização. Para a esquerda em tipos de regras, estão listados os dois tipos diferentes: Entrada e saída. Entrada e saída é na vista de metaverso. Principalmente vai se concentrar nas regras de entrada nesta visão geral. A verdadeira lista de regras de sincronização depende o esquema detetado no AD. Na imagem acima, a floresta de conta (fabrikamonline.com) não tem quaisquer serviços, como o Exchange e Lync, e não há regras de sincronização foram criadas para estes serviços. No entanto, na floresta de recursos (res.fabrikamonline.com) localize as regras de sincronização para estes serviços. O conteúdo das regras é diferente consoante a versão detetada. Por exemplo, numa implementação com o Exchange 2013 há mais fluxos de atributos configurados que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra de Sincronização
Uma regra de sincronização é um objeto de configuração com um conjunto de atributos que flui quando uma condição é satisfeita. Ele também é usado para descrever como um objeto num espaço conector está relacionado a um objeto no metaverse, conhecido como **associação** ou **corresponde ao**. As regras de sincronização de ter um valor de precedência que indica como se relacionam entre si. Uma regra de sincronização com um valor numérico inferior tem uma precedência superior e num conflito de fluxo de atributo, a precedência superior wins a resolução de conflito.

Por exemplo, examine a regra de sincronização **do AD – utilizador AccountEnabled**. Marcar esta linha no SRE e selecione **editar**.

Uma vez que esta regra é uma regra de out-of-box, receberá um aviso ao abrir a regra. Não deve fazer qualquer [alterações às regras de out-of-box](how-to-connect-sync-best-practices-changing-default-configuration.md), por isso, é-lhe perguntado quais são suas intenções. Neste caso, apenas pretende ver a regra. Selecione **não**.

![Sincronização de regras de aviso](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Uma regra de sincronização tem quatro seções de configuração: Descrição, o filtro de Scoping, regras de associação e transformações.

#### <a name="description"></a>Descrição
A primeira secção fornece informações básicas, como um nome e descrição.

![Descrição do separador editor de regras em sincronia](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Encontrará também informações sobre qual sistema ligado esta regra está relacionada à, qual objeto de tipo no sistema ligado aplica-se a e o tipo de objeto do metaverso. O tipo de objeto do metaverso é sempre pessoa independentemente quando o tipo de objeto de origem for um utilizador, iNetOrgPerson ou contactar. O tipo de objeto do metaverso nunca deveria ser alterado para que ele é criado como um tipo genérico. O tipo de ligação pode ser definido para associação, StickyJoin ou aprovisionamento. Esta definição funciona em conjunto com a secção de regras de associação e é abordada mais tarde.

Também pode ver que esta regra de sincronização é utilizada para sincronização de palavra-passe. Se um utilizador estiver no âmbito para esta regra de sincronização, a palavra-passe é sincronizada no local para a nuvem (partindo do princípio de que ativou a funcionalidade de sincronização de palavra-passe).

#### <a name="scoping-filter"></a>Filtro de âmbito
A seção de filtro de âmbito é usada para configurar se deve aplicar uma regra de sincronização. Uma vez que o nome da regra de sincronização está a visualizar indica apenas devem ser aplicada para utilizadores ativados, o âmbito é configurado por isso, o atributo de AD **userAccountControl** não deve ter o bit 2 definidos. Quando o motor de sincronização encontra um utilizador no AD, ele se aplica esta sincronização regra quando **userAccountControl** está definido como o valor decimal 512 (utilizador normal ativado). Não é aplicável a regra quando o utilizador tem **userAccountControl** definido como 514 (utilizador normal desativado).

![Separador de âmbito no editor de regra de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

O filtro de âmbito tem grupos e as cláusulas que podem ser aninhadas. Todas as cláusulas dentro de um grupo devem ser satisfeitas para uma regra de sincronização aplicar. Quando vários grupos são definidos, pelo menos um grupo deve ser satisfeito para a regra se aplique. Ou seja, ou é avaliada entre grupos e uma lógica e é avaliada dentro de um grupo. Um exemplo desta configuração pode ser encontrado na regra de sincronização de saída **expansão para AAD – associação de grupo**. Existem vários grupos de filtro de sincronização, por exemplo, um para grupos de segurança (`securityEnabled EQUAL True`) e outro para grupos de distribuição (`securityEnabled EQUAL False`).

![Separador de âmbito no editor de regra de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Esta regra é utilizada para definir a quais grupos devem ser fornecidos para o Azure AD. Grupos de distribuição tem de ser ativado para ser sincronizados com o Azure AD de correio, mas nos grupos de segurança de uma mensagem de e-mail não é necessária.

#### <a name="join-rules"></a>Regras de associação
A terceira seção é utilizada para configurar a inter-relação entre objetos no espaço conector para objetos no metaverso. A regra que vimos anteriormente não tem qualquer configuração para regras de associação, então, em vez disso, vai examinar **do AD – associar utilizador**.

![Junte-se a guia regras no editor de regra de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

O conteúdo da regra de associação depende da opção correspondente selecionada no Assistente de instalação. Para uma regra de entrada, a avaliação começa com um objeto no espaço de conector de origem e cada grupo nas regras de associação é avaliado em sequência. Se um objeto de origem for avaliado de acordo com exatamente um objeto no metaverso com as regras de associação, os objetos estão associados. Se todas as regras foram avaliadas e não há nenhuma correspondência, em seguida, é utilizado o tipo de ligação na página de descrição. Se esta configuração é definida como **aprovisionar**, em seguida, é criado um novo objeto no destino, o metaverse. Para aprovisionar um novo objeto para o metaverso é também conhecido como **projeto** um objeto de metaverso.

As regras de associação só são avaliadas de uma vez. Quando estão associados um objeto de espaço conector e um objeto de metaverso, permanecem associados, desde que o âmbito da regra de sincronização é ainda satisfeito.

Ao avaliar as regras de sincronização, tem de ser apenas uma regra de sincronização com regras de associação definidas no âmbito. Se várias regras de sincronização com regras de associação forem encontradas para um objeto, ocorrerá um erro. Por esse motivo, a melhor prática é ter apenas uma regra de sincronização com a associação é definida quando várias regras de sincronização se encontrem no âmbito para um objeto. A configuração de out-of-box para sincronização do Azure AD Connect, estas regras podem ser encontrado ao observar o nome e localizar o item com a palavra **associar** no final do nome. Uma regra de sincronização sem quaisquer regras de associação definidas aplica-se os fluxos de atributos quando Unidas os objetos de outra regra de sincronização ou aprovisionado um novo objeto no destino.

Se examinar a imagem acima, pode ver que a regra está tentando ingressar **objectSID** com **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), qual é o que esperamos numa topologia de floresta de recursos da conta. Encontre a mesma regra em todas as florestas. A pressuposição é de que todas as florestas poderiam ser uma conta ou o recurso de floresta. Esta configuração também funciona se tiver contas que residem numa única floresta e não tem de ser associado.

#### <a name="transformations"></a>Transformações
A secção de transformação define todos os fluxos de atributos que se aplicam ao objeto de destino quando os objetos que estão associados e o filtro de âmbito é satisfeito. Voltando para o **do AD – utilizador AccountEnabled** regra de sincronização, encontrará as seguintes transformações:

![Transformações de separador editor de regras em sincronia](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Colocar esta configuração no contexto, numa implantação de floresta de recursos de conta, espera-se para localizar uma conta ativada na floresta de conta e uma conta desabilitada na floresta de recursos com as definições do Exchange e Lync. A regra de sincronização está a visualizar contém os atributos necessários para início de sessão e esses atributos devem fluir da floresta onde existe uma conta ativada. Todos os fluxos de atributos são colocados juntos numa regra de sincronização.

Uma transformação pode ter diferentes tipos: Constante, direto e a expressão.

* Um fluxo constante fluxos sempre um valor embutido em código. No caso, ele sempre que define o valor **True** no atributo metaverso com o nome **accountEnabled**.
* O valor do atributo na origem para o atributo de destino como fluxos sempre de um fluxo direto-é.
* O terceiro tipo de fluxo é uma expressão e permite para configurações mais avançadas.

A linguagem de expressão é VBA (Visual Basic for Applications), portanto, as pessoas com experiência do Microsoft Office ou VBScript reconhecerá o formato. Atributos encontram-se entre parênteses Retos, [attributeName]. Nomes de atributo e nomes de funções diferenciam maiúsculas de minúsculas, mas o Editor de regras de sincronização avalia as expressões e fornecer um aviso se a expressão não é válida. Todas as expressões são expressos numa única linha com funções aninhadas. Para mostrar o poder da linguagem de configuração, eis o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Ver [Noções básicas sobre expressões de aprovisionamento declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) para obter mais informações sobre a linguagem de expressão para fluxos de atributos.

### <a name="precedence"></a>Precedência
Agora tenha observado algumas regras de sincronização individuais, mas as regras funcionam em conjunto na configuração. Em alguns casos, um valor de atributo é contribuído de várias regras de sincronização para o mesmo atributo de destino. Neste caso, a precedência de atributo é utilizada para determinar a atributo que wins. Por exemplo, veja o atributo sourceAnchor. Este atributo é um atributo importante ser capaz de iniciar sessão Azure AD. Pode encontrar um fluxo de atributos para esse atributo nas duas regras de sincronização diferentes, **do AD – utilizador AccountEnabled** e **do AD – utilizador comuns**. Devido a precedência da regra de sincronização, o atributo sourceAnchor é colaborador da floresta com uma conta ativada primeiro quando há vários objetos associados ao objeto de metaverso. Se não existem não existem contas ativadas, em seguida, o motor de sincronização utiliza a regra de sincronização catch-all **do AD – utilizador comuns**. Esta configuração garante que mesmo para as contas que estão desativadas, há ainda um sourceAnchor.

![Regras de sincronização de entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

A precedência para regras de sincronização é definida em grupos pelo Assistente de instalação. Todas as regras num grupo de tem o mesmo nome, mas estão ligados a diferentes diretórios conectados. O Assistente de instalação dá a regra **do AD – associar utilizador** mais alta precedência e faz a iteração sobre todos os ligados a diretórios AD. Em seguida, prossegue com os seguintes grupos de regras numa ordem predefinida. Dentro de um grupo, as regras são adicionadas na ordem que os conectores foram adicionados no assistente. Se outro conector for adicionado através do assistente, as regras de sincronização são reordenadas e regras do novo conector são inseridas pela última vez em cada grupo.

### <a name="putting-it-all-together"></a>Juntar tudo
Agora sabemos suficiente sobre regras de sincronização para ser capaz de entender como a configuração funciona com as regras de sincronização diferentes. Se examinar um utilizador e os atributos que são contribuíram para o metaverso, as regras são aplicadas pela seguinte ordem:

| Name | Comentário |
|:--- |:--- |
| Do AD – associação do utilizador |Regra para ingressar em objetos de espaço conector com metaverso. |
| Do AD – UserAccount ativada |Atributos necessários para início de sessão para o Azure AD e o Office 365. Queremos que esses atributos da conta ativada. |
| Do AD – comuns de usuário do Exchange |Atributos encontrados na lista de endereços Global. Vamos supor que a qualidade de dados é melhor na floresta em que Achamos caixa de correio do utilizador. |
| Do AD – comum do utilizador |Atributos encontrados na lista de endereços Global. No caso de não foi possível encontrar uma caixa de correio, qualquer outro objeto associado ao pode contribuir com o valor do atributo. |
| Do AD – Exchange do utilizador |Só existe se Exchange foi detetado. Fluxos de todos os atributos do Exchange de infraestrutura. |
| Do AD – utilizador Lync |Só existe se Lync foi detetado. Fluxos de todos os atributos do Lync de infraestrutura. |

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o modelo de configuração [aprovisionamento declarativo da compreensão](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Saiba mais sobre a linguagem de expressão no [Noções básicas sobre expressões de aprovisionamento declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Continue a ler como a configuração de out-of-box funciona no [entender utilizadores e contactos](concept-azure-ad-connect-sync-user-and-contacts.md)
* Veja como fazer uma alteração prática com o aprovisionamento declarativo [como fazer uma alteração à configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

