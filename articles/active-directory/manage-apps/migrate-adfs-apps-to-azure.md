---
title: Autenticação de aplicações móveis de AD FS para Azure Ative Directory
description: Saiba como utilizar o Azure Ative Directory para substituir os Serviços da Federação de Diretórios Ativos (AD FS), dando aos utilizadores um único sinal de todas as suas aplicações.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418468"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Mover a autenticação da aplicação do AD FS para o AAD

[O Azure Ative Directory (Azure AD)](../fundamentals/active-directory-whatis.md) oferece uma plataforma de identidade universal que fornece às suas pessoas, parceiros e clientes uma única identidade para aceder a aplicações e colaborar a partir de qualquer plataforma e dispositivo. A Azure AD tem um [conjunto completo de capacidades de gestão de identidade.](../fundamentals/active-directory-whatis.md) A normalização da autenticação da sua aplicação e a autorização para a Azure AD proporciona estes benefícios.

> [!TIP]
> Este artigo está escrito para um público desenvolvedor. Os gestores de projetos e administradores que pretendam mover uma aplicação para Azure AD devem considerar a [leitura da autenticação de aplicações migratórias para a Azure AD](migrate-application-authentication-to-azure-active-directory.md).

## <a name="azure-ad-benefits"></a>Benefícios da AD Azure

Se tiver um diretório no local que contenha contas de utilizador, é provável que tenha muitas aplicações para as quais os utilizadores autenticam. Cada uma destas aplicações está configurada para que os utilizadores acedam utilizando as suas identidades.

Os utilizadores também podem autenticar diretamente com o seu Ative Directory no local. Ative Directory Federation Services (AD FS) é um serviço de identidade baseado em padrões no local. Alarga a capacidade de utilizar a funcionalidade de sso (SSO) de acordo com a capacidade de utilizar uma única funcionalidade de ss on (SSO) para que os utilizadores não sejam obrigados a assinar separadamente a cada aplicação. Isto é conhecido como identidade federada.

