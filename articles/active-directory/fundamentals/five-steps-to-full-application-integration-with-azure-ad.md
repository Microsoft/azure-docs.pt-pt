---
title: Cinco passos para integrar todas as suas apps com Azure AD
description: Este guia explica como integrar todas as suas aplicações com Azure AD. Em cada passo, explicamos o valor e fornecemos ligações a recursos que explicarão os detalhes técnicos.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057258"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Cinco passos para integrar todas as suas apps com Azure AD

Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A Azure AD fornece soluções seguras de autenticação e autorização para que clientes, parceiros e colaboradores possam aceder às aplicações de que necessitam. Com Azure AD, [acesso condicional,](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) [autenticação multi-factor,](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) [sinal único,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)e fornecimento automático de [utilizadores](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) tornam a gestão de identidade e acesso fácil e segura.

Se a sua empresa tiver uma subscrição microsoft 365, provavelmente [já utilize](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) a Azure AD. No entanto, o Azure AD pode ser usado para todas as suas aplicações, e [ao centralizar a sua gestão de aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) pode usar as mesmas funcionalidades de gestão de identidade, ferramentas e políticas em todo o seu portfólio de aplicações. Ao fazê-lo, proporcionará uma solução unificada que melhore a segurança, reduz os custos, aumente a produtividade e permita-lhe garantir o cumprimento. E terá acesso remoto a aplicações no local.

Este guia explica como integrar todas as suas aplicações com Azure AD. Em cada passo, explicamos o valor e fornecemos ligações a recursos que explicarão os detalhes técnicos. Apresentamos estes passos numa ordem que recomendamos. No entanto, pode saltar para qualquer parte do processo para começar com o que adicionar mais valor para si.

Outros recursos sobre este tema, incluindo os papeleiros em processo de negócio aprofundado, que podem ser encontrados nos [nossos Recursos para aplicações migratórias para a página do Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)

## <a name="1-use-azure-ad-for-new-applications"></a>1. Utilize a Azure AD para novas aplicações

Primeiro, concentre-se nas aplicações recém-adquiridas. Quando o seu negócio começar a usar uma nova aplicação, [adicione-a imediatamente ao seu inquilino AZure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) Crie uma política da empresa para que adicionar novas aplicações ao Azure AD seja a prática padrão na sua organização. Isto é minimamente disruptivo para os processos de negócio existentes e permite-lhe investigar e provar o valor que obtém da integração de apps sem alterar a forma como as pessoas fazem negócios no seu ambiente hoje.

O Azure Ative Directory (Azure AD) tem uma galeria que contém milhares de aplicações pré-integradas para facilitar o seu início. Pode [adicionar uma aplicação de galeria à sua organização Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) com [tutoriais](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) passo a passo para integrar aplicações populares como:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Além disso, pode [integrar aplicações que não constituam na galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), incluindo qualquer aplicação que já exista na sua organização, ou qualquer aplicação de terceiros de um fornecedor que ainda não faça parte da galeria Azure AD. Também pode [adicionar a sua aplicação à galeria](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) se não estiver lá.

Finalmente, também pode integrar as aplicações que desenvolve internamente. Isto está coberto no quinto passo deste guia.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. Determinar a utilização da aplicação existente e priorizar o trabalho

Em seguida, descubra as aplicações que os colaboradores estão a usar frequentemente, e priorize o seu trabalho para integrá-las com Azure AD.

Pode começar por utilizar o Microsoft Cloud App Security&#39;[ferramentas](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) de descoberta em nuvem para descobrir e gerir &quot; it sombra na sua rede &quot; (isto é, aplicações não geridas pelo departamento de TI). Pode [utilizar o Microsoft Defender Advanced Threat Protection (ATP)](https://docs.microsoft.com/cloud-app-security/wdatp-integration) para simplificar e alargar o processo de descoberta.

Além disso, pode utilizar o [relatório de atividade da aplicação AD FS](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) no portal Azure para descobrir todas as aplicações AD FS na sua organização, o número de utilizadores únicos que se inscreveram neles, e compatibilidade para integrá-las com Azure AD.

