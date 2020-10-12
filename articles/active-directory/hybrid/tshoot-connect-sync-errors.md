---
title: 'Azure AD Connect: Erros de resolução de problemas durante a sincronização Microsoft Docs'
description: Explica como resolver os problemas encontrados durante a sincronização com o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd779c26cd523bbf33fa1be6c87f21b4415c152
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016423"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Erros de resolução de problemas durante a sincronização
Podem ocorrer erros quando os dados de identidade são sincronizados do Windows Server Ative Directory (AD DS) para o Azure Ative Directory (Azure AD). Este artigo fornece uma visão geral de diferentes tipos de erros de sincronização, alguns dos cenários possíveis que causam esses erros e formas potenciais de corrigir os erros. Este artigo inclui os tipos de erros comuns e pode não cobrir todos os erros possíveis.

 Este artigo pressupõe que o leitor está familiarizado com os [conceitos de design subjacentes de Azure AD e Azure AD Connect](plan-connect-design-concepts.md).

Com a versão mais recente do Azure AD Connect \( agosto de 2016 ou superior \) , um relatório de Erros de Sincronização está disponível no [portal Azure](https://aka.ms/aadconnecthealth) como parte do Azure AD Connect Health para sincronização.

A partir de 1 de setembro de 2016, a funcionalidade [duplicada de atributos do Azure Ative Directory Attribute Resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) será ativada por padrão para todos os *novos* Inquilinos do Diretório Ativo Azure. Esta funcionalidade será automaticamente ativada para os inquilinos existentes nos próximos meses.

A Azure AD Connect realiza três tipos de operações a partir dos diretórios que mantém em sincronização: Importação, Sincronização e Exportação. Erros podem ocorrer em todas as operações. Este artigo centra-se principalmente em erros durante a Exportação para Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante a exportação para Azure AD
A secção seguinte descreve diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para Azure AD utilizando o conector AZURE AD. Este conector pode ser identificado pelo formato de nome sendo "contoso". *onmicrosoft.com*".
Erros durante a Exportação para Azure AD indicam que a operação \( adicionar, atualizar, excluir etc. \) tentativa do Azure AD Connect \( Sync Engine no \) Azure Ative Directory falhou.

![Visão geral dos erros de exportação](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erros de incompatibilidade de dados
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
* Quando o motor de sincronização Azure AD Connect \( \) instrui o Azure Ative Directory a adicionar ou atualizar objetos, o Azure AD corresponde ao objeto de entrada utilizando o atributo **sourceAnchor** ao atributo **ImuttableId** de objetos em Azure AD. Este jogo chama-se **Hard Match**.
* Quando o Azure AD **não encontra** nenhum objeto que corresponda ao atributo **imutávelid** com o atributo **SourceAnchor** do objeto de entrada, antes de aprovisionar um novo objeto, volta a utilizar os atributos ProxyAddresses e UserPrincipalName para encontrar uma correspondência. Esta partida chama-se **Soft Match**. O Soft Match foi concebido para combinar os objetos já presentes no AZure AD (que são obtidos em Azure AD) com os novos objetos a serem adicionados/atualizados durante a sincronização que representam a mesma entidade (utilizadores, grupos) nas instalações.
* **InvalidSoftMatch** erro ocorre quando o jogo duro não encontra nenhum objeto correspondente **E** correspondência suave encontra um objeto correspondente, mas esse objeto tem um valor diferente de *ImuttableId* do que o *SourceAnchor*do objeto de entrada, sugerindo que o objeto correspondente foi sincronizado com outro objeto a partir das instalações Ative Directy.

Por outras palavras, para que o soft match funcione, o objeto a combinar com não deve ter qualquer valor para o *imutávelId*. Se algum objeto com *imutável conjunto* com um valor estiver a falhar no hard-match mas a satisfazer os critérios de soft-match, a operação resultaria num erro de sincronização invalidSoftMatch.

O esquema do Azure Ative Directory não permite que dois ou mais objetos tenham o mesmo valor dos seguintes atributos. \(Esta não é uma lista exaustiva.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [A azure AD Attribute Duplicate Attribute Resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) também está sendo lançado como o comportamento padrão do Azure Ative Directory.  Isto reduzirá o número de erros de sincronização vistos pelo Azure AD Connect (bem como outros clientes sincronizados) tornando a Azure AD mais resiliente na forma como lida com os atributos duplicados de ProxyAddresses e UserPrincipalName presentes em ambientes AD das instalações. Esta função não corrige os erros de duplicação. Portanto, os dados ainda precisam de ser corrigidos. Mas permite o fornecimento de novos objetos que, de outro modo, estão impedidos de serem aprovisionados devido a valores duplicados em Azure AD. Isto também reduzirá o número de erros de sincronização devolvidos ao cliente de sincronização.
> Se esta função estiver ativada para o seu Inquilino, não verá os erros de sincronização invalidSoftMatch vistos durante o fornecimento de novos objetos.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Cenários de exemplo para InvalidSoftMatch
1. Existem dois ou mais objetos com o mesmo valor para os atributos ProxyAddresses no Diretório Ativo no local. Só um está a ser atetado no Azure AD.
2. Existem dois ou mais objetos com o mesmo valor para o atributo UserPrincipalName no Ative Directory. Só um está a ser atetado no Azure AD.
3. Um objeto foi adicionado no diretório ativo nas instalações com o mesmo valor de ProxyAddresses atribuem que o de um objeto existente no Azure Ative Directory. O objeto adicionado no local não está a ser a provisionado no Diretório Ativo Azure.
4. Um objeto foi adicionado nas instalações Ative Directy com o mesmo valor do atributo UserPrincipalName que o de uma conta no Azure Ative Directory. O objeto não está a ser a provisionado no Diretório Ativo Azure.
5. Uma conta sincronizada foi transferida da Floresta A para a Floresta B. Azure AD Connect (motor de sincronização) estava a usar o atributo ObjectGUID para calcular o SourceAnchor. Após o movimento da floresta, o valor do SourceAnchor é diferente. O novo objeto (da Floresta B) está a falhar em sincronizar-se com o objeto existente em Azure AD.
6. Um objeto sincronizado foi acidentalmente eliminado nas instalações Ative Directory e um novo objeto foi criado em Ative Directory para a mesma entidade (tal como o utilizador) sem eliminar a conta no Azure Ative Directory. A nova conta não sincroniza com o objeto AZure AD existente.
7. O Azure AD Connect foi desinstalado e reinstalado. Durante a reinstalação, um atributo diferente foi escolhido como SourceAnchor. Todos os objetos que tinham sincronizado anteriormente deixaram de sincronizar com o erro invalidSoftMatch.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um utilizador sincronizado no Azure Ative Directory a partir das instalações Ative Directory of *contoso.com*
2. O Nome **Do UtilizadorPrincipal de** Bob Smith está definido como **bobs \@ contoso.com**.
3. **"abcdefghijlmnopqrstuv==** **"SourceAnchor"** é o SourceAnchor calculado pela Azure AD Connect utilizando **o objectGUID** de Bob Smith nas instalações Ative Directory, que é o **imutávelid** de Bob Smith em Azure Ative Directory.
4. Bob também tem valores a seguir para o atributo **proxyAddresses:**
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob \@ contoso.com**
5. Um novo utilizador, **Bob Taylor,** é adicionado ao diretório ativo nas instalações.
6. O Nome **Do Utilizador Do Bob** Taylor está definido como **bobt \@ contoso.com**.
7. **"abcdefghijkl0123456789=="= "abcdefghijl0123456789="=** **"fonte"** é a fonte calculada pela Azure AD Connect utilizando o **objectGUID** de Bob Taylor a partir das instalações Ative Directory. O objeto de Bob Taylor ainda não sincronizou com o Azure Ative Directory.
8. Bob Taylor tem os seguintes valores para o atributo proxyAddresses
   * smtp: bobt@contoso.com
   * smtp: bob.taylor@contoso.com
   * **smtp: bob \@ contoso.com**
9. Durante a sincronização, o Azure AD Connect reconhecerá a adição de Bob Taylor nas instalações Ative Directory e pedirá à Azure AD que faça a mesma alteração.
10. A Azure AD vai primeiro executar um jogo duro. Ou seja, procurará se houver algum objeto com o imutávelId igual a "abcdefghijkl0123456789=". Hard Match falhará, uma vez que nenhum outro objeto em Azure AD terá aquele imutávelId.
11. A Azure AD tentará então combinar com Bob Taylor. Ou seja, procurará se houver algum objeto com proxyAddresses iguais aos três valores, incluindo smtp: bob@contoso.com
12. A Azure AD vai encontrar o objeto de Bob Smith para corresponder aos critérios de soft-match. Mas este objeto tem o valor de imutávelD = "abcdefghijklmnopqrstuv=". o que indica que este objeto foi sincronizado a partir de outro objeto a partir das instalações Ative Directory. Assim, o Azure AD não pode combinar com estes objetos e resulta num erro de sincronização **InvalidSoftMatch.**

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Como corrigir erro invalidSoftMatch
A razão mais comum para o erro InvalidSoftMatch é dois objetos com imutableId sourceanchor \( \) diferentes têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName, que são usados durante o processo de soft-match em AD Azure. A fim de corrigir o Soft Match Inválido

1. Identifique os proxyAddresses duplicados, userPrincipalName ou outro valor de atributo que está a causar o erro. Identifique também quais dois \( ou mais \) objetos estão envolvidos no conflito. O relatório gerado pela [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual o objeto que deve continuar a ter o valor duplicado e qual o objeto que não deve.
3. Retire o valor duplicado do objeto que NÃO deve ter esse valor. Deve fazer a mudança no diretório de onde o objeto é proveniente. Em alguns casos, poderá ser necessário eliminar um dos objetos em conflito.
4. Se escoste a alteração no AD nas instalações, deixe o Azure AD Ligar a alteração.

Os relatórios de erro sincronizados dentro do Azure AD Connect Health para sincronização são atualizados a cada 30 minutos e incluem os erros da última tentativa de sincronização.

> [!NOTE]
> Imutável, por definição, não deve mudar durante a vida útil do objeto. Se o Azure AD Connect não estiver configurado com alguns dos cenários em mente da lista acima, poderá acabar numa situação em que o Azure AD Connect calcule um valor diferente do SourceAnchor para o objeto AD que representa a mesma entidade (mesmo utilizador/grupo/contacto, etc.) que tem um Objeto AD Azure existente que deseja continuar a utilizar.
>
>

#### <a name="related-articles"></a>Artigos Relacionados
* [Atributos duplicados ou inválidos impedem a sincronização do diretório na Microsoft 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando a Azure AD tenta combinar dois objetos suavemente, é possível que dois objetos de diferente "tipo de objeto" (como Utilizador, Grupo, Contacto, etc.) tenham os mesmos valores para os atributos utilizados para executar a combinação suave. Como a duplicação destes atributos não é permitida no Azure AD, a operação pode resultar num erro de sincronização "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Exemplo de cenários para erro objectTypeMismatch
* Um grupo de segurança ativado por correio é criado no Microsoft 365. A Admin adiciona um novo utilizador ou contacto nas instalações AD (que ainda não está sincronizado com Azure AD) com o mesmo valor para o atributo ProxyAddresses que o do grupo Microsoft 365.

#### <a name="example-case"></a>Caso de exemplo
1. A Admin cria um novo grupo de segurança habilitado para o e-mail na Microsoft 365 para o departamento fiscal e fornece um endereço de e-mail como tax@contoso.com . Este grupo é atribuído ao ProxyAddresses valor de atributo de **smtp: tax \@ contoso.com**
2. Um novo utilizador junta-se a Contoso.com e é criada uma conta para o utilizador nas instalações com o proxyAddress como **smtp: tax \@ contoso.com**
3. Quando o Azure AD Connect sincronizar a nova conta de utilizador, obterá o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Como corrigir erro objectTypeMismatch
A razão mais comum para o erro ObjectTypeMismatch é dois objetos de tipo diferente (Utilizador, Grupo, Contacto, etc.) têm o mesmo valor para o atributo ProxyAddresses. Para corrigir o ObjectTypeMismatch:

1. Identifique o valor duplicado de proxyAddresses (ou outro atributo) que está a causar o erro. Identifique também quais dois \( ou mais \) objetos estão envolvidos no conflito. O relatório gerado pela [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual o objeto que deve continuar a ter o valor duplicado e qual o objeto que não deve.
3. Retire o valor duplicado do objeto que NÃO deve ter esse valor. Note que deve fazer a alteração no diretório de onde o objeto é obtido. Em alguns casos, poderá ser necessário eliminar um dos objetos em conflito.
4. Se escoste a alteração no AD nas instalações, deixe o Azure AD Ligar a alteração. O relatório de erro sincronizado dentro do Azure AD Connect Health para sincronização é atualizado a cada 30 minutos e inclui os erros da última tentativa de sincronização.

## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AtributoValueMustBeUnique
#### <a name="description"></a>Descrição
O esquema do Azure Ative Directory não permite que dois ou mais objetos tenham o mesmo valor dos seguintes atributos. Este é cada objeto em Azure AD é forçado a ter um valor único destes atributos em um dado caso.

* ProxyAddresses
* UserPrincipalName

Se o Azure AD Connect tentar adicionar um novo objeto ou atualizar um objeto existente com um valor para os atributos acima que já está atribuído a outro objeto no Azure Ative Directory, a operação resulta no erro de sincronização "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Cenários possíveis:
1. O valor duplicado é atribuído a um objeto já sincronizado, que entra em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um utilizador sincronizado no Azure Ative Directory a partir das instalações Ative Directory of contoso.com
2. O Nome **Do UtilizadorPrincipal de** Bob Smith no local está definido como **bobs \@ contoso.com**.
3. Bob também tem valores a seguir para o atributo **proxyAddresses:**
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob \@ contoso.com**
4. Um novo utilizador, **Bob Taylor,** é adicionado ao diretório ativo nas instalações.
5. O Nome **Do Utilizador Do Bob** Taylor está definido como **bobt \@ contoso.com**.
6. **Bob Taylor** tem os seguintes valores para o atributo **ProxyAddresses** i. smtp: bobt@contoso.com ii. smtp: bob.taylor@contoso.com
7. O objeto de Bob Taylor está sincronizado com a Azure AD com sucesso.
8. A Admin decidiu atualizar o atributo **ProxyAddresses** de Bob Taylor com o seguinte valor: i. **smtp: bob \@ contoso.com**
9. A Azure AD tentará atualizar o objeto de Bob Taylor em AZure AD com o valor acima, mas essa operação falhará, uma vez que o valor da ProxyAddresses já está atribuído a Bob Smith, resultando num erro "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Como corrigir atributoValueMustBeUnique erro
A razão mais comum para o erro AttributeValueMustBeUnique é dois objetos com imutável SourceAnchor \( \) diferentes têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName. Para corrigir o erro do AtributoValueMustBeUnique

1. Identifique os proxyAddresses duplicados, userPrincipalName ou outro valor de atributo que está a causar o erro. Identifique também quais dois \( ou mais \) objetos estão envolvidos no conflito. O relatório gerado pela [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual o objeto que deve continuar a ter o valor duplicado e qual o objeto que não deve.
3. Retire o valor duplicado do objeto que NÃO deve ter esse valor. Note que deve fazer a alteração no diretório de onde o objeto é obtido. Em alguns casos, poderá ser necessário eliminar um dos objetos em conflito.
4. Se escoda a alteração no AD das instalações, deixe o Azure AD Ligar sincronizar a alteração para que o erro seja corrigido.

#### <a name="related-articles"></a>Artigos Relacionados
-[Atributos duplicados ou inválidos impedem a sincronização do diretório na Microsoft 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Falhas de validação de dados
### <a name="identitydatavalidationfailed"></a>IdentidadeDataValidationFailed
#### <a name="description"></a>Descrição
O Azure Ative Directory impõe várias restrições aos dados em si antes de permitir que esses dados sejam escritos no diretório. Estas restrições destinam-se a garantir que os utilizadores finais obtenham as melhores experiências possíveis ao utilizarem as aplicações que dependem destes dados.

#### <a name="scenarios"></a>Cenários
a. O valor do atributo UserPrincipalName tem caracteres inválidos/não suportados.
b. O atributo UserPrincipalName não segue o formato exigido.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Como corrigir IdentidadeDatasedireição Erro falhado
a. Certifique-se de que o atributo UserPrincipalName tem caracteres suportados e formato necessário.

#### <a name="related-articles"></a>Artigos Relacionados
* [Prepare-se para a provisionar utilizadores através da sincronização do diretório para o Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Descrição
Este caso resulta num erro de sincronização **"FederatedDomainChangeError"** quando o sufixo do UserPrincipalName de um utilizador é alterado de um domínio federado para outro domínio federado.

#### <a name="scenarios"></a>Cenários
Para um utilizador sincronizado, o sufixo de Nome Do UtilizadorPrincipal foi alterado de um domínio federado para outro domínio federado nas instalações. Por exemplo, *UserPrincipalName = bob \@ contoso.com* foi alterado para *UserPrincipalName = bob \@ fabrikam.com*.

#### <a name="example"></a>Exemplo
1. Bob Smith, uma conta para Contoso.com, é adicionado como um novo utilizador no Ative Directory com o UserPrincipalName bob@contoso.com
2. Bob muda-se para uma divisão diferente de Contoso.com chamada Fabrikam.com e seu Nome UserPrincipal é alterado para bob@fabrikam.com
3. Tanto os domínios contoso.com como fabrikam.com são domínios federados com o Azure Ative Directory.
4. O nome de utilizador do BobPrincipalName não é atualizado e resulta num erro de sincronização "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Como corrigir
Se o sufixo do Nome De Utilizador de um utilizador foi atualizado de bob@**contoso.com** para bob \@ **fabrikam.com**, onde tanto **contoso.com** como **fabrikam.com** são **domínios federados**, siga estes passos para corrigir o erro de sincronização

1. Atualize o Nome Do UtilizadorPrincipalName em Azure AD de bob@contoso.com bob@contoso.onmicrosoft.com . Pode utilizar o seguinte comando PowerShell com o Módulo AD PowerShell Azure: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Deixe o próximo ciclo de sincronização tentar a sincronização. Desta vez, a sincronização será bem sucedida e atualizará o Nome Do UtilizadorPrincipal de Bob bob@fabrikam.com como esperado.

#### <a name="related-articles"></a>Artigos Relacionados
* [As alterações não são sincronizadas pela ferramenta Azure Ative Directory Sync depois de alterar a UPN de uma conta de utilizador para utilizar um domínio federado diferente](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>Grande Polícia
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, limite de comprimento ou limite de contagem definido pelo esquema do Azure Ative Directory, a operação de sincronização resulta no erro de sincronização **LargeObject** ou **ExceededAllowedLength.** Tipicamente este erro ocorre para os seguintes atributos

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Possíveis Cenários
1. O atributo de utilizador do Bob Certifica está a armazenar demasiados certificados atribuídos ao Bob. Estes podem incluir certificados mais antigos e expirados. O limite é de 15 certificados. Para obter mais informações sobre como lidar com erros do LargeObject com o atributo userCertificate, consulte o artigo [Manuseamento de erros do LargeObject causados pelo atributo userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. O atributo de utilizador do Bob, OMIMECertificado, está a armazenar demasiados certificados atribuídos ao Bob. Estes podem incluir certificados mais antigos e expirados. O limite é de 15 certificados.
3. O conjunto de miniaturas do Bob em Ative Directory é demasiado grande para ser sincronizado no Azure AD.
4. Durante a população automática do atributo ProxyAddresses no Ative Directory, um objeto tem muitos ProxyAddresses atribuídos.

### <a name="how-to-fix"></a>Como corrigir
1. Certifique-se de que o atributo que está a causar o erro está dentro da limitação permitida.

## <a name="existing-admin-role-conflict"></a>Conflito de funções de administrador existente

### <a name="description"></a>Descrição
Um **conflito de funções de administrador existente** ocorrerá num objeto do utilizador durante a sincronização quando esse objeto do utilizador tiver:

- permissões administrativas e
- o mesmo Nome UserPrincipal como um objeto AD Azure existente

O Azure AD Connect não está autorizado a combinar suavemente um objeto do utilizador a partir de AD no local com um objeto do utilizador em Azure AD que tem uma função administrativa atribuída ao mesmo.  Para mais informações consulte [a população do Azure AD UserPrincipalName](plan-connect-userprincipalname.md)

![Administrador existente](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Como corrigir
Para resolver esta questão, faça o seguinte:

1. Remova a conta AD (proprietário) de todas as funções de administração. 
2. **Elimine duramente** o objeto de quarentena na nuvem. 
3. O próximo ciclo de sincronização irá tratar de combinar suavemente o utilizador no local com a conta cloud (uma vez que o utilizador da nuvem já não é um GA global). 
4. Restaurar os membros de função para o proprietário. 

>[!NOTE]
>Pode atribuir novamente a função administrativa ao objeto de utilizador existente após a partida suave entre o objeto do utilizador no local e o objeto do utilizador Azure AD.

## <a name="related-links"></a>Ligações relacionadas
* [Localizar objetos de diretório ativo no Centro Administrativo de Diretório Ativo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Como consultar o Azure Ative Directory para um objeto que usa o Azure Ative Directory PowerShell](/previous-versions/azure/jj151815(v=azure.100))