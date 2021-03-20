---
title: Implementar sincronização de hash de palavra-passe com sincronização Azure AD Connect | Microsoft Docs
description: Fornece informações sobre como funciona a sincronização de hash de palavra-passe e como se configurar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47d7d541ed7d9805641ffdfde381d482c8700006
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96858744"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização hash de palavras-passe com a sincronização do Azure AD Connect
Este artigo fornece informações de que precisa de sincronizar as suas palavras-passe de utilizador de uma instância do Ative Directory no local para uma instância do Azure Ative Directory (Azure AD) baseada na nuvem.

## <a name="how-password-hash-synchronization-works"></a>Como funciona a sincronização de hash de palavra-passe
O serviço de domínio Ative Directory armazena senhas sob a forma de uma representação de valor de haxixe, da senha de utilizador real. Um valor de haxixe é o resultado de uma função matemática unidirecional (o *algoritmo de hashing).* Não existe nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. 

Para sincronizar a sua palavra-passe, o Azure AD Connect sincroniza a sua palavra-passe a partir do exemplo do Ative Directory no local. O processamento de segurança extra é aplicado ao hash da palavra-passe antes de ser sincronizado com o serviço de autenticação do Azure Ative Directory. As palavras-passe são sincronizadas por utilizador e por ordem cronológica.

O fluxo real de dados do processo de sincronização de hash de palavra-passe é semelhante à sincronização dos dados do utilizador. No entanto, as palavras-passe são sincronizadas com mais frequência do que a janela de sincronização padrão de diretório para outros atributos. O processo de sincronização de hash de palavra-passe funciona a cada 2 minutos. Não é possível modificar a frequência deste processo. Quando sincroniza uma palavra-passe, substitui a palavra-passe em nuvem existente.

A primeira vez que ativa a funcionalidade de sincronização de hash de palavra-passe, realiza uma sincronização inicial das palavras-passe de todos os utilizadores em alcance. Não é possível definir explicitamente um subconjunto de palavras-passe do utilizador que pretende sincronizar. No entanto, se existirem vários conectores, é possível desativar a sincronização de haxixe de palavra-passe para alguns conectores, mas não para outros que utilizam o [cmdlet Set-ADSyncPasswordSyncConfiguration.](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md)

Quando se altera uma palavra-passe no local, a palavra-passe atualizada é sincronizada, na maioria das vezes em questão de minutos.
A função de sincronização de hash de palavra-passe automaticamente retribra tentativas falhadas de sincronização. Se ocorrer um erro durante uma tentativa de sincronizar uma palavra-passe, é registado um erro no seu visualizador de eventos.

A sincronização de uma palavra-passe não tem qualquer impacto no utilizador que está atualmente inscrito.
A sua sessão de serviço na nuvem atual não é imediatamente afetada por uma alteração de senha sincronizada que ocorre, enquanto estiver assinado, num serviço de cloud. No entanto, quando o serviço de nuvem requer que volte a autenticar, tem de fornecer a sua nova palavra-passe.

Um utilizador deve introduzir as suas credenciais corporativas uma segunda vez para autenticar a Azure AD, independentemente de estar inscrito na sua rede corporativa. Este padrão pode ser minimizado, no entanto, se o utilizador selecionar a caixa de verificação Keep me signed in (KMSI) no início de sação. Esta seleção define um cookie de sessão que contorna a autenticação durante 180 dias. O comportamento kmsi pode ser ativado ou desativado pelo administrador AZure AD. Além disso, é possível reduzir as solicitações de palavra-passe ligando [o Seamless SSO](how-to-connect-sso.md), que automaticamente assina os utilizadores quando estão nos seus dispositivos corporativos ligados à sua rede corporativa.

> [!NOTE]
> A sincronização de palavra-passe só é suportada para o utilizador do tipo de objeto no Ative Directory. Não é suportado para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrição detalhada de como funciona a sincronização do hash da palavra-passe

A secção seguinte descreve, em profundidade, como funciona a sincronização de hash de palavra-passe entre o Ative Directory e o Azure AD.

