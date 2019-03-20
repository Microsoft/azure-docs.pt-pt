---
title: Políticas de reposição de palavra-passe de Self-Service do AD do Azure
description: Configurar opções de política de reposição de palavra-passe self-service do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6888a8787856ef23c459c7ffc18f8e2b4de17f6f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901149"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas de palavra-passe e restrições no Azure Active Directory

Este artigo descreve as políticas de palavra-passe e os requisitos de complexidade associados a contas de utilizador no seu inquilino do Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Diferenças de política de reposição de administrador

**Microsoft impõe uma *dois-gate* política para qualquer função de administrador do Azure de reposição de palavra-passe** esta política pode ser diferente da que definiu para os seus utilizadores e não pode ser alterado. Sempre deve testar a funcionalidade de reposição de palavra-passe como um utilizador sem quaisquer funções de administrador do Azure atribuídas.

Com uma política de porta de dois **os administradores não têm a capacidade de utilizar perguntas de segurança**.

A política de porta de dois requer dois tipos de dados de autenticação, por exemplo, um **endereço de e-mail**, **aplicação de autenticador**, ou uma **número de telefone**. Uma política de porta de dois aplica-se nas seguintes circunstâncias:

* Todas as seguintes funções de administrador do Azure são afetadas:
  * Administrador de suporte técnico
  * Administrador de assistência técnica
  * Administrador de faturação
  * Parceiro de Suporte de Escalão 1
  * Parceiro de Suporte de Escalão 2
  * Administrador do Exchange
  * Administrador do Skype para Empresas
  * Administrador de utilizadores
  * Escritores de diretórios
  * Administrador global ou administrador de empresa
  * Administrador do SharePoint
  * Administrador de Conformidade
  * Administrador de aplicações
  * Administrador de segurança
  * Administrador com Função Privilegiada
  * Administrador do Intune
  * Administrador de serviço de proxy de aplicações
  * Administrador do Dynamics 365
  * Administrador de serviço do Power BI
  * Administrador da autenticação
  * Administrador com privilégios de autenticação

* Se a 30 dias decorridos numa subscrição de avaliação; ou
* Um domínio personalizado está presente, por exemplo, contoso.com; ou
* O Azure AD Connect está a sincronizar identidades do seu diretório no local

### <a name="exceptions"></a>Exceções

Uma política de uma porta requer um conjunto de dados de autenticação, como um endereço de e-mail *ou* número de telefone. Uma porta de uma política aplica-se nas seguintes circunstâncias:

* É nos primeiros 30 dias de uma subscrição de avaliação; ou
* Um domínio personalizado não estiver presente (*. onmicrosoft.com); e
* O Azure AD Connect não está a sincronizar identidades

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas de UserPrincipalName que se aplicam a todas as contas de utilizador

Cada conta de utilizador que tem de iniciar sessão Azure AD tem de ter um valor de atributo de nome principal (UPN) de utilizador exclusivo associado à sua conta. A tabela seguinte descreve as políticas que se aplicam a ambas as contas de utilizador de Active Directory no local que são sincronizadas para a cloud e às contas de utilizador apenas na cloud:

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Carateres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Carateres não permitidos |<ul> <li>Qualquer "\@ \" caractere que não é separar o nome de utilizador do domínio.</li> <li>Não pode conter um caráter de ponto final "." imediatamente anterior a "\@ \" símbolo</li></ul> |
| Restrições de tamanho |<ul> <li>O comprimento total não pode exceder 113 carateres</li><li>Pode haver até 64 carateres antes do "\@ \" símbolo</li><li>Pode haver até 48 carateres após o "\@ \" símbolo</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de palavra-passe que só se aplicam a contas de utilizador de cloud

A tabela seguinte descreve as definições de política de palavra-passe aplicadas a contas de utilizador que são criadas e geridas no Azure AD:

