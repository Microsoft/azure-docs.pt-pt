---
title: 'Azure AD Connect: Resolver problemas de erros durante a sincronização | Documentos da Microsoft'
description: Explica como resolver problemas de erros encontrados durante a sincronização com o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ba74961eb549afd2fcf7c10f2d8b981e389a2c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845101"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Resolução de problemas de erros durante a sincronização
Erros podem ocorrer quando os dados de identidade são sincronizados a partir do Windows Server Active Directory (AD DS) para o Azure Active Directory (Azure AD). Este artigo fornece uma visão geral dos diferentes tipos de erros de sincronização, alguns dos possíveis cenários que fazem com que esses erros e potenciais formas para corrigir os erros. Este artigo inclui os tipos de erro comuns e talvez não abranjam todos os erros possíveis.

 Este artigo pressupõe que o leitor está familiarizado com subjacente [projetar os conceitos do Azure AD e do Azure AD Connect](plan-connect-design-concepts.md).

Com a versão mais recente do Azure AD Connect \(Agosto de 2016 ou superior\), um relatório de erros de sincronização está disponível na [portal do Azure](https://aka.ms/aadconnecthealth) como parte do Azure AD Connect Health para sincronização.

A partir de 1 de Setembro de 2016 [do Azure Active Directory duplicar resiliência de atributos](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funcionalidade será ativada por predefinição para todos os *novo* inquilinos do Azure Active Directory do. Esta funcionalidade será ativada automaticamente para os inquilinos existentes nos próximos meses.

Azure AD Connect efetua três tipos de operações de diretórios mantém sincronizado: Importar, sincronização e exportação. Erros podem ocorrer em todas as operações. Este artigo enfoca principalmente erros durante a exportação para o Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante a exportação para o Azure AD
Secção seguinte descreve os diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para o Azure AD com o conector do Azure AD. Este conector pode ser identificado com o formato de nome a ser "contoso. *onmicrosoft.com*".
Erros durante a exportação para o Azure AD indicam que a operação \(adicionar, atualizar e eliminar etc.\) tentada pelo Azure AD Connect \(motor de sincronização\) no Azure Active Directory falhou.

![Descrição geral de erros de exportação](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erros de correspondência de dados
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
* Quando o Azure AD Connect \(motor de sincronização\) dá instruções ao Azure Active Directory para adicionar ou atualizar objetos, do Azure AD corresponde ao objeto de entrada, utilizando o **sourceAnchor** atributo para o **immutableId**  atributo dos objetos no Azure AD. Esta correspondência é chamada um **corresponder difícil**.
* Quando do Azure AD **não encontrar** qualquer objeto que corresponda a **immutableId** atributo com o **sourceAnchor** atributo do objeto de entrada, antes do aprovisionamento de um novo objeto, retrocede para usar os atributos ProxyAddresses e UserPrincipalName para encontrar uma correspondência. Esta correspondência é chamada um **corresponder de forma recuperável**. Corresponder Soft destina-se na correspondência de objetos já presentes no Azure AD (que têm origem no Azure AD) com os novos objetos a ser adicionadas/atualizadas durante a sincronização que representam a mesma entidade (utilizadores, grupos) no local.
* **InvalidSoftMatch** erro ocorre quando a correspondência de disco rígida não encontra qualquer objeto correspondente **AND** correspondência de forma recuperável localiza um objeto correspondente, mas esse objeto tem um valor diferente de *immutableId* que o do objeto de entrada *SourceAnchor*, sugerir que o objeto correspondente foi sincronizado com outro objeto de ativado no local do Active Directory.

Em outras palavras, por ordem para a correspondência de forma recuperável funcionar, o objeto a ser correspondido de forma recuperável com o não deve ter qualquer valor para o *immutableId*. Se qualquer um objeto com *immutableId* conjunto com um valor está a falhar-match difícil, mas que satisfaça os critérios de correspondência de forma recuperável, a operação resultaria num erro de sincronização InvalidSoftMatch.

Esquema do Active Directory do Azure não permite duas ou mais objetos para ter o mesmo valor dos seguintes atributos. \(Não se trata de uma lista exaustiva.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [O Azure AD atributo duplicar resiliência de atributos](how-to-connect-syncservice-duplicate-attribute-resiliency.md) recurso também está a ser lançado como o comportamento padrão do Azure Active Directory.  Isto irá reduzir o número de erros de sincronização vistos pelo Azure AD Connect (bem como outros clientes de sincronização), fazendo com que o Azure AD mais resiliente da forma que ele manipula duplicados atributos ProxyAddresses e UserPrincipalName presentes em ambientes locais AD. Esta funcionalidade não corrige os erros de duplicação. Portanto, os dados ainda precisam de ser corrigidos. Mas permite que o aprovisionamento de novos objetos que, caso contrário, são impedidos de que está a ser aprovisionada devido a valores duplicados no Azure AD. Isso também reduzirá o número de erros de sincronização devolvido para o cliente de sincronização.
> Se esta funcionalidade está ativada para o seu inquilino, não verá os erros de sincronização de InvalidSoftMatch vistos durante o aprovisionamento de novos objetos.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Exemplos de cenários de InvalidSoftMatch
1. Dois ou mais objetos com o mesmo valor para o atributo ProxyAddresses existem no Active Directory no local. Apenas um é obter aprovisionado no Azure AD.
2. Dois ou mais objetos com o mesmo valor para o atributo userPrincipalName existe no Active Directory no local. Apenas um é obter aprovisionado no Azure AD.
3. Um objeto foi adicionado no local do Active Directory com o mesmo valor de atributo ProxyAddresses como que de um objeto existente no Azure Active Directory. O objeto adicionado no local não está a obter aprovisionado no Azure Active Directory.
4. Um objeto foi adicionado no local do Active Directory com o mesmo valor do atributo userPrincipalName como que de uma conta no Azure Active Directory. O objeto não está a obter aprovisionado no Azure Active Directory.
5. Uma conta de sincronização tiver sido movida de floresta à floresta B. Azure AD Connect (motor de sincronização) estava a utilizar o atributo de ObjectGUID para computar o SourceAnchor. Após a mudança de floresta, o valor de SourceAnchor é diferente. O novo objeto (de floresta B) está a conseguir sincronizar com o objeto existente no Azure AD.
6. Um objeto sincronizado acidentalmente foi eliminado no local do Active Directory e um novo objeto foi criado no Active Directory para a mesma entidade (por exemplo, o utilizador) sem a eliminação da conta no Azure Active Directory. A nova conta não consegue sincronizar com o objeto do Azure AD existente.
7. O Azure AD Connect foi desinstalado e reinstalado. Durante a reinstalação, um atributo diferente foi escolhido como o SourceAnchor. Todos os objetos que tivessem sincronizado anteriormente parado a sincronizar com o erro de InvalidSoftMatch.

#### <a name="example-case"></a>Caso de exemplo:
1. **BOB Smith** é um utilizador sincronizado no Azure Active Directory de ativado no local do Active Directory de *contoso.com*
2. BOB Smith **UserPrincipalName** está definido como **bobs\@contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** é o **SourceAnchor** calculado ao Connect de AD do Azure com Bob Smith **objectGUID** no local do Active Directory, que está a  **immutableId** de Bob Smith no Azure Active Directory.
4. BOB também tem valores para a seguir a **proxyAddresses** atributo:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob\@contoso.com**
5. Um novo utilizador **Bob Taylor**, é adicionado ao Active Directory no local.
6. De Bob Taylor **UserPrincipalName** está definido como **bobt\@contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** é o **sourceAnchor** calculado ao Connect do Azure AD através de Bob Taylor **objectGUID** no local do Active Directory. Objeto de Bob Taylor não tenha sincronizado ao Azure Active Directory ainda.
8. BOB Taylor tem os seguintes valores para o atributo proxyAddresses
   * smtp: bobt@contoso.com
   * smtp: bob.taylor@contoso.com
   * **smtp: bob\@contoso.com**
9. Durante a sincronização, o Azure AD Connect irá reconhecer a adição de Bob Taylor no local do Active Directory e pedir ao Azure AD para fazer a mesma alteração.
10. Azure AD pela primeira vez irá efetuar a correspondência de disco rígida. Ou seja, ele pesquisará se houver qualquer objeto com o immutableId igual a "abcdefghijkl0123456789 = =". Correspondência de disco rígida falhará como nenhum outro objeto no Azure AD terão que immutableId.
11. Correspondência de forma recuperável Bob Taylor, em seguida, tenta do Azure AD. Ou seja, ele pesquisará se houver qualquer objeto com proxyAddresses igual a três valores, incluindo smtp: bob@contoso.com
12. Do Azure AD irá encontrar o objeto de Bob Smith, de acordo com os critérios de correspondência de forma recuperável. Mas este objeto tem o valor de immutableId = "abcdefghijklmnopqrstuv = =". indica que este objeto foi sincronizado a partir de outro objeto no local do Active Directory. Portanto, do Azure AD não é possível soft-match esses objetos e resulta numa **InvalidSoftMatch** um erro de sincronização.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Como corrigir o erro de InvalidSoftMatch
A razão mais comum para o erro InvalidSoftMatch é dois objetos com diferente SourceAnchor \(immutableId\) têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName, que são utilizados durante a correspondência de forma recuperável processar no Azure AD. Para corrigir a correspondência de forma recuperável inválido

1. Identifique o proxyAddresses duplicado, userPrincipalName ou outro valor de atributo que está causando o erro. Também identificar que dois \(ou mais\) objetos estiverem envolvidos no conflito. O relatório gerado pelo [do Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) podem ajudar a identificar os dois objetos.
2. Identifica qual objeto deve continuar a ter o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Deve efetuar a alteração no diretório onde o objeto de origem. Em alguns casos, poderá ter de eliminar um dos objetos em conflito.
4. Se efetuou a alteração no local no AD, deixe o Azure AD Connect, a alteração de sincronização.

Relatórios de erros de sincronização no Azure AD Connect Health para sincronização são atualizados a cada 30 minutos e incluem os erros à tentativa de sincronização mais recente.

> [!NOTE]
> ImmutableId, por definição, não deve alterar durante a vida útil do objeto. Se o Azure AD Connect não foi configurado com alguns dos cenários, lembre-se na lista acima, poderia acabar numa situação em que o Azure AD Connect calcula um valor diferente de SourceAnchor para o objeto do AD que representa a mesma entidade (mesmo utilizador/grupo / contacto, etc.) que tem um objeto de AD existente do Azure que pretende continuar a utilizar.
>
>

#### <a name="related-articles"></a>Artigos relacionados
* [Atributos duplicados ou é inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando o AD do Azure tenta por software comparar dois objetos, é possível que dois objetos diferentes de "tipo de objeto" (por exemplo, o utilizador, grupo, etc. de contacto) tem os mesmos valores para os atributos utilizados para efetuar a correspondência de forma recuperável. Como a duplicação desses atributos não é permitida no Azure AD, a operação pode resultar em erros de sincronização "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Exemplos de cenários de erro ObjectTypeMismatch
* Um grupo de segurança com capacidade de correio é criado no Office 365. Administrador adiciona um novo utilizador ou contacto no AD no local (que não está sincronizado com o Azure AD ainda) com o mesmo valor para o atributo ProxyAddresses do grupo do Office 365.

#### <a name="example-case"></a>Caso de exemplo
1. Administrador cria um novo grupo de segurança com capacidade de correio no Office 365 para o departamento de imposto sobre e fornece um endereço de e-mail como tax@contoso.com. Este grupo é atribuído o valor do atributo ProxyAddresses do **smtp: imposto\@contoso.com**
2. Um novo utilizador associa Contoso.com e é criada uma conta para o utilizador no local com o proxyAddress como **smtp: imposto\@contoso.com**
3. Quando o Azure AD Connect irá sincronizar a nova conta de utilizador, obterá o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Como corrigir o erro de ObjectTypeMismatch
A razão mais comum para o erro ObjectTypeMismatch é dois objetos de tipo diferente (utilizador, grupo, etc. de contacto) têm o mesmo valor para o atributo ProxyAddresses. Para corrigir o ObjectTypeMismatch:

1. Identificar o proxyAddresses duplicados (ou outro atributo) valor que está causando o erro. Também identificar que dois \(ou mais\) objetos estiverem envolvidos no conflito. O relatório gerado pelo [do Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) podem ajudar a identificar os dois objetos.
2. Identifica qual objeto deve continuar a ter o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Tenha em atenção de que deve fazer a alteração no diretório onde o objeto de origem. Em alguns casos, poderá ter de eliminar um dos objetos em conflito.
4. Se efetuou a alteração no local no AD, deixe o Azure AD Connect, a alteração de sincronização. Relatório de erros de sincronização no Azure AD Connect Health para sincronização é atualizado a cada 30 minutos e inclui os erros à tentativa de sincronização mais recente.

## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrição
Esquema do Active Directory do Azure não permite duas ou mais objetos para ter o mesmo valor dos seguintes atributos. É que cada objeto no Azure AD é forçado a ter um valor exclusivo destes atributos numa determinada instância.

* ProxyAddresses
* UserPrincipalName

Se o Azure AD Connect tenta adicionar um novo objeto ou atualizar um objeto existente com um valor para os atributos acima que já está atribuído a outro objeto no Azure Active Directory, a operação resulta num erro de sincronização "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Cenários possíveis:
1. Valor duplicado é atribuído a um objeto já sincronizado, o que está em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Caso de exemplo:
1. **BOB Smith** é um utilizador sincronizado no Azure Active Directory de ativado no local do Active Directory contoso.com
2. BOB Smith **UserPrincipalName** no local é definido como **bobs\@contoso.com**.
3. BOB também tem valores para a seguir a **proxyAddresses** atributo:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob\@contoso.com**
4. Um novo utilizador **Bob Taylor**, é adicionado ao Active Directory no local.
5. De Bob Taylor **UserPrincipalName** está definido como **bobt\@contoso.com**.
6. **BOB Taylor** tem os seguintes valores para o **ProxyAddresses** atributo i. smtp: bobt@contoso.com ii. smtp: bob.taylor@contoso.com
7. Objeto de Bob Taylor é sincronizado com o Azure AD com êxito.
8. Administrador decidiu atualizar de Bob Taylor **ProxyAddresses** atributo com o seguinte valor: eu. **smtp: bob\@contoso.com**
9. O Azure AD irá tentar atualizar objeto de Bob Taylor no Azure AD com o valor acima, mas que a operação falhe como que ProxyAddresses valor já está atribuído a Bob Smith, resultando no erro de "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Como corrigir o erro de AttributeValueMustBeUnique
A razão mais comum para o erro AttributeValueMustBeUnique é dois objetos com diferente SourceAnchor \(immutableId\) têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName. Para corrigir o erro de AttributeValueMustBeUnique

1. Identifique o proxyAddresses duplicado, userPrincipalName ou outro valor de atributo que está causando o erro. Também identificar que dois \(ou mais\) objetos estiverem envolvidos no conflito. O relatório gerado pelo [do Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) podem ajudar a identificar os dois objetos.
2. Identifica qual objeto deve continuar a ter o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Tenha em atenção de que deve fazer a alteração no diretório onde o objeto de origem. Em alguns casos, poderá ter de eliminar um dos objetos em conflito.
4. Se efetuou a alteração no local no AD, deixe o Azure AD Connect, a alteração para o erro obter fixo de sincronização.

#### <a name="related-articles"></a>Artigos relacionados
-[Atributos duplicados ou é inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Falhas de validação de dados
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrição
O Azure Active Directory impõe várias restrições nos dados antes de permitir que os dados sejam gravados no diretório. Estas restrições são para garantir que os utilizadores finais obtêm as melhores experiências possíveis ao utilizar as aplicações que dependem destes dados.

#### <a name="scenarios"></a>Cenários
a. O valor do atributo UserPrincipalName tem carateres inválido/não suportado.
b. O atributo UserPrincipalName não segue o formato necessário.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Como corrigir o erro de IdentityDataValidationFailed
a. Certifique-se de que o atributo userPrincipalName tem suporte a caracteres e o formato necessário.

#### <a name="related-articles"></a>Artigos relacionados
* [Preparar para aprovisionar os usuários por meio de sincronização de diretórios para o Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Descrição
Neste caso, resulta numa **"FederatedDomainChangeError"** um erro de sincronização quando o sufixo de UserPrincipalName um utilizador é alterado de um domínio federado para outro domínio federado.

#### <a name="scenarios"></a>Cenários
Para um utilizador sincronizado, o sufixo de UserPrincipalName foi alterado de um domínio federado para outro domínio federado no local. Por exemplo, *UserPrincipalName = bob\@contoso.com* foi alterada para *UserPrincipalName = bob\@fabrikam.com*.

#### <a name="example"></a>Exemplo
1. BOB Smith, uma conta para Contoso.com, é adicionado como um novo utilizador no Active Directory com o UserPrincipalName bob@contoso.com
2. BOB move para uma divisão diferente de Contoso.com chamado Fabrikam.com e seu UserPrincipalName é alterado para bob@fabrikam.com
3. Os domínios contoso.com e fabrikam.com são domínios federados com o Azure Active Directory.
4. UserPrincipalName do Bob não for atualizado e resulta num erro de sincronização "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Como corrigir
Se o sufixo de UserPrincipalName do utilizador foi atualizado de bob @**contoso.com** para bob\@**fabrikam.com**, em que ambos **contoso.com** e  **Fabrikam.com** estão **federado domínios**, em seguida, siga estes passos para corrigir o erro de sincronização

1. Atualizar o UserPrincipalName do utilizador no Azure AD a partir bob@contoso.com para bob@contoso.onmicrosoft.com. Pode utilizar o seguinte comando do PowerShell com o módulo do PowerShell do Azure AD: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Permitir que o próximo ciclo de sincronização para a tentativa de sincronização. Esta sincronização de hora será concluída com êxito e atualizará o UserPrincipalName de Bob para bob@fabrikam.com conforme esperado.

#### <a name="related-articles"></a>Artigos relacionados
* [As alterações não são sincronizadas pela ferramenta do Azure Active Directory Sync depois de alterar o UPN de uma conta de utilizador para utilizar um domínio federado diferente](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, o limite de comprimento ou o limite de contagem definido pelo esquema do Active Directory do Azure, a operação de sincronização resulta na **LargeObject** ou **ExceededAllowedLength**erro de sincronização. Este erro ocorre normalmente nos seguintes atributos

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Cenários possíveis
1. Atributo de userCertificate de Bob está armazenando demasiados certificados atribuídos para Bob. Estas podem incluir certificados mais antigos e expirados. O limite de disco rígido é 15 certificados. Para obter mais informações sobre como lidar com erros de LargeObject com o atributo userCertificate, consulte artigo [erros de processamento de LargeObject causados pelo atributo userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Atributo de userSMIMECertificate de Bob está armazenando demasiados certificados atribuídos para Bob. Estas podem incluir certificados mais antigos e expirados. O limite de disco rígido é 15 certificados.
3. ThumbnailPhoto de Bob definido no Active Directory é demasiado grande para ser sincronizados no Azure AD.
4. Durante a população automática do atributo ProxyAddresses do Active Directory, um objeto tem demasiadas ProxyAddresses atribuídos.

### <a name="how-to-fix"></a>Como corrigir
1. Certifique-se de que o atributo causando o erro está dentro da limitação permitida.

## <a name="existing-admin-role-conflict"></a>Conflito de Função de Administrador Existente

### <a name="description"></a>Descrição
Uma **conflito de função de administrador existente** irá ocorrer num objeto de utilizador durante a sincronização quando esse objeto de utilizador tem:

- permissões administrativas e
- o UserPrincipalName mesmo como um objeto do Azure AD existente

O Azure AD Connect não tem permissão para correspondência de forma recuperável um objeto de usuário locais AD com um objeto de utilizador no Azure AD que tenha uma função administrativa atribuída ao mesmo.  Para obter mais informações consulte [população de UserPrincipalName do Azure AD](plan-connect-userprincipalname.md)

![Administrador existente](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Como corrigir
Para resolver este problema proceda um dos seguintes:


- alterar o UserPrincipalName para um valor que não corresponde ao que um utilizador administrador no Azure AD - que irá criar um novo utilizador no Azure AD com o UserPrincipalName correspondente
- Remova a função administrativa do utilizador de administrador no Azure AD, o que permitirá a correspondência de forma recuperável entre o objeto de utilizador no local e o objeto de utilizador do Azure AD existente.

>[!NOTE]
>Pode atribuir a função administrativa para o objeto de utilizador existente novamente depois de concluída a correspondência de forma recuperável entre o objeto de utilizador no local e o objeto de utilizador do Azure AD.

## <a name="related-links"></a>Ligações relacionadas
* [Localizar objetos do Active Directory no Centro de administração do Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Como consultar o Azure Active Directory para um objeto com o Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
