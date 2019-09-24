---
title: Implementar a sincronização de hash de palavra-passe com o Azure AD Connect sync | Documentos da Microsoft
description: Fornece informações sobre como funciona a sincronização de hash de palavra-passe e como configurar.
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
ms.openlocfilehash: 0ce0ac4f40f3dd1bd7252689618459769d0aeb56
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203077"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização de hash de palavra-passe com a sincronização do Azure AD Connect
Este artigo fornece informações que precisa sincronizar suas senhas de usuário de uma instância do Active Directory no local para uma instância do Azure Active Directory (Azure AD) com base na cloud.

## <a name="how-password-hash-synchronization-works"></a>Como funciona a sincronização de hash de palavra-passe
O serviço de domínio do Active Directory armazena as palavras-passe na forma de uma representação de valor de hash, a palavra-passe de utilizador reais. Um valor de hash é um resultado de uma função de matemática unidirecional (a *algoritmo hash*). Não existe nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. 

Para sincronizar a palavra-passe, a sincronização do Azure AD Connect extrai o hash de palavra-passe da instância do Active Directory no local. Processamento extra de segurança é aplicado para o hash de palavra-passe antes dos dados foram sincronizados para o serviço de autenticação do Azure Active Directory. Palavras-passe são sincronizadas numa base por utilizador e por ordem cronológica.

O fluxo de dados reais do processo de sincronização de hash de palavra-passe é semelhante da sincronização dos dados de utilizador. No entanto, as palavras-passe são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de hash de palavra-passe é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Ao sincronizar uma palavra-passe, ele substitui a palavra-passe na cloud existente.

Na primeira vez que habilitar a funcionalidade de sincronização de hash de palavra-passe, ele executa uma sincronização inicial das palavras-passe de todos os utilizadores dentro do âmbito. Não é possível definir explicitamente um subconjunto de palavras-passe de utilizador que pretende sincronizar.

Quando altera uma palavra-passe no local, a palavra-passe atualizada está sincronizado, com mais freqüência em questão de minutos.
A funcionalidade de sincronização de hash de palavra-passe tenta automaticamente repete tentativas com falha de sincronização. Se ocorrer um erro durante uma tentativa para sincronizar uma palavra-passe, é registado um erro no Visualizador de eventos.

A sincronização de uma palavra-passe não tem impacto sobre o utilizador que tem atualmente sessão iniciado.
A sessão do serviço de nuvem atual não é afetada imediatamente por uma alteração de palavra-passe sincronizadas que ocorre, enquanto tem sessão iniciada, um serviço em nuvem. No entanto, quando o serviço em nuvem tem de autenticar novamente, terá de fornecer a sua nova palavra-passe.

Um utilizador tem de introduzir as credenciais da empresa uma segunda vez para autenticar para o Azure AD, independentemente se tem sessão iniciadas respetiva rede empresarial. Este padrão pode ser minimizado, no entanto, se o utilizador seleciona mantenha-me conectado na caixa de verificação (KMSI) no início de sessão. Esta seleção define um cookie de sessão que ignora a autenticação por 180 dias. Comportamento KMSI pode ser ativado ou desativado pelo administrador do Azure AD. Além disso, pode reduzir os pedidos de palavra-passe, ativando [SSO totalmente integrado](how-to-connect-sso.md), que inicia automaticamente os utilizadores quando estão nos respetivos dispositivos empresariais ligados à sua rede empresarial.

> [!NOTE]
> Sincronização de palavra-passe só é suportada para o utilizador de tipo de objeto no Active Directory. Não é suportada para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrição detalhada do funcionamento da sincronização de hash de palavra-passe

A seguinte secção descreve, detalhada, como funciona a sincronização de hash de palavra-passe entre o Active Directory e o Azure AD.

