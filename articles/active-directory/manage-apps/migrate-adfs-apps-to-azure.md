---
title: Mova as aplicações do AD FS para o Azure AD. | Microsoft Docs
description: Este artigo destina-se para ajudar as organizações a compreender como migrar aplicativos para o Azure AD, com foco em aplicações SaaS federadas.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2739b5d2d944ea9a8b8cefdcc741abc8a2b632a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113406"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>Mover aplicações do AD FS para o Azure AD 

Este artigo ajuda-o a compreender como mover aplicativos do AD FS para o Azure Active Directory (Azure AD). Aborda em especial as aplicações SaaS federadas. 

Este artigo não é uma orientação passo a passo. Disponibiliza orientações conceptuais para o ajudar a concretizar a migração ao explicar de que forma é que as configurações no local se traduzem no Azure AD. Também aborda cenários comuns.

## <a name="introduction"></a>Introdução

Se tiver um diretório no local que contenha as contas de utilizador, é provável que tenha, pelo menos, uma ou duas aplicações. E estas aplicações estão configuradas para os utilizadores acederem mediante o início de sessão com essas identidades.

E se a sua organização for igual à maioria, o mais provável é que já esteja a meio caminho para adotar as aplicações e identidades na cloud. Se calhar, está a utilizar o Office 365 e o Azure AD Connect. Talvez tenha configurado aplicações SaaS baseadas na cloud para algumas cargas de trabalho importantes, mas não para todas.  

Muitas organizações têm aplicações SaaS ou de linha de negócio (LOB) personalizadas federadas diretamente num serviço de início de sessão no local, como o Active Directory Federation Service (AD FS), juntamente com aplicações baseadas no Office 365 e no Azure AD. Este guia descreve por que e como mover seus aplicativos para o Azure AD.

>[!NOTE]
>Este guia disponibiliza informações detalhadas sobre a configuração e migração de aplicações SaaS, com informações de alto nível relativamente a aplicações LOB personalizadas. Está prevista a disponibilização de mais orientações detalhadas para aplicações LOB no futuro.

![Aplicações ligadas diretamente no local](media/migrate-adfs-apps-to-azure/migrate1.png)

