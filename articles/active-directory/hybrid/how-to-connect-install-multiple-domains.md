---
title: Azure AD conecta vários domínios
description: Este documento descreve a configuração e configuração de vários domínios de nível superior com o Microsoft 365 e AZure AD.
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
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53a0da5b5db21c9a543d39d1b252b0b4c64e2a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306366"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Suporte para Vários Domínios para Federação com o Azure AD
A documentação a seguir fornece orientações sobre como usar vários domínios e subdomínios de nível superior ao federar com os domínios AD microsoft 365 ou Azure.

## <a name="multiple-top-level-domain-support"></a>Suporte de domínio de vários níveis
A federação de vários domínios de alto nível com Ad Azure requer alguma configuração adicional que não é necessária quando federa com um domínio de nível superior.

Quando um domínio é federado com Azure AD, várias propriedades são definidas no domínio em Azure.  Um importante é o EmiterUri.  Esta propriedade é um URI que é usado pela Azure AD para identificar o domínio com o qual o símbolo está associado.  O URI não precisa de resolver nada, mas deve ser um URI válido.  Por predefinição, o Azure AD define o URI para o valor do identificador de serviço da federação na configuração AD FS no local.

> [!NOTE]
> O identificador de serviço da federação é um URI que identifica exclusivamente um serviço da federação.  O serviço da federação é um exemplo de AD FS que funciona como o serviço de símbolo de segurança.
>
>

Pode ver o EmitenteUri utilizando o comando PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>` .

![Screenshot que mostra resultados depois de entrar no comando "Get-MsdomainFederationSettings" em PowerShell.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Um problema surge quando se adiciona mais de um domínio de nível superior.  Por exemplo, digamos que criou uma federação entre a Azure AD e o seu ambiente no local.  Para este documento, o domínio, bmcontoso.com está a ser utilizado.  Agora, um segundo domínio de alto nível, bmfabrikam.com foi adicionado.

![Uma imagem mostrando vários domínios de alto nível](./media/how-to-connect-install-multiple-domains/domains.png)

Quando se tenta converter o domínio bmfabrikam.com a ser federado, ocorre um erro.  A razão é que a Azure AD tem uma restrição que não permite que a propriedade EmiterUri tenha o mesmo valor para mais de um domínio.  

![Screenshot que mostra um erro da federação em PowerShell.](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parâmetro de ApoioMultipleDomain
Para contornar este constrangimento, é necessário adicionar um EmiterUri diferente, o que pode ser feito utilizando o `-SupportMultipleDomain` parâmetro.  Este parâmetro é utilizado com os seguintes cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Este parâmetro faz com que o Azure AD configuure o EmiterUri de modo a que se baseie no nome do domínio.  O EmiterUri será único em todos os diretórios em Azure AD.  A utilização do parâmetro permite que o comando PowerShell complete com sucesso.

![Screenshot que mostra uma conclusão bem sucedida do comando PowerShell.](./media/how-to-connect-install-multiple-domains/convert.png)

Olhando para as definições para o domínio bmfabrikam.com pode ver o seguinte:

![Screenshot que mostra as definições para o domínio "bmfabrikam.com".](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` não altera os outros pontos finais, que ainda estão configurados para apontar para o serviço da federação em adfs.bmcontoso.com.

Outra coisa que `-SupportMultipleDomain` faz é garantir que o sistema AD FS inclui o valor adequado do Emitente em fichas emitidas para a Azure AD. Este valor é definido tomando a parte de domínio dos utilizadores UPN e definindo-o como o domínio no EmiterUri, ou seja, https://{upn sufixix}/adfs/services/trust.

Assim, durante a autenticação para Azure AD ou Microsoft 365, o elemento EmiterUri no token do utilizador é utilizado para localizar o domínio em Azure AD. Se não for possível encontrar uma correspondência, a autenticação falhará.

Por exemplo, se a UPN de um utilizador for bsimon@bmcontoso.com , o elemento EmiterUri no token, problemas de FS AD, será definido para `http://bmcontoso.com/adfs/services/trust` . Este elemento corresponderá à configuração AD Azure, e a autenticação terá sucesso.

Segue-se a regra de reivindicação personalizada que implementa esta lógica:

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> Para utilizar o interruptor -SupportMultipleDomain ao tentar adicionar novos ou converter domínios já existentes, a sua confiança federada já precisa de ter sido criada para os suportar.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Como atualizar a confiança entre AD FS e Azure AD
Se não criou a confiança federada entre a AD FS e o seu caso de Azure AD, poderá ter de recriar essa confiança.  A razão é que, quando é originalmente configurado sem o `-SupportMultipleDomain` parâmetro, o EmitenteUri é definido com o valor padrão.  Na imagem abaixo, pode ver que o EmitenteUri está definido para `https://adfs.bmcontoso.com/adfs/services/trust` .

Se tiver adicionado com sucesso um novo domínio no portal AD Azure e tentar convertê-lo através `Convert-MsolDomaintoFederated -DomainName <your domain>` do mesmo, obterá o seguinte erro.

![Screenshot que mostra um erro da federação em PowerShell depois de tentar converter um novo domínio com o comando "Convert-MsdomaintoFederated".](./media/how-to-connect-install-multiple-domains/trust1.png)

Se tentar adicionar o `-SupportMultipleDomain` interruptor, receberá o seguinte erro:

![Screenshot que mostra um erro da federação depois de adicionar o interruptor "SupportMultipleDomain".](./media/how-to-connect-install-multiple-domains/trust2.png)

Simplesmente tentar executar `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` o domínio original também resultará num erro.

![Erro da Federação](./media/how-to-connect-install-multiple-domains/trust3.png)

Utilize os passos abaixo para adicionar um domínio adicional de nível superior.  Se já adicionou um domínio e não usou o `-SupportMultipleDomain` parâmetro, comece com os passos para remover e atualizar o seu domínio original.  Se ainda não tiver adicionado um domínio de nível superior, pode começar com os passos para adicionar um domínio utilizando o PowerShell de Azure AD Connect.

Utilize os seguintes passos para remover a confiança do Microsoft Online e atualizar o seu domínio original.

1. No seu servidor da federação AD FS **abre-se a Gestão FS AD.**
2. À esquerda, expandir **relações de confiança** e **confiança no partido**
3. À direita, elimine a entrada **da Plataforma de Identidade Microsoft Office 365.**
   ![Remover a Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Numa máquina que tem [módulo de Diretório Ativo Azure para Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) instalado no mesmo, executar o seguinte: `$cred=Get-Credential` .  
5. Introduza o nome de utilizador e a palavra-passe de um administrador global para o domínio Azure AD com o que está a federar.
6. Em PowerShell, insira `Connect-MsolService -Credential $cred`
7. Em PowerShell, insira `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` .  Esta atualização é para o domínio original.  Assim, usando os domínios acima seria:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Use os seguintes passos para adicionar o novo domínio de nível superior usando PowerShell

1. Numa máquina que tem [módulo de Diretório Ativo Azure para Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) instalado no mesmo, executar o seguinte: `$cred=Get-Credential` .  
2. Introduza o nome de utilizador e a palavra-passe de um administrador global para o domínio AZure AD com o que está a federar
3. Em PowerShell, insira `Connect-MsolService -Credential $cred`
4. Em PowerShell, insira `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilize os seguintes passos para adicionar o novo domínio de nível superior utilizando o Azure AD Connect.

1. Lançar Azure AD Connect a partir do ambiente de trabalho ou menu inicial
2. Escolha "Adicionar um screenshot adicional do domínio AD Azure" ![ que mostra a página "Tarefas adicionais" com "Adicionar um domínio AD adicional" selecionado.](./media/how-to-connect-install-multiple-domains/add1.png)
3. Insira as suas credenciais de AD AD e Ative Directory
4. Selecione o segundo domínio que deseja configurar para a federação.
   ![Adicione um domínio AD Azure adicional](./media/how-to-connect-install-multiple-domains/add2.png)
5. Clique em Instalar

### <a name="verify-the-new-top-level-domain"></a>Verifique o novo domínio de nível superior
Ao utilizar o comando PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>` pode ver o EmitenteUri atualizado.  A imagem abaixo mostra que as definições da federação foram atualizadas no domínio original `http://bmcontoso.com/adfs/services/trust`

![Screenshot que mostra as definições da federação atualizadas no domínio original.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

E o EmiterUri sobre o novo domínio foi definido para `https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Apoio aos subdomínios
Quando adicionar um subdomínio, devido à forma como a Azure AD manuseou os domínios, herdará as definições do progenitor.  Então, o EmitenteUri, precisa de combinar com os pais.

Digamos, por exemplo, que tenho bmcontoso.com e depois acrescentar corp.bmcontoso.com.  O EmiterUri para um utilizador de corp.bmcontoso.com terá de ser **`http://bmcontoso.com/adfs/services/trust`** .  No entanto, a regra padrão implementada acima para Azure AD, gerará um símbolo com um emitente como **`http://corp.bmcontoso.com/adfs/services/trust`** . que não corresponderão ao valor e autenticação exigidos pelo domínio.

### <a name="how-to-enable-support-for-subdomains"></a>Como permitir o suporte a subdomínios
Para contornar este comportamento, o AD FS confiando na confiança do partido para o Microsoft Online precisa de ser atualizado.  Para isso, tem de configurar uma regra de reclamação personalizada para que se retire de quaisquer subdomínios do sufixo UPN do utilizador ao construir o valor do Emitente personalizado.

A seguinte alegação o fará:

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
O último número no conjunto de expressão regular é quantos domínios-mãe existem no seu domínio raiz. Aqui bmcontoso.com é usado, por isso são necessários dois domínios-mãe. Se três domínios-mãe fossem mantidos (isto é, corp.bmcontoso.com), então o número teria sido três. Eventualmente, um intervalo pode ser indicado, a partida será sempre feita para corresponder ao máximo de domínios. {2,3}" " corresponderá a dois a três domínios (isto é, bmfabrikam.com e corp.bmcontoso.com).

Utilize os seguintes passos para adicionar uma reivindicação personalizada para suportar subdomínios.

1. Gestão aberta do AD FS
2. Clique com o botão direito no Microsoft Online RP trust e escolha as regras de Reclamação de Edição
3. Selecione a terceira regra de reclamação e ![ substitua a reclamação de Edição](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Substitua a reclamação atual:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    com o

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![Substituir reclamação](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Clique em Ok.  Clique em Aplicar.  Clique em Ok.  Feche a Gestão do AD FS.

## <a name="next-steps"></a>Passos seguintes
Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [Atualização automática](how-to-connect-install-automatic-upgrade.md), [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).

Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](how-to-connect-sync-feature-scheduler.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).