![Fluxo de palavra-passe detalhadas](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. A cada dois minutos, o agente de sincronização de hash de palavra-passe nos pedidos de servidor AD Connect armazenados hashes de palavra-passe (o atributo unicodePwd) de um controlador de domínio.  Este pedido é via a norma [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) protocolo de replicação utilizado para sincronizar dados entre controladores de domínio. A conta de serviço tem de ter os replicar as alterações de diretório e replicar Directory todas as alterações de AD permissões (por predefinição em instalação), para obter a palavra-passe hashes.
2. Antes de enviar, o controlador de domínio criptografa o hash de palavra-passe MD4 com uma chave que é um [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) hash da chave de sessão RPC e um salt. Em seguida, envia o resultado para o agente de sincronização de hash de palavra-passe através de RPC. O controlador de domínio também passa a salt para o agente de sincronização utilizando o protocolo de replicação do controlador de domínio, por isso, o agente será capaz de descriptografar o envelope.
3. Depois do agente de sincronização de hash de palavra-passe tiver o envelope criptografado, ele usa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e o salt para gerar uma chave para descriptografar os dados recebidos de volta para o formato MD4 original. O agente de sincronização de hash de palavra-passe nunca tem acesso para a palavra-passe de texto não encriptado. Utilização de palavra-passe hash sincronização do agente de MD5 é estritamente para compatibilidade de protocolo de replicação com o controlador de domínio e só é utilizado no local entre o controlador de domínio e o agente de sincronização de hash de palavra-passe.
4. O agente de sincronização de hash de palavra-passe expande o hash de senha de binários de 16 bytes em 64 bytes, converta primeiro o hash para uma cadeia hexadecimal de 32 bytes, em seguida, convertendo essa cadeia de caracteres de volta no binário com codificação UTF-16.
5. O agente de sincronização de hash de palavra-passe adiciona um por salt de utilizador, que consiste de salt um comprimento de 10 bytes, para o binário de 64 bytes para proteger ainda mais o hash original.
6. O agente de sincronização de hash de palavra-passe, em seguida, combina o hash MD4 mais a por salt de usuário e insere-lo para o [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) função. 1000 iterações do [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) algoritmo de hash com chave são usados. 
7. O agente de sincronização de hash de palavra-passe leva o hash de 32 bytes resultante, concatena ambos o por salt de utilizador e o número de SHA256 iterações a ele (para utilização pelo Azure AD), transmite, em seguida, a cadeia a partir do Azure AD Connect para o Azure AD através de SSL.</br> 
8. Quando um utilizador tenta iniciar sessão Azure AD e introduz a palavra-passe, a palavra-passe é executada através do mesmo MD4 + salt + PBKDF2 + HMAC-SHA256 processo. Se o hash resultante corresponda ao hash armazenado no Azure AD, o utilizador ter introduzido a palavra-passe correta e é autenticado.

> [!NOTE]
> O hash MD4 original não é transmitido para o Azure AD. Em vez disso, o hash de SHA256 do original MD4 hash é transmitido. Como resultado, se o hash armazenado no Azure AD é obtido, não pode ser utilizado num ataque de passagem do hash no local.

### <a name="security-considerations"></a>Considerações de segurança

Durante a sincronização de palavras-passe, a versão de texto sem formatação da sua palavra-passe não é exposta para o recurso de sincronização de hash de palavra-passe, para o Azure AD, ou qualquer um dos serviços associados.

É feita no Azure AD, em vez de na instância do Active Directory da organização a autenticação de utilizador. Os dados de palavra-passe de SHA256 armazenados no Azure AD – um hash do hash MD4 original, é mais seguro do que o que é armazenado no Active Directory. Além disso, porque não é possível desencriptar este hash SHA256, ele não pode ser recuperado para o ambiente do Active Directory da organização e apresentado como uma palavra-passe de utilizador válido num ataque de passagem do hash.

### <a name="password-policy-considerations"></a>Considerações de política de palavra-passe

Existem dois tipos de políticas de palavra-passe que são afetadas por ativar a sincronização de hash de palavra-passe:

* Política de complexidade de palavra-passe
* Política de expiração de palavra-passe

#### <a name="password-complexity-policy"></a>Política de complexidade de palavra-passe

Quando a sincronização de hash de palavra-passe está ativada, as políticas de complexidade de palavra-passe na sua instância do Active Directory no local substituir políticas de complexidade na cloud para os utilizadores sincronizados. Pode utilizar todas as palavras-passe válidas da sua instância do Active Directory no local para aceder aos serviços do Azure AD.

> [!NOTE]
> Palavras-passe para utilizadores que são criados diretamente na cloud, estão ainda sujeitos a políticas de palavra-passe, conforme definido na cloud.

#### <a name="password-expiration-policy"></a>Política de expiração de palavra-passe

Se um usuário estiver no escopo da sincronização de hash de senha, por padrão, a senha da conta de nuvem será definida para *nunca expirar*.

Pode continuar a iniciar sessão para os serviços cloud, com uma palavra-passe sincronizada expirado em seu ambiente no local. A palavra-passe da cloud é atualizada da próxima vez que alterar a palavra-passe no ambiente no local.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Visualização pública do recurso *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Se houver usuários sincronizados que interagem somente com os serviços integrados do Azure AD e também devem estar em conformidade com uma política de expiração de senha, você poderá forçá-los a cumprir sua política de expiração de senha do Azure AD habilitando o  *Recurso EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

Quando *EnforceCloudPasswordPolicyForPasswordSyncedUsers* está desabilitado (que é a configuração padrão), Azure ad Connect define o atributo PasswordPolicies de usuários sincronizados como "DisablePasswordExpiration". Isso é feito toda vez que a senha de um usuário é sincronizada e instrui o Azure AD a ignorar a política de expiração de senha de nuvem para esse usuário. Você pode verificar o valor do atributo usando o módulo do PowerShell do Azure AD com o seguinte comando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Para habilitar o recurso EnforceCloudPasswordPolicyForPasswordSyncedUsers, execute o seguinte comando usando o módulo MSOnline PowerShell:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers  $true`

Uma vez habilitado, o Azure ad não vai para cada usuário sincronizado para `DisablePasswordExpiration` remover o valor do atributo PasswordPolicies. Em vez disso, o valor é `None` definido como durante a próxima sincronização de senha para cada usuário quando ele altera sua senha no AD local.  

É recomendável habilitar EnforceCloudPasswordPolicyForPasswordSyncedUsers, antes de habilitar a sincronização de hash de senha, para que a sincronização inicial de hashes de senha não `DisablePasswordExpiration` adicione o valor ao atributo PasswordPolicies para os usuários.

A política de senha padrão do Azure AD exige que os usuários alterem suas senhas a cada 90 dias. Se sua política no AD também for de 90 dias, as duas políticas deverão corresponder. No entanto, se a política do AD não for de 90 dias, você poderá atualizar a política de senha do Azure AD para fazer a correspondência usando o comando do PowerShell Set-MsolPasswordPolicy.

O Azure AD dá suporte a uma política de expiração de senha separada por domínio registrado.

ADVERTÊNCIA Se houver contas sincronizadas que precisam ter senhas que não expiram no Azure AD, você deverá adicionar explicitamente o `DisablePasswordExpiration` valor ao atributo PasswordPolicies do objeto de usuário no Azure AD.  Você pode fazer isso executando o comando a seguir.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Este recurso está em visualização pública no momento.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Visualização pública de sincronização de senhas temporárias e "forçar senha no próximo logon"

É comum forçar um usuário a alterar sua senha durante o primeiro logon, especialmente depois que uma redefinição de senha de administrador ocorre.  Ele é comumente conhecido como definir uma senha "temporária" e é concluído verificando o sinalizador "o usuário deve alterar a senha no próximo logon" em um objeto de usuário no Active Directory (AD).
  
A funcionalidade de senha temporária ajuda a garantir que a transferência de propriedade da credencial seja concluída na primeira utilização, para minimizar a duração de tempo em que mais de um indivíduo tem conhecimento dessa credencial.

Para dar suporte a senhas temporárias no Azure ad para usuários sincronizados, você pode habilitar o recurso *ForcePasswordResetOnLogonFeature* , executando o seguinte comando no servidor de <AAD Connector Name> Azure ad Connect, substituindo pelo nome do conector específico para seu ambiente:

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

Você pode usar o seguinte comando para determinar o nome do conector:

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

ADVERTÊNCIA  Forçar um usuário a alterar sua senha no próximo logon requer uma alteração de senha ao mesmo tempo.  O AD Connect não selecionará o sinalizador forçar alteração de senha por si só, ele é suplementar à alteração de senha detectada que ocorre durante a sincronização de hash de senha.

> [!CAUTION]
> Se você não habilitar a redefinição de senha de autoatendimento (SSPR) nos usuários do Azure AD, haverá uma experiência confusa ao redefinir sua senha no Azure AD e, em seguida, tentar entrar no Active Directory com a nova senha, pois a nova senha não é válida em Active Directory . Você só deve usar esse recurso quando o SSPR e o Write-back de senha estiverem habilitados no locatário.

> [!NOTE]
> Este recurso está em visualização pública no momento.

#### <a name="account-expiration"></a>Expiração da conta

Se a sua organização utiliza o atributo accountExpires como parte da gestão de contas de utilizador, este atributo não está sincronizado com o Azure AD. Como resultado, uma conta do Active Directory expirada num ambiente configurado para sincronização de hash de palavra-passe ainda estará ativa no Azure AD. Recomendamos que, se a conta tiver expirada, uma ação de fluxo de trabalho deve acionar um script do PowerShell que desativa a conta de utilizador do Azure AD (utilizar o [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) cmdlet). Por outro lado, quando a conta estiver ativada, a instância do Azure AD deve ser ativada.

### <a name="overwrite-synchronized-passwords"></a>Substituir as palavras-passe sincronizadas

Um administrador pode repor a palavra-passe manualmente com o Windows PowerShell.

Neste caso, a nova palavra-passe substitui a palavra-passe sincronizada e todas as políticas de palavra-passe definidas na cloud são aplicadas para a nova palavra-passe.

Se alterar a palavra-passe de locais novamente, a nova palavra-passe é sincronizado para a cloud e substitui a palavra-passe atualizada manualmente.

A sincronização de uma palavra-passe não tem qualquer impacto no utilizador do Azure que tenha sessão iniciado. A sessão do serviço de nuvem atual não é afetada imediatamente por uma alteração de palavra-passe sincronizadas que ocorre enquanto tem sessão iniciada num serviço em nuvem. KMSI estende a duração essa diferença. Quando o serviço em nuvem tem de autenticar novamente, tem de fornecer a sua nova palavra-passe.

### <a name="additional-advantages"></a>Vantagens adicionais

- Em geral, a sincronização de hash de palavra-passe é mais simples de implementar do que um serviço de Federação. Ele não requer quaisquer servidores adicionais e elimina a dependência de um serviço de Federação de elevada disponibilidade para autenticar os utilizadores.
- Também pode ser ativada a sincronização de hash de palavra-passe, além de Federação. Pode ser utilizada como contingência se o seu serviço de Federação sofrer um período de indisponibilidade.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Processo de sincronização de hash de senha para Azure AD Domain Services

Se você usar Azure AD Domain Services para fornecer autenticação herdada para aplicativos e serviços que precisam usar Keberos, LDAP ou NTLM, alguns processos adicionais fazem parte do fluxo de sincronização de hash de senha. Azure AD Connect usa o seguinte processo adicional para sincronizar os hashes de senha para o Azure AD para uso no Azure AD Domain Services:

> [!IMPORTANT]
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

## <a name="enable-password-hash-synchronization"></a>Ativar a sincronização de hash de palavra-passe

>[!IMPORTANT]
>Se estiver a migrar do AD FS (ou outras tecnologias de Federação) para a sincronização de Hash de palavra-passe, recomendamos vivamente que siga o nosso guia de implementação detalhados publicado [aqui](https://aka.ms/adfstophsdpdownload).

Ao instalar o Azure AD Connect utilizando as **definições rápidas** opção, a sincronização de hash de palavra-passe é ativada automaticamente. Para obter mais informações, consulte [introdução ao Azure AD Connect com as definições rápidas](how-to-connect-install-express.md).

Se utilizar definições personalizadas quando instalar o Azure AD Connect, sincronização de hash de palavra-passe está disponível na página de início de sessão do utilizador. Para obter mais informações, consulte [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

![Ativar a sincronização de hash de palavra-passe](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronização de hash de palavra-passe e FIPS
Se o servidor tiver sido bloqueado para baixo, de acordo com Federal Information Processing Standard (FIPS), em seguida, MD5 está desativada.

**Para ativar o MD5 para sincronização de hash de palavra-passe, execute os seguintes passos:**

1. Vá para Sync\Bin %programfiles%\Azure AD.
2. Abra miiserver.exe.config.
3. Vá para o nó de configuração/tempo de execução no final do ficheiro.
4. Adicione o nó seguinte: `<enforceFIPSPolicy enabled="false"/>`
5. Guarde as alterações.

Para referência, este trecho de código é o que deve ser semelhante:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre a segurança e FIPS, consulte [sincronização de hash de palavra-passe do Azure AD, a encriptação e conformidade FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Resolver problemas de sincronização de hash de palavra-passe
Se tiver problemas com a sincronização de hash de palavra-passe, veja [resolver problemas de sincronização de hash de palavra-passe](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos seguintes
* [Sincronização do Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Obter um plano de implementação passo a passo para migrar do AD FS para sincronização de Hash de palavra-passe](https://aka.ms/authenticationDeploymentPlan)