![Aplicações federadas através do Azure AD](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>Motivos para mover aplicações para o Azure AD

Para uma organização que já utiliza o AD FS, Ping ou outro fornecedor de autenticação no local, a mover aplicações para o Azure AD permite as seguintes vantagens:

**Acesso mais seguro**
- Configurar controlos de acesso granulares por aplicação, incluindo o Azure Multi-Factor Authentication, mediante a utilização do [acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md). Estas políticas podem ser aplicadas a aplicações SaaS e personalizadas tal e qual como faz atualmente para o Office 365
- Para detetar ameaças e ajudar proteger o início de sessão com base em machine learning e heurística que identificam o tráfego de risco, tire partido do [Azure AD Identity Protection](../active-directory-identityprotection.md)

**Colaboração B2B do Azure AD**
- Quando o início de sessão nas aplicações SaaS se basear no Azure AD, pode dar aos parceiros acesso a recursos na cloud com a [colaboração B2B do Azure AD](../b2b/what-is-b2b.md).

**Experiência de administração mais fácil e capacidades adicionais do Azure Ad**
- O Azure AD, enquanto fornecedor de identidade para aplicações SaaS, suporta capacidades adicionais, tais como:
  - Certificados de assinatura de tokens por aplicação.
  - [Datas de expiração de certificados configuráveis](manage-certificates-for-federated-single-sign-on.md).
  - [Aprovisionamento automatizado](user-provisioning.md) de contas de utilizador (em determinadas aplicações do Azure Marketplace) com base em identidades do Azure AD.

**Manter as vantagens de um fornecedor de identidades no local**
- Enquanto estiver a obter as vantagens do Azure AD, pode continuar a utilizar a sua solução no local para a autenticação. Dessa forma, as vantagens como as soluções de Multi-Factor Authentication, registo e auditoria no local continuam implementadas. 

**Ajudar a extinguir o fornecedor de identidade no local**
- Para organizações que pretendem extinguir o produto de autenticação no local, mover aplicações para o Azure AD permite uma transição mais fácil ao remover parte do trabalho fora do caminho. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mapear tipos de aplicações no local para tipos de aplicações no Azure AD
A maioria das aplicações pertence a uma de algumas categorias com base no tipo de início de sessão que utilizam. Estas categorias determinam a forma como a aplicação é representada no Azure AD.

Resumindo, as aplicações SAML 2.0 podem ser integradas no Azure AD através da galeria de aplicações do Azure AD no Marketplace ou como aplicações externas a este. Do mesmo modo, as aplicações que utilizam OAuth 2.0 ou OpenID Connect podem ser integradas no Azure AD como *registos de aplicações*. Leia mais detalhes abaixo.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Aplicações SaaS vs. aplicações LOB personalizadas federadas
As aplicações federadas incluem aplicações das categorias apresentadas a seguir:

- Aplicações SaaS 
    - Se os seus utilizadores iniciarem sessão nas aplicações SaaS, como Salesforce, ServiceNow ou Workday, e estiver a integrar num fornecedor de identidade no local, como o AD FS ou o Ping, significa que está a utilizar o início de sessão federado nas aplicações SaaS.
    - Geralmente, as aplicações utilizam o protocolo SAML 2.0 para início de sessão federado.
    - As aplicações que pertencem a esta categoria podem ser integradas no Azure AD como aplicações empresariais, a partir do Marketplace ou como aplicações externas a este.
- Aplicações LOB personalizadas
    - Referem-se a aplicações não SaaS, desenvolvidas internamente pela sua organização ou disponíveis como produtos de pacote padrão que são instalados no seu datacenter. Incluem aplicações do SharePoint e aplicações criadas com base no Windows Identity Foundation.
    - As aplicações podem utilizar SAML 2.0, WS-Federation, OAuth ou OpenID Connect para o início de sessão federado.
    - As aplicações personalizadas que utilizam OAuth 2.0, OpenID Connect ou WS-Federation podem ser integradas no Azure AD como registos de aplicações. As aplicações personalizadas que utilizam SAML 2.0 ou WS-Federation podem ser integradas como aplicações externas ao Marketplace dentro as aplicações empresariais.

### <a name="non-federated-apps"></a>Aplicações não federadas
Pode utilizar o Proxy de Aplicações do Azure AD e as capacidades relacionadas para integrar as aplicações não federadas no Azure AD. As aplicações não federadas incluem:
- Aplicações que utilizam o Windows Integrated Authentication diretamente com o Active Directory. Pode integrar estas aplicações no Azure AD com o [Proxy de Aplicações do Azure AD](application-proxy-add-on-premises-application.md).
- Aplicações que se integram no seu fornecedor de início de sessão único através de um agente e que utilizam cabeçalhos para autorização. As aplicações no local que utilizam um agente instalado para início de sessão e a autorização baseada em cabeçalhos podem ser configuradas para o início de sessão baseado no Azure AD através do Proxy de Aplicações do Azure AD com o [PingAccess for Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Traduzir aplicações federadas no local para o Azure AD 
O AD FS e o Azure funcionam de forma semelhante, pelo que os conceitos de configurar os identificadores e os URLs de confiança, início de sessão e fim de sessão aplicam-se em ambos os casos. No entanto, tem de compreender algumas pequenas diferenças quando fizer a transição.

As tabelas seguintes mapeiam várias ideias importantes que o AD FS, o Azure AD e as aplicações SaaS partilham, para o ajudar a traduzir. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Representar a aplicação no Azure AD ou no AD FS
A migração começa com a avaliação da forma como a aplicação está configurada no local e mapear essa configuração para o Azure AD. A tabela seguinte mapeia os elementos de configuração da entidade confiadora do AD FS para os elementos correspondentes no Azure AD.  
- Termo do AD FS: Entidade confiadora ou confiança de entidade confiadora.
- Termo do AD do Azure: Enterprise application ou registo de aplicações (consoante o tipo de aplicação).

|Elemento de configuração da aplicação|Descrição|Localização na configuração do AD FS|Localização correspondente na configuração do Azure AD|Elemento de token SAML|
|-----|-----|-----|-----|-----|
|URL de início de sessão da aplicação|URL da página de início de sessão desta aplicação. É aonde o utilizador acede para iniciar sessão na aplicação num fluxo SAML iniciado por SP.|N/A|No Azure AD, o URL de início de sessão é configurado no portal do Azure, nas Propriedades de **Início de Sessão Único** da aplicação, como o URL de início de sessão.</br></br>(Poderá ter de clicar em **Mostrar definições de URL avançadas** para ver o URL de início de sessão).|N/A|
|URL de resposta da aplicação|URL da aplicação na perspetiva do fornecedor de identidade (IdP). É para aqui que o utilizador e o token são enviados depois de o utilizador iniciar sessão no IdP.</br></br> Por vezes, é denominado “ponto final de consumidor de asserção de SAML”.|Está disponível na fidedignidade da entidade confiadora do AD FS da aplicação. Clique com o botão direito do rato na entidade confiadora, selecione **Propriedades** e selecione o separador **Pontos Finais**.|No Azure AD, o URL de resposta é configurado no portal do Azure, nas Propriedades **Início de Sessão Único** da aplicação, como o URL de resposta.</br></br>(Poderá ter de selecionar **Mostrar definições de URL avançadas** para ver o URL de resposta).|É mapeado para o elemento **Destino** no token SAML.</br></br> Valor de exemplo: `https://contoso.my.salesforce.com`|
|URL de fim de sessão da aplicação|URL para o qual são enviados os pedidos de “limpeza de fim de sessão” quando o utilizador termina sessão numa aplicação, para terminar sessão em todas as outras aplicações nas quais o IdP tenha iniciado sessão pelo utilizador.|Está disponível na Gestão do AD FS em **Fidedignidades da Entidade Confiadora**. Clique com o botão direito do rato na entidade confiadora, selecione **Propriedades** e selecione o separador **Pontos Finais**.|N/D. O Azure AD não suporta o “fim de sessão único”, o que significa terminar sessão em todas as aplicações. Simplesmente termina a sessão do utilizador do próprio Azure AD.|N/A|
|Identificador de aplicação|O identificador da aplicação na perspetiva do IdP. O valor do URL de início de sessão é, muitas vezes, utilizado como o identificador (mas não sempre).</br></br> Por vezes, a aplicação refere-se a este identificador como "ID da entidade".|No AD FS, este é o ID da entidade confiadora. Clique com o botão direito do rato na entidade confiadora, selecione **Propriedades** e selecione o separador **Identificadores**.|No Azure AD, o identificador é configurado no portal do Azure, nas Propriedades **Início de Sessão Único** da aplicação, como o identificador em **Domínio e URLs**. (Poderá ter de clicar na caixa de verificação **Mostrar definições de URL avançadas**.)|Corresponde ao elemento **Audiência** no token SAML.|
|Metadados de federação da aplicação|Localização dos metadados de federação da aplicação. O IdP utiliza-os para atualizar automaticamente definições de configuração específicas, como pontos finais ou certificados de encriptação.|O URL dos metadados de federação da aplicação estão disponíveis na fidedignidade da entidade confiadora do AD FS da aplicação. Clique com o botão direito do rato na confiança, selecione **Propriedades** e selecione o separador **Monitorização**.|N/D. O Azure AD não suporta o consumo direto dos metadados de federação da aplicação.|N/A|
|Identificador de utilizador/**NameID**|Atributo que é utilizado para indicar exclusivamente a identidade a partir do utilizador do Azure AD ou do AD FS para a sua aplicação.</br></br> Normalmente, este atributo é o UPN ou o endereço de e-mail do utilizador.|No AD FS, está disponível como regra de afirmação na entidade confiadora. Na maioria dos casos, a regra de afirmação emite uma afirmação com um tipo que termina em "nameidentifier".|No Azure AD, o identificador de utilizador está disponível no portal do Azure, nas Propriedades **Início de Sessão Único** da aplicação, no cabeçalho **Atributos do Utilizador**.</br></br>Por predefinição, é utilizado o UPN.|Comunicado a partir do IdP para a aplicação como o elemento “**NameID** no token SAML.|
|Outras afirmações que vão ser enviadas para a aplicação|Para além do identificador do utilizador/**NameID**, são normalmente enviadas outras informações de afirmações do IdP para a aplicação. Alguns exemplos incluem o nome próprio, o apelido, o endereço de e-mail e grupos dos quais o utilizador é membro.|No AD FS, está disponível como outras regras de afirmação na entidade confiadora.|No Azure AD, está disponível no portal do Azure, nas Propriedades **Início de Sessão Único** da aplicação, no cabeçalho **Atributos do Utilizador**. Selecione **Vista** e edite todos os outros atributos de utilizador|N/A|  

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Representar o Azure AD como fornecedor de identidade numa aplicações SaaS
Como parte da migração, tem de configurar a aplicação para apontar para o Azure AD (por oposição ao fornecedor de identidade no local). Esta secção debruça-se nas aplicações SaaS que utilizam o protocolo SAML e não nas aplicações LOB personalizadas. No entanto, os conceitos estendem-se a estas últimas. 

Num nível mais elevado, alguns aspetos importantes apontam uma aplicação SaaS para o Azure AD:
- Emissor do fornecedor de identidade: https&#58;//sts.windows.net/{tenant-id}/
- URL de início de sessão do fornecedor de identidade: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- URL de fim de sessão do fornecedor de identidade: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Localização dos metadados de federação: https&#58;//login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id} 

Substitua {tenant-id} pelo ID do seu inquilino, que está disponível no portal do Azure em **Azure Active Directory** > **Propriedades** como **ID do Diretório**. Substitua {application-id} pelo ID da sua aplicação, que está disponível nas propriedades da aplicação como **ID da Aplicação**.

A tabela seguinte descreve os principais elementos de configuração do IdP para configurar as definições de SSO na aplicação e os respetivos valores ou localizações no AD FS e no Azure AD. A arquitetura de referência da tabela é a aplicação SaaS, que tem de saber para onde enviar os pedidos de autenticação e como validar os tokens recebidos.

|Elemento de configuração|Descrição|AD FS|Azure AD|
|---|---|---|---|
|Metadados </br>início de sessão </br>do IdP|URL de início de sessão do IdP na perspetiva da aplicação (para onde o utilizador é redirecionado para iniciar sessão).|O URL de início de sessão do AD FS é o nome do serviço de federação do AD FS seguido de “/adfs/ls/.” Por exemplo: https&#58;//fs.contoso.com/adfs/ls/|O valor correspondente para o Azure AD segue o padrão em que {tenant-id} é substituído pelo ID do seu inquilino. Este está disponível no portal do Azure em **Azure Active Directory** > **Propriedades** como **ID do Diretório**.</br></br>Para as aplicações que utilizam o protocolo SAML-P: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 </br></br>Para as aplicações que utilizam o protocolo WS-Federation: https&#58;//login.microsoftonline.com/{tenant-id}/wsfed|
|Metadados </br>de fim de sessão </br>do IdP|URL de fim de sessão do IdP na perspetiva da aplicação (para onde o utilizador é redirecionado quando escolhe terminar sessão na aplicação).|No AD FS, o URL de fim de sessão é o URL de início de sessão ou é o mesmo URL com “wa=wsignout1.0” anexado. Por exemplo: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|O valor correspondente para o Azure AD depende de a aplicação suportar o fim de sessão SAML 2.0.</br></br>Se o suportar, o valor segue o padrão em que o valor de {tenant-id} é substituído pelo ID do inquilino. Este está disponível no portal do Azure em **Azure Active Directory** > **Propriedades** como **ID do Diretório**: https&#58;//login.microsoftonline.com/{tenant-id}/saml2</br></br>Se a aplicação não suportar o fim de sessão SAML: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Certificado de </br>assinatura de </br>certificado|O certificado cuja chave privada é utilizada pelo IdP para assinar tokens emitidos. Verifica se o token provém do mesmo IdP para o qual a aplicação está configurada para confiar.|O certificado de assinatura de tokens do AD FS está disponível na Gestão do AD FS, em **Certificados**.|No Azure AD, o certificado de assinatura de tokens está disponível no portal do Azure, nas propriedades **Início de Sessão Único** da aplicação, no cabeçalho **Certificado de Assinatura SAML**. Aí, pode transferir o certificado para carregamento para a aplicação.</br></br> Se a aplicação tiver mais de um certificado, todos os certificados estão disponíveis no ficheiro XML dos metadados de federação.|
|Identificador/</br>“emissor”|O identificador do IdP na perspetiva da aplicação (por vezes, denominado “emissor” ou “ID do emissor”).</br></br>No token SAML, o valor aparece como o elemento **Emissor**.|Geralmente, o identificador no AD FS é o identificador do serviço de federação na Gestão do AD FS, em **Serviço** >  **Editar Propriedades do Serviço de Federação**. Por exemplo: http&#58;//fs.contoso.com/adfs/services/trust|O valor correspondente para o Azure AD segue o padrão em que o valor de {tenant-id} é substituído pelo ID do inquilino. Este está disponível no portal do Azure em **Azure Active Directory** > **Propriedades** como **ID do Diretório**: https&#58;//sts.windows.net/{tenant-id}/|
|Metadados </br>de federação </br>do IdP|A localização dos metadados de federação publicamente disponíveis do IdP. (Algumas aplicações utilizam os metadados de federação como alternativa à configuração individual, por parte do administrador, de URLs, do identificador e do certificado de assinatura de tokens.)|Encontrar o URL de metadados de Federação do AD FS na gestão do AD FS, em **serviço** > **pontos finais** > **metadados**  >   **Tipo: Metadados de Federação**. Por exmplo: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|O valor correspondente para o Azure AD segue o padrão https&#58;//login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml. O valor de {TenantDomainName} pelo nome do seu inquilino, no formato “contoso.onmicrosoft.com.” </br></br>Para obter mais informações, veja [Federation metadata](../develop/azure-ad-federation-metadata.md) (Metadados da federação).

## <a name="moving-saas-apps"></a>Mover aplicações SaaS
Mover aplicações SaaS do AD FS ou de outro fornecedor de identidade para o Azure AD é um processo manual de hoje. Para obter orientações para aplicações específicas, veja a [lista de tutoriais relativos à integração de aplicações SaaS disponíveis no Marketplace](../saas-apps/tutorial-list.md).

Os tutoriais de integração partem do princípio de que está a fazer uma integração “green field”. À medida que planeia, avalia, configura e faz a transição das aplicações, existem alguns conceitos importantes específicos da migração que deverá conhecer:  
- Algumas aplicações podem ser migradas facilmente. As aplicações que tenham requisitos mais complexos, como afirmações personalizadas, podem exigir configuração adicional no Azure AD e/ou no Azure AD Connect.
- Nos cenários mais comuns, apenas a afirmação **NameID** e outras afirmações de identificador de utilizador comuns são necessárias para as aplicações. Para determinar se são precisas afirmações adicionais, examine que afirmações está a emitir do AD FS ou do seu fornecedor de identidade de terceiros.
- Após determinar que são necessárias afirmações adicionais, confirme que estão disponíveis no Azure AD. Verifique a configuração da sincronização do Azure AD Connect para garantir que um atributo necessário, como, por exemplo, **samAccountName**, está a ser sincronizado com o Azure AD.
- Quando os atributos estiverem disponíveis no Azure AD, pode adicionar regras de emissão de afirmações ao Azure AD para incluir esses atributos como afirmações nos tokens emitidos. Estas regras são adicionadas nas propriedades **Início de sessão único** da aplicação no Azure AD.

### <a name="assess-what-can-be-moved"></a>Avaliar o que podem ser movido
As aplicações SAML 2.0 podem ser integradas no Azure AD através da galeria de aplicações do Azure AD no Marketplace ou como aplicações externas a este.  

Algumas configurações exigem passos adicionais para configuração no Azure AD e algumas que não são atualmente suportadas. Para determinar o que pode ser movido, observe a configuração atual de cada uma das suas aplicações. Mais concretamente, procure:
- Regras de afirmações configuradas (regras de transformação de emissão).
- O formato e atributo **NameID** de SAML.
- As versões do token SAML emitidas.
- Outras configurações, como regras de autorização de emissão ou políticas de controlo de acesso e regras de Multi-Factor Authentication (autenticação adicional).

#### <a name="what-can-be-moved-today"></a>O que pode ser movido hoje mesmo
As aplicações que pode mover facilmente hoje incluem aplicações SAML 2.0 que utilizam o conjunto padrão de elementos de configuração e afirmações. Estas aplicações podem consistir em:
- Nome principal de utilizador.
- Endereço de e-mail.
- Nome próprio.
- Apelido.
- Atributo alternativo como **NameID** de SAML, incluindo o atributo de correio do Azure AD, o prefixo de correio, o ID de funcionário, os atributos de extensão 1-15 ou o atributo **SamAccountName**. Para obter mais informações, veja [Editing the NameIdentifier claim](../develop/active-directory-saml-claims-customization.md) (Editar a afirmação NameIdentifier).
- Afirmações personalizadas. Para obter informações sobre os mapeamentos de afirmações suportadas, veja [Claims mapping in Azure Active Directory](../develop/active-directory-claims-mapping.md) (Mapeamento de afirmações no Azure Active Directory) e [Customizing claims issued in the SAML token for enterprise applications in Azure Active Directory](../develop/active-directory-saml-claims-customization.md) (Personalizar afirmações emitidas no token SAML para aplicações empresariais no Azure Active Directory).

Além das afirmações personalizadas e dos elementos **NameID**, as configurações que exigem passos adicionais no Azure AD como parte da migração são:
- Regras de autorização personalizada ou de Multi-Factor Authentication no AD FS. Pode utilizar a funcionalidade de [acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md) para configurá-las.
- Aplicações com vários pontos finais de SAML. Pode configurá-las no Azure AD através do PowerShell. (Esta capacidade não está disponível no portal).
- Aplicações WS-Federation, como as aplicações do SharePoint, que precisam de tokens SAML da versão 1.1. Tem de configurá-las manualmente com o PowerShell.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplicações e configurações não suportadas atualmente no Azure AD
As aplicações que requerem as capacidades seguintes não podem ser migradas atualmente. Se tiver aplicações que precisem destas funcionalidades, envie os seus comentários na secção de comentários.
- Capacidades do protocolo:
    - Suporte para Fim de Sessão Único (SLO) de SAML em todas as aplicações com sessão iniciada.
    - Suporte para o padrão WS-Trust ActAs.
    - Resolução de artefactos SAML.
    - Verificação da assinatura de pedidos SAML assinados. Tenha em conta que os pedidos assinados são aceites, mas a assinatura não é verificada.
- Capacidades de tokens: 
    - Encriptação de tokens SAML. 
    - Tokens SAML com a versão 1.1 em respostas do protocolo SAML. 
- Afirmações em capacidades de tokens:
    - Emissão de nomes de grupos no local como afirmações.
    - Afirmações de arquivos que não o Azure AD.
    - Regras de transformação de emissão de afirmações complexas. Para obter informações sobre os mapeamentos de afirmações suportadas, veja [Claims mapping in Azure Active Directory](../develop/active-directory-claims-mapping.md) (Mapeamento de afirmações no Azure Active Directory) e [Customizing claims issued in the SAML token for enterprise applications in Azure Active Directory](../develop/active-directory-saml-claims-customization.md) (Personalizar afirmações emitidas no token SAML para aplicações empresariais no Azure Active Directory).
    - Emissão de extensões de diretório como afirmações.
    - Especificação personalizada do formato **NameID**.
    - Emissão de atributos de vários valores.

>[!NOTE]
>O Azure AD evolui continuamente para acrescentar capacidades nesta área. Atualizamos este artigo regularmente. 

### <a name="configure-azure-ad"></a>Configurar o Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Configurar as definições início de sessão único (SSO) para a aplicação SaaS

No Azure AD, a configuração do início de sessão de SAML (conforme exigido pela sua aplicação) é feita nas propriedades **Início de sessão único**, em **Atributos do Utilizador**.

![Secção Propriedades de início de sessão único com "Atributos do Utilizador"](media/migrate-adfs-apps-to-azure/migrate3.png)

Selecione **Ver e editar todos os outros atributos de utilizador** para ver os atributos que vão ser enviados como afirmações no token de segurança.

![Lista de atributos](media/migrate-adfs-apps-to-azure/migrate4.png)

Selecione a linha de um atributo específico para editar ou selecione **Adicionar atributo** para adicionar um atributo novo.

![Painel “Editar Atributo”](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Atribuir utilizadores à aplicação
Para que os seus utilizadores do Azure AD possam iniciar sessão numa aplicação SaaS, tem de lhes dar acesso.

Para atribuir utilizadores no portal do Azure AD, navegue para a página da aplicação SaaS e selecione **Utilizadores e grupos**, na barra lateral. Para adicionar um utilizador ou um grupo, selecione **Adicionar utilizador**, na parte superior do painel. 

![Botão "Adicionar utilizador" em "Utilizadores e grupos"](media/migrate-adfs-apps-to-azure/migrate6.png) 

![Painel “Adicionar Atribuição”](media/migrate-adfs-apps-to-azure/migrate7.png)

Para verificar o acesso, os utilizadores deverão ver a aplicação SaaS no respetivo [painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md) quando iniciarem sessão. O painel de acesso está disponível em https://myapps.microsoft.com. Neste exemplo, foi atribuído com êxito a um utilizador acesso ao Salesforce e ao ServiceNow.

![Painel de acesso de exemplo com aplicações Salesforce e ServiceNow](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Configurar a aplicação SaaS
O processo de transição da federação no local para o Azure AD depende de a aplicação SaaS com que está a trabalhar suportar ou não vários fornecedores de identidade. Seguem-se algumas perguntas comuns sobre o suporte para vários IdPs:

   **P: O que significa para uma aplicação suportar vários IdPs?**
    
   R: Aplicações SaaS que suportam vários IdPs permitem-lhe introduzir todas as informações sobre o IdP novo (no nosso caso, o Azure AD) antes de consolidar a alteração da experiência de início de sessão. Quando a configuração estiver concluída, pode mudar a configuração de autenticação da aplicação para apontar para o Azure AD.

   **P: Por que motivo é relevante a aplicação SaaS suportar vários IdPs?**

   R: Se não forem suportados vários IdPs, o administrador tem reservar um curto período de tempo como para falha de serviço ou manutenção durante o qual configurar o Azure AD como o IdP novo da aplicação. Durante este período de inatividade, os utilizadores devem ser notificados de que não conseguirão iniciar sessão nas respetivas contas.

   Se uma aplicação suportar vários IdPs, os IdPs adicionais podem ser configurados com antecedência. O administrador pode, então, mudar o IdP quando estiver a transitar para o Azure.

   Se a aplicação suportar vários IdPs e quiser ter vários IdPs a processar em simultâneo a autenticação de início de sessão, o utilizador pode escolher que IdP vai utilizar na autenticação na respetiva página de início de sessão.

#### <a name="example-support-for-multiple-idps"></a>Exemplo: Suporte para vários IdPs
Por exemplo, no Salesforce, a configuração do IdP está disponível em **Settings** (Definições) > **Company Settings** (Definições da Empresa) > **My Domain** (O Meu Domínio) > **Authentication Configuration** (Configuração da Autenticação).

![Secção "Configuração da Autenticação" na aplicação Salesforce](media/migrate-adfs-apps-to-azure/migrate9.png)

Devido à configuração que criámos anteriormente em **Identity** (Identidade) > **Single sign-on settings** (Definições de início de sessão único), deverá conseguir alterar o IdP para a configuração da autenticação. Por exemplo, pode alterá-lo do AD FS para o Azure AD. 

![Selecionar o Azure AD como o serviço de autenticação](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Opcional: Configurar o aprovisionamento de utilizador no Azure AD
Se quiser que o Azure AD processe diretamente o aprovisionamento de utilizadores numa aplicação SaaS, veja [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS com o Azure Active Directory).

## <a name="next-steps"></a>Passos Seguintes

- [Managing applications with Azure Active Directory](what-is-application-management.md) (Gerir aplicações com o Azure Active Directory)
- [Gerir o acesso a aplicações](what-is-access-management.md)
- [Azure AD Connect Federation](../hybrid/how-to-connect-fed-whatis.md) (Federação do Azure AD Connect)
