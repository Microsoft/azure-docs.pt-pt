---
title: Autenticação de aplicações móveis de AD FS para Azure Ative Directory
description: Este artigo destina-se a ajudar as organizações a entender como mover as aplicações para a Azure AD, com foco nas aplicações federadas do SaaS.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9d2f295394d89432f3c6dd99585cc4363d4ff74
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641369"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Autenticação de aplicações móveis dos Serviços da Federação de Diretórios Ativos para o Diretório Ativo do Azure

[O Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) oferece uma plataforma de identidade universal que fornece às suas pessoas, parceiros e clientes uma única identidade para aceder a aplicações e colaborar a partir de qualquer plataforma e dispositivo. A Azure AD tem um [conjunto completo de capacidades de gestão de identidade.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) A normalização da autenticação e autorização da Azure AD permite os benefícios que estas capacidades proporcionam. 

> [!NOTE]
> Este artigo centra-se na autenticação de aplicações móveis dos serviços de Diretório Ativo e Diretório Ativo para Azure AD. Consulte a [autenticação da aplicação de migração em](https://aka.ms/migrateapps/whitepaper) papel branco para a Azure AD para uma visão geral do planeamento deste movimento. O Livro Branco discute como planear a migração, os testes e os insights.

## <a name="introduction"></a>Introdução

Se tiver um diretório no local que contenha contas de utilizador, é provável que tenha muitas aplicações para as quais os utilizadores autenticam. Cada uma destas aplicações está configurada para que os utilizadores acedam utilizando as suas identidades. 


Os utilizadores também podem autenticar diretamente com o seu Ative Directory no local. Ative Directory Federation Services (AD FS) é um serviço de identidade baseado no local. O AD FS alarga a capacidade de utilizar a funcionalidade de sso (SSO) entre parceiros de negócios de confiança sem exigir que os utilizadores se inscrevam separadamente em cada aplicação. Isto é conhecido como Federação.

Muitas organizações têm aplicações de Software como serviço (SaaS) ou aplicações personalizadas da Linha de Negócios (LOB) federadas diretamente para AD FS, ao lado de aplicações baseadas no Office 365 e Azure AD. 

![Aplicações ligadas diretamente no local](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Para aumentar a segurança da aplicação, o seu objetivo é ter um único conjunto de controlos de acesso e políticas em todos os seus locais e ambientes em nuvem.** 

![Aplicações ligadas através do Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Tipos de apps para migrar

Migrar toda a autenticação da sua aplicação para a Azure AD é o ideal, pois dá-lhe um único plano de controlo para gestão de identidade e acesso.

As suas aplicações podem utilizar protocolos modernos ou antigos para autenticação. Considere as primeiras aplicações migratórias que utilizam protocolos de autenticação modernos (como SAML e Open ID Connect). Estas aplicações podem ser reconfiguradas para autenticar com Azure AD através de um conector incorporado na nossa Galeria de Aplicações, ou registando a aplicação em Azure AD. As aplicações que utilizam protocolos mais antigos podem ser integradas usando [o Application Proxy.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) 

Para mais informações, veja [que tipo de aplicações posso integrar com a Azure AD?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)

Pode utilizar o relatório de [atividade da aplicação AD FS para migrar aplicações para Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) se tiver [a Azure AD Connect Health ativada](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs). 

### <a name="the-migration-process"></a>O processo de migração

Durante o processo de mudança da autenticação da sua aplicação para Azure AD, teste adequadamente as suas aplicações e configuração. Recomendamos que continue a utilizar ambientes de teste existentes para testes de migração que se deslocam para o ambiente de produção. Se um ambiente de teste não estiver disponível atualmente, pode configurar um utilizando [o Azure App Service](https://azure.microsoft.com/services/app-service/) ou a [Azure Virtual Machines,](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)dependendo da arquitetura da aplicação.

Pode optar por configurar um inquilino Azure AD de teste separado para usar à medida que desenvolve as configurações da sua aplicação. 

O seu processo de migração pode ser assim:

**Fase 1 - Estado atual: App de produção autenticada com FS AD**

![Fase de migração 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Fase 2 - OPCIONAL: Teste de instância de app que aponta para testar inquilino do Azure**

Atualize a configuração para apontar a sua instância de teste da aplicação para um inquilino AZure AD de teste, e faça quaisquer alterações necessárias. A aplicação pode ser testada com os utilizadores no teste Azure AD inquilino. Durante o processo de desenvolvimento, pode utilizar ferramentas como [o Fiddler](https://www.telerik.com/fiddler) para comparar e verificar pedidos e respostas.

Se a configuração de um inquilino de teste separado não for viável, salte este palco e levante-se numa instância de teste de uma app e aponte-a para o seu inquilino Azure AD de produção, conforme descrito na fase 3 abaixo.

![Fase de migração 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fase 3 - App de teste que aponta para a produção do inquilino Azure**

Atualize a configuração para apontar a sua instância de teste da aplicação para a sua instância de produção de Azure. Pode agora testar com os utilizadores na sua produção. Se necessário, reveja a secção deste artigo sobre utilizadores em transição.

![Fase de migração 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fase 4 - App de produção que aponta para a produção de inquilino AD**

Atualize a configuração da sua aplicação de produção para indicar o seu inquilino Azure de produção.

![Fase de migração 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 As aplicações que autenticam com AD FS podem utilizar grupos de Diretório Ativo para permissões. Utilize [a sincronização Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) para sincronizar dados de identidade entre o ambiente no local e o AD Azure antes de iniciar a migração. Verifique esses grupos e a adesão antes da migração para que possa conceder acesso aos mesmos utilizadores quando a aplicação é migrada.

### <a name="line-of-business-lob-apps"></a>Aplicativos de linha de negócios (LOB)

As aplicações LOB são desenvolvidas internamente pela sua organização ou disponíveis como um produto embalado padrão que está instalado no seu centro de dados. Exemplos incluem aplicações construídas em aplicativos Windows Identity Foundation e SharePoint (não SharePoint Online). 

As aplicações LOB que utilizam OAuth 2.0, OpenID Connect ou WS-Federation podem ser integradas com a AD AZure como [registos de aplicações.](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) Integrar aplicações personalizadas que utilizem SAML 2.0 ou WS-Federation como [aplicações não-galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) na página de aplicações da empresa no [portal Azure](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Sinal único baseado em SAML

As aplicações que utilizam o SAML 2.0 para autenticação podem ser configuradas para [um único sign-on baseado em SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SSO baseado em SAML). Com [sSO baseado em SAML,](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)pode mapear os utilizadores para funções de aplicação específicas com base em regras que define nas suas alegações SAML. 

Para configurar um pedido SaaS para um único sign-on baseado em SAML, consulte um [único sinal baseado em SAML baseado em Configure](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications). 

![Imagens de utilizador SSO SAML ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Muitas aplicações SaaS têm um [tutorial específico para aplicações](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) que o movem através da configuração para um único sign-on baseado em SAML.

![tutorial de aplicativo](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Algumas aplicações podem ser migradas facilmente. As aplicações que tenham requisitos mais complexos, como afirmações personalizadas, podem exigir configuração adicional no Azure AD e/ou no Azure AD Connect. Para obter informações sobre mapeamentos de reclamações suportados, consulte [o mapeamento de reclamações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

Tenha em mente as seguintes limitações ao mapear atributos:

* Nem todos os atributos que podem ser emitidos em AD FS aparecerão no Azure AD como atributos para emitir para fichas SAML, mesmo que esses atributos estejam sincronizados. Quando editar o atributo, a lista de dropdown do Valor irá mostrar-lhe os diferentes atributos que estão disponíveis no Azure AD. Verifique a configuração [de sincronização Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) para garantir que um atributo necessário - por exemplo, samAccountName -- está a ser sincronizado com a Azure AD. Pode utilizar os atributos de extensão para emitir qualquer alegação que não faça parte do esquema padrão do utilizador em Azure AD.

* Nos cenários mais comuns, apenas a afirmação NameID e outras afirmações de identificador de utilizador comuns são necessárias para as aplicações. Para determinar se são necessárias quaisquer reclamações adicionais, examine as alegações que está a emitir a partir de AD FS.

* Nem todas as reclamações podem ser problemas, uma vez que algumas reclamações estão protegidas no Azure AD. 

* A capacidade de usar fichas SAML encriptadas está agora em pré-visualização. Ver [Como: personalizar reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

 

### <a name="software-as-a-service-saas-apps"></a>Software como um serviço (SaaS) apps

Se o seu utilizador iniciar seduzição em aplicações saaS como Salesforce, ServiceNow ou Workday, e estiver integrado com FS AD, está a utilizar o sign-on federado para aplicações SaaS. 

A maioria das aplicações SaaS já podem ser configuradas em Azure AD. A Microsoft tem muitas ligações pré-configuradas para aplicações SaaS na galeria de  [aplicações AD AZure](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), o que facilitará a sua transição. As aplicações SAML 2.0 podem ser integradas com a Azure AD através da galeria de aplicações AD AZure ou como [aplicações não-galerias.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

Do mesmo modo, as aplicações que utilizam OAuth 2.0 ou OpenID Connect podem ser integradas no Azure AD como [registos de aplicações](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). As aplicações que utilizam protocolos legados podem usar [o Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para autenticar com Azure AD.

Para qualquer problema com o embarque das suas aplicações SaaS, pode contactar o [pseudónimo de suporte à Integração de Aplicações SaaS.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

**Certificados de assinatura SAML para SSO**: Os certificados de assinatura são uma parte importante de qualquer implantação SSO. A Azure AD cria os certificados de assinatura para estabelecer SSO federado baseado em SAML nas suas aplicações SaaS. Uma vez adicionadas aplicações de galeria ou não galeria, irá configurar a aplicação adicionada usando a opção SSO federada. Consulte [os certificados de gestão de um único sinal federado no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplicativos e configurações que podem ser movidos hoje

As aplicações que pode mover-se facilmente hoje incluem aplicações SAML 2.0 que usam o conjunto padrão de elementos de configuração e reivindicações:

* Nome Principal de Utilizador

* Endereço de e-mail

* Nome próprio

* Apelido

* Atributo alternativo como **NameID** de SAML, incluindo o atributo de correio do Azure AD, o prefixo de correio, o ID de funcionário, os atributos de extensão 1-15 ou o atributo **SamAccountName**. Para obter mais informações, veja [Editing the NameIdentifier claim](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) (Editar a afirmação NameIdentifier).

* Afirmações personalizadas.

Os seguintes requerem medidas de configuração adicionais para migrar para Azure AD:

* Autorização personalizada ou regras de autenticação multi-factor (MFA) em FS AD. Configura-os utilizando a função [de Acesso Condicional Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* Aplicativos com vários pontos finais url de resposta. Configura-os em Ad Azure utilizando o PowerShell ou na interface do portal Azure.

* Aplicações WS-Federation, como as aplicações do SharePoint, que precisam de tokens SAML da versão 1.1. Pode configurá-los manualmente utilizando o PowerShell. Também pode adicionar um modelo genérico pré-integrado para aplicações SharePoint e SAML 1.1 da galeria. Apoiamos o protocolo SAML 2.0.

* A emissão de reclamações complexas transforma regras. Para obter informações sobre mapeamentos de reclamações apoiadas, consulte:
   *  [Mapeamento de afirmações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Personalização de reclamações emitidas no token SAML para aplicações empresariais no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplicações e configurações não suportadas atualmente no Azure AD

As aplicações que requerem as seguintes capacidades não podem ser migradas hoje em dia.

**Capacidades de protocolo**

* Apoio ao padrão WS-Trust Atas

* Resolução de artefactos SAML

* Verificação de assinaturas dos pedidos saml assinados  
Note que os pedidos assinados são aceites, mas a assinatura não é verificada.  
Dado que a Azure AD só devolverá o token aos pontos finais pré-configurados no pedido, a verificação da assinatura provavelmente não é necessária na maioria dos casos.

**Reclamações em capacidades simbólicas**

* Reclamações de lojas de atributos que não o diretório AD Azure, a menos que esses dados sejam sincronizados com a Azure AD. Para obter mais informações, consulte a visão geral da [API de sincronização AZure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Emissão de atributos de vário valor do diretório. Por exemplo, não podemos emitir uma reivindicação multivalorizada para endereços de procuração neste momento.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Definições de aplicativo de mapa de AD FS a Azure AD

A migração começa com a avaliação da forma como a aplicação está configurada no local e mapear essa configuração para o Azure AD. A AD FS e Azure AD funcionam da mesma forma, pelo que os conceitos de configuração de URLs de confiança, de inscrição e de assinatura, e identificadores aplicam-se em ambos os casos. Documente as definições de configuração AD FS das suas aplicações para que possa configurar facilmente as suas aplicações em Azure AD.

### <a name="map-app-configuration-settings"></a>Definições de configuração de aplicativos de mapa

A tabela seguinte descreve alguns dos mapeamentos mais comuns de configurações entre um AD FS Relying Party Trust para Azure AD Enterprise Application:

* AD FS – Encontre a definição no AD FS Relying Party Trust para a aplicação. Clique com a direita na festa de cingimento e selecione Propriedades. 

* Azure AD– A definição está configurada dentro do [portal Azure](https://portal.azure.com/) nas propriedades de assinatura única de cada aplicação.

| Definição de configuração| AD FS| Como configurar em Azure AD| Ficha SAML |
| - | - | - | - |
| **URL de início de sessão da aplicação** <p>O URL para o utilizador iniciar a sedção da app num fluxo SAML iniciado pelo Fornecedor de Serviços (SP).| N/D| Abra a configuração básica de SAML a partir do sign-on baseado em SAML| N/D |
| **URL de resposta da aplicação** <p>O URL da aplicação a partir da perspetiva do fornecedor de identidade (IdP' s). O IdP envia o utilizador e o token aqui depois de o utilizador ter assinado no IdP.  Isto também é conhecido como **ponto final de afirmação SAML.**| Selecione o **separador Pontos finais**| Abra a configuração básica de SAML a partir do sign-on baseado em SAML| Elemento de destino no token SAML. Valor de exemplo: `https://contoso.my.salesforce.com` |
| **URL de fim de sessão da aplicação** <p>Este é o URL para o qual são enviados pedidos de "limpeza de sinal" quando um utilizador assina a partir de uma aplicação. O IdP envia o pedido para assinar o utilizador de todas as outras aplicações também.| Selecione o **separador Pontos finais**| Abra a configuração básica de SAML a partir do sign-on baseado em SAML| N/D |
| **Identificador de aplicação** <p>Este é o identificador de aplicações na perspetiva do IdP. O valor URL de inscrição é frequentemente utilizado para o identificador (mas nem sempre).  Às vezes a aplicação chama a isto o "ID da entidade".| Selecione o **separador Identifiers**|Abra a configuração básica de SAML a partir do sign-on baseado em SAML| Mapas para o elemento **do Público** no símbolo DOL. |
| **Metadados de federação da aplicação** <p>Esta é a localização dos metadados da federação da aplicação. O IdP utiliza-os para atualizar automaticamente definições de configuração específicas, como pontos finais ou certificados de encriptação.| Selecione o **separador Monitoring**| N/D. A Azure AD não suporta o consumo de metadados da federação de aplicações diretamente. Pode importar manualmente os metadados da federação.| N/D |
| **Identificador de utilizador/ ID de nome** <p>Atributo que é utilizado para indicar exclusivamente a identidade a partir do utilizador do Azure AD ou do AD FS para a sua aplicação.  Este atributo é normalmente a UPN ou o endereço de e-mail do utilizador.| Regras de reivindicação. Na maioria dos casos, a regra de reclamação emite uma reclamação com um tipo que termina com o Identificador de Nome.| Pode encontrar o identificador sob o cabeçalho **Atributos e Reclamações do Utilizador**. Por padrão, a UPN é usada| Mapeia para o elemento **NameID** no token SAML. |
| **Outras reclamações** <p>Exemplos de outras informações de reclamação que são geralmente enviadas do IdP para a aplicação incluem Primeiro Nome, Apelido, endereço de e-mail e membro do grupo.| No AD FS, está disponível como outras regras de afirmação na entidade confiadora.| Pode encontrar o identificador sob o cabeçalho **Atributos do Utilizador & Reclamações**. Selecione **Vista** e edite todos os outros atributos de utilizador| N/D |


### <a name="map-identity-provider-idp-settings"></a>Definições do Fornecedor de Identidade do Mapa (IdP)

Configure as suas aplicações para apontar para Azure AD contra AD FS para SSO. Aqui, estamos focados em aplicações SaaS que usam o protocolo SAML. No entanto, este conceito estende-se também a aplicações LOB personalizadas.

> [!NOTE]
> Os valores de configuração para Azure AD seguem o padrão em que o seu ID do Inquilino Azure substitui {tenant-id} e o ID de aplicação substitui {application-id}. Encontra esta informação no [portal Azure](https://portal.azure.com/) no âmbito do Azure Ative Directory > Properties: 

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
| **URL de inscrição idP** <p>URL de entrada do IdP do ponto de vista da aplicação (onde o utilizador é redirecionado para login).| O URL de assinatura da AD FS é o nome de serviço da federação AD FS seguido de "/adfs/ls/". <p>Por exemplo: `https://fs.contoso.com/adfs/ls/`| Substitua {tenant-id} por identificação do seu inquilino. <p> Para aplicações que usam o protocolo SAML-P: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Para aplicações que usam o protocolo WS-Federação: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL de aprovação idP**<p>URL de assinatura do IdP do ponto de vista da aplicação (onde o utilizador é redirecionado quando escolhe assinar fora da app).| O URL de sinalização é o mesmo que o URL de inscrição, ou o mesmo URL com "wa=wsignout1.0" anexado. Por exemplo: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Substitua {tenant-id} por identificação do seu inquilino.<p>Para aplicações que usam o protocolo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Para aplicações que usam o protocolo WS-Federação: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificado de assinatura token**<p>O IdP utiliza a chave privada do certificado para assinar fichas emitidas. Verifica se o token provém do mesmo IdP para o qual a aplicação está configurada para confiar.| O certificado de assinatura de tokens do AD FS está disponível na Gestão do AD FS, em **Certificados**.| Encontre-o no portal Azure nas propriedades de **inscrição única** da aplicação sob o certificado de **assinatura SAML do cabeçalho**. Aí, pode transferir o certificado para carregamento para a aplicação.  <p>Se o pedido tiver mais de um certificado, pode encontrar todos os certificados no ficheiro XML dos metadados da federação. |
| **Identificador/ "emitente"**<p>Identificador do IdP do ponto de vista da aplicação (às vezes chamado de "ID emitente").<p>No token SAML, o valor aparece como elemento emitente.| O identificador para AD FS é geralmente o identificador de serviço da federação em Gestão FS AD em **serviço > Propriedades de Serviço da Federação de Edição**. Por exemplo: `http://fs.contoso.com/adfs/services/trust`| Substitua {tenant-id} por identificação do seu inquilino.<p>https: \/ /sts.windows.net/{tenant-id}/ |
| **Metadados da federação IdP**<p>Localização dos metadados da federação publicamente disponíveis do IdP. (Algumas aplicações utilizam os metadados de federação como alternativa à configuração individual, por parte do administrador, de URLs, do identificador e do certificado de assinatura de tokens.)| Encontre o URL de metadados da federação AD FS em Gestão AD FS em **Serviço > Pontos finais > Tipo de Metadados > Tipo: Metadados da Federação**. Por exemplo: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| O valor correspondente para Azure AD segue o padrão [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Substitua {TenantDomainName} pelo nome do seu inquilino no formato "contoso.onmicrosoft.com".   <p>Para obter mais informações, veja [Federation metadata](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata) (Metadados da federação). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Represente as políticas de segurança da AD FS no Azure AD

Ao mover a autenticação da sua aplicação para Azure AD, crie mapeamentos das políticas de segurança existentes para as suas variantes equivalentes ou alternativas disponíveis no Azure AD. Garantir que estes mapeamentos podem ser feitos ao mesmo tempo que cumprem os padrões de segurança exigidos pelos proprietários das suas aplicações tornará o resto da migração da aplicação significativamente mais fácil.

Para cada tipo de regra e seus exemplos, sugerimos aqui como a regra se parece em AD FS, o código equivalente de regra de regra AD FS, e como este mapa em Azure AD.

### <a name="map-authorization-rules"></a>Regras de autorização do mapa

Seguem-se exemplos de tipos de regras de autorização em AD FS e como pode mapeá-las para Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Exemplo 1: Permitir o acesso a todos os utilizadores

Permit Access to All Users looks like in AD FS: 

![Fase de migração 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Este mapa para Azure AD de uma das seguintes formas:

No [portal Azure:](https://portal.azure.com/)
* Opção 1: Definir a atribuição do utilizador necessária para não ![editar a política de controlo de acesso para apps SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Note que a definição da atribuição do Utilizador requer a mudança para Sim requer que os utilizadores sejam designados para a aplicação para terem acesso. Quando definido para Nº, todos os utilizadores têm acesso. Este switch não controla o que mostra para os utilizadores na experiência My Apps. 

 
* Opção 2: No separador Utilizadores e grupos, atribua a sua aplicação ao grupo automático "Todos os Utilizadores". <p>
Deve [ativar grupos dinâmicos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) no seu inquilino AZure AD para que o grupo predefinido 'Todos os Utilizadores' esteja disponível.

   ![My SaaS Apps in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Exemplo 2: Permitir um grupo explicitamente

Autorização explícita do grupo em AD FS:


![Regras de autorização de emissão ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


É assim que a regra mapeia para Azure AD:

No [portal Azure,](https://portal.azure.com/)primeiro [criará um grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) de utilizadores que corresponda ao grupo de utilizadores a partir de FS AD e, em seguida, atribuirá permissões de aplicações a esse grupo:

![Adicionar Atribuição ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Exemplo 3: Autorizar um utilizador específico

Autorização explícita do utilizador em AD FS:

![Regras de autorização de emissão ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

É assim que a regra mapeia para Azure AD:

No [portal Azure](https://portal.azure.com/), adicione um utilizador à aplicação através do separador Add Assignment da aplicação, como mostrado abaixo:

![As minhas aplicações SaaS em Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Mapear regras de autenticação multi-factor 

Uma implementação no local de [Autenticação Multi-Factor (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) e FS AD continuará a funcionar após a migração porque você é federado com FS AD. No entanto, considere migrar para as capacidades de MFA incorporadas da Azure que estão ligadas aos fluxos de trabalho de acesso condicional da Azure AD. 

Seguem-se exemplos de tipos de regras de MFA em AD FS, e como pode mapeá-las para Azure AD com base em diferentes condições:

Definições de regras MFA em AD FS:

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exemplo 1: Impor MFA com base em utilizadores/grupos

O seletor de Utilizadores/Grupos é uma regra que lhe permite impor MFA numa base por grupo (Grupo SID) ou por utilizador (SID Primário). Para além das atribuições de Utilizador/Grupos, todas as caixas de verificação adicionais na configuração UI de configuração AD FS funcionam como regras adicionais que são avaliadas após a aplicação da regra utilizador/grupos. 


Especificar as regras de MFA para um utilizador ou um grupo em Azure AD:

1. Criar uma [nova política de acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Selecione **Atribuições**. Adicione o(s) utilizador(s) ou grupo(s) em que pretende impor MFA.

3. Configure as opções **de controlo do acesso** como mostrado abaixo:  
‎

![Definições de AAD MFA](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exemplo 2: Impor MFA para dispositivos não registados

Especificar as regras de MFA para dispositivos não registados em Azure AD:

1. Criar uma [nova política de acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Definir as **Atribuições** a **Todos os utilizadores**.

3. Configure as opções **de controlo do acesso** como mostrado abaixo:  
‎

![Definições de AAD MFA](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Quando define a opção de controlos para vários controlos para exigir um dos controlos selecionados, significa que se alguma das condições especificadas pela caixa de verificação for preenchida pelo utilizador, será-lhes concedido acesso à sua aplicação.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exemplo 3: Impor MFA com base na localização

Especificar as regras de MFA com base na localização de um utilizador em Azure AD:

1. Criar uma [nova política de acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Definir as **Atribuições** a **Todos os utilizadores**.

1. [Configurar localizações nomeadas em Azure AD,](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) caso contrário, a federação de dentro da sua rede corporativa é confiável. 

1. Configure as **regras de Condições** para especificar os locais para os quais pretende aplicar o MFA.

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Configure as opções **de controlo do acesso** como mostrado abaixo:


![Políticas de controlo de acesso ao mapa](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Mapa Emit atributos como regra de Reclamações

Aqui está um exemplo de como os atributos são mapeados em FS AD:

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


É assim que a regra mapeia para Azure AD:

No [portal Azure](https://portal.azure.com/), selecione **Aplicações empresariais**, **inscrição única**, e adicione **atributos SAML Token** como mostrado abaixo:

![Definições de MFA Azure AD](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mapear políticas de controlo de acesso incorporadas

A AD FS 2016 tem várias políticas de controlo de acesso incorporadas que pode escolher:

![Azure AD construído no controlo de acessos](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Para implementar políticas incorporadas em Azure AD, pode utilizar uma [nova política de acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) e configurar os controlos de acesso, ou pode utilizar o designer de políticas personalizados em AD FS 2016 para configurar políticas de controlo de acesso. O Editor de Regras tem uma lista exaustiva de licenças e opções que podem ajudá-lo a fazer todo o tipo de permutações.

![Políticas de controlo de acesso AZure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



Nesta tabela, listamos algumas opções úteis de licença e exceto opções e como eles mapeiam para Azure AD. 


| Opção | Como configurar a opção de licença em Azure AD?| Como configurar Exceto opção em Azure AD? |
| - | - | - |
| A partir de rede específica| Mapas para [localização nomeada](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) em Azure AD| Utilize a opção **Excluir** para [locais fidedignos](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| De grupos específicos| [Definir uma atribuição de utilizador/grupos](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Utilize a opção **Excluir** em Utilizadores e Grupos |
| De dispositivos com nível de confiança específico| Desa esta definição do controlo "Estado do Dispositivo" em atribuições -condições >| Utilize a opção **Excluir** sob condição do Estado do Dispositivo e inclua **todos os dispositivos** |
| Com Reclamações Específicas no Pedido| Esta definição não pode ser migrada| Esta definição não pode ser migrada |


Um exemplo de como configurar a opção Excluir para localizações fidedignas no Portal Azure:

![Screenshot das políticas de controlo de acesso de mapeamento](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Utilizadores de transição de AD FS para Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sync AD FS grupos em Azure AD

Ao mapear as regras de autorização, as aplicações que autenticam com AD FS podem utilizar grupos de Diretório Ativo para permissões. Neste caso, utilize [o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) para sincronizar estes grupos com a Azure AD antes de migrar as aplicações. Certifique-se de que verifica esses grupos e a adesão antes da migração para que possa conceder acesso aos mesmos utilizadores quando a aplicação é migrada.

Para obter mais informações, consulte [Pré-requisitos para a utilização de atributos do Grupo sincronizados a partir do Ative Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims).

### <a name="setup-user-self-provisioning"></a>Configuração auto-provisão do utilizador 

Algumas aplicações SaaS suportam a capacidade de auto-prestação de utilizadores quando se inscrevem pela primeira vez na aplicação. No Azure Ative Directory (Azure AD), o termo provisionamento de aplicações refere-se à criação automática de identidades e funções de utilizador nas aplicações da cloud[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)às que os utilizadores precisam de acesso. Os utilizadores que estão migrados já terão uma conta na aplicação SaaS. Quaisquer novos utilizadores adicionados após a migração terão de ser a provisionados. Teste [o provisionamento da aplicação SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) uma vez que a aplicação é migrada.

### <a name="sync-external-users-in-azure-ad"></a>Sync utilizadores externos em Azure AD

Os seus utilizadores externos existentes podem ser configurados de duas maneiras principais dentro do AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Utilizadores externos com uma conta local dentro da sua organização

Continuará a poder utilizar estas contas da mesma forma que as suas contas internas de utilizador funcionam. Estas contas externas de utilizadores têm um nome de princípio dentro da sua organização, embora o e-mail da conta possa apontar externamente. À medida que progride com a sua migração, pode aproveitar os benefícios que o [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) oferece ao migrar estes utilizadores para usar a sua própria identidade corporativa quando tal identidade estiver disponível. Isto simplifica o processo de inscrição para esses utilizadores, uma vez que são muitas vezes assinados com a sua própria logon corporativa. A administração da sua organização também será facilitada, por já não ter de gerir contas para utilizadores externos.

#### <a name="federated-external-identities"></a>Identidades externas federadas

Se está atualmente a federar com uma organização externa, tem algumas abordagens a tomar:

* [Adicione utilizadores de colaboração Azure Ative Directory B2B no portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). Você pode enviar proativamente convites de colaboração B2B do portal administrativo AZure AD para a organização de parceiros para que membros individuais continuem a usar as aplicações e ativos a que estão habituados. 

* [Crie um fluxo de trabalho de inscrição B2B autosserviço](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) que gere um pedido para utilizadores individuais na sua organização parceira usando o convite B2B API.

Não importa como os utilizadores externos existentes estejam configurados, eles provavelmente têm permissões que estão associadas à sua conta, seja em membros do grupo ou permissões específicas. Avalie se estas permissões precisam de ser migradas ou limpas. As contas dentro da sua organização que representam um utilizador externo precisam de ser desativadas uma vez que o utilizador tenha sido migrado para uma identidade externa. O processo de migração deve ser discutido com os seus parceiros de negócio, pois pode haver uma interrupção na sua capacidade de ligação aos seus recursos.

## <a name="migrate-and-test-your-apps"></a>Migrar e testar as suas apps

Acompanhe o processo de migração detalhado neste artigo.

Em seguida, vá ao [portal Azure](https://aad.portal.azure.com/) para testar se a migração foi um sucesso. Siga as instruções abaixo:
1. Selecione **Aplicações empresariais**  >  **Todas as aplicações** e encontre a sua aplicação na lista.

1. **Selecione Gerir**  >  **Utilizadores e grupos** para atribuir pelo menos um utilizador ou grupo à aplicação.

1. **Selecione Gerir**  >  **o Acesso Condicional**. Reveja a sua lista de políticas e certifique-se de que não está a bloquear o acesso à aplicação com uma [política de acesso condicional.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

Dependendo da configuração da sua aplicação, verifique se o SSO funciona corretamente. 

| Tipo de autenticação| Testar |
| - | - |
| OAuth / OpenID Connect| Selecione **as aplicações da Enterprise > Permissões** e certifique-se de que consentiu na aplicação a ser usada na sua organização nas definições do utilizador para a sua aplicação.  
‎ |
| SAML-based SSO (SSO baseado no SAML)| Utilize o botão [de definições DE TESTE SAML](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) encontrado sob **o sign-on único**.  
‎ |
| SSO baseado em palavra-passe| Descarregue e instale o [sinal seguro myApps](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) [-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) [na extensão.](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) Esta extensão ajuda-o a iniciar qualquer uma das aplicações em nuvem da sua organização que o exijam a utilizar um processo SSO.  
‎ |
| Proxy da aplicação| Certifique-se de que o seu conector está em funcionamento e atribuído à sua aplicação. Visite o [guia de resolução de problemas da Aplicação Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) para obter mais assistência.  
‎ |

> [!NOTE]
> Os cookies do antigo ambiente AD FS continuarão a ser persistentes nas máquinas do utilizador. Estes cookies podem causar problemas com a migração, uma vez que os utilizadores podem ser direcionados para o antigo ambiente de login da AD FS em comparação com o novo login Azure AD. Poderá necessitar de limpar os cookies do navegador do utilizador manualmente ou utilizar um script. Também pode utilizar o Gestor de Configuração do Centro de Sistema ou uma plataforma semelhante.

### <a name="troubleshoot"></a>Resolução de problemas

Se houver erros no teste das aplicações migradas, a resolução de problemas pode ser o primeiro passo antes de voltar às partes de apoio da AD FS. Ver [como depurar o único sign-on baseado em SAML para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues).

### <a name="rollback-migration"></a>Migração de retrocesso

Se a migração falhar, recomendamos que deixe as partes dependentes existentes nos servidores AD FS e remova o acesso às Partes Dependentes. Isto permitirá uma rápida queda, se necessário durante a implantação.

### <a name="end-user-communication"></a>Comunicação de utilizador final

Embora a janela de paragem planeada em si possa ser mínima, você ainda deve planejar comunicar estes prazos proativamente aos empregados enquanto faz o corte de AD FS para Azure AD. Certifique-se de que a sua experiência de aplicação tem um botão de Feedback ou aponta para o seu helpdesk para problemas.

Uma vez concluída a implementação, pode enviar comunicações informando os utilizadores da implementação bem sucedida e lembrá-los de quaisquer novos passos que precisem de tomar.

* Instrua os utilizadores a utilizarem as [Minhas Apps](https://myapps.microsoft.com) para acederem a todas as aplicações migradas. 

* Lembre aos utilizadores que podem precisar de atualizar as suas definições de MFA. 

* Se o Reset de Palavra-Passe de Autosserviço for implementado, os utilizadores poderão ter de atualizar ou verificar os seus métodos de autenticação. Consulte os modelos de comunicação do utilizador final [MFA](https://aka.ms/mfatemplates) e [SSPR.](https://aka.ms/ssprtemplates)

Comunicação para utilizadores externos: Este grupo de utilizadores é geralmente o mais impactado criticamente em caso de problemas. Isto é especialmente verdade se a sua postura de segurança ditar um conjunto diferente de regras de Acesso Condicional ou perfis de risco para parceiros externos. Certifique-se de que os parceiros externos estão cientes do calendário de migração em nuvem e têm um prazo durante o qual são encorajados a participar numa implementação piloto que testa todos os fluxos exclusivos da colaboração externa. Finalmente, certifique-se de que eles têm uma maneira de aceder ao seu helpdesk em caso de quebra de problemas.

## <a name="next-steps"></a>Passos Seguintes
Ler  [autenticação de aplicação migratória para Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Configurar [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) e [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
