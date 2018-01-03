---
title: "Novidades Notas de versão para o Azure Active Directory | Microsoft Docs"
description: "Saiba o que há de novo no Azure Active Directory (Azure AD), incluindo notas de versão mais recentes, problemas, correções de erros, funcionalidade preterida e as alterações futuras conhecidos."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 408e1680e7aee13663bb6511e65fd5685eb7d518
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2017
---
# <a name="whats-new-in-azure-active-directory"></a>O que é novo no Azure Active Directory?




> Se manter atualizado em relação às quais são as novidades no Azure Active Directory ao subscrever a nossa ![RSS](./media/whats-new/feed-icon-16x16.png) [feed](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Iremos estão melhorar o Azure Active Directory numa base contínua. Para ativar a se manter atualizado com os desenvolvimentos mais recentes, este tópico fornece informações sobre:

-   As versões mais recentes 
-   Problemas conhecidos 
-   Correções de erros 
-   Funcionalidade preterida 
-   Planos de alterações 

Revê esta página regularmente, tal como estamos a são atualizar mensalmente.


## <a name="december-2017"></a>Dezembro de 2017
 

### <a name="terms-of-use-in-the-access-panel-for-end-users"></a>Termos de utilização no painel de acesso para os utilizadores finais

**Tipo:** nova funcionalidade  
**Categoria de serviço:** termos de utilização  
**Capacidade de produto:** governação/compatibilidade
 
Os utilizadores finais tem agora a capacidade de aceder ao painel de acesso e ver os termos de utilização que possam tem aceitado anteriormente.

Os utilizadores podem rever e ver os termos de utilização aceitaram. Isto pode ser feito utilizando o seguinte procedimento:

1. Navegue até e início de sessão para o [MyApps portal](https://myapps.microsoft.com).

2. No canto superior direito, clique no seu nome e selecione **Perfil** no menu pendente. 

3. No seu Perfil, clique em **Rever os termos de utilização**. 

4. A partir daí, pode rever os termos de utilização que aceitou. 

Para obter mais informações, consulte [funcionalidade do Active Directory termos de utilização do Azure (pré-visualização)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de início de sessão do Azure AD

**Tipo:** nova funcionalidade  
**Categoria de serviço:** do Azure AD  
**Capacidade de produto:** autenticação de utilizador
 
Como parte da journey para convergir do Azure AD e sistemas de identidade de conta Microsoft, podemos ter reestruturado a IU em ambos os sistemas, de modo a que disponham de uma aspeto e funcionalidade consistentes. Além disso, ter paginated a página de início de sessão do Azure AD para que iremos recolher o nome de utilizador em primeiro lugar, seguido a credencial num ecrã segundo.

Para obter mais informações, consulte [a nova experiência de início de sessão do Azure AD está agora em pré-visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)
 
---
 

### <a name="fewer-login-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-login"></a>Menos avisos de início de sessão: uma experiência de novo "manter a minha sessão iniciada" para início de sessão do Azure AD

**Tipo:** nova funcionalidade  
**Categoria de serviço:** do Azure AD  
**Capacidade de produto:** autenticação de utilizador
 
Podemos ter substituído o **manter a minha sessão iniciada** autentica a caixa de verificação na página de início de sessão do Azure AD com uma nova linha que aparece depois do utilizador com êxito. 

Se um utilizador responde **Sim** para esta linha de comandos, o serviço fornece-lhes um token de atualização persistente. Este é o mesmo comportamento de quando o utilizador verifica o **manter a minha sessão iniciada** a experiência de antiga de caixa de verificação. Para inquilinos federados, esta linha de comandos é apresentados depois do utilizador se autentica com êxito com o serviço federado.

Para obter mais informações, consulte [menos avisos de início de sessão: A nova experiência de "Manter a minha sessão iniciada" para o Azure AD está em pré-visualização](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) 

---
 

### <a name="add-configuration-to-require-the-tou-to-be-expanded-prior-to-accepting"></a>Adicione configuração para exigir TOU expandir antes de a aceitar.

**Tipo:** nova funcionalidade  
**Categoria de serviço:** termos de utilização  
**Capacidade de produto:** governação
 
Adicionámos tem agora uma opção para os administradores exigir que os respetivos utilizadores finais para expandir os termos de utilização antes de aceitar os termos.

Selecione ou desativar para exigir que os utilizadores expandir os termos de utilização. Se estiver ativado, será pedido aos utilizadores finais que vejam os termos de utilização antes de os aceitarem.

Para obter mais informações, consulte [funcionalidade do Active Directory termos de utilização do Azure (pré-visualização)](active-directory-tou.md)
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação âmbito existência de atribuições de função elegíveis

**Tipo:** nova funcionalidade  
**Categoria de serviço:** Privileged Identity Management  
**Capacidade de produto:** Privileged Identity Management
 
Ativação âmbito permite-lhe ativar as atribuições de função de recursos do Azure elegível com menos autonomia em que as predefinições de atribuição original. Por exemplo, são atribuídos proprietário de uma subscrição no seu inquilino. Com a ativação de âmbito, pode ativar o proprietário de até cinco recursos contidos dentro da subscrição (considere os grupos de recursos, máquinas virtuais, etc...). A ativação de controlo de âmbito, poderá reduzir a possibilidade de executar indesejáveis alterações a recursos do Azure críticos.

Para obter mais informações, consulte [que é o Azure AD Privileged Identity Management?](active-directory-privileged-identity-management-configure.md).
 
---
 

### <a name="new-federated-apps-in-azure-ad-app-gallery"></a>Novas aplicações federadas na Galeria de aplicações do Azure AD

**Tipo:** nova funcionalidade  
**Categoria de serviço:** aplicações da empresa  
**Capacidade de produto:** 3rd integração intervenientes
 
Dezembro de 2017, foram adicionados seguintes de novas aplicações na nossa Galeria de aplicações com a Federação suportam:

|Nome|Tipo de integração|Descrição|
|:-- |----------------|:----------|
|EFI Digital StoreFront|SAML 2.0|[Aplicação de impressão Web 2](https://go.microsoft.com/fwlink/?linkid=861685)|
|Vodeclic|SAML 2.0|[Utilizar o Azure AD para gerir o acesso de utilizador e ative início de sessão único com Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522).  Requer uma conta de Vodeclic existente.|
|Accredible|SAML 2.0|[Criar, gerir e fornecer certificados, destaques e blockchain credenciais](https://go.microsoft.com/fwlink/?linkid=863523)|
|FactSet|SAML 2.0|[Início de sessão único para aplicações de FDSWeb do FactSet](https://go.microsoft.com/fwlink/?linkid=863525)|
|Integração do MobileIron Azure AD|SAML 2.0|[MobileIron](https://go.microsoft.com/fwlink/?linkid=858027) missão é permitir que as empresas modernas proteger e gerir as informações são transmitidos para mobile e na nuvem, preservando a privacidade do utilizador final e fidedignidade.|
|FUNCIONA DA IMAGEM|SAML 2.0|Utilizar o Azure AD para gerir o acesso de utilizador, aprovisionar contas de utilizador e ative início de sessão único com [imagem FUNCIONA](https://go.microsoft.com/fwlink/?linkid=863517). Requer uma subscrição FUNCIONA de imagem existente.|
|SAML SSO para Bitbucket pela resolução GmbH|SAML 2.0|[SSO Bitbucket](https://go.microsoft.com/fwlink/?linkid=863519) autenticação delegados para o Azure AD, os utilizadores já sessão iniciada para o Azure AD pode aceder diretamente Bitbucket. Os utilizadores podem ser criados e atualizada no momento com dados de atributos SAML.|
|SAML SSO para Bamboo pela resolução GmbH|SAML 2.0|[SSO Bamboo](https://go.microsoft.com/fwlink/?linkid=863520) autenticação delegados para o Azure AD, os utilizadores já sessão iniciada para o Azure AD pode aceder diretamente Bamboo.|
|Communifire|SAML 2.0|[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) é o software de intranet de redes sociais moderna, excelentes que suporta os seus funcionários e a sua empresa.|
|MOBI|SAML 2.0|[Centralizar, comprehend e controlar o seu ecossistema de dispositivo completo](https://go.microsoft.com/fwlink/?linkid=863521).|
|Reflektive|SAML 2.0|[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518) é uma plataforma moderna para gestão de desempenho, comentários em tempo real e definição de objetivo. Iremos capacitar os funcionários para orientar os seus próprios desenvolvimento, pelo que pode ser mais estratégicos.|
|CybSafe|OpenID Connect & OAuth|CybSafe é uma plataforma de deteção automática de certificado GCHQ informático. Utiliza a tecnologia avançada e análise de dados para reduzir demonstrably o aspeto humano de segurança de informático e o risco de proteção de dados.|
|WebHR|OpenID Connect & OAuth|De todas as pessoas favorito HR sociais tudo-em-um Software. Fidedignos por mais de 20.000 empresas 197 países|
 |Integração do Zenegy Azure AD|OpenID Connect & OAuth|Com esta aplicação pode utilizar credenciais do Azure Active Directory da sua empresa para iniciar sessão Zenegy.|
|Gestor de experiência de Adobe|SAML 2.0|O Gestor de experiência de Adobe (AEM) é uma solução de plataforma de Gestão abrangente de conteúdo para a criação de Web sites, aplicações móveis e formulários - facilitando a gerir os seus conteúdos de marketing e recursos.|

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para funções de diretório do Azure AD

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** Privileged Identity Management  
**Capacidade de produto:** Privileged Identity Management
 
Fluxo de trabalho de aprovação para funções de diretório do Azure AD é geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de com função privilegiada podem exigir a membros da função elegível pedem a ativação de função antes de poder utilizar a função com privilégios.
Vários utilizadores e grupos que podem ser responsabilidades de aprovação delegado membros da função elegível recebem notificações quando a aprovação é concluída e a respetiva função está ativa

---
 

### <a name="pass-through-authentication---skype-for-business-support"></a>Autenticação pass-through – Skype para suporte de negócio

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** autenticação de utilizador


Agora a autenticação pass-through suporta utilizador inícios de sessão ao Skype para aplicações de cliente de negócio que suportam a autenticação moderna, incluindo topologias Online e híbrida. 

Para obter mais informações, consulte [Skype para topologias de negócio suportado com a autenticação moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-active-directory-privileged-identity-management-pim-for-azure-rbac-preview"></a>Atualizações para Azure Active Directory diretório Privileged Identity Management (PIM) para RBAC do Azure (pré-visualização)

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** PIM  
**Capacidade de produto:** Privileged Identity Management
 
Com o nosso público pré-visualização atualizar do Azure Active Directory diretório com privilégios identidade gestão (PIM) para RBAC do Azure, pode agora:

Utilizar apenas suficiente administração exigir a aprovação para ativar as funções dos recursos agendar uma ativação futura de uma função que requeira a aprovação para o AAD e funções de RBAC do Azure

 
Para obter mais informações, consulte [PIM para recursos do Azure (pré-visualização)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

 
---
 
## <a name="november-2017"></a>Novembro de 2017
 
### <a name="retiring-acs"></a>Extinguir dos ACS



**Tipo:** plano de alteração  
**Categoria de serviço:** dos ACS  
**Capacidade de produto:** serviço de controlo de acesso 


Microsoft Azure Active Directory controlo de acesso (também conhecido como o serviço de controlo de acesso ou ACS) será descontinuado dentro de enlace tardio 2018.  Informações adicionais, incluindo uma agenda de detalhado & orientações de migração de elevado nível, irão ser fornecidas de algumas semanas seguintes. Entretanto, deixe comentários nesta página com quaisquer perguntas sobre ACS e um membro da nossa equipa irá ajudar a responder.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso ao browser para o browser gerido do Intune 


**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Capacidade de produto:** segurança de identidade e de proteção




Com este comportamento, conseguirá restringir o acesso ao browser do Office 365 e outras aplicações de nuvem do Azure AD ligados utilizando o Browser gerido do Intune como uma aplicação aprovada. 

Esta alteração permite-lhe configurar a seguinte condição de acesso condicional baseado na aplicação:

**Aplicações de cliente:** Browser

**O que é o efeito da alteração?**

Hoje, o acesso é bloqueado quando utilizar esta condição. Quando a pré-visualização deste comportamento está disponível, todos os acessos irão exigir a utilização da aplicação de browser gerido. 

Procure para esta capacidade e mais nas notas de lançamento e blogues futuras. 

Para obter mais informações, consulte [de acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações de cliente aprovada para acesso condicional de baseado em aplicações do Azure AD

 
**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Capacidade de produto:** segurança de identidade e de proteção




As seguintes aplicações estão a ser planeadas a adicionar à lista de [aprovados aplicações de cliente](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Para obter mais informações, consulte:

- [Requisito da aplicação de cliente aprovada](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory com base na aplicação acesso condicional](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Termos de utilização suporte para vários idiomas



**Tipo:** nova funcionalidade    
**Categoria de serviço:** termos de utilização  
**Capacidade de produto:** governação/compatibilidade





Os administradores podem agora criar novos termos de utilização (TOU) que contém vários documentos PDF. Pode marcar estes documentos PDF com um idioma correspondente. Os utilizadores que se inserem no âmbito são apresentados o PDF com o com base nas respetivas preferências de idioma correspondente. Se não houver nenhuma correspondência, é apresentado o idioma predefinido.


---
 

### <a name="realtime-password-writeback-client-status"></a>Estado de cliente de repetição de escrita de palavras-passe em tempo real



**Tipo:** nova funcionalidade  
**Categoria de serviço:** SSPR  
**Capacidade de produto:** autenticação de utilizador


 

Agora pode rever o estado do seu cliente de repetição de escrita de palavras-passe no local. Esta opção está disponível no **integração no local** secção o  **[reposição de palavra-passe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**  página. 

Se existirem problemas com a ligação para o seu cliente de repetição de escrita no local, verá uma mensagem de erro que fornece-lhe:

- Informações sobre o motivo pelo qual não é possível ligar ao seu cliente de repetição de escrita no local 
- Uma ligação para a documentação que ajuda a resolver o problema. 


Para obter mais informações, consulte [integração no local](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD com base na aplicação acesso condicional 



 
**Tipo:** nova funcionalidade  
**Categoria de serviço:** do Azure AD  
**Capacidade de produto:** segurança de identidade e de proteção





Agora pode restringir o acesso ao Office 365 e outras aplicações de nuvem do Azure AD ligados para [aprovados aplicações de cliente](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) que suportam as políticas de proteção de aplicação do Intune utilizando [acesso condicional de baseado em aplicações do Azure AD](active-directory-conditional-access-mam.md). Políticas de proteção de aplicações do Intune são utilizadas para configurar e proteger dados da empresa nestas aplicações de cliente.

Ao combinar [baseado na aplicação](active-directory-conditional-access-mam.md) com [baseado nos dispositivos](active-directory-conditional-access-policy-connected-applications.md) políticas de acesso condicional, tem a flexibilidade para proteger os dados pessoais e de dispositivos da empresa.

As seguintes condições e os controlos estão agora disponíveis para utilização com acesso condicional baseado na aplicação:

**Condição de plataforma suportada**

- iOS
- Android

**Condição de aplicações de cliente**

- Aplicações móveis e clientes de ambiente de trabalho

**Controlo de acesso**

- Exigir a aplicação cliente aprovados


Para obter mais informações, consulte [acesso condicional de baseado em aplicações do Azure Active Directory](active-directory-conditional-access-mam.md).

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Gestão de dispositivos do Azure AD no portal do Azure



**Tipo:** nova funcionalidade  
**Categoria de serviço:** registo de dispositivos e gestão  
**Capacidade de produto:** segurança de identidade e de proteção

 



Agora pode encontrar todos os dispositivos ligados ao Azure AD e as atividades relacionadas com o dispositivo num único local. Há uma nova experiência de administração para gerir identidades de dispositivo e definições no portal do Azure. Nesta versão, pode:

- Ver todos os dispositivos que estão disponíveis para o acesso condicional no Azure AD

- Dispositivos associados ao ver propriedades, incluindo o Azure AD híbrido

- Localizar as chaves do BitLocker para os seus dispositivos associados a um AD do Azure, gerir o seu dispositivo com o Intune e muito mais.

- Gerir definições relacionadas com o dispositivo do Azure AD


Para obter mais informações, consulte [gerir dispositivos através do portal do Azure](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como plataforma de dispositivo para o acesso condicional do Azure AD 



**Tipo:** nova funcionalidade    
**Categoria de serviço:** acesso condicional  
**Capacidade de produto:** segurança de identidade e de proteção 
 

Pode agora incluir (ou excluir) macOS como condição de plataforma do dispositivo na sua política de acesso condicional do Azure AD. Com a adição de macOS para as plataformas de dispositivos suportados, pode:

- **Inscrever e gerir dispositivos macOS através do Intune** -semelhante para outras plataformas, como iOS e Android, uma aplicação do portal da empresa está disponível para macOS fazer inscrições unificadas. A nova aplicação do portal da empresa para macOS permite-lhe inscrever um dispositivo com o Intune e registá-lo com o Azure AD.
 
- **Certifique-se de que dispositivos macOS cumprem as políticas de conformidade da sua organização definidas no Intune** -no Intune no portal do Azure, pode agora configurar políticas de conformidade para dispositivos macOS. 
  
- **Restringir o acesso a aplicações no Azure AD apenas a dispositivos conformes macOS** -criação de política de acesso condicional tem macOS como uma opção de plataforma de dispositivos separadas. Esta opção permite-lhe criar políticas de acesso condicional específicas macOS para o conjunto de aplicações visado no Azure.

Para obter mais informações, consulte:

- [Criar uma política de conformidade de dispositivos para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Extensão NPS para o MFA do Azure 


**Tipo:** nova funcionalidade    
**Categoria de serviço:** MFA  
**Capacidade de produto:** autenticação de utilizador




A extensão de servidor de políticas de rede (NPS) para o Azure MFA adiciona funcionalidades MFA baseado na nuvem à sua infraestrutura de autenticação utilizando os servidores existentes. Com a extensão NPS, pode adicionar chamada telefónica, mensagem de texto ou verificação de aplicação de telefone para o fluxo de autenticação existente sem ter de instalar, configurar e manter novos servidores. 

Esta extensão foi criada para as organizações que pretendem proteger as ligações VPN sem a implementar o servidor MFA do Azure. A extensão NPS atua como um adaptador entre o RADIUS e baseado na nuvem do MFA do Azure para fornecer um segundo fator de autenticação para federada ou utilizadores sincronizados.


Para obter mais informações, consulte [integrar a sua infraestrutura existente do NPS com multi-factor Authentication do Azure](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaure ou remover utilizadores eliminados permanentemente


**Tipo:** nova funcionalidade    
**Categoria de serviço:** gestão de utilizadores  
**Capacidade de produto:** diretório 



No Centro de administração do Azure AD, pode agora:

- Restaurar um utilizador eliminado 
- Eliminar permanentemente um utilizador 


**Para experimentar:**

1. No Centro de administração do Azure AD, selecione [ **todos os utilizadores** ](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) no **gerir** secção. 

2. Do **mostrar** lista, selecione **recentemente eliminado utilizadores**. 

4. Selecione um ou mais utilizadores recentemente eliminados e, em seguida, restaurá-los ou eliminá-los permanentemente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações de cliente aprovada para acesso condicional de baseado em aplicações do Azure AD

 
**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** acesso condicional  
**Capacidade de produto:** segurança de identidade e de proteção


Foram adicionadas as seguintes aplicações à lista de [aprovados aplicações de cliente](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Microsoft Azure Information Protection 


Para obter mais informações, consulte:

- [Requisito da aplicação de cliente aprovada](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory com base na aplicação acesso condicional](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>Capacidade para 'ou' entre controlos numa política de acesso condicional 


**Tipo:** funcionalidade foi alterado    
**Categoria de serviço:** acesso condicional  
**Capacidade de produto:** segurança de identidade e de proteção

 
A capacidade de 'Ou' (exigir um dos controlos selecionados) foi lançado controlos de acesso condicional. Esta funcionalidade permite-lhe criar políticas com um **ou** entre controlos de acesso. Por exemplo, pode utilizar esta funcionalidade para criar uma política que requer que um utilizador iniciar sessão com a autenticação multifator **ou** ser num dispositivo em conformidade.

Para obter mais informações, consulte [controlos de acesso condicional do Azure Active Directory](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-realtime-risk-events"></a>Agregação de eventos de risco em tempo real


**Tipo:** funcionalidade foi alterado    
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** segurança de identidade e de proteção


Para melhorar a sua experiência de administração, no Azure AD Identity Protection, todos os eventos de risco em tempo real que foram teve o mesmo endereço IP num determinado dia agora são agregados para cada tipo de eventos de risco. Esta alteração limita o volume de eventos de risco apresentados sem qualquer alteração na segurança do utilizador.

A deteção em tempo real subjacente funciona sempre que o utilizador iniciar sessão. Se tiver uma configuração de política de segurança de início de sessão risco para MFA ou bloquear o acesso, ainda é acionado durante cada arriscado início de sessão.

 
---
 




## <a name="october-2017"></a>Outubro de 2017


### <a name="deprecating-azure-ad-reports"></a>A descontinuar a relatórios do Azure AD


**Tipo:** plano de alteração  
**Categoria de serviço:** Reporting Services  
**Capacidade de produto:** gestão de ciclo de vida de identidade  



O portal do Azure fornece-lhe:

- Uma nova consola de administração do Azure Active Directory 
- Novas APIs para relatórios de atividade e segurança
 
Devido a estas novas funcionalidades, o relatório APIs sob o **/relatórios** endpoint será descontinuado no 10 de Dezembro de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Deteção de campo de início de sessão automático


**Tipo:** fixo   
**Categoria de serviço:** as minhas aplicações  
**Capacidade de produto:** SSO  



Azure Active Directory suporta deteção de campo de início de sessão automático para as aplicações que compor um campo de nome de utilizador e palavra-passe HTML.  Estes passos documentados em [como capturar automaticamente os campos de início de sessão para uma aplicação](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Pode encontrar esta capacidade adicionando um *não galeria* aplicação no **aplicações empresariais** página no [portal do Azure](http://aad.portal.azure.com). Além disso, pode configurar o **Single Sign-on** modo nesta aplicação nova para **baseada em palavra-passe Single Sign-on**, introduzir um URL de web e, em seguida, ao guardar a página.
 
Devido a um problema do serviço, esta funcionalidade foi temporariamente desativada durante um período de tempo. O problema foi resolvido e a deteção de campo de início de sessão automático está novamente disponível.

---

### <a name="new-mfa-features"></a>Novas funcionalidades MFA


**Tipo:** nova funcionalidade  
**Categoria de serviço:** MFA  
**Capacidade de produto:** segurança de identidade e de proteção  



Autenticação Multifator (MFA) é uma parte essencial dos proteger a sua organização. Para tornar as credenciais mais adaptável e a experiência mais integrada, foram adicionadas as seguintes funcionalidades: 

- Integração de resultados de desafio multifator diretamente para o Azure AD início de sessão no relatório, incluindo acesso programático para resultados MFA

- Experiência de integração mais profunda da configuração da MFA para a configuração do AD do Azure no portal do Azure

Com esta pré-visualização pública, gestão de MFA e relatórios são uma parte integrada da experiência de configuração do principal do Azure AD. Agregar os dois recursos permite-lhe gerir a funcionalidade de portal de gestão MFA dentro da experiência do Azure AD.

Para obter mais informações, consulte [referência para a autenticação multifator relatórios no portal do Azure](active-directory-reporting-activity-sign-ins-mfa.md) 


---

### <a name="introducing-terms-of-use"></a>Introduzir termos de utilização



**Tipo:** nova funcionalidade  
**Categoria de serviço:** termos de utilização  
**Capacidade de produto:** governação  



Azure AD os termos de utilização fornecem-lhe um método simple para apresentar informações aos utilizadores finais. Isto garante que os utilizadores verão exclusões de responsabilidade relevantes para os requisitos legais ou conformidade.

Pode utilizar os termos do Azure AD de utilização nos seguintes cenários:

- Termos de utilização gerais para todos os utilizadores da sua organização. 

- Termos específicos de utilização com base nos atributos de um utilizador (ex. doctors vs nurses ou empregados internacional vs locais, efetuados pelo grupos dinâmicos). 

- Termos específicos de utilização para aceder a aplicações de impacto comercial elevada, como o Salesforce.

Para obter mais informações, consulte [do Azure Active Directory os termos de utilização](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>Melhoramentos no privileged identity management


**Tipo:** nova funcionalidade  
**Categoria de serviço:** PIM  
**Capacidade de produto:** Privileged Identity Management  


Do Azure Active Directory Privileged Identity gestão (PIM), já pode gerir, controlar e monitorizar o acesso a recursos do Azure (pré-visualização) na sua organização para:

- Subscrições
- Grupos de recursos
- Máquinas virtuais. 

Todos os recursos no portal do Azure que tiram partido da funcionalidade de controlo de acesso com base do Azure funções (RBAC) podem tirar partido de todas as a segurança e as capacidades de gestão do ciclo de vida do Azure AD PIM tem para oferecer.

Para obter mais informações, consulte [PIM para recursos do Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="introducing-access-reviews"></a>Introdução ao acesso revê


**Tipo:** nova funcionalidade  
**Categoria de serviço:** revisões de acesso  
**Capacidade de produto:** governação  



Revisões de acesso (pré-visualização) permitem às organizações gerir membros do grupo de forma eficiente e acesso a aplicações da empresa: 

- Pode recertify utilizando revisões de acesso do respetivo acesso a aplicações e as associações de grupos de acesso de utilizadores convidados. As informações que forneceu as revisões de acesso ativar revisores de forma eficiente decidir se convidados devem continuaram acesso.

- Pode voltar a certificar o acesso dos colaboradores às aplicações e as associações a grupos com revisões de acesso.

Pode recolher os controlos de revisão de acesso relativos a programas relevantes para a sua organização de modo a controlar as revisões de conformidade ou de aplicações sensíveis a riscos.

Para obter mais informações, consulte [acesso do Azure AD revê](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>Ocultar a minha aplicações e o Iniciador do Office 365 de aplicações de terceiros



**Tipo:** nova funcionalidade  
**Categoria de serviço:** as minhas aplicações  
**Capacidade de produto:** SSO  



Agora pode gerir melhor aplicações apresentados nos portais do utilizador através de um novo **ocultar aplicação** propriedade. Ocultar aplicações ajuda-o com casos em que os mosaicos da aplicação são ser apresentado para serviços de back-end ou mosaicos duplicados e acaba por ficar cluttering launchers de aplicação do utilizador. O botão de alternar está localizado na secção de propriedades da aplicação de terceiros e assinalada como **Visible ao utilizador?** Também pode ocultar uma aplicação através de programação através do PowerShell. 

Para obter mais informações, consulte [ocultar uma aplicação de terceiros da experiência do utilizador no Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**O que está disponível?**

 Como parte da transição para a nova consola de administração, 2 novas APIs para obter os registos de atividade do Azure AD estão disponíveis. O novo conjunto de APIs fornece mais rico de filtragem e ordenação funcionalidade além de proporcionarem auditoria mais completas e as atividades de início de sessão. Os dados previamente disponíveis através de relatórios de segurança agora podem ser acedidos através de eventos de risco Identity Protection API no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-microsoft-identity-manager"></a>Correção do Microsoft Identity Manager


**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** do Microsoft Identity Manager  
**Capacidade de produto:** gestão de ciclo de vida de identidade  



Um pacote de rollup de correção (compilação 4.4.1642.0) está disponível a partir 25 de Setembro de 2017, do Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1). Este pacote de rollup:

- Resolve problemas e adiciona melhorias
- É uma atualização cumulativa que substitui todas as atualizações de MIM 2016 SP1 até compilação 4.4.1459.0 do Microsoft Identity Manager 2016. 
- Requer que tenha **4.4.1302.0 de compilação do Microsoft Identity Manager 2016.** 

Para obter mais informações, consulte [(compilação 4.4.1642.0) do pacote de rollup de correção está disponível para o Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/help/4021562). 

---