![Fluxo de senha detalhado](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. De dois em dois minutos, o agente de sincronização de hash de palavra-passe no servidor AD Connect solicita hashes de senha armazenadas (o atributo unicodePwd) de um DC.  Este pedido é através do protocolo padrão de replicação [MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) utilizado para sincronizar dados entre DCs. A conta de serviço deve ter Alterações de Diretório de Replica e Alterações de Diretório de Replicação Todas as permissões de AD (concedidas por padrão na instalação) para obter os hashes de palavra-passe.
2. Antes de enviar, o DC encripta o hash de senha MD4 utilizando uma chave que é um haxixe [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) da tecla de sessão RPC e um sal. Em seguida, envia o resultado para o agente de sincronização de hash de palavra-passe sobre o RPC. O DC também passa o sal para o agente de sincronização usando o protocolo de replicação de DC, para que o agente possa desencriptar o envelope.
3. Depois de o agente de sincronização de hash de palavra-passe ter o envelope encriptado, utiliza [o MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) e o sal para gerar uma chave para desencriptar os dados recebidos de volta ao seu formato MD4 original. O agente de sincronização de hash de palavra-passe nunca tem acesso à senha de texto clara. A utilização do agente de sincronização de haxixe de palavra-passe de MD5 é estritamente para compatibilidade do protocolo de replicação com o DC, e só é usada no local entre o DC e o agente de sincronização de hash password.
4. O agente de sincronização de hash de palavra-passe expande o hash de senha binária de 16 bytes para 64 bytes, convertendo primeiro o haxixe para uma corda hexadémica de 32 bytes, depois convertendo esta corda de volta em binário com a codificação UTF-16.
5. O agente de sincronização de hash de palavra-passe adiciona um sal por utilizador, constituído por um sal de 10 bytes, ao binário de 64 bytes para proteger ainda mais o haxixe original.
6. O agente de sincronização de hash de palavra-passe combina então o hash MD4 mais o sal por utilizador e introduz-o na função [PBKDF2.](https://www.ietf.org/rfc/rfc2898.txt) São utilizadas 1000 iterações do algoritmo de hashing com chave [HMAC-SHA256.](/dotnet/api/system.security.cryptography.hmacsha256) 
7. O agente de sincronização de hash de palavra-passe leva o haxixe de 32 byte resultante, concatena tanto o sal por utilizador como o número de iterações SHA256 (para utilização por Azure AD), em seguida, transmite a cadeia de Azure AD Connect a Azure AD sobre TLS.</br> 
8. Quando um utilizador tenta iniciar sação no Azure AD e introduz a sua palavra-passe, a palavra-passe é executada através do mesmo processo MD4+sal+PBKDF2+HMAC-SHA256. Se o haxixe resultante corresponder ao haxixe armazenado no Azure AD, o utilizador introduziu a palavra-passe correta e é autenticado.

> [!NOTE]
> O haxixe MD4 original não é transmitido para Azure AD. Em vez disso, o haxixe SHA256 do haxixe MD4 original é transmitido. Como resultado, se o haxixe armazenado em Azure AD for obtido, ele não pode ser usado em um ataque de passagem-o-haxixe no local.

### <a name="security-considerations"></a>Considerações de segurança

Ao sincronizar palavras-passe, a versão de texto simples da sua palavra-passe não está exposta à funcionalidade de sincronização de hash de palavra-passe, à Azure AD ou a qualquer um dos serviços associados.

A autenticação do utilizador ocorre contra a Azure AD e não contra a instância do próprio Ative Directory da organização. Os dados de senha SHA256 armazenados em Azure AD -- um haxixe do hash MD4 original -- são mais seguros do que os armazenados no Ative Directory. Além disso, como este haxixe SHA256 não pode ser desencriptado, não pode ser devolvido ao ambiente ative da organização e apresentado como uma senha de utilizador válida num ataque de passagem ao hash.

### <a name="password-policy-considerations"></a>Considerações de política de senha

Existem dois tipos de políticas de palavra-passe que são afetadas por permitir a sincronização de hash de palavra-passe:

* Política de complexidade de palavras-passe
* Política de expiração da palavra-passe

#### <a name="password-complexity-policy"></a>Política de complexidade de palavras-passe

Quando a sincronização de hash de palavra-passe é ativada, as políticas de complexidade da palavra-passe no seu local Desafinado de Direção Ativo substituem as políticas de complexidade na nuvem para utilizadores sincronizados. Pode utilizar todas as palavras-passe válidas a partir do seu local de acesso ao Ative Directory para aceder aos serviços Azure AD.

> [!NOTE]
> As palavras-passe para os utilizadores que são criados diretamente na nuvem ainda estão sujeitas a políticas de senhas, tal como definidas na nuvem.

#### <a name="password-expiration-policy"></a>Política de expiração da palavra-passe

Se um utilizador estiver no âmbito da sincronização de hash de palavra-passe, por padrão a palavra-passe da conta de nuvem é definida como *Never Expire*.

Pode continuar a iniciar súm na sua nuvem utilizando uma palavra-passe sincronizada que está expirada no seu ambiente no local. A sua palavra-passe em nuvem é atualizada da próxima vez que alterar a palavra-passe no ambiente no local.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Se existirem utilizadores sincronizados que interagem apenas com os serviços integrados AZure AD e que também devem cumprir uma política de expiração de senha, pode forçá-los a cumprir a sua política de expiração da palavra-passe AZure AD, permitindo a funcionalidade *EnforcePasswordPolicyForPasswordSyncedUsers.*

Quando *o EnforceCloudPasswordPolicyForPasswordSyncedUsers é desativado* (que é a definição padrão), o Azure AD Connect define o atributo PasswordPolicies de utilizadores sincronizados para "DisablePasswordExpiration". Isto é feito sempre que a palavra-passe de um utilizador é sincronizada e instrui a Azure AD a ignorar a política de validade da palavra-passe na nuvem para esse utilizador. Pode verificar o valor do atributo utilizando o módulo Azure AD PowerShell com o seguinte comando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Para ativar a funcionalidade EnforceCloudPasswordPolicyForPasswordSyncedUsers, executa o seguinte comando utilizando o módulo MSOnline PowerShell, conforme mostrado abaixo. Você teria que escrever sim para o parâmetro Enable como mostrado abaixo :

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Uma vez ativado, o AZure AD não vai a cada utilizador sincronizado para remover o `DisablePasswordExpiration` valor do atributo PasswordPolicies. Em vez disso, o `DisablePasswordExpiration` valor é removido da PasswordPolicies durante a próxima sincronização de haxixe de palavra-passe para cada utilizador, após a sua próxima alteração de palavra-passe no AD no local.

Recomenda-se ativar o EnforceCloudPasswordPolicyForPasswordSyncedUsers, antes de permitir a sincronização de haxixe de palavra-passe, de modo a que a sincronização inicial dos hashes de palavra-passe não adicione o `DisablePasswordExpiration` valor ao atributo PasswordPolicies para os utilizadores.

A política de senha AD Azure predefinida requer que os utilizadores alterem as suas palavras-passe a cada 90 dias. Se a sua política em AD também for de 90 dias, as duas políticas devem coincidir. No entanto, se a política de AD não for de 90 dias, pode atualizar a política de senha AD Azure para combinar utilizando o comando powerShell Set-MsolPasswordPolicy.

A Azure AD suporta uma política de expiração de senha separada por domínio registado.

Caveat: Se existirem contas sincronizadas que precisam de ter senhas não expiradas no AD AZure, deve adicionar explicitamente o `DisablePasswordExpiration` valor ao atributo PasswordPolicies do objeto do utilizador em Azure AD.  Pode fazê-lo executando o seguinte comando.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> O comando Set-MsolPasswordPolicy PowerShell não funcionará em domínios federados. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Sincronizar senhas temporárias e "Alterar palavras-passe no próximo logon"

É típico forçar um utilizador a alterar a sua palavra-passe durante a sua primeira sposição, especialmente depois de ocorrer um reset de palavra-passe de administração.  É vulgarmente conhecida como definição de uma palavra-passe "temporária" e é completada verificando a bandeira "User must change at next logon" num objeto de utilizador em Ative Directy (AD).
  
A funcionalidade de senha temporária ajuda a garantir que a transferência de propriedade da credencial seja concluída na primeira utilização, para minimizar a duração do tempo em que mais de um indivíduo tem conhecimento dessa credencial.

Para suportar palavras-passe temporárias em AZure AD para utilizadores sincronizados, pode ativar a função *ForcePasswordChangeOnLogOn,* executando o seguinte comando no seu servidor Azure AD Connect:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Forçar um utilizador a alterar a sua palavra-passe no próximo início de sê-lo requer uma alteração de senha ao mesmo tempo.  O Azure AD Connect não vai pegar a bandeira de alteração da palavra-passe de força por si só; é suplementar à alteração detetada da palavra-passe que ocorre durante a sincronização de haxixe de palavra-passe.

> [!CAUTION]
> Só deve utilizar esta funcionalidade quando o SSPR e o Password Writeback estiverem ativados no arrendatário.  Isto é para que, se um utilizador alterar a sua palavra-passe através de SSPR, seja sincronizado com o Ative Directory.

#### <a name="account-expiration"></a>Expiração da conta

Se a sua organização utilizar o atributo Despires como parte da gestão da conta de utilizador, este atributo não é sincronizado com a Azure AD. Como resultado, uma conta de Ative Directory expirada num ambiente configurado para sincronização de hash de palavra-passe continuará ativa no Azure AD. Recomendamos que, se a conta expirar, uma ação de fluxo de trabalho desencadeie um script PowerShell que desative a conta AD Azure do utilizador (utilize o [cmdlet Set-AzureADUser).](/powershell/module/azuread/set-azureaduser) Inversamente, quando a conta é ligada, a instância AD Azure deve ser ligada.

### <a name="overwrite-synchronized-passwords"></a>Palavras-passe sincronizadas de substituição

Um administrador pode redefinir manualmente a sua palavra-passe utilizando o Windows PowerShell.

Neste caso, a nova palavra-passe substitui a sua palavra-passe sincronizada e todas as políticas de palavra-passe definidas na nuvem são aplicadas à nova palavra-passe.

Se alterar novamente a palavra-passe no local, a nova palavra-passe é sincronizada na nuvem e substitui a palavra-passe atualizada manualmente.

A sincronização de uma palavra-passe não tem qualquer impacto no utilizador do Azure que está inscrito. A sua sessão atual de serviço na nuvem não é imediatamente afetada por uma alteração de senha sincronizada que ocorre enquanto está inscrito num serviço de cloud. A KMSI prolonga a duração desta diferença. Quando o serviço de nuvem requer que volte a autenticar, tem de fornecer a sua nova palavra-passe.

### <a name="additional-advantages"></a>Vantagens adicionais

- Geralmente, a sincronização de hash de palavra-passe é mais simples de implementar do que um serviço de federação. Não requer nenhum servidor adicional, e elimina a dependência de um serviço de federação altamente disponível para autenticar os utilizadores.
- A sincronização de hash de palavra-passe também pode ser ativada para além da federação. Pode ser usado como recuo se o seu serviço de federação sofrer uma paragem.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Processo de sincronização de hash de palavra-passe para serviços de domínio Ad AD Azure

Se utilizar os Serviços de Domínio Azure AD para fornecer autenticação legado para aplicações e serviços que necessitem de utilizar Kerberos, LDAP ou NTLM, alguns processos adicionais fazem parte do fluxo de sincronização de hash de palavra-passe. O Azure AD Connect utiliza o processo adicional seguinte para sincronizar hashes de palavra-passe para Azure AD para utilização nos Serviços de Domínio AD AZure:

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio Azure AD DS gerido para sincronizar objetos de volta ao Azure AD.
>
> O Azure AD Connect apenas sincroniza as hashes de senhas antigas quando ativa o Azure AD DS para o seu inquilino AZure AD. Os passos seguintes não são utilizados se utilizar apenas o Azure AD Connect para sincronizar um ambiente AD DS no local com a Azure AD.
>
> Se as suas aplicações antigas não utilizarem a autenticação NTLM ou ligações simples LDAP, recomendamos que desative a sincronização de hash de palavra-passe NTLM para Azure AD DS. Para obter mais informações, consulte [Desativar as suítes de cifra fracas e a sincronização de haxixe credencial NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect recupera a chave pública para a instância do inquilino dos Serviços de Domínio AD AZure.
1. Quando um utilizador altera a sua palavra-passe, o controlador de domínio no local armazena o resultado da alteração da palavra-passe (hashes) em dois atributos:
    * *unicodePwd* para o hash de senha NTLM.
    * *suplementoscretários* para o haxixe de senha Kerberos.
1. O Azure AD Connect deteta alterações de palavra-passe através do canal de replicação do diretório (atribui alterações que precisam de se replicar para outros controladores de domínio).
1. Para cada utilizador cuja palavra-passe foi alterada, o Azure AD Connect executa os seguintes passos:
    * Gera uma chave simétrica AES de 256 bits aleatória.
    * Gera um vetor de inicialização aleatório necessário para a primeira ronda de encriptação.
    * Extrai hashes de palavra-passe Kerberos dos *atributos suplementais DosCredentais.*
    * Verifica a configuração de segurança dos Serviços de Domínio Azure *AD SyncNtlmPasswords.*
        * Se esta definição for desativada, gera um haxixe NTLM aleatório e de alta entropia (diferente da palavra-passe do utilizador). Este haxixe é então combinado com os hashes de senha kerberos exatos dos *suplementosCrendetials atribuídos* numa única estrutura de dados.
        * Se ativado, combina o valor do atributo *unicodePwd* com os hashes de senha kerberos extraídos dos *inquéritos suplementares atribuídos* numa única estrutura de dados.
    * Encripta a estrutura de dados única utilizando a chave simétrica AES.
    * Encripta a chave simétrica AES utilizando a chave pública Azure AD Domain Services do arrendatário.
1. O Azure AD Connect transmite a chave simétrica encriptada da AES, a estrutura de dados encriptada que contém os hashes de palavra-passe e o vetor de inicialização para Azure AD.
1. A Azure AD armazena a chave simétrica encriptada da AES, a estrutura de dados encriptada e o vetor de inicialização para o utilizador.
1. O Azure AD empurra a chave simétrica encriptada da AES, a estrutura de dados encriptada e o vetor de inicialização utilizando um mecanismo interno de sincronização sobre uma sessão HTTP encriptada para serviços de domínio Ad Ad Azure.
1. AZure AD Domain Services recupera a chave privada para a instância do inquilino do cofre Azure Key.
1. Para cada conjunto de dados encriptados (representando a alteração de senha de um único utilizador), os Serviços de Domínio AD AD Azure executam então os seguintes passos:
    * Utiliza a sua chave privada para desencriptar a chave simétrica AES.
    * Usa a chave simétrica AES com o vetor de inicialização para desencriptar a estrutura de dados encriptada que contém os hashes de palavra-passe.
    * Escreve a palavra-passe Kerberos que recebe para o controlador de domínio Azure AD Domain Services. Os hashes são guardados no atributo *suplementar de reconhecimento* do objeto do utilizador que é encriptado para a chave pública do controlador de domínio Azure AD Services.
    * AZure AD Domain Services escreve o hash de palavra-passe NTLM que recebeu para o controlador de domínio Azure AD Domain Services. O haxixe é guardado no atributo *unicodePwd* do objeto do utilizador que é encriptado para a chave pública do controlador de domínio Ad Do AD Do Azure.

## <a name="enable-password-hash-synchronization"></a>Ativar a sincronização de palavras-passe de hash

>[!IMPORTANT]
>Se estiver a migrar de FS AD (ou outras tecnologias da federação) para a Sincronização de Password Hash, recomendamos vivamente que siga o nosso guia de implementação detalhado publicado [aqui.](https://aka.ms/adfstophsdpdownload)

Quando instala o Azure AD Connect utilizando a opção **Definições Expressas,** a sincronização de hash de palavra-passe está automaticamente ativada. Para obter mais informações, consulte [Começar com a Azure AD Connect utilizando as definições expressas](how-to-connect-install-express.md).

Se utilizar definições personalizadas quando instalar o Azure AD Connect, a sincronização de hash de palavra-passe está disponível na página de entrada do utilizador. Para mais informações, consulte [a instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

![Ativar a sincronização de hash de palavras-passe](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronização de hash de palavra-passe e FIPS
Se o seu servidor tiver sido bloqueado de acordo com o Federal Information Processing Standard (FIPS), então o MD5 está desativado.

**Para ativar a sincronização de haxixe de palavra-passe MD5, execute os seguintes passos:**

1. Vá a %programfiles%\Azure AD Sync\Bin.
2. Abra miiserver.exe.config.
3. Vá para o nó de configuração/tempo de execução no final do ficheiro.
4. Adicione o seguinte nó: `<enforceFIPSPolicy enabled="false"/>`
5. Guarde as alterações.

Para referência, este corte é o que deve parecer:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS, consulte [a sincronização de hash de palavra-passe AD Azure, encriptação e conformidade com o FIPS.](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshoot-password-hash-synchronization"></a>Sincronização de hash de palavra-passe de resolução de problemas
Se tiver problemas com a sincronização de hash de palavra-passe, consulte [a sincronização de hash da palavra-passe de resolução de problemas](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos seguintes
* [Azure AD Connect sincronização: Personalizar opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Obtenha um plano de implementação passo a passo para migrar da ADFS para a sincronização de hash password](https://aka.ms/authenticationDeploymentPlan)
