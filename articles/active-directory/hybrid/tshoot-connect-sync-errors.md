---
title: 'Azure AD Connect: solucionar erros durante a sincronização | Microsoft Docs'
description: Explica como solucionar problemas de erros encontrados durante a sincronização com Azure AD Connect.
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
ms.openlocfilehash: d824606b1b602d006e53be619d6d955ac2cfb71f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213037"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Solucionando erros durante a sincronização
Podem ocorrer erros quando os dados de identidade são sincronizados do Windows Server Active Directory (AD DS) para Azure Active Directory (Azure AD). Este artigo fornece uma visão geral dos diferentes tipos de erros de sincronização, alguns dos possíveis cenários que causam esses erros e possíveis maneiras de corrigir os erros. Este artigo inclui os tipos de erro comuns e pode não abranger todos os erros possíveis.

 Este artigo pressupõe que o leitor esteja familiarizado com os [conceitos de design subjacentes do Azure AD e Azure ad Connect](plan-connect-design-concepts.md).

Com a versão mais recente do Azure AD Connect \(\)de agosto de 2016 ou superior, um relatório de erros de sincronização está disponível no [portal do Azure](https://aka.ms/aadconnecthealth) como parte do Azure ad Connect Health para sincronização.

A partir de 1º de setembro de 2016 Azure Active Directory recurso de [resiliência de atributo duplicado](how-to-connect-syncservice-duplicate-attribute-resiliency.md) será habilitado por padrão para todos os *novos* locatários de Azure Active Directory. Esse recurso será habilitado automaticamente para locatários existentes nos próximos meses.

Azure AD Connect executa três tipos de operações dos diretórios que ele mantém em sincronia: importação, sincronização e exportação. Os erros podem ocorrer em todas as operações. Este artigo se concentra principalmente em erros durante a exportação para o Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante a exportação para o Azure AD
A seção a seguir descreve os diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para o AD do Azure usando o conector do AD do Azure. Esse conector pode ser identificado pelo formato de nome "contoso. *onmicrosoft.com*".
Erros durante a exportação para o Azure AD indicam que a operação \(adicionar, atualizar, excluir etc.\) tentou Azure AD Connect \(mecanismo de sincronização\) em Azure Active Directory falhou.

![Visão geral de erros de exportação](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erros de incompatibilidade de dados
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
* Quando Azure AD Connect mecanismo de sincronização \(\) instrui o Azure Active Directory a adicionar ou atualizar objetos, o Azure AD corresponde ao objeto de entrada usando o atributo **sourceAnchor** para o atributo **imutávelid** de objetos no Azure AD. Essa correspondência é chamada de **correspondência rígida**.
* Quando o Azure AD não **encontra** nenhum objeto que corresponda ao atributo **imutávelid** com o atributo **sourceAnchor** do objeto de entrada, antes de provisionar um novo objeto, ele volta a usar os atributos ProxyAddresses e userPrincipalName para encontrar uma correspondência. Essa correspondência é chamada de **correspondência flexível**. A correspondência flexível foi projetada para corresponder objetos já presentes no Azure AD (que são originados no Azure AD) com os novos objetos que estão sendo adicionados/atualizados durante a sincronização que representam a mesma entidade (usuários, grupos) no local.
* O erro **InvalidSoftMatch** ocorre quando a correspondência rígida não encontra nenhum objeto correspondente **e** a correspondência flexível encontra um objeto correspondente, mas esse objeto tem um valor diferente de *imutávelid* que o *SourceAnchor*do objeto de entrada, sugerindo que o objeto correspondente foi sincronizado com outro objeto do local Active Directory.

Em outras palavras, para que a correspondência flexível funcione, o objeto com correspondência flexível não deve ter nenhum valor para a *imutável*. Se qualquer objeto com *imutável* definido com um valor estiver falhando na correspondência fixa, mas atendendo aos critérios de correspondência flexível, a operação resultaria em um erro de sincronização de InvalidSoftMatch.

Azure Active Directory esquema não permite que dois ou mais objetos tenham o mesmo valor dos atributos a seguir. \(essa não é uma lista exaustiva.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* objectId

> [!NOTE]
> O recurso de [resiliência de atributo duplicado do atributo do Azure ad](how-to-connect-syncservice-duplicate-attribute-resiliency.md) também está sendo distribuído como o comportamento padrão de Azure Active Directory.  Isso reduzirá o número de erros de sincronização vistos por Azure AD Connect (bem como outros clientes de sincronização) tornando o Azure AD mais resiliente no modo como ele lida com atributos ProxyAddresses e UserPrincipalName duplicados presentes em ambientes de AD locais. Esse recurso não corrige os erros de duplicação. Portanto, os dados ainda precisam ser corrigidos. Mas permite o provisionamento de novos objetos que, de outra forma, estão impedidos de serem provisionados devido a valores duplicados no Azure AD. Isso também reduzirá o número de erros de sincronização retornados para o cliente de sincronização.
> Se esse recurso estiver habilitado para seu locatário, você não verá os erros de sincronização do InvalidSoftMatch vistos durante o provisionamento de novos objetos.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Cenários de exemplo para InvalidSoftMatch
1. Dois ou mais objetos com o mesmo valor para o atributo ProxyAddresses existem no Active Directory local. Somente um está sendo provisionado no Azure AD.
2. Há dois ou mais objetos com o mesmo valor para o atributo userPrincipalName no Active Directory local. Somente um está sendo provisionado no Azure AD.
3. Um objeto foi adicionado no local Active Directory com o mesmo valor do atributo ProxyAddresses que um objeto existente no Azure Active Directory. O objeto adicionado no local não está sendo provisionado no Azure Active Directory.
4. Um objeto foi adicionado no local Active Directory com o mesmo valor do atributo userPrincipalName que de uma conta no Azure Active Directory. O objeto não está sendo provisionado no Azure Active Directory.
5. Uma conta sincronizada foi movida da floresta A para a floresta B. Azure AD Connect (mecanismo de sincronização) estava usando o atributo objectGUID para computar o SourceAnchor. Depois que a floresta é movida, o valor de SourceAnchor é diferente. O novo objeto (da floresta B) está falhando ao sincronizar com o objeto existente no Azure AD.
6. Um objeto sincronizado foi excluído acidentalmente do local Active Directory e um novo objeto foi criado em Active Directory para a mesma entidade (como usuário) sem excluir a conta no Azure Active Directory. Falha na sincronização da nova conta com o objeto do Azure AD existente.
7. Azure AD Connect foi desinstalado e reinstalado. Durante a reinstalação, um atributo diferente foi escolhido como o SourceAnchor. Todos os objetos que foram sincronizados anteriormente interromperam a sincronização com o erro InvalidSoftMatch.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um usuário sincronizado no Azure Active Directory local Active Directory do *contoso.com*
2. O **userPrincipalName** de Bob Smith é definido como **bobs\@contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** é o **SourceAnchor** calculado por Azure ad Connect usando o **objectGUID** de Bob Smith da Active Directory local, que é **imutável** para Bob Smith em Azure Active Directory.
4. Bob também tem os seguintes valores para o atributo **proxyAddresses** :
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: Bob\@contoso.com**
5. Um novo usuário, **Bob Taylor**, é adicionado à Active Directory local.
6. O **userPrincipalName** de Bob Taylor é definido como **bobt\@contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** é o **sourceAnchor** calculado por Azure ad Connect usando o **objectGUID** de Bob Taylor do Active Directory local. O objeto de Bob Taylor ainda não foi sincronizado com o Azure Active Directory.
8. Bob Taylor tem os seguintes valores para o atributo proxyAddresses
   * SMTP: bobt@contoso.com
   * SMTP: bob.taylor@contoso.com
   * **SMTP: Bob\@contoso.com**
9. Durante a sincronização, Azure AD Connect reconhecerá a adição de Bob Taylor no local Active Directory e pedirá ao Azure AD para fazer a mesma alteração.
10. O Azure AD executará primeiro a correspondência rígida. Ou seja, ele pesquisará se houver qualquer objeto com a imutávelid igual a "abcdefghijkl0123456789 = =". A correspondência rígida falhará, pois nenhum outro objeto no Azure AD terá esse imutável.
11. Em seguida, o Azure AD tentará fazer a correspondência suave de Bob Taylor. Ou seja, ele pesquisará se houver qualquer objeto com proxyAddresses igual aos três valores, incluindo SMTP: bob@contoso.com
12. O Azure AD encontrará o objeto de Bob Smith para corresponder aos critérios de correspondência flexível. Mas esse objeto tem o valor de imutável = "abcdefghijklmnopqrstuv = =". Isso indica que esse objeto foi sincronizado a partir de outro objeto do local Active Directory. Portanto, o Azure AD não pode fazer a correspondência flexível desses objetos e resulta em um erro de sincronização de **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Como corrigir o erro InvalidSoftMatch
O motivo mais comum para o erro InvalidSoftMatch é dois objetos com SourceAnchor diferentes \(imutávelid\) ter o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName, que são usados durante o processo de correspondência flexível no Azure AD. Para corrigir a correspondência flexível inválida

1. Identifique o valor duplicado proxyAddresses, userPrincipalName ou outro atributo que está causando o erro. Identifique também quais dois \(ou mais objetos\) estão envolvidos no conflito. O relatório gerado pelo [Azure ad Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar com o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Você deve fazer a alteração no diretório do qual o objeto é originado. Em alguns casos, talvez seja necessário excluir um dos objetos em conflito.
4. Se você fez a alteração no AD local, deixe Azure AD Connect sincronizar a alteração.

Os relatórios de erros de sincronização dentro do Azure AD Connect Health para sincronização são atualizados a cada 30 minutos e incluem os erros da tentativa de sincronização mais recente.

> [!NOTE]
> Imutávelid, por definição, não deve ser alterado no tempo de vida do objeto. Se Azure AD Connect não tiver sido configurado com alguns dos cenários em mente na lista acima, você poderá terminar em uma situação em que Azure AD Connect calcula um valor diferente do SourceAnchor para o objeto do AD que representa a mesma entidade (mesmo usuário/grupo/contato etc) que tem um objeto do Azure AD existente que você deseja continuar usando.
>
>

#### <a name="related-articles"></a>Artigos relacionados
* [Atributos duplicados ou inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando o Azure AD tenta fazer a correspondência flexível de dois objetos, é possível que dois objetos de "tipo de objeto" diferentes (como usuário, grupo, contato etc.) tenham os mesmos valores para os atributos usados para executar a correspondência flexível. Como a duplicação desses atributos não é permitida no Azure AD, a operação pode resultar em um erro de sincronização "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Cenários de exemplo para erro ObjectTypeMismatch
* Um grupo de segurança habilitado para email é criado no Office 365. O administrador adiciona um novo usuário ou contato no AD local (que ainda não está sincronizado com o Azure AD) com o mesmo valor para o atributo ProxyAddresses do grupo do Office 365.

#### <a name="example-case"></a>Caso de exemplo
1. O administrador cria um novo grupo de segurança habilitado para email no Office 365 para o departamento fiscal e fornece um endereço de email como tax@contoso.com. Esse grupo é atribuído ao valor do atributo ProxyAddresses de **SMTP: tax\@contoso.com**
2. Um novo usuário ingressa em Contoso.com e uma conta é criada para o usuário local com o proxyAddress como **SMTP: imposto\@contoso.com**
3. Quando Azure AD Connect sincronizará a nova conta de usuário, ele receberá o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Como corrigir o erro ObjectTypeMismatch
O motivo mais comum para o erro ObjectTypeMismatch é dois objetos de tipo diferente (usuário, grupo, contato etc.) têm o mesmo valor para o atributo ProxyAddresses. Para corrigir o ObjectTypeMismatch:

1. Identifique o valor duplicado de proxyAddresses (ou outro atributo) que está causando o erro. Identifique também quais dois \(ou mais objetos\) estão envolvidos no conflito. O relatório gerado pelo [Azure ad Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar com o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Observe que você deve fazer a alteração no diretório do qual o objeto é originado. Em alguns casos, talvez seja necessário excluir um dos objetos em conflito.
4. Se você fez a alteração no AD local, deixe Azure AD Connect sincronizar a alteração. O relatório de erros de sincronização dentro do Azure AD Connect Health para sincronização é atualizado a cada 30 minutos e inclui os erros da tentativa de sincronização mais recente.

## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrição
Azure Active Directory esquema não permite que dois ou mais objetos tenham o mesmo valor dos atributos a seguir. Cada objeto no Azure AD é forçado a ter um valor exclusivo desses atributos em uma determinada instância.

* ProxyAddresses
* UserPrincipalName

Se Azure AD Connect tentar adicionar um novo objeto ou atualizar um objeto existente com um valor para os atributos acima que já está atribuído a outro objeto em Azure Active Directory, a operação resultará no erro de sincronização "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Cenários possíveis:
1. O valor duplicado é atribuído a um objeto já sincronizado, que está em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um usuário sincronizado no Azure Active Directory local Active Directory do contoso.com
2. O **userPrincipalName** local de Bob Smith está definido como **bobs\@contoso.com**.
3. Bob também tem os seguintes valores para o atributo **proxyAddresses** :
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: Bob\@contoso.com**
4. Um novo usuário, **Bob Taylor**, é adicionado à Active Directory local.
5. O **userPrincipalName** de Bob Taylor é definido como **bobt\@contoso.com**.
6. **Bob Taylor** tem os seguintes valores para o atributo **proxyAddresses** i. SMTP: bobt@contoso.com II. SMTP: bob.taylor@contoso.com
7. O objeto de Bob Taylor está sincronizado com o Azure AD com êxito.
8. O administrador decidiu atualizar o atributo **proxyAddresses** de Bob Taylor com o seguinte valor: i. **SMTP: Bob\@contoso.com**
9. O Azure AD tentará atualizar o objeto de Bob Taylor no Azure AD com o valor acima, mas essa operação falhará, pois esse valor de ProxyAddresses já está atribuído a Bob Smith, resultando no erro "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Como corrigir o erro AttributeValueMustBeUnique
O motivo mais comum para o erro AttributeValueMustBeUnique é dois objetos com SourceAnchor diferentes \(imutávelid\) ter o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName. Para corrigir o erro AttributeValueMustBeUnique

1. Identifique o proxyAddresses duplicado, userPrincipalName ou outro valor de atributo que está causando o erro. Identifique também quais dois \(ou mais objetos\) estão envolvidos no conflito. O relatório gerado pelo [Azure ad Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar com o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Observe que você deve fazer a alteração no diretório do qual o objeto é originado. Em alguns casos, talvez seja necessário excluir um dos objetos em conflito.
4. Se você fez a alteração no AD local, permita que Azure AD Connect sincronize a alteração para que o erro seja corrigido.

#### <a name="related-articles"></a>Artigos relacionados
-[atributos duplicados ou inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Falhas de validação de dados
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrição
Azure Active Directory impõe várias restrições nos dados em si antes de permitir que os dados sejam gravados no diretório. Essas restrições são para garantir que os usuários finais obtenham as melhores experiências possíveis ao usar os aplicativos que dependem desses dados.

#### <a name="scenarios"></a>Cenários
a. O valor do atributo UserPrincipalName tem caracteres inválidos/sem suporte.
b. O atributo UserPrincipalName não segue o formato necessário.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Como corrigir o erro IdentityDataValidationFailed
a. Verifique se o atributo userPrincipalName tem caracteres com suporte e o formato obrigatório.

#### <a name="related-articles"></a>Artigos relacionados
* [Preparar para provisionar usuários por meio da sincronização de diretório para o Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Descrição
Esse caso resulta em um erro de sincronização **"FederatedDomainChangeError"** quando o sufixo de userPrincipalName de um usuário é alterado de um domínio federado para outro domínio federado.

#### <a name="scenarios"></a>Cenários
Para um usuário sincronizado, o sufixo UserPrincipalName foi alterado de um domínio federado para outro domínio federado local. Por exemplo, *userPrincipalName = bob\@contoso.com* foi alterado para *userprincipalname = Bob\@fabrikam.com*.

#### <a name="example"></a>Exemplo
1. Bob Smith, uma conta para Contoso.com, é adicionada como um novo usuário no Active Directory com o UserPrincipalName bob@contoso.com
2. Bob se move para uma divisão diferente de Contoso.com chamada Fabrikam.com e seu UserPrincipalName é alterado para bob@fabrikam.com
3. Os domínios contoso.com e fabrikam.com são domínios federados com Azure Active Directory.
4. O userPrincipalName de Bob não é atualizado e resulta em um erro de sincronização "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Como corrigir
Se o sufixo UserPrincipalName de um usuário tiver sido atualizado de bob@**contoso.com** para bob\@**fabrikam.com**, em que **contoso.com** e **fabrikam.com** são **domínios federados**, siga estas etapas para corrigir o erro de sincronização

1. Atualize o UserPrincipalName do usuário no Azure AD de bob@contoso.com para bob@contoso.onmicrosoft.com. Você pode usar o seguinte comando do PowerShell com o módulo do PowerShell do Azure AD: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Permitir que o próximo ciclo de sincronização tente a sincronização. Desta vez, a sincronização será bem-sucedida e atualizará o UserPrincipalName de Bob para bob@fabrikam.com conforme o esperado.

#### <a name="related-articles"></a>Artigos relacionados
* [As alterações não são sincronizadas pela ferramenta de sincronização Azure Active Directory depois que você altera o UPN de uma conta de usuário para usar um domínio federado diferente](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, o limite de comprimento ou o limite de contagem definido por Azure Active Directory esquema, a operação de sincronização resulta no erro de sincronização **grandeobject** ou **ExceededAllowedLength** . Normalmente, esse erro ocorre para os seguintes atributos

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Cenários possíveis
1. O atributo userCertificate de Bob está armazenando muitos certificados atribuídos a Bob. Eles podem incluir certificados mais antigos e expirados. O limite rígido é de 15 certificados. Para obter mais informações sobre como lidar com erros Grandesobject com o atributo userCertificate, consulte o artigo [Manipulando erros grandesobject causados pelo atributo userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. O atributo userSMIMECertificate de Bob está armazenando muitos certificados atribuídos a Bob. Eles podem incluir certificados mais antigos e expirados. O limite rígido é de 15 certificados.
3. O thumbnailPhoto de Bob definido em Active Directory é muito grande para ser sincronizado no Azure AD.
4. Durante a população automática do atributo ProxyAddresses no Active Directory, um objeto tem um número excessivo de ProxyAddresses atribuído.

### <a name="how-to-fix"></a>Como corrigir
1. Verifique se o atributo que está causando o erro está dentro da limitação permitida.

## <a name="existing-admin-role-conflict"></a>Conflito de função de administrador existente

### <a name="description"></a>Descrição
Um **conflito de função de administrador existente** ocorrerá em um objeto de usuário durante a sincronização quando o objeto de usuário tiver:

- permissões administrativas e
- o mesmo UserPrincipalName como um objeto existente do Azure AD

Azure AD Connect não tem permissão para fazer a correspondência flexível de um objeto de usuário do AD local com um objeto de usuário no Azure AD que tem uma função administrativa atribuída a ele.  Para obter mais informações, consulte [população userPrincipalName do Azure ad](plan-connect-userprincipalname.md)

![Administrador existente](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Como corrigir
Para resolver esse problema, siga um destes procedimentos:

 - Remova a conta do Azure AD (proprietário) de todas as funções de administrador. 
 - **Exclua** o objeto em quarentena na nuvem. 
 - O próximo ciclo de sincronização cuidará da correspondência flexível do usuário local para a conta de nuvem (já que o usuário da nuvem agora não é mais uma GA global). 
 - Restaure as associações de função para o proprietário. 

>[!NOTE]
>Você pode atribuir a função administrativa ao objeto de usuário existente novamente depois que a correspondência flexível entre o objeto de usuário local e o objeto de usuário do Azure AD for concluída.

## <a name="related-links"></a>Ligações relacionadas
* [Localizar objetos de Active Directory no Centro Administrativo do Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Como consultar Azure Active Directory de um objeto usando Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
