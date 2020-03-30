---
title: Implementar sincronização de hash de senha com sincronização Azure AD Connect / Microsoft Docs
description: Fornece informações sobre como funciona a sincronização de hash de palavra-passe e como configurar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: c41b11ab65f5710d338ce0041579e1eb4678ec42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331373"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização hash de palavras-passe com a sincronização do Azure AD Connect
Este artigo fornece informações de que necessita de sincronizar as suas palavras-passe de utilizador de uma instância de Diretório Ativo no local para uma instância de Azure Ative Directory (Azure AD) baseada na nuvem.

## <a name="how-password-hash-synchronization-works"></a>Como funciona a sincronização de hash de palavra-passe
O serviço de domínio Ative Directory armazena senhas sob a forma de uma representação de valor hash, da senha real do utilizador. Um valor de hash é o resultado de uma função matemática de sentido único (o algoritmo de *hashing).* Não existe nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. 

Para sincronizar a sua palavra-passe, o sincronia Azure AD Connect extrai o seu hash de palavra-passe a partir da instância de Diretório Ativo no local. O processamento de segurança extra é aplicado ao hash de senha antes de ser sincronizado no serviço de autenticação do Diretório Ativo Azure. As palavras-passe são sincronizadas por utilizador e por ordem cronológica.

O fluxo real de dados do processo de sincronização de hash de palavra-passe é semelhante à sincronização dos dados dos utilizadores. No entanto, as palavras-passe são sincronizadas com mais frequência do que a janela padrão de sincronização do diretório para outros atributos. O processo de sincronização de hash de palavra-passe decorre a cada 2 minutos. Não é possível modificar a frequência deste processo. Ao sincronizar uma palavra-passe, substitui a senha de nuvem existente.

A primeira vez que ativa a funcionalidade de sincronização de hash de palavra-passe, realiza uma sincronização inicial das palavras-passe de todos os utilizadores no âmbito. Não é possível definir explicitamente um subconjunto de palavras-passe do utilizador que pretende sincronizar. No entanto, se existirem vários conectores, é possível desativar a sincronização de hash de palavra-passe para alguns conectores, mas não outros utilizando o [cmdlet Set-ADSyncADPasswordSyncConfiguration.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)

Quando muda uma senha no local, a palavra-passe atualizada é sincronizada, na maioria das vezes em questão de minutos.
A função de sincronização de hash de palavra-passe tenta automaticamente tentar sincronização falhada. Se ocorrer um erro durante uma tentativa de sincronização de uma palavra-passe, é registado um erro no seu visualizador de eventos.

A sincronização de uma palavra-passe não tem qualquer impacto no utilizador que está atualmente inscrito.
A sua atual sessão de serviço na nuvem não é imediatamente afetada por uma alteração de senha sincronizada que ocorre, enquanto está inscrito, num serviço na nuvem. No entanto, quando o serviço de nuvem requer que volte a autenticar, tem de fornecer a sua nova senha.

Um utilizador deve introduzir as suas credenciais corporativas uma segunda vez para autenticar a Azure AD, independentemente de estarem inscritos na sua rede corporativa. No entanto, este padrão pode ser minimizado se o utilizador selecionar a caixa de verificação Keep me signed in (KMSI) no registo. Esta seleção define um cookie de sessão que contorna a autenticação por 180 dias. O comportamento da KMSI pode ser ativado ou desativado pelo administrador da AD Azure. Além disso, pode reduzir as indicações de palavra-passe ligando o [SSO Sem Emenda](how-to-connect-sso.md), que automaticamente institu os utilizadores quando estão nos seus dispositivos corporativos ligados à sua rede corporativa.

> [!NOTE]
> A sincronização de palavras-passe só é suportada para o utilizador do tipo de objeto no Diretório Ativo. Não é suportado para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrição detalhada de como funciona a sincronização de hash de palavra-passe

A secção seguinte descreve, em profundidade, como funciona a sincronização de hash de senha entre o Ative Directory e o Azure AD.

