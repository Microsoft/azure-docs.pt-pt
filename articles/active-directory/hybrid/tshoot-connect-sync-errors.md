---
title: 'Azure AD Connect: Erros de resolução de problemas durante a sincronização Microsoft Docs'
description: Explica como resolver erros encontrados durante a sincronização com o Azure AD Connect.
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
ms.openlocfilehash: 745ddcc95bb91e61478307265aec1ac8a7ebba54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75609201"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Erros de resolução de problemas durante a sincronização
Erros podem ocorrer quando os dados de identidade são sincronizados do Diretório Ativo do Servidor do Windows (AD DS) para o Diretório Ativo Azure (Azure AD). Este artigo fornece uma visão geral de diferentes tipos de erros de sincronização, alguns dos cenários possíveis que causam esses erros e formas potenciais de corrigir os erros. Este artigo inclui os tipos de erros comuns e pode não cobrir todos os erros possíveis.

 Este artigo assume que o leitor está familiarizado com os [conceitos de design subjacentes da Azure AD e Azure AD Connect](plan-connect-design-concepts.md).

Com a versão mais recente \(do Azure AD\)Connect agosto de 2016 ou superior, um relatório de Erros de Sincronização está disponível no [portal Azure](https://aka.ms/aadconnecthealth) como parte da Azure AD Connect Health para sincronização.

A partir de 1 de setembro de 2016, a funcionalidade de Resiliência de Atribuição de Atribuição de [Atributos de Diretório Ativo Azure](how-to-connect-syncservice-duplicate-attribute-resiliency.md) será ativada por padrão para todos os *novos* Inquilinos de Diretório Ativo Azure. Esta funcionalidade será automaticamente ativada para os inquilinos existentes nos próximos meses.

A Azure AD Connect realiza três tipos de operações a partir dos diretórios que mantém em sincronização: Import, Synchronization e Export. Erros podem ocorrer em todas as operações. Este artigo centra-se principalmente em erros durante a Exportação para a AD Azure.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante exportação para a AD Azure
A secção seguinte descreve diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para a AD Azure utilizando o conector Azure AD. Este conector pode ser identificado pelo formato de nome "contoso. *onmicrosoft.com".*
Erros durante a Exportação para o \(Azure AD\) indicam que a operação \(adicionar,\) atualizar, eliminar etc. tentou o Motor de Sincronização de Ligação Azure AD no Diretório Ativo Azure falhou.

![Visão geral dos erros de exportação](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erros de desfasamento de dados
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
* Quando o \(motor\) de sincronização Azure AD Connect instrui o Diretório Ativo do Azure a adicionar ou atualizar objetos, o Azure AD corresponde ao objeto de entrada utilizando o atributo **sourceAnchor** ao **atributo imutável id** de objetos em Azure AD. Este jogo chama-se **Hard Match.**
* Quando a AD Azure **não encontra** nenhum objeto que corresponda ao atributo **imutável id com** o atributo **sourceAnchor** do objeto de entrada, antes de fornecer um novo objeto, recua para usar os atributos ProxyAddresss e UserPrincipalName para encontrar uma correspondência. Este jogo chama-se **Soft Match.** O Soft Match foi concebido para combinar com objetos já presentes em Azure AD (que são produzidos em Azure AD) com os novos objetos adicionados/atualizados durante a sincronização que representam a mesma entidade (utilizadores, grupos) nas instalações.
* O erro **invalidSoftMatch** ocorre quando o jogo difícil não encontra nenhum objeto correspondente **E** o jogo suave encontra um objeto correspondente, mas esse objeto tem um valor diferente do *Imutável Id* do que o *SourceAnchor*do objeto de entrada, sugerindo que o objeto correspondente foi sincronizado com outro objeto de premissas Ative Directory.

Por outras palavras, para que o jogo suave funcione, o objeto a ser comparado não deve ter qualquer valor para o *Id imutável*. Se qualquer objeto com um conjunto *imutável* com um valor estiver a falhar no hard-match mas a satisfazer os critérios de soft-match, a operação resultaria num erro de sincronização InvalidSoftMatch.

O esquema do Diretório Ativo Azure não permite que dois ou mais objetos tenham o mesmo valor dos seguintes atributos. \(Esta não é uma lista exaustiva.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> A funcionalidade de resiliência do [atributo adato do Azure AD](how-to-connect-syncservice-duplicate-attribute-resiliency.md) também está a ser lançada como o comportamento padrão do Diretório Ativo Azure.  Isto reduzirá o número de erros de sincronização vistos pelo Azure AD Connect (bem como outros clientes sincronizados) tornando o Azure AD mais resistente na forma como lida com proxyAddresses duplicados e atributos userPrincipalName presentes nos ambientes AD das instalações. Esta função não corrige os erros de duplicação. Portanto, os dados ainda precisam de ser corrigidos. Mas permite o fornecimento de novos objetos que, de outra forma, estão bloqueados de serem provisionados devido a valores duplicados em Azure AD. Isto também reduzirá o número de erros de sincronização devolvidos ao cliente de sincronização.
> Se esta funcionalidade estiver ativada para o seu Inquilino, não verá os erros de sincronização InvalidSoftMatch vistos durante o fornecimento de novos objetos.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Cenários de exemplo para InvalidSoftMatch
1. Dois ou mais objetos com o mesmo valor para os proxyAddresss atributos existem no diretório ativo no local. Só um está a ser aprovisionado em Azure AD.
2. Dois ou mais objetos com o mesmo valor para o atributo do userPrincipalName existem no Diretório Ativo no local. Só um está a ser aprovisionado em Azure AD.
3. Um objeto foi adicionado no diretório ativo no local com o mesmo valor de ProxyAddresses atribui-se ao de um objeto existente no Diretório Ativo Azure. O objeto adicionado nas instalações não está a ser provisionado no Diretório Ativo azure.
4. Um objeto foi adicionado nas instalações Ative Directory com o mesmo valor do atributo do userPrincipalName que o de uma conta no Diretório Ativo azure. O objeto não está a ser provisionado no Diretório Ativo azure.
5. Uma conta sincronizada foi transferida da Floresta A para a Floresta B. Azure AD Connect (motor de sincronização) estava a usar o atributo ObjectGUID para calcular o SourceAnchor. Após o movimento florestal, o valor do SourceAnchor é diferente. O novo objeto (da Floresta B) não está a sincronizar-se com o objeto existente em Azure AD.
6. Um objeto sincronizado foi acidentalmente apagado das instalações Ative Directory e um novo objeto foi criado em Diretório Ativo para a mesma entidade (como utilizador) sem apagar a conta no Diretório Ativo azure. A nova conta não sincroniza com o objeto Azure AD existente.
7. O Azure AD Connect foi desinstalado e reinstalado. Durante a reinstalação, um atributo diferente foi escolhido como SourceAnchor. Todos os objetos que tinham sincronizado anteriormente deixaram de sincronizar com o erro InvalidSoftMatch.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um utilizador sincronizado no Azure Ative Directory a partir de instalações Ative Directory of *contoso.com*
2. O Nome **principal** do utilizador de Bob Smith é definido como **bobs\@contoso.com**.
3. **"abcdefghijklmnopqrstuv=="** é o **SourceAnchor** calculado pela Azure AD Connect utilizando o **objetivo DE** Bob Smith a partir de instalações Ative Directory, que é o **idito imutável** para Bob Smith no Diretório Ativo Azure.
4. Bob também tem valores seguintes para o atributo **proxyAddresss:**
   * SMTP:bobs@contoso.com
   * SMTP:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
5. Um novo utilizador, **Bob Taylor,** é adicionado ao Diretório Ativo no local.
6. O Nome **principal** do utilizador de Bob Taylor está definido como **bobt\@contoso.com**.
7. **"abcdefghijkl0123456789=""** é a **fonteAnchor** calculada pela Azure AD Connect usando o **objetivo de** Bob Taylor a partir de instalações Ative Directory. O objeto de Bob Taylor ainda não se sincronizou com o Diretório Ativo Azure.
8. Bob Taylor tem os seguintes valores para o atributo proxyAddresss
   * SMTP:bobt@contoso.com
   * SMTP:bob.taylor@contoso.com
   * **smtp:\@bob contoso.com**
9. Durante a sincronização, o Azure AD Connect reconhecerá a adição de Bob Taylor nas instalações do Ative Directory e pedirá ao Azure AD para fazer a mesma alteração.
10. A Azure AD fará primeiro um jogo difícil. Ou seja, procurará se existe algum objeto com o Imutável Id igual a "abcdefghijkl0123456789==". O Hard Match falhará, uma vez que nenhum outro objeto em Azure AD terá esse idiável.
11. A Azure AD tentará então igualar Bob Taylor. Ou seja, irá pesquisar se existe algum objeto com proxyAddresss igual aos três valores, incluindo smtp:bob@contoso.com
12. A Azure AD vai encontrar o objeto de Bob Smith para corresponder aos critérios de soft-match. Mas este objeto tem o valor de ImutávelId = "abcdefghijklmnopqrstuv==". o que indica que este objeto foi sincronizado a partir de outro objeto de instalações Ative Directory. Assim, o Azure AD não pode comparar estes objetos e resulta num erro de sincronização **InvalidSoftMatch.**

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Como corrigir o erro InvalidSoftMatch
A razão mais comum para o erro InvalidSoftMatch é que dois objetos com diferentes SourceAnchor \(imutáveisId\) têm o mesmo valor para os atributos ProxyAddresss e/ou UserPrincipalName, que são usados durante o processo de soft-match no Azure AD. Para corrigir o Soft Match Inválido

1. Identifique os proxyAddresses duplicados, userPrincipalName ou outro valor de atributo que esteja a causar o erro. Identifique também \(quais\) dois ou mais objetos estão envolvidos no conflito. O relatório gerado pela [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual o objeto que deve continuar a ter o valor duplicado e qual o objeto que não deve.
3. Retire o valor duplicado do objeto que NÃO deve ter esse valor. Deve fazer a mudança no diretório de onde o objeto é proveniente. Em alguns casos, poderá ser necessário apagar um dos objetos em conflito.
4. Se fez a alteração no ad local, deixe o Azure AD Connect sincronizar a alteração.

Os relatórios de erro sincronizados dentro do Azure AD Connect Health para sincronização são atualizados a cada 30 minutos e incluem os erros da mais recente tentativa de sincronização.

> [!NOTE]
> ImutávelId, por definição, não deve mudar durante a vida útil do objeto. Se o Azure AD Connect não foi configurado com alguns dos cenários em mente da lista acima, pode acabar numa situação em que o Azure AD Connect calcula um valor diferente do SourceAnchor para o objeto AD que representa a mesma entidade (mesmo utilizador/grupo/contacto, etc.
>
>

#### <a name="related-articles"></a>Artigos Relacionados
* [Atributos duplicados ou inválidos impedem a sincronização do diretório no Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando a AD Azure tenta combinar dois objetos, é possível que dois objetos de diferentes "tipos de objetos" (tais como Utilizador, Grupo, Contacto, etc.) tenham os mesmos valores para os atributos utilizados para executar o soft match. Como a duplicação destes atributos não é permitida em AD Azure, a operação pode resultar em erro de sincronização "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Cenários de exemplo para erro objectTypeMismatch
* Um grupo de segurança ativado por correio é criado no Office 365. A Admin adiciona um novo utilizador ou contacto nas instalações aD (que ainda não está sincronizada com a AD Azure) com o mesmo valor para os ProxyAddresss atributos que o do grupo Office 365.

#### <a name="example-case"></a>Caso exemplo
1. A Administração cria um novo grupo de segurança ativado por correio no tax@contoso.comOffice 365 para o Departamento fiscal e fornece um endereço de e-mail como . Este grupo é atribuído ao ProxyAddresss valor de atributo de **SMTP:\@tax contoso.com**
2. Um novo utilizador junta-se Contoso.com e é criada uma conta para o utilizador nas instalações com o proxyAddress como **smtp:\@tax contoso.com**
3. Quando o Azure AD Connect sincronizar a nova conta de utilizador, obtém o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Como corrigir o erro do ObjectTypeMismatch
A razão mais comum para o erro ObjectTypeMismatch são dois objetos de tipo diferente (Utilizador, Grupo, Contacto, etc.) têm o mesmo valor para o atributo proxyAddresses. Para corrigir o ObjectTypeMismatch:

1. Identifique o valor duplicado do proxyAddresss (ou outro atributo) que está a causar o erro. Identifique também \(quais\) dois ou mais objetos estão envolvidos no conflito. O relatório gerado pela [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual o objeto que deve continuar a ter o valor duplicado e qual o objeto que não deve.
3. Retire o valor duplicado do objeto que NÃO deve ter esse valor. Note que deve fazer a alteração no diretório de onde o objeto é proveniente. Em alguns casos, poderá ser necessário apagar um dos objetos em conflito.
4. Se fez a alteração no ad local, deixe o Azure AD Connect sincronizar a alteração. O relatório de erro sincronizado dentro do Azure AD Connect Health para sincronização é atualizado a cada 30 minutos e inclui os erros da mais recente tentativa de sincronização.

## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AtribuivalueMustBeUnique
#### <a name="description"></a>Descrição
O esquema do Diretório Ativo Azure não permite que dois ou mais objetos tenham o mesmo valor dos seguintes atributos. Isto é, cada objeto em Azure AD é forçado a ter um valor único destes atributos em uma determinada instância.

* ProxyAddresses
* UserPrincipalName

Se o Azure AD Connect tentar adicionar um novo objeto ou atualizar um objeto existente com um valor para os atributos acima referidos que já está atribuído a outro objeto no Diretório Ativo do Azure, a operação resulta no erro de sincronização "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Cenários possíveis:
1. O valor duplicado é atribuído a um objeto já sincronizado, que entra em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um utilizador sincronizado no Azure Ative Directory a partir de instalações Ative Directory of contoso.com
2. O **UserPrincipalName** de Bob Smith nas instalações está definido como **\@bobs contoso.com**.
3. Bob também tem valores seguintes para o atributo **proxyAddresss:**
   * SMTP:bobs@contoso.com
   * SMTP:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
4. Um novo utilizador, **Bob Taylor,** é adicionado ao Diretório Ativo no local.
5. O Nome **principal** do utilizador de Bob Taylor está definido como **bobt\@contoso.com**.
6. **Bob Taylor** tem os seguintes valores para o atributo **ProxyAddresss** i. smtp: bobt@contoso.com ii. SMTP:bob.taylor@contoso.com
7. O objeto de Bob Taylor é sincronizado com a AD Azure com sucesso.
8. A Administração decidiu atualizar o **proxyAddresss** de Bob Taylor com o seguinte valor: i. **smtp:\@bob contoso.com**
9. A Azure AD tentará atualizar o objeto de Bob Taylor em Azure AD com o valor acima referido, mas essa operação falhará uma vez que o valor proxyAddresss já está atribuído a Bob Smith, resultando num erro "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Como corrigir o erro AttributeValueMustBeUnique
A razão mais comum para o erro AttributeValueMustBeUnique é dois objetos com diferentes SourceAnchor \(imutáveisId\) têm o mesmo valor para os atributos ProxyAddresss e/ou UserPrincipalName. Para corrigir o erro AttributeValueMustBeUnique

1. Identifique os proxyAddresses duplicados, userPrincipalName ou outro valor de atributo que está a causar o erro. Identifique também \(quais\) dois ou mais objetos estão envolvidos no conflito. O relatório gerado pela [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual o objeto que deve continuar a ter o valor duplicado e qual o objeto que não deve.
3. Retire o valor duplicado do objeto que NÃO deve ter esse valor. Note que deve fazer a alteração no diretório de onde o objeto é proveniente. Em alguns casos, poderá ser necessário apagar um dos objetos em conflito.
4. Se tiver feito a alteração no ad-local, deixe o Azure AD Connect sincronizar a alteração para que o erro seja corrigido.

#### <a name="related-articles"></a>Artigos Relacionados
-[Atributos duplicados ou inválidos impedem a sincronização do diretório no Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Falhas na validação de dados
### <a name="identitydatavalidationfailed"></a>Validação de Dados de IdentidadeFalhada
#### <a name="description"></a>Descrição
O Azure Ative Directory impõe várias restrições aos próprios dados antes de permitir que esses dados sejam escritos no diretório. Estas restrições são para garantir que os utilizadores finais obtêm as melhores experiências possíveis ao utilizar as aplicações que dependem destes dados.

#### <a name="scenarios"></a>Cenários
a. O valor do atributo userprincipalName tem caracteres inválidos/não suportados.
b. O atributo UserPrincipalName não segue o formato exigido.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Como corrigir o erro falhado da IdentidadeDataValidação
a. Certifique-se de que o atributo do userPrincipalName tem caracteres suportados e formato necessário.

#### <a name="related-articles"></a>Artigos Relacionados
* [Prepare-se para fornecer utilizadores através da sincronização do diretório para o Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>Erro de mudança de domínio federado
#### <a name="description"></a>Descrição
Este caso resulta num erro de sincronização **"FederatedDomainChangeError"** quando o sufixo do UserPrincipalName de um utilizador é alterado de um domínio federado para outro domínio federado.

#### <a name="scenarios"></a>Cenários
Para um utilizador sincronizado, o sufixo UserPrincipalName foi alterado de um domínio federado para outro domínio federado nas instalações. Por exemplo, *userPrincipalName\@= bob contoso.com* foi alterado para *\@UserPrincipalName = bob fabrikam.com*.

#### <a name="example"></a>Exemplo
1. Bob Smith, uma conta para Contoso.com, é adicionado como um novo utilizador em Diretório Ativo com o UserPrincipalNamebob@contoso.com
2. Bob muda-se para uma divisão diferente de Contoso.com chamado Fabrikam.com e o seu UserPrincipalName é alterado parabob@fabrikam.com
3. Tanto contoso.com como fabrikam.com domínios são domínios federados com Diretório Ativo Azure.
4. O userPrincipalName do Bob não é atualizado e resulta num erro de sincronização "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Como corrigir
Se o sufixo userprincipalName de um**contoso.com** utilizador\@foi atualizado de bob@ contoso.com para bob**fabrikam.com**, onde tanto **contoso.com** como **fabrikam.com** são **domínios federados**, siga estes passos para corrigir o erro de sincronização

1. Atualize o UserPrincipalName do utilizador em bob@contoso.com bob@contoso.onmicrosoft.comAzure AD de . Pode utilizar o seguinte comando PowerShell com o Módulo PowerShell Azure AD:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Deixe o próximo ciclo de sincronização tentar a sincronização. Desta vez, a sincronização será bem sucedida e atualizará bob@fabrikam.com o UserPrincipalName of Bob para o esperado.

#### <a name="related-articles"></a>Artigos Relacionados
* [As alterações não são sincronizadas pela ferramenta Azure Ative Directory Sync depois de alterar a UPN de uma conta de utilizador para utilizar um domínio federado diferente](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, o limite de comprimento ou o limite de contagem definido pelo esquema de diretório ativo do Azure, a operação de sincronização resulta no erro de sincronização **LargeObject** ou **ExceededAllowedLength.** Tipicamente este erro ocorre para os seguintes atributos

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Cenários possíveis
1. O atributo do Certificado de utilizador do Bob está a armazenar demasiados certificados atribuídos ao Bob. Estes podem incluir certificados antigos e expirados. O limite máximo são 15 certificados. Para obter mais informações sobre como lidar com erros do LargeObject com atributo do UserCertificate, consulte o artigo Manipulação de [erros de BigObject causados pelo atributo](tshoot-connect-largeobjecterror-usercertificate.md)do UserCertificate .
2. O atributo do utilizador do Bob, SMIMECertificate, está a armazenar demasiados certificados atribuídos ao Bob. Estes podem incluir certificados antigos e expirados. O limite máximo são 15 certificados.
3. O conjunto de miniaturas do BobPhoto em Ative Directory é demasiado grande para ser sincronizado em Azure AD.
4. Durante a população automática do proxyAddresss atribui-se em Diretório Ativo, um objeto tem demasiados ProxyAddresss atribuídos.

### <a name="how-to-fix"></a>Como corrigir
1. Certifique-se de que o atributo que causa o erro está dentro da limitação permitida.

## <a name="existing-admin-role-conflict"></a>Conflito de papéis de administrador existente

### <a name="description"></a>Descrição
Um conflito de **papéis** de administrador existente ocorrerá num objeto de utilizador durante a sincronização quando esse objeto de utilizador tiver:

- permissões administrativas e
- o mesmo Nome Principal utilizador como um objeto AD Azure existente

O Azure AD Connect não está autorizado a combinar um objeto de utilizador a partir de AD no local com um objeto de utilizador em Azure AD que tem uma função administrativa atribuída ao mesmo.  Para mais informações consulte a população de Utilizadores [de Anúncios Azure](plan-connect-userprincipalname.md)

![Administrador existente](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Como corrigir
Para resolver esta questão faça o seguinte:

1. Retire a conta Azure AD (proprietário) de todas as funções de administrador. 
2. **Eliminar duramente** o objeto de quarentena na nuvem. 
3. O próximo ciclo de sincronização cuidará da correspondência suave do utilizador no local com a conta cloud (uma vez que o utilizador da nuvem já não é um GA global). 
4. Restaurar os membros do papel para o proprietário. 

>[!NOTE]
>Pode atribuir novamente a função administrativa ao objeto de utilizador existente após a correspondência suave entre o objeto de utilizador no local e o objeto de utilizador da AD Azure ter terminado.

## <a name="related-links"></a>Ligações relacionadas
* [Localizar objetos de diretório ativo no Centro Administrativo de Diretório Ativo](https://technet.microsoft.com/library/dd560661.aspx)
* [Como consultar o Diretório Ativo azure para um objeto que usa o Diretório Ativo Azure PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
