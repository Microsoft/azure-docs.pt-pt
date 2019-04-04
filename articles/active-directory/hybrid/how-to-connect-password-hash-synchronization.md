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
ms.openlocfilehash: 146fdc3ca2af708a96e6b9a604493eb63c2e6530
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916381"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização de hash de palavra-passe com a sincronização do Azure AD Connect
Este artigo fornece informações que precisa sincronizar suas senhas de usuário de uma instância do Active Directory no local para uma instância do Azure Active Directory (Azure AD) com base na cloud.

## <a name="how-password-hash-synchronization-works"></a>Como funciona a sincronização de hash de palavra-passe
O serviço de domínio do Active Directory armazena as palavras-passe na forma de uma representação de valor de hash, a palavra-passe de utilizador reais. Um valor de hash é um resultado de uma função de matemática unidirecional (a *algoritmo hash*). Não existe nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. Não pode utilizar um hash de palavra-passe para iniciar sessão na sua rede no local.

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
3.  Depois do agente de sincronização de hash de palavra-passe tiver o envelope criptografado, ele usa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e o salt para gerar uma chave para descriptografar os dados recebidos de volta para o formato MD4 original. O agente de sincronização de hash de palavra-passe nunca tem acesso para a palavra-passe de texto não encriptado. Utilização de palavra-passe hash sincronização do agente de MD5 é estritamente para compatibilidade de protocolo de replicação com o controlador de domínio e só é utilizado no local entre o controlador de domínio e o agente de sincronização de hash de palavra-passe.
4.  O agente de sincronização de hash de palavra-passe expande o hash de senha de binários de 16 bytes em 64 bytes, converta primeiro o hash para uma cadeia hexadecimal de 32 bytes, em seguida, convertendo essa cadeia de caracteres de volta no binário com codificação UTF-16.
5.  O agente de sincronização de hash de palavra-passe adiciona um por salt de utilizador, que consiste de salt um comprimento de 10 bytes, para o binário de 64 bytes para proteger ainda mais o hash original.
6.  O agente de sincronização de hash de palavra-passe, em seguida, combina o hash MD4 mais a por salt de usuário e insere-lo para o [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) função. 1000 iterações do [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) algoritmo de hash com chave são usados. 
7.  O agente de sincronização de hash de palavra-passe leva o hash de 32 bytes resultante, concatena ambos o por salt de utilizador e o número de SHA256 iterações a ele (para utilização pelo Azure AD), transmite, em seguida, a cadeia a partir do Azure AD Connect para o Azure AD através de SSL.</br> 
8.  Quando um utilizador tenta iniciar sessão Azure AD e introduz a palavra-passe, a palavra-passe é executada através do mesmo MD4 + salt + PBKDF2 + HMAC-SHA256 processo. Se o hash resultante corresponda ao hash armazenado no Azure AD, o utilizador ter introduzido a palavra-passe correta e é autenticado. 

>[!Note] 
>O hash MD4 original não é transmitido para o Azure AD. Em vez disso, o hash de SHA256 do original MD4 hash é transmitido. Como resultado, se o hash armazenado no Azure AD é obtido, não pode ser utilizado num ataque de passagem do hash no local.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Como a sincronização de hash de palavra-passe funciona com o Azure Active Directory Domain Services
Também pode utilizar a funcionalidade de sincronização de hash de palavra-passe para sincronizar as palavras-passe de no local para [do Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Neste cenário, a instância do Azure Active Directory Domain Services autentica os utilizadores na cloud com todos os métodos disponíveis na sua instância do Active Directory no local. A experiência deste cenário é semelhante a usar o Active Directory migração ADMT (ferramenta) num ambiente no local.

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
Se um utilizador estiver no âmbito da sincronização de hash de palavra-passe, a palavra-passe da conta de cloud está definida como *nunca expirar*.

Pode continuar a iniciar sessão para os serviços cloud, com uma palavra-passe sincronizada expirado em seu ambiente no local. A palavra-passe da cloud é atualizada da próxima vez que alterar a palavra-passe no ambiente no local.

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

## <a name="next-steps"></a>Passos Seguintes
* [Sincronização do Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Obter um plano de implementação passo a passo para migrar do AD FS para sincronização de Hash de palavra-passe](https://aka.ms/authenticationDeploymentPlan)
