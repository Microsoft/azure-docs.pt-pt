---
title: Implementar a sincronização de hash de senha com Azure AD Connect sincronização | Microsoft Docs
description: Fornece informações sobre como funciona a sincronização de hash de senha e como configurá-la.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e77368c7c0c104e777595a16735a7cf1e797a48
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539008"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização de hash de senha com sincronização de Azure AD Connect
Este artigo fornece informações de que você precisa para sincronizar suas senhas de usuário de uma instância de Active Directory local para uma instância do Azure Active Directory (Azure AD) baseada em nuvem.

## <a name="how-password-hash-synchronization-works"></a>Como funciona a sincronização de hash de palavra-passe
O serviço de domínio Active Directory armazena senhas na forma de uma representação de valor de hash, da senha real do usuário. Um valor de hash é um resultado de uma função matemática unidirecional (o *algoritmo de hash*). Não existe nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. 

Para sincronizar sua senha, Azure AD Connect a sincronização extrai o hash de senha da instância Active Directory local. O processamento de segurança extra é aplicado ao hash de senha antes de ser sincronizado com o serviço de autenticação Azure Active Directory. As senhas são sincronizadas de acordo com o usuário e em ordem cronológica.

O fluxo de dados real do processo de sincronização de hash de senha é semelhante à sincronização de dados do usuário. No entanto, as senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de hash de senha é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Quando você sincroniza uma senha, ela substitui a senha de nuvem existente.

Na primeira vez que você habilitar o recurso de sincronização de hash de senha, ele executará uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que deseja sincronizar. No entanto, se houver vários conectores, será possível desabilitar a sincronização de hash de senha para alguns conectores, mas não para outros usando o cmdlet [set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) .

Quando você altera uma senha local, a senha atualizada é sincronizada, geralmente em questão de minutos.
O recurso de sincronização de hash de senha automaticamente tenta tentativas de sincronização com falha. Se ocorrer um erro durante uma tentativa de sincronizar uma senha, um erro será registrado em seu visualizador de eventos.

A sincronização de uma senha não tem impacto sobre o usuário que está conectado no momento.
Sua sessão de serviço de nuvem atual não é afetada imediatamente por uma alteração de senha sincronizada que ocorre, enquanto você está conectado a um serviço de nuvem. No entanto, quando o serviço de nuvem exigir que você se autentique novamente, você precisará fornecer sua nova senha.

Um usuário deve inserir suas credenciais corporativas uma segunda vez para se autenticar no Azure AD, independentemente de estar conectado à rede corporativa. No entanto, esse padrão pode ser minimizado se o usuário marcar a caixa de seleção Mantenha-me conectado (KMSI) ao entrar. Essa seleção define um cookie de sessão que ignora a autenticação por 180 dias. O comportamento de KMSI pode ser habilitado ou desabilitado pelo administrador do Azure AD. Além disso, você pode reduzir os prompts de senha ativando o [SSO contínuo](how-to-connect-sso.md), que conectará automaticamente os usuários quando eles estiverem em seus dispositivos corporativos conectados à sua rede corporativa.

> [!NOTE]
> A sincronização de senha só tem suporte para o usuário do tipo de objeto no Active Directory. Não há suporte para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrição detalhada de como funciona a sincronização de hash de senha

A seção a seguir descreve, detalhadamente, como funciona a sincronização de hash de senha entre Active Directory e o Azure AD.

