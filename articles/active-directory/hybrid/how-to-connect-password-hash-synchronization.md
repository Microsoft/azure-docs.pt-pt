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
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6451507eb5a25f432c73468d0da0db1838c8c9a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261388"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização de hash de palavra-passe com a sincronização do Azure AD Connect
Este artigo fornece informações que precisa sincronizar suas senhas de usuário de uma instância do Active Directory no local para uma instância do Azure Active Directory (Azure AD) com base na cloud.

## <a name="how-password-hash-synchronization-works"></a>Como funciona a sincronização de hash de palavra-passe
O serviço de domínio do Active Directory armazena as palavras-passe na forma de uma representação de valor de hash, a palavra-passe de utilizador reais. Um valor de hash é o resultado de uma função matemática de sentido único (o algoritmo de *hashing).* Não existe nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. 

Para sincronizar a palavra-passe, a sincronização do Azure AD Connect extrai o hash de palavra-passe da instância do Active Directory no local. Processamento extra de segurança é aplicado para o hash de palavra-passe antes dos dados foram sincronizados para o serviço de autenticação do Azure Active Directory. Palavras-passe são sincronizadas numa base por utilizador e por ordem cronológica.

O fluxo de dados reais do processo de sincronização de hash de palavra-passe é semelhante da sincronização dos dados de utilizador. No entanto, as palavras-passe são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de hash de palavra-passe é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Ao sincronizar uma palavra-passe, ele substitui a palavra-passe na cloud existente.

Na primeira vez que habilitar a funcionalidade de sincronização de hash de palavra-passe, ele executa uma sincronização inicial das palavras-passe de todos os utilizadores dentro do âmbito. Não é possível definir explicitamente um subconjunto de palavras-passe de utilizador que pretende sincronizar. No entanto, se existirem vários conectores, é possível desativar a sincronização de hash de palavra-passe para alguns conectores, mas não outros utilizando o [cmdlet Set-ADSyncADPasswordSyncConfiguration.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)

Quando altera uma palavra-passe no local, a palavra-passe atualizada está sincronizado, com mais freqüência em questão de minutos.
A funcionalidade de sincronização de hash de palavra-passe tenta automaticamente repete tentativas com falha de sincronização. Se ocorrer um erro durante uma tentativa para sincronizar uma palavra-passe, é registado um erro no Visualizador de eventos.

A sincronização de uma palavra-passe não tem impacto sobre o utilizador que tem atualmente sessão iniciado.
A sessão do serviço de nuvem atual não é afetada imediatamente por uma alteração de palavra-passe sincronizadas que ocorre, enquanto tem sessão iniciada, um serviço em nuvem. No entanto, quando o serviço em nuvem tem de autenticar novamente, terá de fornecer a sua nova palavra-passe.

Um utilizador tem de introduzir as credenciais da empresa uma segunda vez para autenticar para o Azure AD, independentemente se tem sessão iniciadas respetiva rede empresarial. Este padrão pode ser minimizado, no entanto, se o utilizador seleciona mantenha-me conectado na caixa de verificação (KMSI) no início de sessão. Esta seleção define um cookie de sessão que ignora a autenticação por 180 dias. Comportamento KMSI pode ser ativado ou desativado pelo administrador do Azure AD. Além disso, pode reduzir as indicações de palavra-passe ligando o [SSO Sem Emenda](how-to-connect-sso.md), que automaticamente institu os utilizadores quando estão nos seus dispositivos corporativos ligados à sua rede corporativa.

> [!NOTE]
> Sincronização de palavra-passe só é suportada para o utilizador de tipo de objeto no Active Directory. Não é suportada para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrição detalhada do funcionamento da sincronização de hash de palavra-passe

A seguinte secção descreve, detalhada, como funciona a sincronização de hash de palavra-passe entre o Active Directory e o Azure AD.

