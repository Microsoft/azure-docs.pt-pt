---
title: Azure AD conecta múltiplos domínios
description: Este documento descreve a configuração e configuração de vários domínios de nível superior com O365 e Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b5f19e3e994aa05fa99caf360d0c1be69ec7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049778"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Suporte para Vários Domínios para Federação com o Azure AD
A seguinte documentação fornece orientações sobre como usar vários domínios e subdomínios de alto nível ao federar com domínios do Office 365 ou Azure AD.

## <a name="multiple-top-level-domain-support"></a>Suporte de domínio de alto nível múltiplo
Federar vários domínios de alto nível com AD Azure requer alguma configuração adicional que não é necessária ao federar com um domínio de nível superior.

Quando um domínio é federado com Azure AD, várias propriedades são definidas no domínio em Azure.  Um importante é o IssuerUri.  Esta propriedade é um URI que é usado pela Azure AD para identificar o domínio com o qual o símbolo está associado.  O URI não precisa de resolver nada, mas tem de ser um URI válido.  Por predefinição, a Azure AD define o URI para o valor do identificador de serviço da federação na sua configuração AD FS no local.

> [!NOTE]
> O identificador de serviço da federação é um URI que identifica exclusivamente um serviço da federação.  O serviço da federação é um exemplo de AD FS que funciona como o serviço de fichas de segurança.
>
>

Pode visualizar o EmitorUri utilizando `Get-MsolDomainFederationSettings -DomainName <your domain>`o comando PowerShell .

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Um problema surge quando se adiciona mais do que um domínio de alto nível.  Por exemplo, digamos que criou uma federação entre o Azure AD e o seu ambiente no local.  Para este documento, o domínio, bmcontoso.com está a ser usado.  Agora, um segundo domínio de nível superior, bmfabrikam.com foi adicionado.

![Domínios](./media/how-to-connect-install-multiple-domains/domains.png)

Quando se tenta converter o domínio bmfabrikam.com a ser federado, ocorre um erro.  A razão é que a Azure AD tem um constrangimento que não permite que a propriedade EmitrUri tenha o mesmo valor para mais de um domínio.  