![Fluxo de senha detalhado](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. A cada dois minutos, o agente de sincronização de hash de senha no servidor do AD Connect solicita hashes de senha armazenados (o atributo unicodePwd) de um controlador de domínio.  Essa solicitação é por meio do protocolo de replicação padrão [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) usado para sincronizar dados entre DCS. A conta de serviço deve ter replicar alterações de diretório e replicar as alterações de diretório todas as permissões do AD (concedidas por padrão na instalação) para obter os hashes de senha.
2. Antes de enviar, o DC criptografa o hash de senha MD4 usando uma chave que é um hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) da chave de sessão RPC e um Salt. Em seguida, ele envia o resultado para o agente de sincronização de hash de senha por RPC. O DC também passa o Salt para o agente de sincronização usando o protocolo de replicação de DC, de modo que o agente poderá descriptografar o envelope.
3. Depois que o agente de sincronização de hash de senha tiver o envelope criptografado, ele usará [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e o Salt para gerar uma chave para descriptografar os dados recebidos de volta para seu formato original MD4. O agente de sincronização de hash de senha nunca tem acesso à senha de texto não criptografado. O uso do MD5 do agente de sincronização de hash de senha é estritamente para a compatibilidade do protocolo de replicação com o controlador de domínio e é usado somente no local entre o controlador de domínio e o agente de sincronização de hash de senha.
4. O agente de sincronização de hash de senha expande o hash de senha binária de 16 bytes para 64 bytes convertendo primeiro o hash em uma cadeia de caracteres hexadecimal de 32 bytes e, em seguida, convertendo essa cadeia de caracteres de volta em binário com codificação UTF-16.
5. O agente de sincronização de hash de senha adiciona um Salt por usuário, que consiste em um Salt de 10 bytes, para o binário de 64 bytes para proteger ainda mais o hash original.
6. O agente de sincronização de hash de senha combina o hash MD4 mais o Salt por usuário e o insere na função [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iterações do algoritmo de hash [com chave HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) são usadas. 
7. O agente de sincronização de hash de senha usa o hash de 32 bytes resultante, concatena tanto o Salt por usuário quanto o número de iterações SHA256 a ele (para uso pelo Azure AD) e, em seguida, transmite a cadeia de caracteres de Azure AD Connect para o Azure AD sobre SSL.</br> 
8. Quando um usuário tenta entrar no Azure AD e insere sua senha, a senha é executada por meio do mesmo processo MD4 + Salt + PBKDF2 + HMAC-SHA256. Se o hash resultante corresponder ao hash armazenado no Azure AD, o usuário terá inserido a senha correta e será autenticado.

> [!NOTE]
> O hash MD4 original não é transmitido ao Azure AD. Em vez disso, o hash SHA256 do hash MD4 original é transmitido. Como resultado, se o hash armazenado no Azure AD for obtido, ele não poderá ser usado em um ataque de passagem de hash local.

### <a name="security-considerations"></a>Considerações de segurança

Ao sincronizar senhas, a versão de texto sem formatação da sua senha não é exposta ao recurso de sincronização de hash de senha, ao Azure AD ou a qualquer um dos serviços associados.

A autenticação do usuário ocorre no Azure AD, e não na instância da própria Active Directory da organização. Os dados de senha SHA256 armazenados no Azure AD – um hash do hash MD4 original – é mais seguro do que o que é armazenado em Active Directory. Além disso, como esse hash SHA256 não pode ser descriptografado, ele não pode ser levado de volta ao ambiente de Active Directory da organização e apresentado como uma senha de usuário válida em um ataque Pass-the-hash.

### <a name="password-policy-considerations"></a>Considerações sobre política de senha

Há dois tipos de diretivas de senha que são afetados pela habilitação da sincronização de hash de senha:

* Política de complexidade de senha
* Política de expiração de senha

#### <a name="password-complexity-policy"></a>Política de complexidade de senha

Quando a sincronização de hash de senha está habilitada, as políticas de complexidade de senha em sua instância de Active Directory local substituem as políticas de complexidade na nuvem para usuários sincronizados. Você pode usar todas as senhas válidas de sua instância de Active Directory local para acessar os serviços do Azure AD.

> [!NOTE]
> As senhas para usuários criados diretamente na nuvem ainda estão sujeitas a políticas de senha, conforme definido na nuvem.

#### <a name="password-expiration-policy"></a>Política de expiração de senha

Se um usuário estiver no escopo da sincronização de hash de senha, por padrão, a senha da conta de nuvem será definida para *nunca expirar*.

Você pode continuar a entrar em seus serviços de nuvem usando uma senha sincronizada que expirou em seu ambiente local. Sua senha de nuvem será atualizada na próxima vez que você alterar a senha no ambiente local.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Visualização pública do recurso *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Se houver usuários sincronizados que interagem somente com os serviços integrados do Azure AD e também devem estar em conformidade com uma política de expiração de senha, você poderá forçá-los a cumprir sua política de expiração de senha do Azure AD habilitando o recurso *EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

Quando *EnforceCloudPasswordPolicyForPasswordSyncedUsers* está desabilitado (que é a configuração padrão), Azure ad Connect define o atributo PasswordPolicies de usuários sincronizados como "DisablePasswordExpiration". Isso é feito toda vez que a senha de um usuário é sincronizada e instrui o Azure AD a ignorar a política de expiração de senha de nuvem para esse usuário. Você pode verificar o valor do atributo usando o módulo do PowerShell do Azure AD com o seguinte comando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Para habilitar o recurso EnforceCloudPasswordPolicyForPasswordSyncedUsers, execute o seguinte comando usando o módulo MSOnline PowerShell:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers -Enable $true`

Uma vez habilitado, o Azure AD não vai para cada usuário sincronizado para remover o valor de `DisablePasswordExpiration` do atributo PasswordPolicies. Em vez disso, o valor é definido como `None` durante a próxima sincronização de senha para cada usuário quando alteram sua senha no AD local.  

É recomendável habilitar EnforceCloudPasswordPolicyForPasswordSyncedUsers, antes de habilitar a sincronização de hash de senha, para que a sincronização inicial de hashes de senha não adicione o valor de `DisablePasswordExpiration` ao atributo PasswordPolicies para os usuários.

A política de senha padrão do Azure AD exige que os usuários alterem suas senhas a cada 90 dias. Se sua política no AD também for de 90 dias, as duas políticas deverão corresponder. No entanto, se a política do AD não for de 90 dias, você poderá atualizar a política de senha do Azure AD para fazer a correspondência usando o comando do PowerShell Set-MsolPasswordPolicy.

O Azure AD dá suporte a uma política de expiração de senha separada por domínio registrado.

ADVERTÊNCIA: se houver contas sincronizadas que precisam ter senhas que não expiram no Azure AD, você deverá adicionar explicitamente o valor de `DisablePasswordExpiration` ao atributo PasswordPolicies do objeto de usuário no Azure AD.  Você pode fazer isso executando o comando a seguir.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Este recurso está em visualização pública no momento.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Visualização pública de sincronização de senhas temporárias e "forçar senha no próximo logon"

É comum forçar um usuário a alterar sua senha durante o primeiro logon, especialmente depois que uma redefinição de senha de administrador ocorre.  Ele é comumente conhecido como definir uma senha "temporária" e é concluído verificando o sinalizador "o usuário deve alterar a senha no próximo logon" em um objeto de usuário no Active Directory (AD).
  
A funcionalidade de senha temporária ajuda a garantir que a transferência de propriedade da credencial seja concluída na primeira utilização, para minimizar a duração de tempo em que mais de um indivíduo tem conhecimento dessa credencial.

Para dar suporte a senhas temporárias no Azure AD para usuários sincronizados, você pode habilitar o recurso *ForcePasswordResetOnLogonFeature* , executando o seguinte comando no servidor de Azure ad Connect, substituindo <AAD Connector Name> pelo nome do conector específico ao seu ambiente:

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

Você pode usar o seguinte comando para determinar o nome do conector:

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

ADVERTÊNCIA: forçar um usuário a alterar sua senha no próximo logon requer uma alteração de senha ao mesmo tempo.  O AD Connect não selecionará o sinalizador forçar alteração de senha por si só, ele é suplementar à alteração de senha detectada que ocorre durante a sincronização de hash de senha.

> [!CAUTION]
> Se você não habilitar a redefinição de senha de autoatendimento (SSPR) nos usuários do Azure AD, haverá uma experiência confusa ao redefinir sua senha no Azure AD e, em seguida, tentar entrar no Active Directory com a nova senha, pois a nova senha não é válida em Active Directory . Você só deve usar esse recurso quando o SSPR e o Write-back de senha estiverem habilitados no locatário.

> [!NOTE]
> Este recurso está em visualização pública no momento.

#### <a name="account-expiration"></a>Expiração da conta

Se sua organização usar o atributo accountExpires como parte do gerenciamento de conta de usuário, esse atributo não será sincronizado com o Azure AD. Como resultado, uma conta de Active Directory expirada em um ambiente configurado para sincronização de hash de senha ainda estará ativa no Azure AD. Recomendamos que, se a conta tiver expirado, uma ação de fluxo de trabalho deverá disparar um script do PowerShell que desabilita a conta do Azure AD do usuário (use o cmdlet [set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) ). Por outro lado, quando a conta é ativada, a instância do Azure AD deve ser ativada.

### <a name="overwrite-synchronized-passwords"></a>Substituir senhas sincronizadas

Um administrador pode redefinir sua senha manualmente usando o Windows PowerShell.

Nesse caso, a nova senha substitui a senha sincronizada e todas as políticas de senha definidas na nuvem são aplicadas à nova senha.

Se você alterar sua senha local novamente, a nova senha será sincronizada com a nuvem e ela substituirá a senha atualizada manualmente.

A sincronização de uma senha não tem impacto sobre o usuário do Azure que está conectado. Sua sessão de serviço de nuvem atual não é afetada imediatamente por uma alteração de senha sincronizada que ocorre enquanto você está conectado a um serviço de nuvem. KMSI estende a duração dessa diferença. Quando o serviço de nuvem exigir que você se autentique novamente, você precisará fornecer sua nova senha.

### <a name="additional-advantages"></a>Vantagens adicionais

- Geralmente, a sincronização de hash de senha é mais simples de implementar do que um serviço de Federação. Ele não exige servidores adicionais e elimina a dependência de um serviço de Federação altamente disponível para autenticar usuários.
- A sincronização de hash de senha também pode ser habilitada, além da Federação. Ele poderá ser usado como um fallback se o serviço de Federação sofrer uma interrupção.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Processo de sincronização de hash de senha para Azure AD Domain Services

Se você usar Azure AD Domain Services para fornecer autenticação herdada para aplicativos e serviços que precisam usar Kerberos, LDAP ou NTLM, alguns processos adicionais serão parte do fluxo de sincronização de hash de senha. Azure AD Connect usa o seguinte processo adicional para sincronizar os hashes de senha para o Azure AD para uso no Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect só devem ser instalados e configurados para sincronização com ambientes de AD DS locais. Não há suporte para instalar Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta para o Azure AD.
>
> Azure AD Connect sincroniza somente os hashes de senha herdados quando você habilita o Azure AD DS para seu locatário do Azure AD. As etapas a seguir não serão usadas se você usar apenas Azure AD Connect para sincronizar um ambiente de AD DS local com o Azure AD.
>
> Se seus aplicativos herdados não usam autenticação NTLM ou associações simples LDAP, recomendamos que você desabilite a sincronização de hash de senha NTLM para o Azure AD DS. Para obter mais informações, consulte [desabilitar pacotes de criptografia fracos e sincronização de hash de credencial NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect recupera a chave pública para a instância do locatário do Azure AD Domain Services.
1. Quando um usuário altera sua senha, o controlador de domínio local armazena o resultado da alteração de senha (hashes) em dois atributos:
    * *unicodePwd* para o hash de senha NTLM.
    * *supplementalCredentials* para o hash de senha Kerberos.
1. Azure AD Connect detecta alterações de senha por meio do canal de replicação de diretório (alterações de atributo que precisam ser replicadas para outros controladores de domínio).
1. Para cada usuário cuja senha foi alterada, Azure AD Connect executa as seguintes etapas:
    * Gera uma chave simétrica do AES de 256 bits aleatória.
    * Gera um vetor de inicialização aleatória necessário para a primeira rodada de criptografia.
    * Extrai hashes de senha Kerberos dos atributos *supplementalCredentials* .
    * Verifica a configuração de *SyncNtlmPasswords* de configuração de segurança Azure AD Domain Services.
        * Se essa configuração estiver desabilitada, o gerará um hash NTLM aleatório de alta entropia (diferente da senha do usuário). Esse hash é então combinado com os hashes de senha Kerberos exatos do atributo *supplementalCrendetials* em uma estrutura de dados.
        * Se habilitado, combina o valor do atributo *unicodePwd* com os hashes de senha Kerberos extraídos do atributo *supplementalCredentials* em uma estrutura de dados.
    * Criptografa a estrutura de dados única usando a chave simétrica AES.
    * Criptografa a chave simétrica AES usando a chave pública de Azure AD Domain Services do locatário.
1. Azure AD Connect transmite a chave simétrica criptografada AES, a estrutura de dados criptografada que contém os hashes de senha e o vetor de inicialização para o Azure AD.
1. O Azure AD armazena a chave simétrica criptografada AES, a estrutura de dados criptografada e o vetor de inicialização para o usuário.
1. O Azure AD envia por push a chave simétrica criptografada AES, a estrutura de dados criptografada e o vetor de inicialização usando um mecanismo de sincronização interno em uma sessão HTTP criptografada para Azure AD Domain Services.
1. Azure AD Domain Services recupera a chave privada para a instância do locatário do Azure Key Vault.
1. Para cada conjunto de dados criptografado (representando a alteração de senha de um único usuário), Azure AD Domain Services executa as seguintes etapas:
    * Usa sua chave privada para descriptografar a chave simétrica AES.
    * Usa a chave simétrica AES com o vetor de inicialização para descriptografar a estrutura de dados criptografada que contém os hashes de senha.
    * Grava os hashes de senha Kerberos que ele recebe para o controlador de domínio Azure AD Domain Services. Os hashes são salvos no atributo *supplementalCredentials* do objeto de usuário que é criptografado para a chave pública do controlador de domínio Azure AD Domain Services.
    * Azure AD Domain Services grava o hash de senha NTLM recebido para o controlador de domínio Azure AD Domain Services. O hash é salvo no atributo *unicodePwd* do objeto de usuário que é criptografado para a chave pública do controlador de domínio Azure AD Domain Services.

## <a name="enable-password-hash-synchronization"></a>Ativar a sincronização de palavras-passe de hash

>[!IMPORTANT]
>Se você estiver migrando do AD FS (ou outras tecnologias de Federação) para a sincronização de hash de senha, é altamente recomendável que você siga nosso guia de implantação detalhado publicado [aqui](https://aka.ms/adfstophsdpdownload).

Quando você instala o Azure AD Connect usando a opção **configurações expressas** , a sincronização de hash de senha é habilitada automaticamente. Para obter mais informações, consulte [introdução ao Azure ad Connect usando as configurações expressas](how-to-connect-install-express.md).

Se você usar configurações personalizadas ao instalar o Azure AD Connect, a sincronização de hash de senha estará disponível na página de entrada do usuário. Para obter mais informações, consulte [instalação personalizada do Azure ad Connect](how-to-connect-install-custom.md).

![Ativar a sincronização de hash de palavras-passe](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronização de hash de senha e FIPS
Se o servidor tiver sido bloqueado de acordo com padrão FIPS (FIPS), o MD5 será desabilitado.

**Para habilitar o MD5 para a sincronização de hash de senha, execute as seguintes etapas:**

1. Vá para%programfiles%\Azure AD Sync\Bin.
2. Abra MIIServer. exe. config.
3. Vá para o nó Configuração/tempo de execução no final do arquivo.
4. Adicione o seguinte nó: `<enforceFIPSPolicy enabled="false"/>`
5. Guarde as alterações.

Para referência, esse trecho de código deve ser semelhante a:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS, consulte [sincronização de hash de senha do Azure AD, criptografia e conformidade com FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Solucionar problemas de sincronização de hash de senha
Se você tiver problemas com a sincronização de hash de senha, consulte [solucionar problemas de sincronização de hash de senha](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos seguintes
* [Sincronização de Azure AD Connect: personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Obtenha um plano de implantação passo a passo para migrar do ADFS para a sincronização de hash de senha](https://aka.ms/authenticationDeploymentPlan)