![Fluxo de palavra-passe detalhadas](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. A cada dois minutos, o agente de sincronização de hash de palavra-passe nos pedidos de servidor AD Connect armazenados hashes de palavra-passe (o atributo unicodePwd) de um controlador de domínio.  Este pedido é através do protocolo padrão de replicação [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) usado para sincronizar dados entre DCs. A conta de serviço tem de ter os replicar as alterações de diretório e replicar Directory todas as alterações de AD permissões (por predefinição em instalação), para obter a palavra-passe hashes.
2. Antes de enviar, o DC encripta o hash de senha MD4 utilizando uma chave que é um hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) da chave de sessão RPC e um sal. Em seguida, envia o resultado para o agente de sincronização de hash de palavra-passe através de RPC. O controlador de domínio também passa a salt para o agente de sincronização utilizando o protocolo de replicação do controlador de domínio, por isso, o agente será capaz de descriptografar o envelope.
3. Depois de o agente de sincronização de hash de palavra-passe ter o envelope encriptado, utiliza [o MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e o sal para gerar uma chave para desencriptar os dados recebidos de volta ao seu formato MD4 original. O agente de sincronização de hash de palavra-passe nunca tem acesso para a palavra-passe de texto não encriptado. Utilização de palavra-passe hash sincronização do agente de MD5 é estritamente para compatibilidade de protocolo de replicação com o controlador de domínio e só é utilizado no local entre o controlador de domínio e o agente de sincronização de hash de palavra-passe.
4. O agente de sincronização de hash de palavra-passe expande o hash de senha de binários de 16 bytes em 64 bytes, converta primeiro o hash para uma cadeia hexadecimal de 32 bytes, em seguida, convertendo essa cadeia de caracteres de volta no binário com codificação UTF-16.
5. O agente de sincronização de hash de palavra-passe adiciona um por salt de utilizador, que consiste de salt um comprimento de 10 bytes, para o binário de 64 bytes para proteger ainda mais o hash original.
6. O agente de sincronização de hash de palavra-passe combina então o hash MD4 mais o sal por utilizador e insere-o na função [PBKDF2.](https://www.ietf.org/rfc/rfc2898.txt) São utilizadas 1000 iterações do algoritmo de hashing com chaves [HMAC-SHA256.](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 
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

Se um utilizador estiver no âmbito da sincronização de hash de palavra-passe, por padrão, a palavra-passe da conta na nuvem está definida para *Nunca Expirar*.

Pode continuar a iniciar sessão para os serviços cloud, com uma palavra-passe sincronizada expirado em seu ambiente no local. A palavra-passe da cloud é atualizada da próxima vez que alterar a palavra-passe no ambiente no local.

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

Uma vez ativado, o Azure AD não vai a cada utilizador sincronizado remover o valor `DisablePasswordExpiration` do atributo PasswordPolicies. Em vez disso, o valor é definido para `None` durante a próxima sincronização de palavra-passe para cada utilizador quando alterar em seguida a sua palavra-passe em AD no local.  

Recomenda-se ativar o EnforceCloudPasswordPolicyForPasswordSyncedUsers, antes de ativar o sincronização de hash de palavra-passe, de modo a que a sincronização inicial de hashes de palavra-passe não adicione o valor `DisablePasswordExpiration` ao atributo passwordPolicies para os utilizadores.

A política de senha seletiva do Azure exige que os utilizadores alterem as suas palavras-passe a cada 90 dias. Se a sua política em AD também for de 90 dias, as duas políticas devem corresponder. No entanto, se a política de AD não for de 90 dias, pode atualizar a política de passwords Azure AD para corresponder utilizando o comando Set-MsolPasswordPolicy PowerShell.

A Azure AD suporta uma política de expiração de senha separada por domínio registado.

Caveat: Se houver contas sincronizadas que precisam de ter senhas não expiradas em AD Azure, deve adicionar explicitamente o valor `DisablePasswordExpiration` ao atributo passwordPolicies do objeto de utilizador em Azure AD.  Pode fazê-lo executando o seguinte comando.

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

Se a sua organização utiliza o atributo accountExpires como parte da gestão de contas de utilizador, este atributo não está sincronizado com o Azure AD. Como resultado, uma conta do Active Directory expirada num ambiente configurado para sincronização de hash de palavra-passe ainda estará ativa no Azure AD. Recomendamos que, se a conta estiver caducada, uma ação de fluxo de trabalho deve desencadear um script PowerShell que desative a conta AD Azure do utilizador (utilize o cmdlet [Set-AzureADUser).](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) Por outro lado, quando a conta estiver ativada, a instância do Azure AD deve ser ativada.

### <a name="overwrite-synchronized-passwords"></a>Substituir as palavras-passe sincronizadas

Um administrador pode repor a palavra-passe manualmente com o Windows PowerShell.

Neste caso, a nova palavra-passe substitui a palavra-passe sincronizada e todas as políticas de palavra-passe definidas na cloud são aplicadas para a nova palavra-passe.

Se alterar a palavra-passe de locais novamente, a nova palavra-passe é sincronizado para a cloud e substitui a palavra-passe atualizada manualmente.

A sincronização de uma palavra-passe não tem qualquer impacto no utilizador do Azure que tenha sessão iniciado. A sessão do serviço de nuvem atual não é afetada imediatamente por uma alteração de palavra-passe sincronizadas que ocorre enquanto tem sessão iniciada num serviço em nuvem. KMSI estende a duração essa diferença. Quando o serviço em nuvem tem de autenticar novamente, tem de fornecer a sua nova palavra-passe.

### <a name="additional-advantages"></a>Vantagens adicionais

- Em geral, a sincronização de hash de palavra-passe é mais simples de implementar do que um serviço de Federação. Ele não requer quaisquer servidores adicionais e elimina a dependência de um serviço de Federação de elevada disponibilidade para autenticar os utilizadores.
- Também pode ser ativada a sincronização de hash de palavra-passe, além de Federação. Pode ser utilizada como contingência se o seu serviço de Federação sofrer um período de indisponibilidade.

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

Se utilizar definições personalizadas quando instalar o Azure AD Connect, sincronização de hash de palavra-passe está disponível na página de início de sessão do utilizador. Para mais informações, consulte a [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

![Ativar a sincronização de hash de palavras-passe](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronização de hash de palavra-passe e FIPS
Se o servidor tiver sido bloqueado para baixo, de acordo com Federal Information Processing Standard (FIPS), em seguida, MD5 está desativada.

**Para ativar o MD5 para a sincronização de hash de palavra-passe, execute os seguintes passos:**

1. Vá para Sync\Bin %programfiles%\Azure AD.
2. Abra miiserver.exe.config.
3. Vá para o nó de configuração/tempo de execução no final do ficheiro.
4. Adicione o seguinte nó: `<enforceFIPSPolicy enabled="false"/>`
5. Guarde as alterações.

Para referência, este trecho de código é o que deve ser semelhante:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS, consulte a [sincronização de hash, encriptação e conformidade com a palavra-passe da AD Azure.](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshoot-password-hash-synchronization"></a>Resolver problemas de sincronização de hash de palavra-passe
Se tiver problemas com a sincronização de hash de senha, consulte a sincronização de hash de [palavra-passe de Troubleshoot](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos seguintes
* [Sincronização Azure AD Connect: Opções de sincronização personalizantes](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Obtenha um plano de implementação passo a passo para migrar da ADFS para a Sincronização de Hash Password](https://aka.ms/authenticationDeploymentPlan)