Muitas organizações têm software como um serviço (SaaS) ou aplicações de linha de negócio personalizadas federadas diretamente para AD FS, ao lado de aplicações baseadas em AD microsoft 365 e Azure.

  ![Aplicações ligadas diretamente no local](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Para aumentar a segurança da aplicação, o seu objetivo é ter um único conjunto de controlos de acesso e políticas em todos os seus locais e ambientes em nuvem.

  ![Aplicações ligadas através do Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Tipos de apps para migrar

Migrar toda a autenticação da sua aplicação para a Azure AD é o ideal, pois dá-lhe um único plano de controlo para gestão de identidade e acesso.

As suas aplicações podem utilizar protocolos modernos ou antigos para autenticação. Quando planeia a sua migração para a Azure AD, considere migrar as aplicações que utilizam protocolos de autenticação modernos (como SAML e Open ID Connect) primeiro. Estas aplicações podem ser reconfiguradas para autenticar com Azure AD, quer através de um conector incorporado da Galeria de Aplicações Azure, quer através do registo da aplicação em Azure AD. As aplicações que utilizam protocolos mais antigos podem ser integradas usando o Application Proxy.

Para obter mais informações, consulte:

* [Utilizar o Azure AD Application Proxy para publicar aplicações no local para utilizadores remotos.](what-is-application-proxy.md)
* [O que é a gestão de aplicações?](what-is-application-management.md)
* [Relatório de atividade da aplicação AD FS para migrar aplicações para Azure AD](migrate-adfs-application-activity.md).
* [Monitor AD FS utilizando Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>O processo de migração

Durante o processo de mudança da autenticação da sua aplicação para Azure AD, teste as suas aplicações e configuração. Recomendamos que continue a utilizar ambientes de teste existentes para testes de migração quando se desloca para o ambiente de produção. Se um ambiente de teste não estiver disponível atualmente, pode configurar um utilizando [o Azure App Service](https://azure.microsoft.com/services/app-service/) ou a [Azure Virtual Machines,](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)dependendo da arquitetura da aplicação.

Pode optar por configurar um inquilino Azure AD de teste separado para desenvolver as configurações da sua aplicação.

O seu processo de migração pode ser assim:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Fase 1 – Estado atual: A app de produção autentica-se com AD FS

  ![Fase de migração 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Fase 2 – (Opcional) Apontar uma instância de teste da app para o teste Azure AD inquilino

Atualize a configuração para apontar a sua instância de teste da aplicação para um inquilino AZure AD de teste, e faça quaisquer alterações necessárias. A aplicação pode ser testada com os utilizadores no teste Azure AD inquilino. Durante o processo de desenvolvimento, pode utilizar ferramentas como [o Fiddler](https://www.telerik.com/fiddler) para comparar e verificar pedidos e respostas.

Se não for possível configurar um inquilino de teste separado, salte esta fase e aponte uma instância de teste da app para o seu inquilino AZure AD de produção, conforme descrito na fase 3 abaixo.

  ![Fase de migração 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Fase 3 - Aponte uma instância de teste da app para a produção Azure AD inquilino

Atualize a configuração para apontar a sua instância de teste da app para o seu inquilino AZure AD de produção. Pode agora testar com os utilizadores do seu inquilino de produção. Se necessário, reveja a secção deste artigo sobre utilizadores em transição.

  ![Fase de migração 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Fase 4 - Aponte a app de produção para a produção Azure AD inquilino

Atualize a configuração da sua app de produção para apontar para o seu inquilino Azure AD de produção.

  ![Fase de migração 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 As aplicações que autenticam com AD FS podem usar grupos de Diretório Ativo para permissões. Utilize [a sincronização Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) para sincronizar dados de identidade entre o ambiente no local e o AD Azure antes de iniciar a migração. Verifique esses grupos e a adesão antes da migração para que possa conceder acesso aos mesmos utilizadores quando a aplicação é migrada.

### <a name="line-of-business-apps"></a>Linha de aplicativos de negócios

As suas aplicações de linha de negócio são aquelas que a sua organização desenvolveu ou aquelas que são um produto embalado padrão. Exemplos incluem aplicações construídas em aplicativos Windows Identity Foundation e SharePoint (não SharePoint Online).

As aplicações de linha de negócios que utilizam o OAuth 2.0, OpenID Connect ou WS-Federation podem ser integradas com a AZure AD como [registos de aplicações.](../develop/quickstart-register-app.md) Integrar aplicações personalizadas que utilizem SAML 2.0 ou WS-Federation como [aplicações não-galeria](add-application-portal.md) na página de aplicações da empresa no [portal Azure](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>SEML-based single sign-on

As aplicações que utilizam o SAML 2.0 para autenticação podem ser configuradas para [um único sign-on (SSO) baseado em SAML.](what-is-single-sign-on.md) Com sSO baseado em SAML, pode mapear os utilizadores para funções de aplicação específicas com base em regras que define nas suas alegações SAML.

Para configurar uma aplicação SaaS para SSO baseada em SAML, consulte [Quickstart: Configurar um único sinal baseado em SAML](add-application-portal-setup-sso.md).

  ![Imagens de utilizador SSO SAML ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Muitas aplicações SaaS têm um [tutorial específico para aplicações](../saas-apps/tutorial-list.md) que o move através da configuração para SSO baseado em SAML.

  ![tutorial de aplicativo](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Algumas aplicações podem ser migradas facilmente. Aplicações com requisitos mais complexos, como reivindicações personalizadas, podem exigir configuração adicional em AZure AD e/ou Azure AD Connect. Para obter informações sobre mapeamentos de reclamações suportados, consulte [Como: Personalizar reclamações emitidas em fichas para uma aplicação específica num inquilino (Preview)](../develop/active-directory-claims-mapping.md).

Tenha em mente as seguintes limitações ao mapear atributos:

* Nem todos os atributos que podem ser emitidos em AD FS aparecem no Azure AD como atributos para emitir fichas SAML, mesmo que esses atributos estejam sincronizados. Quando edita o atributo, a lista de dropdown do **Valor** mostra-lhe os diferentes atributos que estão disponíveis no Azure AD. Verifique a configuração de [tópicos de sincronização Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) para garantir que um atributo necessário — por exemplo, **samAccountName**— está sincronizado com a Azure AD. Pode utilizar os atributos de extensão para emitir qualquer alegação que não faça parte do esquema padrão do utilizador em Azure AD.
* Nos cenários mais comuns, apenas a afirmação **NameID** e outras afirmações de identificador de utilizador comuns são necessárias para as aplicações. Para determinar se são necessárias quaisquer reclamações adicionais, examine as alegações que está a emitir a partir de AD FS.
* Nem todas as reclamações podem ser emitidas, uma vez que algumas reclamações estão protegidas na Azure AD.
* A capacidade de usar fichas SAML encriptadas está agora em pré-visualização. Ver [Como: personalizar reclamações emitidas no token SAML para aplicações empresariais](../develop/active-directory-saml-claims-customization.md).

### <a name="software-as-a-service-saas-apps"></a>Software como um serviço (SaaS) apps

Se os seus utilizadores iniciarem seduzição em aplicações SaaS como Salesforce, ServiceNow ou Workday, e estiverem integrados com FS AD, está a utilizar o sign-on federado para aplicações SaaS.

A maioria das aplicações SaaS podem ser configuradas em Azure AD. A Microsoft tem muitas ligações pré-configuradas para aplicações SaaS na galeria de  [aplicações AZure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), o que facilita a sua transição. As aplicações SAML 2.0 podem ser integradas com a Azure AD através da galeria de aplicações AD AZure ou como [aplicações não-galerias.](add-application-portal.md)

As aplicações que utilizam o OAuth 2.0 ou o OpenID Connect podem ser igualmente integradas com a Azure AD como [registos de aplicações.](../develop/quickstart-register-app.md) As aplicações que utilizam protocolos legados podem usar [o Azure AD Application Proxy](application-proxy.md) para autenticar com Azure AD.

Para qualquer problema com o embarque das suas aplicações SaaS, pode contactar o [pseudónimo de suporte à Integração de Aplicações SaaS.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

### <a name="saml-signing-certificates-for-sso"></a>Certificados de assinatura SAML para SSO

Os certificados de assinatura são uma parte importante de qualquer implantação SSO. A Azure AD cria os certificados de assinatura para estabelecer SSO federado baseado em SAML nas suas aplicações SaaS. Uma vez adicionadas aplicações de galeria ou não galeria, irá configurar a aplicação adicionada usando a opção SSO federada. Consulte [os certificados de gestão de um único sinal federado no Diretório Ativo Azure](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>Encriptação simbólica SAML

Tanto o AD FS como o Azure AD fornecem encriptação simbólica - a capacidade de encriptar as afirmações de segurança SAML que vão para as aplicações. As afirmações são encriptadas com uma chave pública, e desencriptadas pela aplicação recetora com a chave privada correspondente. Ao configurar a encriptação de fichas, carrequiva ficheiros de certificados X.509 para fornecer as chaves públicas.

Para obter informações sobre a encriptação simbólica Azure AD SAML e como configurá-la, consulte [Como: Configurar Azure AD SAML encriptação simbólica](howto-saml-token-encryption.md).  

> [!NOTE]
> A encriptação token é uma funcionalidade premium Azure Ative Directory (Azure AD). Para saber mais sobre as edições, funcionalidades e preços da [AD Azure, consulte os preços da AD Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplicativos e configurações que podem ser movidos hoje

As aplicações que pode mover-se facilmente hoje incluem aplicações SAML 2.0 que usam o conjunto padrão de elementos de configuração e reivindicações. Estes itens padrão são:

* Nome Principal de Utilizador
* Endereço de e-mail
* Nome próprio
* Apelido
* Atributo alternativo como **NameID** de SAML, incluindo o atributo de correio do Azure AD, o prefixo de correio, o ID de funcionário, os atributos de extensão 1-15 ou o atributo **SamAccountName**. Para obter mais informações, veja [Editing the NameIdentifier claim](../develop/active-directory-saml-claims-customization.md) (Editar a afirmação NameIdentifier).
* Afirmações personalizadas.

Os seguintes requerem medidas de configuração adicionais para migrar para Azure AD:

* Autorização personalizada ou regras de autenticação de vários fatores (MFA) em FS AD. Configura-os utilizando a função [de Acesso Condicional Azure AD.](../conditional-access/overview.md)
* Aplicativos com vários pontos finais url de resposta. Configura-os em AD Azure utilizando o PowerShell ou a interface do portal Azure.
* Aplicações WS-Federation, como as aplicações do SharePoint, que precisam de tokens SAML da versão 1.1. Pode configurá-los manualmente utilizando o PowerShell. Também pode adicionar um modelo genérico pré-integrado para aplicações SharePoint e SAML 1.1 da galeria. Apoiamos o protocolo SAML 2.0.
* A emissão de reclamações complexas transforma regras. Para obter informações sobre mapeamentos de reclamações apoiadas, consulte:
   *  [Sinistros mapeamento em Azure Ative Directory](../develop/active-directory-claims-mapping.md).
   * [Personalização de reclamações emitidas no token SAML para aplicações empresariais no Azure Ative Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplicações e configurações não suportadas atualmente no Azure AD

As aplicações que requerem certas capacidades não podem ser migradas hoje em dia.

#### <a name="protocol-capabilities"></a>Capacidades de protocolo

As aplicações que requerem as seguintes capacidades protocolares não podem ser migradas hoje em dia:

* Apoio ao padrão atas WS-Trust
* Resolução de artefactos SAML
* Signature verification of signed SAML requests ‎
  > [!Note]
  > Os pedidos assinados são aceites, mas a assinatura não é verificada.

  ‎Given that Azure AD only returns the token to endpoints preconfigured in the application, signature verification probably isn't required in most cases.

#### <a name="claims-in-token-capabilities"></a>Reclamações em capacidades simbólicas

As aplicações que requerem as seguintes alegações em capacidades simbólicas não podem ser migradas hoje em dia.

* Reclamações de lojas de atributos que não o diretório AD Azure, a menos que esses dados sejam sincronizados com a Azure AD. Para obter mais informações, consulte a visão geral da [API de sincronização AZure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta).
* Emissão de atributos de vário valor do diretório. Por exemplo, não podemos emitir uma reivindicação multivalorizada para endereços de procuração neste momento.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Definições de aplicativo de mapa de AD FS a Azure AD

A migração requer avaliar como a aplicação é configurada no local e, em seguida, mapear essa configuração para Azure AD. O AD FS e o Azure funcionam de forma semelhante, pelo que os conceitos de configurar os identificadores e os URLs de confiança, início de sessão e fim de sessão aplicam-se em ambos os casos. Documente as definições de configuração AD FS das suas aplicações para que possa configurar facilmente as suas aplicações em Azure AD.

### <a name="map-app-configuration-settings"></a>Definições de configuração de aplicativos de mapa

A tabela seguinte descreve alguns dos mapeamentos mais comuns de configurações entre um AD FS Relying Party Trust para Azure AD Enterprise Application:

* AD FS — Encontre a definição no AD FS Relying Party Trust para a aplicação. Clique com a direita na festa de cingimento e selecione Propriedades.
* Azure AD — A definição está configurada dentro do [portal Azure](https://portal.azure.com/) nas propriedades SSO de cada aplicação.

| Definição de configuração| AD FS| Como configurar em Azure AD| Ficha SAML |
| - | - | - | - |
| **URL de início de sessão da aplicação** <p>O URL para o utilizador iniciar súmica na aplicação num fluxo SAML iniciado por um Fornecedor de Serviços (SP).| N/D| Abra a configuração básica de SAML a partir do sign-on baseado em SAML| N/D |
| **URL de resposta da aplicação** <p>O URL da aplicação na perspetiva do fornecedor de identidade (IdP). O IdP envia o utilizador e o token aqui depois de o utilizador ter assinado no IdP.  ‎This is also known as **SAML assertion consumer endpoint**.| Selecione o **separador Pontos finais**| Abra a configuração básica de SAML a partir do sign-on baseado em SAML| Elemento de destino no token SAML. Valor de exemplo: `https://contoso.my.salesforce.com` |
| **URL de fim de sessão da aplicação** <p>Este é o URL para o qual são enviados pedidos de limpeza de assinatura quando um utilizador assina a partir de uma aplicação. O IdP envia o pedido para assinar o utilizador de todas as outras aplicações também.| Selecione o **separador Pontos finais**| Abra a configuração básica de SAML a partir do sign-on baseado em SAML| N/D |
| **Identificador de aplicação** <p>Este é o identificador de aplicações na perspetiva do IdP. O valor do URL de início de sessão é, muitas vezes, utilizado como o identificador (mas não sempre).  ‎Sometimes the app calls this the "entity ID."| Selecione o **separador Identifiers**|Abra a configuração básica de SAML a partir do sign-on baseado em SAML| Mapas para o elemento **do Público** no símbolo DOL. |
| **Metadados de federação da aplicação** <p>Esta é a localização dos metadados da federação da aplicação. O IdP utiliza-os para atualizar automaticamente definições de configuração específicas, como pontos finais ou certificados de encriptação.| Selecione o **separador Monitoring**| N/D. A Azure AD não suporta o consumo de metadados da federação de aplicações diretamente. Pode importar manualmente os metadados da federação.| N/D |
| **Identificador de utilizador/ ID de nome** <p>Atributo que é utilizado para indicar exclusivamente a identidade a partir do utilizador do Azure AD ou do AD FS para a sua aplicação.  ‎This attribute is typically either the UPN or the email address of the user.| Regras de reivindicação. Na maioria dos casos, a regra de reclamação emite uma reclamação com um tipo que termina com o **Identificador de Nome**.| Pode encontrar o identificador sob o cabeçalho **Atributos e Reclamações do Utilizador**. Por padrão, a UPN é usada| Mapeia para o elemento **NameID** no token SAML. |
| **Outras reclamações** <p>Exemplos de outras informações de reclamação que são geralmente enviadas do IdP para a aplicação incluem o primeiro nome, apelido, endereço de e-mail e membro do grupo.| No AD FS, está disponível como outras regras de afirmação na entidade confiadora.| Pode encontrar o identificador sob o cabeçalho **Atributos do Utilizador & Reclamações**. Selecione **Vista** e edite todos os outros atributos de utilizador| N/D |

### <a name="map-identity-provider-idp-settings"></a>Definições do Fornecedor de Identidade do Mapa (IdP)

Configure as suas aplicações para apontar para Azure AD contra AD FS para SSO. Aqui, estamos focados em aplicações SaaS que usam o protocolo SAML. No entanto, este conceito estende-se também a aplicações personalizadas de linha de negócios.

> [!NOTE]
> Os valores de configuração para Azure AD seguem o padrão em que o seu ID do Inquilino Azure substitui {tenant-id} e o ID de aplicação substitui {application-id}. Encontra esta informação no [portal Azure](https://portal.azure.com/) no âmbito **do Azure Ative Directory > Properties:**

* Selecione o ID do Diretório para ver a sua identificação do inquilino.
* Selecione ID de aplicação para ver o seu ID de aplicação.

 Num nível elevado, mapear os seguintes elementos de configuração de aplicações SaaS para Azure AD.

| Elemento| Valor de Configuração |
| - | - |
| Emitente de fornecedor de identidade| https: \/ /sts.windows.net/{tenant-id}/ |
| URL de login do fornecedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL de logout do fornecedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Localização de metadados da Federação| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Map SSO configurações para apps SaaS

As aplicações saaS precisam de saber para onde enviar pedidos de autenticação e como validar os tokens recebidos. A tabela seguinte descreve os elementos para configurar as definições de SSO na aplicação, e os seus valores ou localizações dentro de AD FS e Azure AD

| Definição de configuração| AD FS| Como configurar em Azure AD |
| - | - | - |
| **URL de inscrição idP** <p>URL de entrada do IdP do ponto de vista da aplicação (onde o utilizador é redirecionado para login).| O URL de assinatura da AD FS é o nome de serviço da federação AD FS seguido de "/adfs/ls/". <p>Por exemplo: `https://fs.contoso.com/adfs/ls/`| Substitua {tenant-id} por identificação do seu inquilino. <p> ‎For apps that use the SAML-P protocol: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>‎For apps that use the WS-Federation protocol: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL de aprovação idP**<p>URL de assinatura do IdP do ponto de vista da aplicação (onde o utilizador é redirecionado quando escolhe assinar fora da app).| O URL de sinalização é o mesmo que o URL de inscrição, ou o mesmo URL com "wa=wsignout1.0" anexado. Por exemplo: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Substitua {tenant-id} por identificação do seu inquilino.<p>Para aplicações que usam o protocolo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> ‎For apps that use the WS-Federation protocol: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificado de assinatura token**<p>O IdP utiliza a chave privada do certificado para assinar fichas emitidas. Verifica se o token provém do mesmo IdP para o qual a aplicação está configurada para confiar.| O certificado de assinatura de tokens do AD FS está disponível na Gestão do AD FS, em **Certificados**.| Encontre-o no portal Azure nas propriedades de **inscrição única** da aplicação sob o certificado de **assinatura SAML do cabeçalho**. Aí, pode transferir o certificado para carregamento para a aplicação.  <p>‎If the application has more than one certificate, you can find all certificates in the federation metadata XML file. |
| **Identificador/ "emitente"**<p>Identificador do IdP do ponto de vista da aplicação (às vezes chamado de "ID emitente").<p>‎In the SAML token, the value appears as the Issuer element.| O identificador para AD FS é geralmente o identificador de serviço da federação em Gestão FS AD em **serviço > Propriedades de Serviço da Federação de Edição**. Por exemplo: `http://fs.contoso.com/adfs/services/trust`| Substitua {tenant-id} por identificação do seu inquilino.<p>https: \/ /sts.windows.net/{tenant-id}/ |
| **Metadados da federação IdP**<p>Localização dos metadados da federação publicamente disponíveis do IdP. (Algumas aplicações utilizam os metadados de federação como alternativa à configuração individual, por parte do administrador, de URLs, do identificador e do certificado de assinatura de tokens.)| Encontre o URL de metadados da federação AD FS em Gestão AD FS em **Serviço > Pontos finais > Tipo de Metadados > Tipo: Metadados da Federação**. Por exemplo: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| O valor correspondente para Azure AD segue o padrão [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Substitua {TenantDomainName} pelo nome do seu inquilino no formato "contoso.onmicrosoft.com".   <p>Para obter mais informações, veja [Federation metadata](../azuread-dev/azure-ad-federation-metadata.md) (Metadados da federação). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Represente as políticas de segurança da AD FS no Azure AD

Ao mover a autenticação da sua aplicação para Azure AD, crie mapeamentos das políticas de segurança existentes para as suas variantes equivalentes ou alternativas disponíveis no Azure AD. Garantir que estes mapeamentos podem ser feitos ao mesmo tempo que cumprem os padrões de segurança exigidos pelos proprietários das suas aplicações facilita significativamente o resto da migração da aplicação.

Para cada exemplo de regra, mostramos como é a regra em AD FS, o código equivalente de regra de regra da AD FS, e como este mapeia para Azure AD.

### <a name="map-authorization-rules"></a>Regras de autorização do mapa

Seguem-se exemplos de vários tipos de regras de autorização em AD FS, e como as mapeia para Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Exemplo 1: Permitir o acesso a todos os utilizadores

Permitir o acesso a todos os utilizadores em AD FS:

  ![A screenshot mostra o Conjunto Single Sign-On com caixa de diálogo SAML.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Este mapa para Azure AD de uma das seguintes formas:

1. Definir **a atribuição do utilizador necessária** ao **Nº**.

    ![editar a política de controlo de acesso para apps SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Definir **a atribuição do Utilizador necessária** ao **Sim** requer que os utilizadores sejam designados para a aplicação para terem acesso. Quando definido para **Nº,** todos os utilizadores têm acesso. Este switch não controla o que os utilizadores vêem na experiência **My Apps.**

1. No **separador Utilizadores e grupos,** atribua a sua aplicação ao grupo automático **Todos os Utilizadores.** Deve [ativar grupos dinâmicos](../enterprise-users/groups-create-rule.md) no seu inquilino AZure AD para que o grupo padrão **todos os utilizadores** estejam disponíveis.

    ![My SaaS Apps in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Exemplo 2: Permitir um grupo explicitamente

Autorização explícita do grupo em AD FS:

  ![A screenshot mostra a caixa de diálogo de regra de edição para a regra de reivindicação de administradores de domínio de permitir.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Para mapear esta regra para Azure AD:

1. No [portal Azure](https://portal.azure.com/), [crie um grupo de utilizadores](../fundamentals/active-directory-groups-create-azure-portal.md) que corresponda ao grupo de utilizadores da AD FS.
1. Atribuir permissões de aplicações ao grupo:

    ![Adicionar Atribuição ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Exemplo 3: Autorizar um utilizador específico

Autorização explícita do utilizador em AD FS:

  ![A screenshot mostra a caixa de diálogo de regra de edição para permitir uma regra de reclamação específica do utilizador com um tipo de reclamação de entrada de S I D primário.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Para mapear esta regra para Azure AD:

* No [portal Azure](https://portal.azure.com/), adicione um utilizador à aplicação através do separador Add Assignment da aplicação, como mostrado abaixo:

    ![As minhas aplicações SaaS em Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Mapear regras de autenticação de vários fatores

Uma implantação no local de [Autenticação Multi-Factor (MFA)](../authentication/concept-mfa-howitworks.md) e AD FS ainda funciona após a migração porque você é federado com FS AD. No entanto, considere migrar para as capacidades de MFA incorporadas da Azure que estão ligadas aos fluxos de trabalho de acesso condicional da Azure AD.

Seguem-se exemplos de tipos de regras de MFA em AD FS, e como pode mapeá-las para Azure AD com base em diferentes condições.

Definições de regras MFA em AD FS:

  ![A screenshot mostra Condições para Azure A D no portal Azure.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exemplo 1: Impor MFA com base em utilizadores/grupos

O seletor de utilizadores/grupos é uma regra que permite impor mFA numa base por grupo (Grupo SID) ou por utilizador (SID Primário). Para além das atribuições de utilizadores/grupos, todas as caixas de verificação adicionais na configuração UI de configuração AD FS funcionam como regras adicionais que são avaliadas após a aplicação da regra dos utilizadores/grupos.

Especificar as regras de MFA para um utilizador ou um grupo em Azure AD:

1. Criar uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Selecione **Atribuições**. Adicione o(s) ou grupo(s) para os quais pretende impor MFA.
1. Configure as opções **de controlo do acesso** como mostrado abaixo:

    ‎![A screenshot mostra o painel Grant onde pode conceder acesso.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exemplo 2: Impor MFA para dispositivos não registados

Especificar as regras de MFA para dispositivos não registados em Azure AD:

1. Criar uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Definir as **Atribuições** a **Todos os utilizadores**.
1. Configure as opções **de controlo do acesso** como mostrado abaixo:

    ![A screenshot mostra o painel Grant onde pode conceder acesso e especificar outras restrições.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Quando define a opção **de controlos para vários controlos** para **exigir um dos controlos selecionados**, significa que se alguma das condições especificadas pela caixa de verificação for satisfeita pelo utilizador, o utilizador tem acesso à sua aplicação.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exemplo 3: Impor MFA com base na localização

Especificar as regras de MFA com base na localização de um utilizador em Azure AD:

1. Criar uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Definir as **Atribuições** a **Todos os utilizadores**.
1. [Configurar localizações nomeadas em Azure AD](../reports-monitoring/quickstart-configure-named-locations.md). Caso contrário, a federação de dentro da sua rede corporativa é de confiança.
1. Configure as **regras de Condições** para especificar os locais para os quais pretende aplicar o MFA.

    ![A screenshot mostra o painel de localizações para as regras de condições.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Configure as opções **de controlo do acesso** como mostrado abaixo:

    ![Políticas de controlo de acesso ao mapa](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Mapa Emit atributos como regra de Reclamações

Emitia atributos como regra de Reclamações em AD FS:

  ![A screenshot mostra a caixa de diálogo de regra de edição para atributos Emit como Sinistros.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Para mapear a regra para Azure AD:

1. No [portal Azure,](https://portal.azure.com/)selecione **Aplicações empresariais** e, em seguida, **único para** visualizar a configuração de sign-on baseada em SAML:

    ![A screenshot mostra a página de inscrição única para a sua Aplicação Enterprise.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Selecione **Editar** (realçado) para modificar os atributos:

    ![Esta é a página para editar atributos e reclamações do utilizador](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Mapear políticas de controlo de acesso incorporadas

Políticas de controlo de acessos incorporadas em AD FS 2016:

  ![Azure AD construído no controlo de acessos](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Para implementar políticas incorporadas no Azure AD, utilize uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) e configuure os controlos de acesso, ou use o designer de políticas personalizado em AD FS 2016 para configurar políticas de controlo de acesso. O Editor de Regras tem uma lista exaustiva de licenças e opções que podem ajudá-lo a fazer todo o tipo de permutações.

  ![Políticas de controlo de acesso AZure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

Nesta tabela, listamos algumas opções úteis de licença e exceto opções e como eles mapeiam para Azure AD.

| Opção | Como configurar a opção de licença em Azure AD?| Como configurar Exceto opção em Azure AD? |
| - | - | - |
| A partir de rede específica| Mapas para [localização nomeada](../reports-monitoring/quickstart-configure-named-locations.md) em Azure AD| Utilize a opção **Excluir** para [locais fidedignos](../conditional-access/location-condition.md) |
| De grupos específicos| [Definir uma atribuição de utilizador/grupos](assign-user-or-group-access-portal.md)| Utilize a opção **Excluir** em Utilizadores e Grupos |
| De dispositivos com nível de confiança específico| Desa esta definição do controlo **do Estado** do Dispositivo em atribuições -condições >| Utilize a opção **Excluir** sob condição do Estado do Dispositivo e inclua **todos os dispositivos** |
| Com Reclamações Específicas no Pedido| Esta definição não pode ser migrada| Esta definição não pode ser migrada |

Aqui está um exemplo de como configurar a opção Excluir para localizações fidedignas no portal Azure:

  ![Screenshot das políticas de controlo de acesso de mapeamento](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Utilizadores de transição de AD FS para Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sync AD FS grupos em Azure AD

Ao mapear as regras de autorização, as aplicações que autenticam com AD FS podem utilizar grupos de Diretório Ativo para permissões. Neste caso, utilize [o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) para sincronizar estes grupos com a Azure AD antes de migrar as aplicações. Certifique-se de que verifica esses grupos e a adesão antes da migração para que possa conceder acesso aos mesmos utilizadores quando a aplicação é migrada.

Para obter mais informações, consulte [Pré-requisitos para a utilização de atributos do Grupo sincronizados a partir do Ative Directory](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="set-up-user-self-provisioning"></a>Configurar o auto-fornecimento do utilizador

Algumas aplicações SaaS suportam a capacidade de auto-fornecimento de utilizadores quando iniciaram a sua primeira solução para a aplicação. No Azure AD, o provisionamento de aplicações refere-se à criação automática de identidades e funções dos utilizadores na nuvem[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)aplicações a que os utilizadores precisam de aceder. Os utilizadores que estão migrados já têm uma conta na aplicação SaaS. Todos os novos utilizadores adicionados após a migração precisam de ser a provisionados. Teste [o provisionamento da aplicação SaaS](../app-provisioning/user-provisioning.md) uma vez que a aplicação é migrada.

### <a name="sync-external-users-in-azure-ad"></a>Sync utilizadores externos em Azure AD

Os seus utilizadores externos existentes podem ser configurados destas duas formas em AD FS:

* **Utilizadores externos com uma conta local dentro** da sua organização — Continua a utilizar estas contas da mesma forma que as suas contas internas de utilizador funcionam. Estas contas externas de utilizadores têm um nome de princípio dentro da sua organização, embora o e-mail da conta possa apontar externamente. À medida que progride com a sua migração, pode aproveitar os benefícios que o [Azure AD B2B](../external-identities/what-is-b2b.md) oferece ao migrar estes utilizadores para usar a sua própria identidade corporativa quando tal identidade estiver disponível. Isto simplifica o processo de inscrição para esses utilizadores, uma vez que são muitas vezes assinados com a sua própria logon corporativa. A administração da sua organização também é mais fácil, por não ter de gerir contas para utilizadores externos.
* **Identidades externas federadas**— Se está atualmente a federar com uma organização externa, tem algumas abordagens a tomar:
  * [Adicione utilizadores de colaboração Azure Ative Directory B2B no portal Azure](../external-identities/add-users-administrator.md). Você pode enviar proativamente convites de colaboração B2B do portal administrativo AZure AD para a organização parceira para que membros individuais continuem a usar as aplicações e ativos a que estão habituados.
  * [Crie um fluxo de trabalho de inscrição B2B autosserviço](../external-identities/self-service-portal.md) que gere um pedido para utilizadores individuais na sua organização parceira usando o convite B2B API.

Não importa como os utilizadores externos existentes estejam configurados, eles provavelmente têm permissões que estão associadas à sua conta, seja em membros do grupo ou permissões específicas. Avalie se estas permissões precisam de ser migradas ou limpas. As contas dentro da sua organização que representam um utilizador externo precisam de ser desativadas uma vez que o utilizador tenha sido migrado para uma identidade externa. O processo de migração deve ser discutido com os seus parceiros de negócio, pois pode haver uma interrupção na sua capacidade de ligação aos seus recursos.

## <a name="migrate-and-test-your-apps"></a>Migrar e testar as suas apps

Acompanhe o processo de migração detalhado neste artigo. Em seguida, vá ao [portal Azure](https://aad.portal.azure.com/) para testar se a migração foi um sucesso.

Siga estas instruções:

1. Selecione **Aplicações empresariais**  >  **Todas as aplicações** e encontre a sua aplicação na lista.
1. **Selecione Gerir**  >  **Utilizadores e grupos** para atribuir pelo menos um utilizador ou grupo à aplicação.
1. **Selecione Gerir**  >  **o Acesso Condicional**. Reveja a sua lista de políticas e certifique-se de que não está a bloquear o acesso à aplicação com uma [política de acesso condicional.](../conditional-access/overview.md)

Dependendo da configuração da sua aplicação, verifique se o SSO funciona corretamente.

| Tipo de autenticação| Testar |
| :- | :- |
| OAuth / OpenID Connect| Selecione **as aplicações da Enterprise > Permissões** e certifique-se de que consentiu na aplicação nas definições do utilizador para a sua aplicação.|
| SAML-based SSO (SSO baseado no SAML) | Utilize o botão [de definições DE TESTE SAML](debug-saml-sso-issues.md) encontrado sob **o sign-on único**. |
| SSO Password-Based |  Descarregue e instale o [sinal seguro myApps](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [na extensão.](../user-help/my-apps-portal-end-user-access.md) Esta extensão ajuda-o a iniciar qualquer uma das aplicações em nuvem da sua organização que o exijam a utilizar um processo SSO. |
| Proxy da aplicação | Certifique-se de que o seu conector está em funcionamento e atribuído à sua aplicação. Visite o [guia de resolução de problemas da Aplicação Proxy](application-proxy-troubleshoot.md) para obter mais assistência. |

> [!NOTE]
> Os cookies do antigo ambiente AD FS persistem nas máquinas do utilizador. Estes cookies podem causar problemas com a migração, uma vez que os utilizadores poderiam ser direcionados para o antigo ambiente de login da AD FS em comparação com o novo login Azure AD. Poderá necessitar de limpar os cookies do navegador do utilizador manualmente ou utilizar um script. Também pode utilizar o Gestor de Configuração do Centro de Sistema ou uma plataforma semelhante.

### <a name="troubleshoot"></a>Resolução de problemas

Se houver erros no teste das aplicações migradas, a resolução de problemas pode ser o primeiro passo antes de voltar às partes de apoio da AD FS. Ver [como depurar o único sign-on baseado em SAML para aplicações no Azure Ative Directory](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Migração de retrocesso

Se a migração falhar, recomendamos que deixe as partes dependentes existentes nos servidores AD FS e remova o acesso às Partes Dependentes. Isto permite uma rápida queda, se necessário durante a implantação.

### <a name="employee-communication"></a>Comunicação dos colaboradores

Embora a janela de paragem planeada em si possa ser mínima, você ainda deve planejar comunicar estes prazos proativamente aos empregados enquanto muda de AD FS para Azure AD. Certifique-se de que a sua experiência de aplicação tem um botão de feedback ou aponta para o seu helpdesk para problemas.

Uma vez concluída a implementação, pode informar os utilizadores da implementação bem sucedida e lembrá-los de quaisquer passos que precisem de tomar.

* Instrua os utilizadores a utilizarem as [Minhas Apps](https://myapps.microsoft.com) para acederem a todas as aplicações migradas.
* Lembre aos utilizadores que podem precisar de atualizar as suas definições de MFA.
* Se Self-Service Redefinição de passwords for implementada, os utilizadores poderão ter de atualizar ou verificar os seus métodos de autenticação. Consulte os modelos de comunicação do utilizador final [MFA](https://aka.ms/mfatemplates) e [SSPR.](https://aka.ms/ssprtemplates)

### <a name="external-user-communication"></a>Comunicação externa do utilizador

Este grupo de utilizadores é geralmente o mais impactado criticamente em caso de problemas. Isto é especialmente verdade se a sua postura de segurança ditar um conjunto diferente de regras de Acesso Condicional ou perfis de risco para parceiros externos. Certifique-se de que os parceiros externos estão cientes do calendário de migração em nuvem e têm um prazo durante o qual são encorajados a participar numa implementação piloto que testa todos os fluxos exclusivos da colaboração externa. Finalmente, certifique-se de que eles têm uma maneira de aceder ao seu helpdesk em caso de problemas.

## <a name="next-steps"></a>Passos seguintes

* Leia  [a autenticação da aplicação migratória para a Azure AD](https://aka.ms/migrateapps/whitepaper).
* Configurar [acesso condicional](../conditional-access/overview.md) e [MFA](../authentication/concept-mfa-howitworks.md).
* Experimente uma amostra de código em termos de passo:[AD FS para Azure AD aplicação livro de migração para desenvolvedores](https://aka.ms/adfsplaybook).