![Fluxo de senha detalhado](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. De dois em dois minutos, o agente de sincronização de hash de palavra-passe no servidor AD Connect solicita hashes de palavra-passe armazenados (o atributo unicodePwd) de um DC.  Este pedido é através do protocolo padrão de replicação [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) usado para sincronizar dados entre DCs. A conta de serviço deve ter Alterações de Diretório replicadas e alterações de diretório de replicação Todas as permissões ad (concedidas por predefinição na instalação) para obter as hashes de senha.
2. Antes de enviar, o DC encripta o hash de senha MD4 utilizando uma chave que é um hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) da chave de sessão RPC e um sal. Em seguida, envia o resultado para o agente de sincronização de hash de palavra-passe sobre RPC. O DC também passa o sal para o agente de sincronização usando o protocolo de replicação de DC, para que o agente seja capaz de desencriptar o envelope.
3. Depois de o agente de sincronização de hash de palavra-passe ter o envelope encriptado, utiliza [o MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e o sal para gerar uma chave para desencriptar os dados recebidos de volta ao seu formato MD4 original. O agente de sincronização de hash de palavra-passe nunca tem acesso à senha de texto clara. A utilização do MD5 pelo agente de sincronização de hash por palavra-passe é estritamente para a compatibilidade do protocolo de replicação com o DC, e só é utilizada em instalações entre o DC e o agente de sincronização de hash de palavra-passe.
4. O agente de sincronização de hash de palavra-passe expande o hash binário de 16 bytes para 64 bytes, convertendo primeiro o hash para uma corda hexadecimal de 32 bytes, depois convertendo esta cadeia de volta em binário com a codificação UTF-16.
5. O agente de sincronização de hash de palavra-passe adiciona um sal por utilizador, composto por um sal de 10 bytes, ao binário de 64 bytes para proteger ainda mais o hash original.
6. O agente de sincronização de hash de palavra-passe combina então o hash MD4 mais o sal por utilizador e insere-o na função [PBKDF2.](https://www.ietf.org/rfc/rfc2898.txt) São utilizadas 1000 iterações do algoritmo de hashing com chaves [HMAC-SHA256.](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 
7. O agente de sincronização de hash de palavra-passe toma o hash de 32 bytes resultante, concatena tanto o sal por utilizador como o número de iterações SHA256 para ele (para utilização por Azure AD), em seguida, transmite a cadeia de Azure AD Connect para Azure AD através de TLS.</br> 
8. Quando um utilizador tenta iniciar sessão no Azure AD e introduz a sua palavra-passe, a palavra-passe é executada através do mesmo processo MD4+salt+PBKDF2+HMAC-SHA256. Se o hash resultante corresponder ao hash armazenado em Azure AD, o utilizador introduziu a senha correta e é autenticado.

> [!NOTE]
> O hash MD4 original não é transmitido para Azure AD. Em vez disso, o hash SHA256 do hash MD4 original é transmitido. Como resultado, se o hash armazenado em Azure AD for obtido, não pode ser usado num ataque de passe-o-hash no local.

### <a name="security-considerations"></a>Considerações de segurança

Ao sincronizar palavras-passe, a versão de texto simples da sua palavra-passe não está exposta à funcionalidade de sincronização de hash de palavra-passe, à AD Azure ou a qualquer um dos serviços associados.

A autenticação do utilizador ocorre contra a AD Azure e não contra a instância de Diretório Ativo da própria organização. Os dados de senha SHA256 armazenados em Azure AD -- um hash do hash MD4 original -- é mais seguro do que o armazenado no Ative Directory. Além disso, como este hash SHA256 não pode ser desencriptado, não pode ser trazido de volta para o ambiente ative diretório da organização e apresentado como uma senha de utilizador válida num ataque de passe-o-hash.

### <a name="password-policy-considerations"></a>Considerações de política de senha

Existem dois tipos de políticas de senha que são afetadas por permitir a sincronização de hash de senha:

* Política de complexidade de senhas
* Política de expiração de palavra-passe

#### <a name="password-complexity-policy"></a>Política de complexidade de senhas

Quando a sincronização de hash de palavra-passe é ativada, as políticas de complexidade da palavra-passe no seu caso Ative Diretório no local sobrepõem as políticas de complexidade na nuvem para utilizadores sincronizados. Pode utilizar todas as palavras-passe válidas da sua instância de Diretório Ativo no local para aceder aos serviços da Azure AD.

> [!NOTE]
> As palavras-passe para utilizadores que são criadas diretamente na nuvem ainda estão sujeitas a políticas de senha, tal como definidas na nuvem.

#### <a name="password-expiration-policy"></a>Política de expiração de palavra-passe

Se um utilizador estiver no âmbito da sincronização de hash de palavra-passe, por padrão, a palavra-passe da conta na nuvem está definida para *Nunca Expirar*.

Pode continuar a fazer sessão nos seus serviços na nuvem utilizando uma senha sincronizada que está expirada no seu ambiente no local. A sua palavra-passe na nuvem é atualizada da próxima vez que alterar a palavra-passe no ambiente no local.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Pré-visualização pública da funcionalidade *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Se existirem utilizadores sincronizados que apenas interagem com os serviços integrados da AD Azure e também devem cumprir uma política de expiração de palavra-passe, pode forçá-los a cumprir a sua política de expiração da palavra-passe AD Azure, permitindo a funcionalidade *EnforceCloudPasswordPolicyForPasswordSyncedUsers.*

Quando o *EnforceCloudPasswordPolicyForPasswordSyncedUsers* é desativado (que é a definição padrão), o Azure AD Connect define o atributo passwordPolicies de utilizadores sincronizados para "DisablePasswordExpiration". Isto é feito sempre que a palavra-passe de um utilizador é sincronizada e instrui o Azure AD a ignorar a política de expiração da palavra-passe em nuvem para esse utilizador. Pode verificar o valor do atributo utilizando o módulo PowerShell Azure AD com o seguinte comando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Para ativar a funcionalidade EnforceCloudPasswordPolicyForPasswordSyncedUsers, execute o seguinte comando utilizando o módulo MSOnline PowerShell, como mostrado abaixo. Teria de escrever sim para o parâmetro Enable, como mostrado abaixo:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Uma vez ativado, o Azure AD não vai a `DisablePasswordExpiration` cada utilizador sincronizado remover o valor do atributo PasswordPolicies. Em vez disso, `None` o valor é definido durante a próxima sincronização de palavra-passe para cada utilizador quando alterar em seguida a sua palavra-passe em AD no local.  

Recomenda-se ativar o EnforceCloudPasswordPolicyForPasswordSyncedUsers, antes de ativar o sincronização de hash de palavra-passe, de modo a que a sincronização inicial de hashes de palavra-passe não adicione o `DisablePasswordExpiration` valor ao atributo passwordPolicies para os utilizadores.

A política de senha seletiva do Azure exige que os utilizadores alterem as suas palavras-passe a cada 90 dias. Se a sua política em AD também for de 90 dias, as duas políticas devem corresponder. No entanto, se a política de AD não for de 90 dias, pode atualizar a política de passwords Azure AD para corresponder utilizando o comando Set-MsolPasswordPolicy PowerShell.

A Azure AD suporta uma política de expiração de senha separada por domínio registado.

Caveat: Se houver contas sincronizadas que precisam de ter senhas não expiradas em AD `DisablePasswordExpiration` Azure, deve adicionar explicitamente o valor ao atributo passwordPolicies do objeto de utilizador em Azure AD.  Pode fazê-lo executando o seguinte comando.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Esta funcionalidade está na Visualização Pública neste momento.
> O comando Set-MsolPasswordPolicy PowerShell não funcionará em domínios federados. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Pré-visualização pública de sincronização de senhas temporárias e "Alteração da Palavra-Passe da Força no Próximo Logon"

É típico forçar um utilizador a alterar a sua palavra-passe durante o primeiro início de sessão, especialmente após o reset da palavra-passe de administração.  É vulgarmente conhecido como definição de uma palavra-passe "temporária" e é completado verificando a bandeira "Utilizador deve alterar a palavra-passe no próximo logon" num objeto de utilizador em Diretório Ativo (AD).
  
A funcionalidade de senha temporária ajuda a garantir que a transferência de propriedade da credencial seja concluída na primeira utilização, para minimizar a duração do tempo em que mais de um indivíduo tem conhecimento dessa credencial.

Para suportar senhas temporárias no Azure AD para utilizadores sincronizados, pode ativar a funcionalidade *ForcePasswordChangeOnLogOn,* executando o seguinte comando no seu servidor Azure AD Connect:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Forçar um utilizador a alterar a sua palavra-passe no próximo login requer uma alteração de palavra-passe ao mesmo tempo.  O Azure AD Connect não captará por si só a bandeira de mudança de senha de força; é suplementar à alteração de palavra-passe detetada que ocorre durante a sincronização de hash password.

> [!CAUTION]
> Só deve utilizar esta funcionalidade quando o SSPR e a Password Writeback estiverem ativados no inquilino.  Isto é para que, se um utilizador alterar a sua palavra-passe via SSPR, será sincronizada para o Diretório Ativo.

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública neste momento.

#### <a name="account-expiration"></a>Expiração da conta

Se a sua organização utilizar o atributo da conta Expira como parte da gestão da conta de utilizador, este atributo não é sincronizado com a AD Azure. Como resultado, uma conta de Diretório Ativo expirada num ambiente configurado para sincronização de hash de palavra-passe continuará a estar ativa em Azure AD. Recomendamos que, se a conta estiver caducada, uma ação de fluxo de trabalho deve desencadear um script PowerShell que desative a conta AD Azure do utilizador (utilize o cmdlet [Set-AzureADUser).](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) Inversamente, quando a conta é ligada, a instância da AD Azure deve ser ligada.

### <a name="overwrite-synchronized-passwords"></a>Sobrepor palavras-passe sincronizadas

Um administrador pode redefinir manualmente a sua palavra-passe utilizando o Windows PowerShell.

Neste caso, a nova senha substitui a sua senha sincronizada, e todas as políticas de senha definidas na nuvem são aplicadas à nova palavra-passe.

Se alterar novamente a sua senha no local, a nova palavra-passe é sincronizada na nuvem e substitui a senha atualizada manualmente.

A sincronização de uma palavra-passe não tem impacto no utilizador azure que está inscrito. A sua atual sessão de serviço na nuvem não é imediatamente afetada por uma alteração de senha sincronizada que ocorre durante a sua assinatura num serviço na nuvem. A KMSI prolonga a duração desta diferença. Quando o serviço de nuvem requer que volte a autenticar, tem de fornecer a sua nova senha.

### <a name="additional-advantages"></a>Vantagens adicionais

- Geralmente, a sincronização de hash de palavra-passe é mais simples de implementar do que um serviço da federação. Não requer servidores adicionais e elimina a dependência de um serviço da federação altamente disponível para autenticar os utilizadores.
- A sincronização de hash de palavra-passe também pode ser ativada para além da federação. Pode ser usado como um recuo se o seu serviço da federação experimentar uma paragem.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Processo de sincronização de hash de palavra-passe para serviços de domínio Azure AD

Se utilizar os Serviços de Domínio Azure AD para fornecer autenticação legado para aplicações e serviços que precisam de utilizar Kerberos, LDAP ou NTLM, alguns processos adicionais fazem parte do fluxo de sincronização de hash de palavra-passe. O Azure AD Connect utiliza o seguinte processo adicional para sincronizar hashes de senha para a AD Azure para utilização nos Serviços de Domínio Azure AD:

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido pelo Azure AD DS para sincronizar objetos de volta ao Azure AD.
>
> O Azure AD Connect apenas sincroniza hashes de senha sincronia quando ativa o Azure AD DS para o seu inquilino Azure AD. Os seguintes passos não são utilizados se utilizar apenas o Azure AD Connect para sincronizar um ambiente AD DS no local com a AD Azure.
>
> Se as suas aplicações antigas não utilizarem a autenticação NTLM ou os simples encaixes LDAP, recomendamos que desative a sincronização de hash de senha NTLM para DS AD Azure. Para obter mais informações, consulte [Desativar as suites de cifra fraca e a sincronização de hash credencial NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. A Azure AD Connect recupera a chave pública para a instância do inquilino dos Serviços de Domínio Azure AD.
1. Quando um utilizador altera a sua palavra-passe, o controlador de domínio no local armazena o resultado da alteração da palavra-passe (hashes) em dois atributos:
    * *unicodePwd* para o hash de senha NTLM.
    * *credenciais suplementares* para o hash de senha Kerberos.
1. O Azure AD Connect deteta alterações de palavra-passe através do canal de replicação do diretório (alterações de atributos que precisam de se replicar a outros controladores de domínio).
1. Para cada utilizador cuja palavra-passe tenha mudado, o Azure AD Connect executa os seguintes passos:
    * Gera uma chave simétrica aleatória AES de 256 bits.
    * Gera um vetor de inicialização aleatório necessário para a primeira ronda de encriptação.
    * Extrai hashes de senha Kerberos dos *atributos suplementares Credenciais.*
    * Verifique a *configuração* de configuração de segurança dos Serviços de Domínio AD Azure.
        * Se esta definição for desativada, gera um hash NTLM aleatório e de alta entropia (diferente da palavra-passe do utilizador). Este haxixe é então combinado com as hashes de senha kerberos exigentes dos *atributos suplementares Crendetials* numa estrutura de dados.
        * Se ativado, combina o valor do atributo *unicodePwd* com as hashes de senha kerberos extraídas do *atributo suplementar Credenciais* numa estrutura de dados.
    * Encripta a estrutura de dados única utilizando a chave simétrica AES.
    * Encripta a chave simétrica da AES utilizando a chave pública do Azure AD Domain Services do inquilino.
1. O Azure AD Connect transmite a chave simétrica aes encriptada, a estrutura de dados encriptada que contém os hashes de senha e o vetor de inicialização para a AD Azure.
1. A Azure AD armazena a chave simétrica aes encriptada, a estrutura de dados encriptada e o vetor de inicialização para o utilizador.
1. A Azure AD empurra a chave simétrica encriptada da AES, a estrutura de dados encriptada e o vetor de inicialização usando um mecanismo de sincronização interna durante uma sessão de HTTP encriptada para os Serviços de Domínio AD Azure.
1. A Azure AD Domain Services recupera a chave privada para o caso do inquilino do cofre Azure Key.
1. Para cada conjunto de dados encriptados (representando a alteração de palavra-passe de um único utilizador), os Serviços de Domínio AD do Azure realizam então os seguintes passos:
    * Usa a sua chave privada para desencriptar a chave simétrica AES.
    * Usa a chave simétrica AES com o vetor de inicialização para desencriptar a estrutura de dados encriptada que contém os hashes de senha.
    * Escreve a palavra-passe Kerberos que recebe para o controlador de domínio azure AD Domain Services. As hashes são guardadas no atributo suplementar do objeto do utilizador *Credenciais* que é encriptado à chave pública do controlador de domínio Azure AD Services.
    * O Azure AD Domain Services escreve o hash de senha NTLM que recebeu para o controlador de domínio azure AD Domain Services. O hash é guardado no atributo *unicodePwd* do objeto do utilizador que é encriptado à chave pública do controlador de domínio Azure AD.

## <a name="enable-password-hash-synchronization"></a>Ativar a sincronização de palavras-passe de hash

>[!IMPORTANT]
>Se está a migrar de AD FS (ou outras tecnologias da federação) para a Sincronização de Hash Password, recomendamos vivamente que siga o nosso guia de implementação detalhado publicado [aqui](https://aka.ms/adfstophsdpdownload).

Quando instala o Azure AD Connect utilizando a opção **Definições expressas,** a sincronização do hash de palavra-passe é ativada automaticamente. Para mais informações, consulte [Iniciar-se com o Azure AD Connect utilizando definições expressas](how-to-connect-install-express.md).

Se utilizar as definições personalizadas quando instalar o Azure AD Connect, a sincronização de hash isisestá disponível na página de entrada do utilizador. Para mais informações, consulte a [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

![Ativar a sincronização de hash de palavras-passe](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronização de hash de palavra-passe e FIPS
Se o seu servidor tiver sido bloqueado de acordo com o Federal Information Processing Standard (FIPS), então o MD5 está desativado.

**Para ativar o MD5 para a sincronização de hash de palavra-passe, execute os seguintes passos:**

1. Vá a %programfiles%\Azure AD Sync\Bin.
2. Abra miiserver.exe.config.
3. Vá ao nó de configuração/tempo de execução no final do ficheiro.
4. Adicione o seguinte nó:`<enforceFIPSPolicy enabled="false"/>`
5. Guarde as alterações.

Para referência, este corte é o que deve parecer:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS, consulte a [sincronização de hash, encriptação e conformidade com a palavra-passe da AD Azure.](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshoot-password-hash-synchronization"></a>Sincronização de hash de palavra-passe de resolução de problemas
Se tiver problemas com a sincronização de hash de senha, consulte a sincronização de hash de [palavra-passe de Troubleshoot](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos seguintes
* [Sincronização Azure AD Connect: Opções de sincronização personalizantes](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Obtenha um plano de implementação passo a passo para migrar da ADFS para a Sincronização de Hash Password](https://aka.ms/authenticationDeploymentPlan)
