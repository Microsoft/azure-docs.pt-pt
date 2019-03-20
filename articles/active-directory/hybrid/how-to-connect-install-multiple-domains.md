---
title: Vários domínios do Azure AD Connect
description: Este documento descreve a definir e configurar vários domínios de nível superior com o Office 365 e do Azure AD.
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
ms.openlocfilehash: 9e822906a072ec8244c7108e98289482adebb5a7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098683"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Suporte para Vários Domínios para Federação com o Azure AD
A seguinte documentação fornece orientação sobre como utilizar vários domínios de nível superior e os subdomínios quando o federação com o Office 365 ou a domínios do Azure AD.

## <a name="multiple-top-level-domain-support"></a>Suporte para vários domínios de nível superior
Domínios de nível superior com o Azure AD a federar vários, necessita de configuração adicional que não é necessária quando o federação com um domínio de nível superior.

Quando um domínio está Federado com o Azure AD, várias propriedades estão definidas no domínio no Azure.  Um importante é IssuerUri.  Esta propriedade é um URI que é utilizado pelo Azure AD para identificar o domínio que o token está associado.  O URI não precisa de resolver para qualquer coisa, tem de ser um URI válido.  Por predefinição, do Azure AD define o URI para o valor do identificador de serviço de Federação no seu local do AD FS configuration.

> [!NOTE]
> O identificador do serviço de Federação é um URI que identifica exclusivamente um serviço de Federação.  O serviço de Federação é uma instância do AD FS que funciona como o serviço de token de segurança.
>
>

Pode ver o IssuerUri utilizando o comando do PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Surge um problema ao adicionar mais de um domínio de nível superior.  Por exemplo, digamos que tiver configurado a Federação entre o Azure AD e o ambiente no local.  Para este documento, o domínio, está a ser utilizado bmcontoso.com.  Agora um nível superior, segundo domínio, bmfabrikam.com foi adicionado.

![Domínios](./media/how-to-connect-install-multiple-domains/domains.png)

Quando tenta converter o domínio bmfabrikam.com, ocorrerá um erro.  O motivo é o Azure AD tem uma restrição que não permite a propriedade IssuerUri ter o mesmo valor para mais de um domínio.  