Uma vez descoberto a sua paisagem existente, vai querer [criar um plano](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) e priorizar as aplicações de maior prioridade para integrar. Algumas perguntas exemplos que pode fazer para orientar este processo são:

- Quais as aplicações mais utilizadas?
- Quais são os mais arriscados?
- Que aplicações serão desativadas no futuro, tornando um movimento desnecessário?
- Que aplicações precisam de permanecer no local e não podem ser movidas para a nuvem?

Verá os maiores benefícios e economias de custos assim que todas as suas aplicações estiverem integradas e deixar de contar com múltiplas soluções de identidade. No entanto, você experimentará uma gestão de identidade mais fácil e uma maior segurança à medida que se move em direção a este objetivo. Quer usar este tempo para priorizar o seu trabalho e decidir o que faz sentido para a sua situação.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. Integrar aplicações que dependem de outros fornecedores de identidade

Durante o seu processo de descoberta, pode ter encontrado aplicações que não são rastreadas pelo departamento de TI, que deixam os seus dados e recursos vulneráveis. Pode também ter aplicações que utilizam soluções de identidade alternativas, incluindo serviços da Federação de Diretórios Ativos (ADFS) ou outros fornecedores de identidade. Considere como pode consolidar a sua identidade e gestão de acesso para economizar dinheiro e aumentar a segurança. A redução do número de soluções identitárias que tem irá:

- Poupe-lhe dinheiro eliminando a necessidade de fornecimento e autenticação de utilizadores no local, bem como taxas de licenciamento pagas a outros fornecedores de identidade em nuvem para o mesmo serviço.
- Reduza as despesas administrativas e permita uma segurança mais apertada com menos despedimentos no seu processo de gestão de identidade e acesso.
- Permitir que os colaboradores obtenham acesso único de acesso a TODAS as aplicações de que necessitam através do [portal MyApps.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)
- Melhorar a inteligência do Azure AD&#39;serviços relacionados com [a proteção](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) de identidade, como o acesso condicional, aumentando a quantidade de dados que obtém do uso da sua aplicação, e alargando os seus benefícios às aplicações recém-adicionadas.