![Erro da federação](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parâmetro de suporte multipledomínio
Para contornar esta restrição, é necessário adicionar um IssuerUri diferente, que pode ser feito utilizando o `-SupportMultipleDomain` parâmetro.  Este parâmetro é utilizado com os seguintes cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Este parâmetro faz com que o Azure AD configure o Emedreto Uri de modo a que seja baseado no nome do domínio.  O IssuerUri será único entre os diretórios em Azure AD.  A utilização do parâmetro permite que o comando PowerShell esteja concluído com sucesso.

![Erro da federação](./media/how-to-connect-install-multiple-domains/convert.png)

Olhando para as definições para o domínio bmfabrikam.com pode ver o seguinte:

![Erro da federação](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain`não altera os outros pontos finais, que ainda estão configurados para apontar para o serviço da federação em adfs.bmcontoso.com.

Outra coisa `-SupportMultipleDomain` que faz é que garante que o sistema AD FS inclui o valor emitente adequado em fichas emitidas para a AD Azure. Este valor é definido tomando a parte de domínio dos utilizadores UPN e definindo-o como o domínio no Emedreuri, ou seja, https://{upn sufixo}/adfs/services/trust.

Assim, durante a autenticação ao Azure AD ou office 365, o elemento Emitreuri no token do utilizador é utilizado para localizar o domínio em Azure AD.  Se, não for possível encontrar uma correspondência, a autenticação falhará.

Por exemplo, se a UPN bsimon@bmcontoso.comde um utilizador for , o elemento EmitorUri no `http://bmcontoso.com/adfs/services/trust`token, problemas AD FS, será definido para . Este elemento corresponderá à configuração da AD Azure, e a autenticação terá sucesso.

Segue-se a regra de reivindicação personalizada que implementa esta lógica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Para utilizar o interruptor -SupportMultipleDomain ao tentar adicionar novos ou converter domínios já existentes, a sua confiança federada precisa de já ter sido criada para os suportar.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Como atualizar a confiança entre a AD FS e a Azure AD
Se não criou a confiança federada entre a AD FS e o seu caso de Azure AD, poderá ter de recriar essa confiança.  A razão é que, quando é `-SupportMultipleDomain` originalmente configurada sem o parâmetro, o EmitenteUri é definido com o valor padrão.  Na imagem abaixo, pode ver que o `https://adfs.bmcontoso.com/adfs/services/trust`Emituri está definido para .

Se tiver adicionado com sucesso um novo domínio no portal Azure `Convert-MsolDomaintoFederated -DomainName <your domain>`AD e tentar convertê-lo usando, terá o seguinte erro.

![Erro da federação](./media/how-to-connect-install-multiple-domains/trust1.png)

Se tentar adicionar `-SupportMultipleDomain` o interruptor, receberá o seguinte erro:

![Erro da federação](./media/how-to-connect-install-multiple-domains/trust2.png)

Simplesmente tentar `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` correr no domínio original também resultará num erro.

![Erro da federação](./media/how-to-connect-install-multiple-domains/trust3.png)

Utilize os passos abaixo para adicionar um domínio adicional de nível superior.  Se já adicionou um domínio e `-SupportMultipleDomain` não usou o parâmetro, comece com os passos para remover e atualizar o seu domínio original.  Se ainda não adicionou um domínio de nível superior, pode começar com os passos para adicionar um domínio utilizando o PowerShell do Azure AD Connect.

Utilize os seguintes passos para remover a confiança do Microsoft Online e atualizar o seu domínio original.

1. No seu servidor da federação AD FS abre **a AD FS Management.**
2. À esquerda, expanda **as relações de confiança** e confia ndo fundos **partidários**
3. À direita, elimine a entrada da **Plataforma de Identidade Microsoft Office 365.**
   ![Remova a Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Numa máquina que tenha [o Módulo de Diretório Ativo Azure para windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) instalado na seleção, executa o seguinte: `$cred=Get-Credential`.  
5. Introduza o nome de utilizador e a palavra-passe de um administrador global para o domínio Azure AD com o que está a federar.
6. No PowerShell, entre`Connect-MsolService -Credential $cred`
7. No PowerShell, `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`introduza .  Esta atualização é para o domínio original.  Assim, usando os domínios acima seria:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Utilize os seguintes passos para adicionar o novo domínio de nível superior usando powerShell

1. Numa máquina que tenha [o Módulo de Diretório Ativo Azure para windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) instalado na seleção, executa o seguinte: `$cred=Get-Credential`.  
2. Introduza o nome de utilizador e a palavra-passe de um administrador global para o domínio Azure AD com o que está a federar
3. No PowerShell, entre`Connect-MsolService -Credential $cred`
4. No PowerShell, entre`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilize os seguintes passos para adicionar o novo domínio de nível superior utilizando o Azure AD Connect.

1. Lançar Azure AD Connect a partir do ambiente de trabalho ou menu de início
2. Escolha "Adicionar um domínio AD ![Azure adicional" Adicione um domínio AD azure adicional](./media/how-to-connect-install-multiple-domains/add1.png)
3. Insira as suas credenciais De AD Azure e Ative Directy
4. Selecione o segundo domínio que pretende configurar para a federação.
   ![Adicione um domínio AD Azure adicional](./media/how-to-connect-install-multiple-domains/add2.png)
5. Clique em Instalar

### <a name="verify-the-new-top-level-domain"></a>Verifique o novo domínio de alto nível
Ao utilizar o `Get-MsolDomainFederationSettings -DomainName <your domain>`comando PowerShell, pode ver o Emedreto Atualizado.  A imagem abaixo mostra que as definições da federação foram atualizadas no domínio original`http://bmcontoso.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

E o EmedretoUri sobre o novo domínio foi definido para`https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Apoio a subdomínios
Quando adicionar um subdomínio, devido à forma como o Azure AD lidou com os domínios, herdará as definições do progenitor.  Então, o EmedrUri, precisa de combinar com os pais.

Digamos, por exemplo, que tenho bmcontoso.com e depois adiciono corp.bmcontoso.com.  O EmitorUri para um utilizador de corp.bmcontoso.com terá de ser ** http://bmcontoso.com/adfs/services/trust.**  No entanto, a regra padrão acima implementada para a AD Azure, gerará um símbolo com um emitente como ** http://corp.bmcontoso.com/adfs/services/trust.** que não corresponda ao valor exigido do domínio e a autenticação falhará.

### <a name="how-to-enable-support-for-subdomains"></a>Como permitir o suporte para subdomínios
Para contornar este comportamento, o AD FS confia na confiança do partido para o Microsoft Online precisa de ser atualizado.  Para isso, deve configurar uma regra de reclamação personalizada para que retire quaisquer subdomínios do sufixo UPN do utilizador ao construir o valor emitente personalizado.

A seguinte alegação fá-lo-á:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
O último número no conjunto de expressão regular é quantos domínios parentais existem no seu domínio raiz. Aqui bmcontoso.com é usado, por isso são necessários dois domínios-mãe. Se três domínios parentais fossem mantidos (isto é, corp.bmcontoso.com), então o número teria sido três. Eventualmente, um intervalo pode ser indicado, a partida será sempre feita para corresponder ao máximo dos domínios. "{2,3}" vai corresponder a dois a três domínios (isto é: bmfabrikam.com e corp.bmcontoso.com).

Utilize os seguintes passos para adicionar uma reivindicação personalizada para suportar subdomínios.

1. Gestão de AD FS aberto
2. Clique na confiança do MICROSOFT Online RP e escolha as regras de Edição
3. Selecione a terceira ![regra de reclamação e substitua a reclamação da Edit](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Substitua a reclamação atual:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Substituir a reclamação](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Clique em Ok.  Clique em Aplicar.  Clique em Ok.  Feche a Gestão do AD FS.

## <a name="next-steps"></a>Passos seguintes
Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [Atualização automática](how-to-connect-install-automatic-upgrade.md), [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).

Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](how-to-connect-sync-feature-scheduler.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