![Erro de Federação](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parâmetro de SupportMultipleDomain
Para contornar esta restrição, tem de adicionar um IssuerUri diferente, o que pode ser feito utilizando o `-SupportMultipleDomain` parâmetro.  Este parâmetro é utilizado com os seguintes cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Este parâmetro faz do Azure AD para configurar o IssuerUri, de modo a que se baseia no nome do domínio.  O IssuerUri irá ser exclusivo em diretórios no Azure AD.  Utilizar o parâmetro permite que o comando do PowerShell concluir com êxito.

![Erro de Federação](./media/how-to-connect-install-multiple-domains/convert.png)

Observando as configurações para o domínio de bmfabrikam.com pode ver o seguinte:

![Erro de Federação](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` não altera os outros pontos de extremidade, que ainda estão configurados para apontar para o serviço de Federação no adfs.bmcontoso.com.

Outra coisa que `-SupportMultipleDomain` faz é que ele garante que o sistema do AD FS inclui o valor de emissor adequado nos tokens emitidos para o Azure AD. Este valor é definido por pegar a parte dos utilizadores UPN de domínio e defini-lo como o domínio no IssuerUri, ou seja, https://{upn sufixo} / fidedignidade/serviços/AD FS.

Assim, durante a autenticação para o Azure AD ou Office 365, o elemento de IssuerUri em token do usuário é utilizado para localizar o domínio no Azure AD.  Se, não é possível encontrar uma correspondência, a autenticação irá falhar.

Por exemplo, se o UPN de um um utilizador é bsimon@bmcontoso.com, o elemento de IssuerUri no token, problemas do AD FS, será definido como <http://bmcontoso.com/adfs/services/trust>. Este elemento corresponderá a configuração do Azure AD e autenticação terá êxito.

Segue-se a regra de afirmação personalizadas que implementa essa lógica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Para utilizar o comutador - SupportMultipleDomain quando tentar adicionar novo ou converter domínios já existentes, a confiança federada precisa já foram configurados para suportá-los.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Como atualizar a fidedignidade entre o AD FS e o Azure AD
Se não configurado a confiança federada entre o AD FS e a sua instância do Azure AD, terá de voltar a criar esta demonstração de confiança.  O motivo é, quando ele estiver originalmente configurado sem o `-SupportMultipleDomain` parâmetro, o IssuerUri está definida com o valor predefinido.  A captura de ecrã abaixo, pode ver o IssuerUri está definido como https://adfs.bmcontoso.com/adfs/services/trust.

Se adicionou com êxito um novo domínio no portal do Azure AD e, em seguida, tentar convertê-lo usando `Convert-MsolDomaintoFederated -DomainName <your domain>`, obterá o seguinte erro.

![Erro de Federação](./media/how-to-connect-install-multiple-domains/trust1.png)

Se tentar adicionar o `-SupportMultipleDomain` mudar, receberá o erro seguinte:

![Erro de Federação](./media/how-to-connect-install-multiple-domains/trust2.png)

Simplesmente tentar executar `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` no domínio original também irá resultar num erro.

![Erro de Federação](./media/how-to-connect-install-multiple-domains/trust3.png)

Utilize os passos abaixo para adicionar um domínio de nível superior adicional.  Se já tiver adicionado um domínio e não utilizou o `-SupportMultipleDomain` parâmetro, começar com os passos para remover e atualizar o seu domínio original.  Se ainda não adicionou um domínio de nível superior, pode começar com os passos para adicionar um domínio com o PowerShell do Azure AD Connect.

Utilize os seguintes passos para remover a fidedignidade do Microsoft Online e atualizar o seu domínio original.

1. No seu servidor de Federação do AD FS, abra **gestão do AD FS.**
2. No lado esquerdo, expanda **relações de confiança** e **da entidade Confiadora confianças de entidades Confiadoras**
3. À direita, eliminar a **plataforma de identidade do Microsoft Office 365** entrada.
   ![Remover o Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Numa máquina que tenha [módulo Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) instalado nele execute o seguinte: `$cred=Get-Credential`.  
5. Introduza o nome de utilizador e palavra-passe de um administrador global para o domínio do Azure AD que são a Federação com o.
6. No PowerShell, introduza: `Connect-MsolService -Credential $cred`
7. No PowerShell, introduza `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Esta atualização é para o domínio original.  Assim, usando os domínios acima seria:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Utilize os seguintes passos para adicionar o novo domínio de nível superior com o PowerShell

1. Numa máquina que tenha [módulo Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) instalado nele execute o seguinte: `$cred=Get-Credential`.  
2. Introduza o nome de utilizador e palavra-passe de um administrador global para o domínio do Azure AD que são a Federação com o
3. No PowerShell, introduza: `Connect-MsolService -Credential $cred`
4. No PowerShell, introduza: `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilize os seguintes passos para adicionar o novo domínio de nível superior com o Azure AD Connect.

1. Inicie o Azure AD Connect da área de trabalho ou no menu Iniciar
2. Escolha "Adicionar um domínio do AD do Azure adicionais" ![adicionar um domínio do Azure AD](./media/how-to-connect-install-multiple-domains/add1.png)
3. Introduza o seu Azure AD e credenciais do Active Directory
4. Selecione o segundo domínio que pretende configurar para a Federação.
   ![Adicionar um domínio do Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Clique em Instalar

### <a name="verify-the-new-top-level-domain"></a>Verifique se o novo domínio de nível superior
Ao utilizar o comando do PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`pode ver o IssuerUri atualizado.  Captura de ecrã abaixo mostra a Federação as definições foram atualizadas no domínio original http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

E o IssuerUri no novo domínio foi definido como https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Suporte para subdomínios
Quando adiciona um subdomínio, devido aos domínios de forma do Azure AD manipulado, ele herdará as definições do principal.  Então, IssuerUri, tem de corresponder os pais.

Vamos dizer que, por exemplo, que bmcontoso.com e, em seguida, adicionar corp.bmcontoso.com.  IssuerUri de corp.bmcontoso.com um utilizador terão de ser  **http://bmcontoso.com/adfs/services/trust.**  No entanto, a regra padrão implementada acima para o Azure AD, irá gerar um token com um emissor como  **http://corp.bmcontoso.com/adfs/services/trust.** que não serão iguais do domínio necessária valor e a autenticação falha.

### <a name="how-to-enable-support-for-subdomains"></a>Como ativar o suporte para subdomínios
Para contornar este comportamento, o AD FS da entidade confiadora fidedignidade do Microsoft Online tem de ser atualizado.  Para tal, tem de configurar uma regra de afirmação personalizada para que remover quaisquer subdomínios do sufixo do UPN do utilizador ao construir o valor de emissor personalizado.

A declaração seguinte vai fazê-lo:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
O último número no conjunto de expressão regular é há quantos domínios de principal são no seu domínio de raiz. Aqui bmcontoso.com é usada, portanto, dois domínios de principal são necessários. Se os três principais domínios tivesse de ser mantidos (ou seja,: corp.bmcontoso.com), em seguida, o número teria sido três. Eventualmente, um intervalo podem ser indicados, sempre será feita a correspondência de acordo com o número máximo de domínios. "{2,3}" corresponderá a duas ou três domínios (ou seja: bmfabrikam.com e corp.bmcontoso.com).

Utilize os seguintes passos para adicionar uma declaração personalizada para suportar subdomínios.

1. Gestão do Open AD FS
2. Faça duplo clique a fidedignidade de RP Online da Microsoft e escolher regras de afirmação editar
3. Selecione a regra de afirmação terceira e substitua ![afirmação de edição](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Substitua a afirmação atual:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Substitua a afirmação](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Clique em Ok.  Clique em aplicar.  Clique em Ok.  Feche a Gestão do AD FS.

## <a name="next-steps"></a>Passos Seguintes
Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [A atualização automática](how-to-connect-install-automatic-upgrade.md), [impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md), e [Azure AD Connect Health](how-to-connect-health-sync.md).

Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](how-to-connect-sync-feature-scheduler.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