| Propriedade | Requisitos |
| --- | --- |
| Carateres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( ) ;</li></ul> |
| Carateres não permitidos |<ul><li>Carateres Unicode.</li><li>Espaços.</li><li> Não pode conter um caráter de ponto "." imediatamente anterior a "\@ \" símbolo".</li></ul> |
| Restrições de palavra-passe |<ul><li>Um mínimo de 8 caracteres e um máximo de 16 carateres.</li><li>Requer três de quatro dos seguintes procedimentos:<ul><li>Carateres em minúsculas.</li><li>Carateres maiúsculos.</li><li>Números (0-9).</li><li>Símbolos (consulte as restrições de palavra-passe anteriores).</li></ul></li></ul> |
| Duração de expiração de palavra-passe |<ul><li>Valor predefinido: **90** dias.</li><li>O valor é configurável utilizando o `Set-MsolPasswordPolicy` cmdlet a partir do módulo Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de palavra-passe |<ul><li>Valor predefinido: **14** dias (antes de expira a palavra-passe).</li><li>O valor é configurável utilizando o `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Expiração de palavra-passe |<ul><li>Valor predefinido: **false** dias (indica que expiração de palavra-passe está ativada).</li><li>O valor pode ser configurado para contas de utilizador individuais ao utilizar o `Set-MsolUser` cmdlet.</li></ul> |
| Histórico de alterações de palavra-passe |A última palavra-passe *não é possível* ser usado novamente quando o utilizador altera uma palavra-passe. |
| Histórico de reposição de palavra-passe | A última palavra-passe *pode* ser usado novamente quando o usuário repõe uma palavra-passe esquecida. |
| Bloqueio de conta |Após 10 tentativas malsucedidas de início de sessão com a palavra-passe errada, o utilizador está bloqueado durante um minuto. Ainda mais o início de sessão incorreto tenta bloquear o utilizador para aumentar a duração de tempo. [Bloqueio inteligente](howto-password-smart-lockout.md) controla os três últimos hashes de palavra-passe incorreta para evitar incrementa o contador de bloqueio para a mesma palavra-passe. Se alguém insere a mesma palavra-passe errada várias vezes, esse comportamento não fará com que a conta de bloqueio. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Definir políticas de expiração de palavra-passe no Azure AD

Um administrador global para um serviço cloud da Microsoft pode utilizar o módulo do Microsoft Azure AD para o Windows PowerShell para definir palavras-passe de utilizador não a expirar. Também pode utilizar cmdlets do Windows PowerShell para remover o nunca-expira a configuração ou para ver o que utilizador palavras-passe são definidas para nunca expirar. 

Esta orientação aplica-se para outros fornecedores, como o Intune e Office 365, que também dependem do Azure AD para os serviços de identidade e de diretório. Expiração de palavra-passe é a única parte da política que pode ser alterada.

> [!NOTE]
> Apenas palavras-passe para contas de utilizador que não estão sincronizadas através da sincronização de diretório podem ser configuradas para não expirar. Para obter mais informações sobre a sincronização de diretórios, consulte [Connect AD com o Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verifique as políticas de palavra-passe com o PowerShell

Para começar, precisa [transferir e instalar o módulo Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois de ter instalado, pode utilizar os seguintes passos para configurar cada campo.

### <a name="check-the-expiration-policy-for-a-password"></a>Verifique a política de expiração para uma palavra-passe

1. Ligar ao Windows PowerShell com suas credenciais de administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para ver se a palavra-passe de um único utilizador está definida para nunca expirar, execute o seguinte cmdlet através do UPN (por exemplo, *aprilr\@contoso.onmicrosoft.com*) ou o ID de utilizador do utilizador que pretende verificar: `Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`
   * Para ver os **palavra-passe nunca expira** definir para todos os utilizadores, execute o seguinte cmdlet: `Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`

### <a name="set-a-password-to-expire"></a>Definir uma palavra-passe a expirar

1. Ligar ao Windows PowerShell com suas credenciais de administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para que a palavra-passe expirar, execute o seguinte cmdlet com o UPN ou o ID de utilizador do utilizador: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None`
   * Para definir as palavras-passe de todos os utilizadores na organização, de modo a que esta expira, utilize o seguinte cmdlet: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None`

### <a name="set-a-password-to-never-expire"></a>Definir uma palavra-passe para nunca expirar

1. Ligar ao Windows PowerShell com suas credenciais de administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para nunca expirar, execute o cmdlet seguinte ao utilizar o UPN ou o ID de utilizador do utilizador: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration`
   * Para definir as palavras-passe de todos os usuários numa organização para nunca expirar, execute o seguinte cmdlet: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration`

   > [!WARNING]
   > As palavras-passe definida como `-PasswordPolicies DisablePasswordExpiration` ainda idade com base no `pwdLastSet` atributo. Se definir as palavras-passe de utilizador para nunca expirar e vão 90 dias, as palavras-passe expirarem. Com base na `pwdLastSet` atributo, se alterar a expiração para `-PasswordPolicies None`, todas as palavras-passe que têm um `pwdLastSet` mais antiga do que 90 dias exigir que o utilizador para alterá-los da próxima vez que iniciarem sessão. Esta alteração pode afetar um grande número de utilizadores. 

## <a name="next-steps"></a>Passos Seguintes

Os seguintes artigos fornecem informações adicionais sobre a palavra-passe, repor através do Azure AD:

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Reponha ou altere a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](../user-help/active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