Publicámos orientações para a gestão do processo de negócio de integração de apps com Azure AD, incluindo um [cartaz](https://aka.ms/AppOnePager) e [apresentação](https://aka.ms/AppGuideline) que pode usar para sensibilizar e interessar os proprietários de empresas e aplicações. Pode modificar essas amostras com a sua própria marca e publicá-las na sua organização através do portal da sua empresa, newsletter ou outro meio à medida que for terminando este processo.

Um bom lugar para começar é avaliando a sua utilização de Serviços da Federação de Diretórios Ativos (ADFS). Muitas organizações usam ADFS para autenticação com aplicações SaaS, aplicações personalizadas da Linha das Empresas e aplicações baseadas no Office 365 e Azure AD:

![O diagrama mostra aplicações no local, linha de aplicações empresariais, aplicações SaaS e, via Azure AD, Office 365, todos conectando-se com linhas pontilhadas em Ative Directory e AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Pode atualizar esta configuração [substituindo o ADFS por Azure AD como o centro](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) da sua solução de gestão de identidade. Ao fazê-lo, permite a entrada em todas as aplicações a que os seus colaboradores pretendem aceder, e facilita que os colaboradores encontrem qualquer aplicação empresarial de que necessitem através do [portal MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access), além dos outros benefícios acima mencionados.

![O diagrama mostra aplicações no local através do Ative Directory e AD FS, linha de aplicações empresariais, aplicações SaaS e Office 365, todas conectando-se com linhas pontilhadas para o Azure Ative Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Uma vez que a Azure AD se torne o fornecedor central de identidade, poderá ser capaz de mudar completamente de ADFS, em vez de utilizar uma solução federada. As aplicações que anteriormente utilizavam o ADFS para autenticação podem agora utilizar apenas o AZure AD.

![O diagrama mostra no local, linha de aplicações empresariais, aplicações SaaS e Office 365, todas conectando-se com linhas pontilhadas no Diretório Ativo Azure. O Diretório Ativo e o AD FS não estão presentes.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Também pode migrar aplicações que usam um fornecedor de identidade diferente baseado na nuvem para Azure AD. A sua organização pode ter múltiplas soluções de Gestão de Acesso à Identidade (IAM) no local. Migrar para uma infraestrutura AD Azure é uma oportunidade para reduzir as dependências das licenças IAM (no local ou na nuvem) e nos custos de infraestrutura. Nos casos em que já tenha pago a Azure AD através das licenças M365, não há razão para pagar o custo adicional de outra solução IAM.

## <a name="4-integrate-on-premises-applications"></a>4. Integrar aplicações no local

Tradicionalmente, as aplicações eram mantidas seguras, permitindo o acesso apenas enquanto estavam ligadas à rede corporativa. No entanto, num mundo cada vez mais conectado queremos permitir o acesso a apps para clientes, parceiros e/ou colaboradores, independentemente de onde estejam no mundo. [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) é uma funcionalidade do Azure AD que liga as suas aplicações existentes no local ao Azure AD e não requer que mantenha servidores de borda ou outra infraestrutura adicional para o fazer.

![Um diagrama mostra o Serviço de Procuração de Aplicação em ação. Um utilizador acede https://sales.contoso.com " " e seu pedido é redirecionado através de " " em https://sales-contoso.msappproxy.net Azure Ative Diretório para o endereço no local " http://sales](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Pode utilizar [tutorial: Adicione uma aplicação no local para acesso remoto através do Application Proxy in Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) para ativar o Application Proxy e adicionar uma aplicação no local ao seu inquilino Azure AD.

Além disso, pode integrar controladores de entrega de aplicações como F5 Big-IP APM ou Zscaler Private Access. Ao integrá-los com a Azure AD, obtém-se a autenticação moderna e gestão de identidade da AZure AD ao lado das funcionalidades de gestão de tráfego e segurança do produto parceiro. Chamamos a esta solução [Secure Hybrid Access](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). Se utilizar algum dos seguintes serviços hoje, temos tutoriais que o acompanharão através da forma de integrá-los com a Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Controlador de entrega de aplicações Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (anteriormente conhecido como Citrix Netscaler)
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. Integrar aplicações que os seus desenvolvedores constroem

Para aplicações que são construídas dentro da sua empresa, os seus desenvolvedores podem usar a [plataforma de identidade](https://docs.microsoft.com/azure/active-directory/develop/) da Microsoft para implementar a autenticação e autorização. As aplicações integradas na plataforma com ser [registadas no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) e geridas como qualquer outra app no seu portfólio.

Os desenvolvedores podem usar a plataforma tanto para aplicações de uso interno como para aplicações viradas para o cliente, e existem outros benefícios que vêm com a utilização da plataforma. [As Bibliotecas de Autenticação da Microsoft (MSAL),](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)que fazem parte da plataforma, permitem aos programadores permitir experiências modernas como a autenticação de vários fatores e o uso de chaves de segurança para aceder às suas apps sem precisarem de a implementar por si próprios. Além disso, as aplicações integradas com a plataforma de identidade da Microsoft podem aceder ao [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) - um ponto final unificado da API que fornece os dados da Microsoft 365 que descrevem os padrões de produtividade, identidade e segurança numa organização. Os desenvolvedores podem usar esta informação para implementar funcionalidades que aumentam a produtividade para os seus utilizadores. Por exemplo, ao identificar as pessoas com quem o utilizador tem vindo a interagir recentemente e a superá-las na aplicação&#39;uI.

Temos uma [série de vídeos](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) que fornece uma introdução abrangente à plataforma, bem como [muitas amostras](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) de código em idiomas e plataformas apoiadas.

## <a name="next-steps"></a>Passos seguintes

- [Recursos para aplicações migratórias para o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
