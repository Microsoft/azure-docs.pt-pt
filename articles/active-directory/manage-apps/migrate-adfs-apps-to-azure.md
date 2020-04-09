---
title: Autenticação de aplicação em movimento de AD FS para Diretório Ativo Azure
description: Este artigo destina-se a ajudar as organizações a entender como mover aplicações para a AD Azure, com foco em aplicações Federadas SaaS.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892121"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Autenticação de aplicações móveis dos Serviços da Federação de Diretórios Ativos para o Diretório Ativo do Azure

O [Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) oferece uma plataforma de identidade universal que fornece aos seus povos, parceiros e clientes uma única identidade para aceder a aplicações e colaborar a partir de qualquer plataforma e dispositivo. A Azure AD tem um [conjunto completo de capacidades de gestão de identidade.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Normalizar a autenticação e autorização da sua aplicação (app) para a Azure AD permite os benefícios que estas capacidades proporcionam. 

> [!NOTE]
> Este artigo centra-se na mudança da autenticação de aplicações de diretório ativo e serviços da Federação de DirectórioAtivo para a AD Azure. Consulte a autenticação da aplicação de migração em papel branco [para a AD Azure](https://aka.ms/migrateapps/whitepaper) para uma visão geral do planeamento deste movimento. O Livro Branco discute como planear a migração, os testes e os insights.

## <a name="introduction"></a>Introdução

Se tiver um diretório no local que contenha contas de utilizador, provavelmente tem muitas aplicações para as quais os utilizadores autenticam. Cada uma destas aplicações está configurada para que os utilizadores acedam usando as suas identidades. 


Os utilizadores também podem autenticar diretamente com o seu Diretório Ativo no local. Ative Directory Federation Services (AD FS) é um serviço de identidade baseado no local. O AD FS alarga a capacidade de utilizar uma única funcionalidade de inscrição (SSO) entre parceiros de negócios fidedignos sem exigir que os utilizadores assinem separadamente a cada aplicação. Isto é conhecido como Federação.

Muitas organizações têm software como serviço (SaaS) ou aplicações personalizadas de Line-of-Business (LOB) federadas diretamente para AD FS, ao lado de aplicações baseadas em AD 365 e Azure AD. 

![Aplicações ligadas diretamente no local](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Para aumentar a segurança das aplicações, o seu objetivo é ter um único conjunto de controlos de acesso e políticas em todos os seus ambientes no local e na nuvem**. 

![Aplicações ligadas através da AD Azure](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Tipos de aplicativos para migrar

Migrar toda a autenticação da sua aplicação para a AD Azure é o ideal, uma vez que lhe dá um único plano de controlo para gestão de identidade e acesso.

As suas aplicações podem utilizar protocolos modernos ou antigos para autenticação. Considere as primeiras aplicações migratórias que utilizam protocolos de autenticação modernos (como SAML e Open ID Connect). Estas aplicações podem ser reconfiguradas para autenticar com a AD Azure através de um conector incorporado na nossa App Gallery, ou através do registo da aplicação em Azure AD. As aplicações que utilizam protocolos mais antigos podem ser integradas utilizando o Proxy da [Aplicação.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) 

Para mais informações, consulte que tipos de aplicações posso integrar com a [Azure AD?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)

Pode utilizar o relatório de atividade de [aplicação AD FS para migrar aplicações para a AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) se tiver [a Azure AD Connect Health ativada](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs). 

### <a name="the-migration-process"></a>O processo de migração

Durante o processo de mudança da autenticação da sua aplicação para a AD Azure, teste adequadamente as suas aplicações e configuração. Recomendamos que continue a utilizar os ambientes de ensaio existentes para testes migratórios que se deslocam para o ambiente de produção. Se um ambiente de teste não estiver disponível atualmente, pode configurar um utilizando o [Azure App Service](https://azure.microsoft.com/services/app-service/) ou [as Máquinas Virtuais Azure,](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)dependendo da arquitetura da aplicação.

Você pode optar por configurar um teste separado do inquilino Azure AD para usar à medida que desenvolve as configurações da sua aplicação. 

O seu processo de migração pode parecer assim:

**Fase 1 – Estado atual: App de produção autenticada com AD FS**

![Fase 1 da migração ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Fase 2 – OPCIONAL: Teste de exemplo de app que aponta para testar inquilino azure**

Atualize a configuração para indicar a sua instância de teste da app a um inquilino azure ad teste, e epreça as alterações necessárias. A aplicação pode ser testada com os utilizadores no teste Azure AD inquilino. Durante o processo de desenvolvimento, pode utilizar ferramentas como [o Fiddler](https://www.telerik.com/fiddler) para comparar e verificar pedidos e respostas.

Se a criação de um inquilino de teste separado não for viável, salte esta fase e levante uma instância de teste de uma app e aponte-a para o seu inquilino Azure AD de produção, conforme descrito na Fase 3 abaixo.

![Fase 2 da migração ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fase 3 – App de teste que aponta para produção do inquilino Azure**

Atualize a configuração para indicar a sua instância de teste da app para a sua instância de produção do Azure. Agora pode testar com os utilizadores na sua instância de produção. Se necessário, reveja a secção deste artigo sobre utilizadores em transição.

![Fase 3 da migração ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fase 4 – App de produção que aponta para o inquilino da Produção AD**

Atualize a configuração da sua aplicação de produção para apontar para a sua produção, inquilino Azure.

![Fase 1 da migração ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 As aplicações que autenticam com AD FS podem utilizar grupos de Diretório ativo para permissões. Utilize o [sincronizacro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) para sincronizar dados de identidade entre o ambiente no local e o Azure AD antes de iniciar a migração. Verifique esses grupos e membros antes da migração para que possa conceder acesso aos mesmos utilizadores quando a aplicação for migrada.

### <a name="line-of-business-lob-apps"></a>Aplicativos de linha de negócios (LOB)

As aplicações LOB são desenvolvidas internamente pela sua organização ou disponíveis como um produto embalado padrão que está instalado no seu centro de dados. Exemplos incluem aplicações construídas na Windows Identity Foundation e aplicações SharePoint (não SharePoint Online). 

As aplicações LOB que utilizam o OAuth 2.0, OpenID Connect ou WS-Federation podem ser integradas com a Azure AD como registos de [aplicações.](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) Integre aplicações personalizadas que utilizem o SAML 2.0 ou wS-Federation como [aplicações não-galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) na página de aplicações da empresa no [portal Azure](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>SAML-based single sign-On

As aplicações que utilizam o SAML 2.0 para autenticação podem ser configuradas para um [único sign-on baseado em SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SSO baseado em SAML). Com [o SSO baseado em SAML,](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)pode mapear os utilizadores para funções específicas de aplicação com base em regras que define nas suas reivindicações SAML. 

Para configurar uma aplicação SaaS para um único sign-on baseado em SAML, consulte configurar um [único sign-on baseado em SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications). 

![Screenshots de utilizador SSO SAML ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Muitas aplicações SaaS têm um [tutorial específico para aplicações](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) que o interpela através da configuração para um único sign-on baseado em SAML.

![tutorial app](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Algumas aplicações podem ser migradas facilmente. As aplicações que tenham requisitos mais complexos, como afirmações personalizadas, podem exigir configuração adicional no Azure AD e/ou no Azure AD Connect. Para obter informações sobre mapeamento de reclamações suportadas, consulte [o mapeamento de Sinistros no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

Tenha em mente as seguintes limitações ao mapear atributos:

* Nem todos os atributos que podem ser emitidos em AD FS aparecerão em Azure AD como atributos para emitir em tokens SAML, mesmo que esses atributos estejam sincronizados. Quando editar o atributo, a lista de dropdown value mostrar-lhe-á os diferentes atributos disponíveis em Azure AD. Verifique a configuração de [sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) para garantir que um atributo necessário- por exemplo, o samAccountName - está a ser sincronizado com o Azure AD. Pode utilizar os atributos de extensão para emitir qualquer reclamação que não faça parte do esquema de utilizador padrão em Azure AD.

* Nos cenários mais comuns, apenas a afirmação NameID e outras afirmações de identificador de utilizador comuns são necessárias para as aplicações. Para determinar se são necessárias reclamações adicionais, examine as alegações que está a emitir da AD FS.

* Nem todas as reivindicações podem ser questões, uma vez que algumas reivindicações estão protegidas em Azure AD. 

* A capacidade de utilizar tokens SAML encriptados está agora em pré-visualização. Ver [Como: personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

 

### <a name="software-as-a-service-saas-apps"></a>Aplicativos de software como serviço (SaaS)

Se o utilizador iniciar sessão em aplicações SaaS como Salesforce, ServiceNow ou Workday, e estiver integrado com AD FS, está a utilizar o sign-on federado para aplicações SaaS. 

A maioria das aplicações SaaS já podem ser configuradas em Azure AD. A Microsoft tem muitas ligações pré-configuradas para aplicações SaaS na galeria de [aplicações Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), o que facilitará a sua transição. As aplicações SAML 2.0 podem ser integradas com a Azure AD através da galeria de aplicações Azure AD ou como [aplicações não-galeria.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

Do mesmo modo, as aplicações que utilizam OAuth 2.0 ou OpenID Connect podem ser integradas no Azure AD como [registos de aplicações](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). As aplicações que utilizam protocolos legados podem utilizar o [Proxy de Aplicação AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para autenticar com a AD Azure.

Para quaisquer problemas com o embarque das suas aplicações SaaS, pode contactar o pseudónimo de suporte à Integração de [Aplicações SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**Certificados de assinatura SAML para SSO**: Os certificados de assinatura são uma parte importante de qualquer implantação sso. A Azure AD cria os certificados de assinatura para estabelecer sSO federado baseado em SAML nas suas aplicações SaaS. Uma vez adicionado si galeria ou aplicações não-galeria, irá configurar a aplicação adicional usando a opção SSO federada. Ver [Gerir certificados para inscrição única federada no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplicativos e configurações que podem ser movidos hoje

As aplicações que pode mover-se facilmente hoje incluem aplicações SAML 2.0 que utilizam o conjunto padrão de elementos de configuração e reivindicações:

* Nome Principal de Utilizador

* Endereço de e-mail

* Nome próprio

* Apelido

* Atributo alternativo como **NameID** de SAML, incluindo o atributo de correio do Azure AD, o prefixo de correio, o ID de funcionário, os atributos de extensão 1-15 ou o atributo **SamAccountName**. Para obter mais informações, veja [Editing the NameIdentifier claim](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) (Editar a afirmação NameIdentifier).

* Afirmações personalizadas.

Os seguintes requerem passos de configuração adicionais para migrar para AD Azure:

* Autorização personalizada ou regras de autenticação multi-factor (MFA) em AD FS. Configurá-los utilizando a função de [Acesso Condicional Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* Aplicativos com vários pontos finais de URL de resposta. Configurá-los em Azure AD utilizando o PowerShell ou na interface do portal Azure.

* Aplicações WS-Federation, como as aplicações do SharePoint, que precisam de tokens SAML da versão 1.1. Pode configurá-los manualmente utilizando o PowerShell. Também pode adicionar um modelo genérico pré-integrado para aplicações SharePoint e SAML 1.1 da galeria. Apoiamos o protocolo SAML 2.0.

* A emissão de reivindicações complexas transforma regras. Para obter informações sobre mapeamento de reclamações suportadas, consulte:
   *  [Mapeamento de afirmações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Personalizando as reclamações emitidas no símbolo SAML para aplicações empresariais no Diretório Ativo da Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplicações e configurações não suportadas atualmente no Azure AD

As aplicações que requerem as seguintes capacidades não podem ser migradas hoje.

**Capacidades protocolares**

* Apoio ao padrão WS-Trust Atas

* Resolução de artefactos SAML

* Verificação de assinatura dos pedidos saml assinados  
Note que os pedidos assinados são aceites, mas a assinatura não é verificada.  
Dado que a AD Azure apenas devolverá o símbolo aos pontos finais pré-configurados no pedido, é provável que a verificação de assinaturas não seja necessária na maioria dos casos.

**Reclamações em capacidades simbólicas**

* Reclamações de lojas de atributos que não o diretório DaAzure, a menos que esses dados sejam sincronizados com a Azure AD. Para mais informações, consulte a visão geral da API de sincronização da [AD Azure](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Emissão de atributos de vários valores do diretório. Por exemplo, não podemos emitir uma reclamação multivalorizada para endereços de procuração neste momento.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Configurações de aplicativos de mapa de AD FS para Azure AD

A migração começa com a avaliação da forma como a aplicação está configurada no local e mapear essa configuração para o Azure AD. A AD FS e Azure AD funcionam da mesma forma, pelo que os conceitos de configuração de URLs de confiança, de entrada e de inscrição, e identificadores aplicam-se em ambos os casos. Documente as definições de configuração AD FS das suas aplicações para que possa configurá-las facilmente em AD Azure.

### <a name="map-app-configuration-settings"></a>Configurações de configuração de aplicativos de mapa

A tabela seguinte descreve alguns dos mapeamentos mais comuns de configurações entre um AD FS Relying Party Trust para AD Enterprise Application:

* AD FS – Encontre a definição no AD FS Relying Party Trust para a app. Clique na parte de fiação e selecione Propriedades. 

* Azure AD– A definição está configurada dentro do [portal Azure](https://portal.azure.com/) nas propriedades de entrada única de cada aplicação.

| Definição de configuração| AD FS| Como configurar em Azure AD| Ficha SAML |
| - | - | - | - |
| **URL de início de sessão da aplicação** <p>O URL para o utilizador iniciar sessão na app num fluxo SAML iniciado por Um Fornecedor de Serviços (SP).| N/D| Configuração Básica SAML aberta a partir de sinal baseado em SAML| N/D |
| **URL de resposta da aplicação** <p>O URL da app na perspetiva do fornecedor de identidade (IDP's). O IdP envia o utilizador e token aqui depois de o utilizador ter assinado o IDP.  Isto também é conhecido como ponto final de **afirmação saml do consumidor**.| Selecione o separador **Pontos Finais**| Configuração Básica SAML aberta a partir de sinal baseado em SAML| Elemento de destino no símbolo SAML. Valor do exemplo:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **URL de fim de sessão da aplicação** <p>Este é o URL para o qual os pedidos de "limpeza de inscrição" são enviados quando um utilizador assina a partir de uma aplicação. O IdP envia o pedido para assinar o utilizador de todas as outras aplicações também.| Selecione o separador **Pontos Finais**| Configuração Básica SAML aberta a partir de sinal baseado em SAML| N/D |
| **Identificador de aplicação** <p>Este é o identificador de aplicações do ponto de vista do IdP. O valor de URL de inscrição é frequentemente utilizado para o identificador (mas nem sempre).  Às vezes, a aplicação chama a isto "ID da entidade".| Selecione o separador **Identificadores**|Configuração Básica SAML aberta a partir de sinal baseado em SAML| Mapas para o elemento **do público** no símbolo SAML. |
| **Metadados de federação da aplicação** <p>Esta é a localização dos metadados da federação da aplicação. O IdP utiliza-os para atualizar automaticamente definições de configuração específicas, como pontos finais ou certificados de encriptação.| Selecione o separador **Monitoramento**| N/D. A Azure AD não suporta consumir diretamente metadados da federação de aplicações. Pode importar manualmente os metadados da federação.| N/D |
| **Identificação do utilizador/ ID de nome** <p>Atributo que é utilizado para indicar exclusivamente a identidade a partir do utilizador do Azure AD ou do AD FS para a sua aplicação.  Este atributo é tipicamente a UPN ou o endereço de e-mail do utilizador.| Reivindicar regras. Na maioria dos casos, a regra da reclamação emite uma reclamação com um tipo que termina com o NameIdentifier.| Pode encontrar o identificador sob o cabeçalho **Atributos e Reclamações**do Utilizador . Por padrão, a UPN é usada| Mapas para o elemento **NameID** no símbolo SAML. |
| **Outras reclamações** <p>Exemplos de outras informações de reclamação que são comumente enviadas do IDP para a app incluem Primeiro Nome, Apelido, Endereço de E-mail e membro do grupo.| No AD FS, está disponível como outras regras de afirmação na entidade confiadora.| Pode encontrar o identificador sob os **atributos**do utilizador & reclamações . Selecione **Vista** e edite todos os outros atributos de utilizador| N/D |


### <a name="map-identity-provider-idp-settings"></a>Definições de Fornecedor de Identidade de Mapas (IdP)

Configure as suas aplicações para apontar para Azure AD versus AD FS para SSO. Aqui, estamos focados em aplicações SaaS que usam o protocolo SAML. No entanto, este conceito estende-se também a aplicações LOB personalizadas.

> [!NOTE]
> Os valores de configuração para Azure AD seguem o padrão em que o seu ID do Inquilino Azure substitui {tenant-id} e o ID de aplicação substitui {application-id}. Encontra esta informação no [portal Azure](https://portal.azure.com/) no âmbito do Azure Ative Directory > Properties: 

* Selecione Id do Diretório para ver o seu ID de Inquilino. 

* Selecione ID de aplicação para ver o ID da sua aplicação.

 Num nível elevado, mapeie os seguintes elementos de configuração de aplicações SaaS para AD Azure. 

 

| Elemento| Valor de Configuração |
| - | - |
| Emitente de fornecedor de identidade| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| URL de login do fornecedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL de logout do fornecedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Localização dos metadados da Federação| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Map SSO definições para apps SaaS

As aplicações SaaS precisam de saber para onde enviar pedidos de autenticação e como validar os tokens recebidos. A tabela seguinte descreve os elementos para configurar as definições de SSO na aplicação, e os seus valores ou localizações dentro da AD FS e Azure AD

| Definição de configuração| AD FS| Como configurar em Azure AD |
| - | - | - |
| **URL de sinalização IDP** <p>Url de início de sessão do IdP do ponto de vista da aplicação (onde o utilizador é redirecionado para login).| O URL de inscrição AD FS é o nome de serviço da federação AD FS seguido por "/adfs/ls/". <p>Por exemplo:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Substitua {tenant-id} com a sua identificação do inquilino. <p> Para aplicações que utilizem o protocolo SAML-P:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Para aplicações que utilizem o protocolo WS-Federação:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL de inscrição idp**<p>Url de inscrição do IDP do ponto de vista da aplicação (onde o utilizador é redirecionado quando opta por assinar fora da app).| O URL de inscrição é o mesmo que o URL de inscrição, ou o mesmo URL com "wa=wsignout1.0" anexado. Por exemplo:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Substitua {tenant-id} com a sua identificação do inquilino.<p>Para aplicações que utilizem o protocolo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Para aplicações que utilizem o protocolo WS-Federação:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificado de assinatura token**<p>O IDP usa a chave privada do certificado para assinar fichas emitidas. Verifica se o token provém do mesmo IdP para o qual a aplicação está configurada para confiar.| O certificado de assinatura de tokens do AD FS está disponível na Gestão do AD FS, em **Certificados**.| Encontre-o no portal Azure nas **propriedades de inscrição única** da aplicação sob o cabeçalho Certificado de Assinatura **SAML**. Aí, pode transferir o certificado para carregamento para a aplicação.  <p>Se a aplicação tiver mais de um certificado, pode encontrar todos os certificados no ficheiro XML dos metadados da federação. |
| **Identificador/ "emitente"**<p>Identificador do IdP do ponto de vista da app (por vezes chamado de "ID emitente").<p>No token SAML, o valor aparece como elemento emitente.| O identificador para AD FS é geralmente o identificador de serviço da federação na Gestão AD FS ao abrigo do **Serviço > Editar Propriedades de Serviço**da Federação . Por exemplo:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Substitua {tenant-id} com a sua identificação do inquilino.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **Metadados da federação idp**<p>Localização dos metadados da federação disponível publicamente. (Algumas aplicações utilizam os metadados de federação como alternativa à configuração individual, por parte do administrador, de URLs, do identificador e do certificado de assinatura de tokens.)| Encontre o URL de metadados da federação AD FS na Gestão AD FS em **Serviço > Pontos Finais > Metadados > Tipo: Metadados da Federação**. Por exemplo:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| O valor correspondente para a AD Azure segue o padrão [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml). Substitua {TenantDomainName} com o nome do seu inquilino no formato "contoso.onmicrosoft.com".   <p>Para obter mais informações, veja [Federation metadata](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata) (Metadados da federação). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Representar políticas de segurança da AD FS em Azure AD

Ao mover a autenticação da sua aplicação para o Azure AD, crie mapeamentos de políticas de segurança existentes para as suas variantes equivalentes ou alternativas disponíveis no Azure AD. Garantir que estes mapeamentos podem ser feitos ao cumprir os padrões de segurança exigidos pelos proprietários das suas aplicações tornará significativamente mais fácil a migração do resto da aplicação.

Para cada tipo de regra e seus exemplos, sugerimos aqui como a regra se parece em AD FS, o código equivalente de linguagem de regra AD FS, e como este mapa em Azure AD.

### <a name="map-authorization-rules"></a>Regras de autorização de mapas

Seguem-se exemplos de tipos de regras de autorização em AD FS, e como pode mapeá-las para AD Azure:

#### <a name="example-1-permit-access-to-all-users"></a>Exemplo 1: Permitir o acesso a todos os utilizadores

Permitir o acesso a todos os utilizadores parece em AD FS: 

![Fase 1 da migração ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Este mapa para AD Azure de uma das seguintes formas:

No [portal Azure:](https://portal.azure.com/)
* Opção 1: Definir a tarefa do utilizador necessária para não ![editar política de controlo de acesso para aplicações SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Note que a definição da atribuição do Utilizador necessária para o Sim requer que os utilizadores sejam atribuídos à aplicação para terem acesso. Quando definido para Não, todos os utilizadores têm acesso. Este switch não controla o que mostra para os utilizadores na experiência My Apps. 

 
* Opção 2: No separador Utilizadores e grupos, atribua a sua aplicação ao grupo automático "Todos os Utilizadores". <p>
Deve [ativar grupos dinâmicos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) no seu inquilino Azure AD para que o grupo 'Todos os Utilizadores' esteja disponível.

   ![Minhas aplicações SaaS em Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Exemplo 2: Permitir explicitamente um grupo

Autorização explícita do grupo em AD FS:


![Regras de Autorização de Emissão ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


É assim que a regra mapeia para AD Azure:

No [portal Azure,](https://portal.azure.com/)primeiro [criará um grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) de utilizadores que corresponde ao grupo de utilizadores da AD FS e, em seguida, atribuirá permissões de aplicações a esse grupo:

![Adicionar Atribuição ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Exemplo 3: Autorizar um utilizador específico

Autorização explícita do utilizador em AD FS:

![Regras de Autorização de Emissão ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

É assim que a regra mapeia para AD Azure:

No [portal Azure,](https://portal.azure.com/)adicione um utilizador à app através do separador Add Assignment da app, como mostrado abaixo:

![Minhas aplicações SaaS em Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Regras de autenticação de vários fatores do mapa 

Uma implementação no local da [Autenticação Multi-Factor (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) e AD FS continuará a funcionar após a migração porque você está federado com AD FS. No entanto, considere migrar para as capacidades de MFA incorporadas do Azure que estão ligadas aos fluxos de trabalho de Acesso Condicional da Azure AD. 

Seguem-se exemplos de tipos de regras de MFA em AD FS, e como pode mapeá-las para AD Azure com base em diferentes condições:

Definições de regras do MFA em AD FS:

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exemplo 1: Impor MFA com base em utilizadores/grupos

O seletor utilizador/grupoé uma regra que lhe permite impor o MFA numa base por Grupo (Sid do Grupo) ou por utilizador (SID Primário). Para além das atribuições de Utilizador/Grupos, todas as caixas de verificação adicionais na função UI de configuração AD FS MFA funcionam como regras adicionais que são avaliadas após a aplicação da regra utilizador/grupos. 


Especificar as regras de MFA para um utilizador ou um grupo em Azure AD:

1. Criar uma nova política de [acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)

2. Selecione **Atribuições**. Adicione o(s) utilizador ou grupo(s) em que pretende impor o MFA.

3. Configure as opções de controlo de **acesso,** conforme mostrado abaixo:  
‎

![Definições de MFA AAD](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exemplo 2: Impor MFA para dispositivos não registados

Especificar as regras de MFA para dispositivos não registados em Azure AD:

1. Criar uma nova política de [acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)

2. Detete as **Atribuições** a **Todos os utilizadores**.

3. Configure as opções de controlo de **acesso,** conforme mostrado abaixo:  
‎

![Definições de MFA AAD](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Quando definir a opção Para vários controlos para exigir um dos controlos selecionados, significa que se qualquer uma das condições especificadas pela caixa de verificação for preenchida pelo utilizador, terão acesso à sua aplicação.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exemplo 3: Impor MFA com base na localização

Especificar as regras de MFA com base na localização de um utilizador em Azure AD:

1. Criar uma nova política de [acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)

1. Detete as **Atribuições** a **Todos os utilizadores**.

1. [Configure localizações nomeadas em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) caso contrário a federação de dentro da sua rede corporativa é de confiança. 

1. Configure as **regras de Condições** para especificar as localizações para as quais deseja fazer cumprir o MFA.

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Configure as opções de controlo de **acesso,** conforme mostrado abaixo:


![Mapeie políticas de controlo de acesso](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Mapa Emit atribui como regra reivindicações

Aqui está um exemplo de como os atributos são mapeados em AD FS:

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


É assim que a regra mapeia para AD Azure:

No [portal Azure,](https://portal.azure.com/)selecione **Aplicações Empresariais,** **Entrada única,** e adicione **Atributos Token SAML** como mostrado abaixo:

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Map built-In access control policies

A AD FS 2016 tem várias políticas de controlo de acesso incorporadas que pode escolher:

![Azure AD construído no controlo de acesso](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Para implementar políticas incorporadas em Azure AD, pode utilizar uma nova política de [acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) e configurar os controlos de acesso, ou pode utilizar o designer de políticas personalizado sinuoso em AD FS 2016 para configurar políticas de controlo de acesso. O Editor de Regras tem uma lista exaustiva de opções de Licença e Exceto que podem ajudá-lo a fazer todo o tipo de permutações.

![Políticas de controlo de acesso da AD Azure](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



Nesta tabela, listamos algumas opções úteis de Autorização e Exceto e como eles mapeiam para Azure AD. 


| | Como configurar a opção licença em Azure AD?| Como configurar a opção Exceto em Azure AD? |
| - | - | - |
| A partir de rede específica| Mapas para [localização nomeada](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) em Azure AD| Utilize a opção **Excluir** para [locais fidedignos](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| De grupos específicos| [Definir uma atribuição de utilizador/grupos](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Utilize a opção **Excluir** em Utilizadores e Grupos |
| De Dispositivos com Nível de Confiança Específico| Defina isto a partir do controlo 'Estado do Dispositivo' em atribuições -> Condições| Utilize a opção **Excluir** sob condição de estado do dispositivo e inclua **todos os dispositivos** |
| Com Reclamações Específicas no Pedido| Este cenário não pode ser migrado| Este cenário não pode ser migrado |


Um exemplo de como configurar a opção Excluir para localizações fidedignas no Portal Azure:

![Screenshot de mapear políticas de controlo de acesso](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Utilizadores de transição de AD FS para Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sync AD FS grupos em Azure AD

Quando mapear regras de autorização, as aplicações que autenticam com AD FS podem usar grupos de Diretório Ativo para permissões. Neste caso, utilize [o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) para sincronizar estes grupos com a AD Azure antes de migrar as aplicações. Certifique-se de que verifica esses grupos e membros antes da migração para que possa conceder acesso aos mesmos utilizadores quando a aplicação for migrada.

Para obter mais informações, consulte [os pré-requisitos para a utilização de atributos do Grupo sincronizados a partir do Diretório Ativo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims).

### <a name="setup-user-self-provisioning"></a>Configuração de auto-provisionamento do utilizador 

Algumas aplicações SaaS suportam a capacidade de auto-fornecer utilizadores quando iniciaram o início a sessão na aplicação. No Azure Ative Directory (Azure AD), o fornecimento de aplicações de termo refere-se à criação automática de identidades e funções de utilizador nas aplicações cloud[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)a que os utilizadores precisam de acesso. Os utilizadores que estão migrados já terão uma conta na aplicação SaaS. Quaisquer novos utilizadores adicionados após a migração terão de ser aprovisionados. Teste o fornecimento de [aplicações SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) uma vez que a aplicação é migrada.

### <a name="sync-external-users-in-azure-ad"></a>Sync utilizadores externos em Azure AD

Os seus utilizadores externos existentes podem ser configurados de duas formas principais dentro do AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Utilizadores externos com uma conta local dentro da sua organização

Continuará a poder utilizar estas contas da mesma forma que as suas contas internas de utilizadores funcionam. Estas contas de utilizador externos têm um nome principal dentro da sua organização, embora o e-mail da conta possa apontar externamente. À medida que avança com a sua migração, pode aproveitar os benefícios que o [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) oferece migrando estes utilizadores para usar a sua própria identidade corporativa quando essa identidade está disponível. Isto simplifica o processo de login para esses utilizadores, uma vez que são frequentemente assinados com o seu próprio logon corporativo. A administração da sua organização também será facilitada, por deixar de ter de gerir contas para utilizadores externos.

#### <a name="federated-external-identities"></a>Identidades externas federadas

Se está atualmente federado com uma organização externa, tem algumas abordagens a tomar:

* Adicione utilizadores de [colaboração Azure Ative Directory B2B no portal Azure.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator) Pode enviar proativamente convites de colaboração B2B do portal administrativo Azure AD para a organização parceira para que os membros individuais continuem a usar as aplicações e os ativos a que estão habituados. 

* [Crie um fluxo de trabalho de inscrição B2B de autosserviço](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) que gere um pedido para utilizadores individuais na sua organização parceira utilizando o convite B2B API.

Não importa como os seus utilizadores externos existentes estejam configurados, eles provavelmente têm permissões que estão associadas à sua conta, seja em membros do grupo ou permissões específicas. Avalie se estas permissões precisam de ser migradas ou limpas. As contas dentro da sua organização que representam um utilizador externo precisam de ser desativadas uma vez que o utilizador tenha sido migrado para uma identidade externa. O processo de migração deve ser discutido com os seus parceiros de negócio, uma vez que pode haver uma interrupção na sua capacidade de ligação aos seus recursos.

## <a name="migrate-and-test-your-apps"></a>Migrar e testar as suas apps

Acompanhe o processo de migração detalhado neste artigo.

Em seguida, vá ao [portal Azure](https://aad.portal.azure.com/) para testar se a migração foi um sucesso. Siga as instruções abaixo:
1. Selecione **Aplicações Empresariais** > **Todas as aplicações** e encontre a sua aplicação na lista.

1. Selecione **Gerir** > **utilizadores e grupos** para atribuir pelo menos um utilizador ou grupo à aplicação.

1. Selecione **Gerir** > **o Acesso Condicional**. Reveja a sua lista de políticas e certifique-se de que não está a bloquear o acesso à aplicação com uma política de [acesso condicional.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

Dependendo da configuração da sua aplicação, verifique se o SSO funciona corretamente. 

| Tipo de autenticação| Testar |
| - | - |
| OAuth / OpenID Connect| Selecione **aplicações da Enterprise > Permissões** e certifique-se de que consentiu na aplicação a ser utilizada na sua organização nas definições do utilizador para a sua aplicação.  
‎ |
| SAML-based SSO (SSO baseado no SAML)| Utilize o botão de [definições SAML](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) de teste encontrado no âmbito do **"Sign-On" único**.  
‎ |
| SSO baseado em palavra-passe| Descarregue e instale o [Sinal](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)Seguro myApps na[extensão](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction). Esta extensão ajuda-o a iniciar qualquer uma das aplicações na nuvem da sua organização que o exijam para usar um processo SSO.  
‎ |
| Proxy da aplicação| Certifique-se de que o conector está em funcionamento e atribuído à sua aplicação. Visite o guia[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)de resolução de [problemas do Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) para obter mais assistência.  
‎ |

> [!NOTE]
> Os cookies do antigo ambiente AD FS continuarão a ser persistentes nas máquinas do utilizador. Estes cookies podem causar problemas com a migração, uma vez que os utilizadores podem ser direcionados para o antigo ambiente de login AD FS contra o novo login Azure AD. Poderá ser necessário limpar manualmente os cookies do navegador de utilizador ou utilizar um script. Também pode utilizar o System Center Configuration Manager ou uma plataforma semelhante.

### <a name="troubleshoot"></a>Resolução de problemas

Se houver algum erro no teste das aplicações migradas, a resolução de problemas pode ser o primeiro passo antes de voltar para as partes dependentes da AD FS existentes. Ver [Como depurar o único sign-on baseado em SAML em aplicações no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues).

### <a name="rollback-migration"></a>Migração de retrocesso

Se a migração falhar, recomendamos que deixe as partes de fidesão existentes nos servidores AD FS e remova o acesso às Partes Fifing. Isto permitirá um recuo rápido, se necessário durante a implantação.

### <a name="end-user-communication"></a>Comunicação do utilizador final

Embora a janela de paragem planeada possa ser mínima, deve ainda planear comunicar estes prazos proativamente aos empregados enquanto faz o corte de AD FS para Azure AD. Certifique-se de que a sua experiência na aplicação tem um botão Feedback ou indicações para o seu helpdesk para problemas.

Uma vez concluída a implementação, pode enviar comunicações informando os utilizadores da implementação bem sucedida e lembrá-los de quaisquer novos passos que eles precisam tomar.

* Instrua os utilizadores a utilizarem o Painel de [Acesso](https://myapps.microsoft.com.com/) para acederem a todas as aplicações migradas. 

* Lembre aos utilizadores que podem precisar de atualizar as definições do MFA. 

* Se o Reset de Palavra-Passe self-service for implementado, os utilizadores poderão precisar de atualizar ou verificar os seus métodos de autenticação. Consulte os modelos de comunicação do utilizador final [mFA](https://aka.ms/mfatemplates) e [SSPR.](https://aka.ms/ssprtemplates)

Comunicação a utilizadores externos: Este grupo de utilizadores é geralmente o mais impactado em caso de problemas. Isto é especialmente verdade se a sua postura de segurança ditar um conjunto diferente de regras de Acesso Condicional ou perfis de risco para parceiros externos. Certifique-se de que os parceiros externos estão cientes do calendário de migração em nuvem e têm um prazo durante o qual são encorajados a participar numa implantação piloto que testa todos os fluxos exclusivos da colaboração externa. Por fim, certifique-se de que têm uma forma de aceder ao seu helpdesk em caso de problemas de rutura.

## <a name="next-steps"></a>Passos Seguintes
Ler [autenticação de candidatura de migração para a AD Azure](https://aka.ms/migrateapps/whitepaper)<p>
Configurar [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) e [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
