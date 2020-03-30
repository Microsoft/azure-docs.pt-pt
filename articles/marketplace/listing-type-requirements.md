---
title: Requisitos por Tipo de Listagem [ Azure
description: Este artigo descreve os critérios de elegibilidade e os requisitos de publicação dos parceiros que tentam entender como publicar aplicações no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: 1ff6e34936234d6636fabc6c5e17dd32416b4ac2
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387857"
---
# <a name="requirements-by-listing-type"></a>Requisitos por Tipo de Listagem  
Os requisitos de conteúdo técnico e de marketing variam de acordo com a montra, o tipo de oferta e o tipo de listagem. Reveja as seguintes especificações para verificar a sua conformidade.  
1. Requisitos de fachada:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Requisitos do tipo de listagem e do tipo de oferta:  
    *   Para mais informações sobre os tipos de listagem e os tipos de oferta, visite a página Determinar o Tipo de Listagem para a Sua Solução localizada em [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Requisitos de montra: AppSource  
O quadro seguinte descreve os requisitos pré-requisitos para a publicação no AppSource.  

| Requisito | Detalhes | Obrigatório ou recomendado |  
|:--- |:--- |:--- |  
| ***Diretório Ativo Azure (Azure AD)*** | A sua aplicação deve permitir que o Azure Ative Directory tenha um único signo (Azure AD federado SSO) com o consentimento habilitado.<ul> <li>Para obter mais informações sobre a habilitação do Azure AD federado SSO, visite o sign-on único da Configuração para aplicações que não estejam na página da galeria de aplicações do Azure Ative Directory localizada em [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Necessário |   
| ***Integração com os Serviços Microsoft Cloud*** | A sua aplicação deverá integrar-se com outros serviços microsoft Cloud, como os serviços Microsoft Power BI, Cortana Intelligence ou Microsoft Azure.<ul> <li>Um exemplo de um serviço Microsoft Cloud é a Internet das Coisas.</li> </ul> | Recomendado |  
| ***Audiência*** | A sua aplicação deve ser para utilizadores de linha de negócios e proprietários de empresas. | Necessário | 
| ***Software como app de serviço (SaaS) para negócios*** | A sua aplicação deve satisfazer os seguintes requisitos.<ul> <li>Uma app saaS de linha de negócios</li> <li>Processo de negócio focado</li> <li>Direcionado para clientes empresariais</li> <li>Ativar os utilizadores para usar as suas credenciais de trabalho para iniciar sessão, como o nome do utilizador e a palavra-passe</li> </ul> | Necessário |  
| ***Período experimental gratuito e experiência experimental*** | A sua aplicação deve incluir uma das seguintes opções para que um cliente utilize a sua aplicação gratuitamente por um período limitado.<ul> <li>Forneça `try` um método, para que os clientes possam iniciar um teste da sua app dentro do AppSource</li> <li>Forneça `request trial` uma opção no AppSource, para que os clientes possam solicitar uma versão experimental da sua app</li> </ul>O teste gratuito que fornece deve oferecer ao cliente uma quantidade pré-definida de tempo para experimentar a sua aplicação sem custos adicionais. | Necessário |  
| ***Solução facilmente configurável e pronta a usar*** | A sua aplicação deve ser fácil e rápida de configurar e configurar sem necessidade de personalização. | Necessário |  
| ***Gestão de oportunidades potenciais*** | Ative o seu CRM a aceitar dados de chumbo antes de receber os cabos da montra.<ul> <li>Exemplos de CRMs são Marketo, Microsoft Dynamics ou Salesforce</li> </ul> | Necessário |  
| ***Política de privacidade e termos de utilização*** | A sua aplicação deve fornecer um link para a sua página de política de privacidade usando um URL público. Os seus termos de utilização devem ser fornecidos durante a publicação como texto. | Necessário |  
| ***Suporte*** | A sua aplicação deve fornecer um link para a sua página de apoio ao cliente utilizando um URL público. Se a sua aplicação for um teste, então não deve suportar a custo adicional durante o período experimental. | Necessário |  

## <a name="storefront-requirements-azure-marketplace"></a>Requisitos de montra: Azure Marketplace  
Seguem-se os requisitos pré-requisitos para os tipos de listagem no Mercado Azure.  

| Requisito | Detalhes | Tipo de listagem |  
|:--- |:--- |:--- |  
| ***Políticas de participação*** | A sua aplicação deve seguir as políticas de participação do Azure Marketplace.<ul> <li>Para mais informações sobre as políticas de participação, visite a página políticas de participação do Azure Marketplace localizada em [azure.microsoft.com/support/legal/marketplace/participation-policies.](https://azure.microsoft.com/support/legal/marketplace/participation-policies)</li></ul> | list<br />transacionado<br />trial |  
| ***Integração com a Microsoft*** | A sua oferta deve utilizar ou alargar os tipos de serviços do Microsoft Azure, tais como computação, networking ou armazenamento. A sua oferta deve alinhar-se com uma categoria de Azure Marketplace existente, como bases de dados, segurança ou networking.<ul> <li>Para mais informações sobre ofertas do Marketplace, visite a página de Aplicações do Marketplace localizada em [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />transacionado<br />trial |  
| ***Audiência*** | A sua oferta deve ser para profissionais de TI, desenvolvedores de nuvem ou outras funções técnicas para o cliente. | list<br />transacionado<br />trial |  
| ***Gestão de oportunidades potenciais*** | Ative o seu CRM (Marketo, Microsoft Dynamics ou Salesforce) para aceitar dados de chumbo antes de receber os leads da montra. | list<br />transacionado<br />trial |  
| ***Política de privacidade e termos de utilização*** | A sua aplicação deve fornecer um link para a sua página de política de privacidade usando um URL público. Os seus termos de utilização devem ser fornecidos durante a publicação como texto. | list<br />transacionado<br />trial |  
| ***Suporte*** | A sua oferta deve fornecer um link para a sua página de apoio ao cliente usando um URL público. Se a sua oferta for um julgamento, então não deve apoiar a custo adicional durante o período experimental. | transacionado<br />trial |    

## <a name="non-transact-listings"></a>Listas não transacionais  
Esta secção descreve todos os tipos de oferta que não utilizam o tipo de listagem Transact. 

### <a name="list"></a>Lista  
O tipo de listagem de listas inclui os seguintes tipos de Oferta nas montras no mercado.  

| Tipo de oferta | Montra | Detalhes |  
|:---        |:---        |:---     |  
| Serviços de Consultoria | AppSource | Requisitos: AppSource: Lista: Serviços de Consultoria |  
| Serviços de Consultoria | Azure Marketplace | Requisitos: Azure Marketplace: Lista: Serviços de Consultoria |  
| Contacte-me | AppSource | [](#) |  
| Contacte-me | Azure Marketplace | Requisitos: AppSource: Lista: Contacte-me |  

#### <a name="requirements-appsource-list-consulting-service"></a>Requisitos: AppSource: Lista: Serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características da oferta de serviço | O seu serviço de consultoria deve cumprir os seguintes critérios.<ul> <li>Entregar um compromisso de âmbito fixo, de duração fixa, de preço fixo (ou gratuito).</li> <li>Oriente principalmente para pré-vendas.</li> <li>Limite a um único cliente.</li> <li>Conduta no local.</li> </ul> |  
| Requisitos de parceiropara serviços de consultoria | Preenche os critérios na área relevante para o seu serviço.<table><tr><th>Área de Solução</th><th>Critérios</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Tenha competência de Gestão de Relacionamento com Clientes silver ou Gold Cloud.</td></tr><tr><td>Dinâmica 365 para Finanças e Operações, edição da Enterprise</td><td>Tenha competência de Planeamento de Recursos empresariais prateados ou dourados e receitas das suas operações na nuvem nos 12 meses de $25.000 ou mais.</td></tr><tr><td>Dinâmica 365 para Finanças e Operações, Edição de Negócios</td><td>Sirva como Fornecedor de Serviços cloud (CSP) ou Parceiro Digital de Record (DPOR) para um ou mais clientes.</td></tr><tr><td>Power BI</td><td>Cumprir os critérios do Parceiro de Solução.</td></tr><tr><td>PowerApps</td><td>Tenha uma solução Partner Showcase.</td></tr></table><ul> <li>Para mais informações sobre a gestão da relação com o cliente, visite a página cloud Customer Relationship Management localizada em [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Para mais informações sobre o planeamento de recursos, visite a página de Planeamento de Recursos Empresariais localizada em [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Para mais informações sobre o CSP, visite a página cloud Services Provider localizada em [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Para mais informações sobre a DPOR, visite a página Digital Partner of Record and Partner Association localizada na [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Para obter mais informações sobre critérios de parceiro de solução, visite o documento de visão geral e incentivos do Parceiro de Solução localizado em [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Para mais informações sobre a montra do parceiro, visite a página Partner Showcase localizada em [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Requisitos: Azure Marketplace: Lista: Serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características da oferta de serviço | O seu serviço de consultoria deve cumprir os seguintes critérios.<ul> <li>Entregar um compromisso de âmbito fixo, de duração fixa, de preço fixo (ou gratuito).</li> <li>Oriente principalmente para pré-vendas.</li> <li>Limite a um único cliente.</li> <li>Conduta no local.</li> </ul> |  
| Requisitos de parceiropara serviços de consultoria | Deve ter prata ou ouro numa das seguintes competências na área relevante para o seu serviço. <table><tr><th>Área de Solução</th><th>Competência</th></tr><td>Plataforma e Infraestruturas cloud</td><td>Plataforma Cloud<br />Data Center</td><tr><td>Desenvolvimento de Aplicações e ISV</td><td>Desenvolvimento de Aplicações<br />Integração de Aplicações<br />DevOps</td></tr><tr><td>Gestão de Dados e Análise</td><td>Análise de Dados<br />Plataforma de Dados</td></tr></table><ul> <li>Para mais informações sobre competências, visite as Competências através da página microsoft Partner Network localizada em [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Para mais informações sobre a listagem, visite a página azure Marketplace Consulting Services localizada em [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Avaliação  

| Tipo de oferta | Montra | Detalhes |  
|:---        |:---        |:---     |  
| Teste gratuito / SaaS | AppSource | Requisitos do tipo de listagem: Trial |  
| Teste gratuito / SaaS | Azure Marketplace | Requisitos: Azure Marketplace: Trial: Free trial / SaaS trial |  
| Demonstração interativa | AppSource | Requisitos do tipo de listagem: Trial |  
| Demonstração interativa | Azure Marketplace | [Requisitos: Azure Marketplace: Trial: Demonstração interativa](#requirements-azure-marketplace-trial-interactive-demo) |  
| Test drive | AppSource | Requisitos do tipo de listagem: Trial |  
| Test drive | Azure Marketplace | [Requisitos: Azure Marketplace: Trial: Test drive](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Requisitos: Azure Marketplace: Trial  

| Requisito | Detalhes |  
|:--- |:--- |  
| Período experimental gratuito e experiência experimental | O seu cliente pode utilizar gratuitamente a sua aplicação por um período limitado.<br /><br />O seu cliente não é obrigado a pagar nenhuma licença ou taxas de subscrição pela sua oferta ou app. O seu cliente não é obrigado a pagar pelo produto ou serviço de primeira parte da Microsoft subjacente. Todas as opções de teste estão implementadas para a sua subscrição Azure. Tem o único controlo da otimização e gestão de custos.<br /><br />Você pode escolher um teste gratuito, demonstração interativa ou test drive. Não importa o que escolha, o seu teste gratuito deve oferecer ao cliente uma quantidade pré-definida de tempo para experimentar a app sem custos adicionais.<ul> <li>Para iniciar o processo de criação de [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)um test drive, envie um e-mail para .</li> </ul>Nota: As experiências experimentais Azure Marketplace SaaS devem permitir que os clientes utilizem credenciais de trabalho para iniciar sessão.<ul> <li>Para mais informações, visite a secção de experiências experimentais AppSource localizada em [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Solução facilmente configurável e pronta a usar | A sua aplicação deve ser fácil e rápida de configurar e configurar. |  
| Disponibilidade / uptime | A sua app ou plataforma SaaS deve ter um tempo de ato de pelo menos 99,9%. |  
| Azure Active Directory | A sua oferta deve permitir que o Azure Ative Directory (Azure AD) tenha um único sinal (SSO) federado (SSO) com o consentimento habilitado. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Requisitos: Azure Marketplace: Trial: Free trial / SaaS trial  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que um cliente experimente o seu produto antes de comprar com um método automatizado de conversão para uso pago. Também permite provas de conceito para o cliente e envolvimento conjunto com as equipas de vendas da Microsoft. | A sua solução é uma máquina virtual ou modelo de solução.<br /><br />A sua solução é uma oferta SaaS e oferece um produto SaaS multiarrendatário.<br /><br />Tem uma experiência de primeira execução para pôr um cliente a funcionar rapidamente.<br /><br />Você tem um único inquilino, mas está adicionando clientes como utilizadores convidados. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Requisitos: Azure Marketplace: Trial: Demonstração interativa  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que os seus clientes vejam a sua solução em ação sem a complexidade da configuração. | A sua solução requer configurações complexas que seriam difíceis de alcançar dentro do período experimental. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Requisitos: Azure Marketplace: Trial: Test drive  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que um cliente experimente o seu produto antes de comprar.<br /><br />Fornece uma experiência guiada da sua solução utilizando configurações pré-configuradas.<br /><br />Os seguintes são benefícios adicionais ao utilizar um test drive.<ul> <li>27% das pesquisas de utilizadores no mercado são refinadas pelos utilizadores para apenas mostrar ofertas com test drives.</li> <li>As ofertas com test drives geram 38% mais leads do que ofertas sem.</li> <li>36% das novas aquisições de clientes no mercado provêm de clientes que fizeram um test drive.</li> <li>As unidades de teste permitem que os vendedores de campo da Microsoft compreendam melhor o seu produto para esforços de co-venda.</li> </ul> | A sua solução é uma máquina virtual, modelo de solução ou app SaaS com um único inquilino, ou é complicado de fornecer. <br /><br />Não tem um método para converter o seu julgamento numa oferta paga. |  

---

## <a name="transact-specific-listings"></a>Listagens específicas de transações

### <a name="transact"></a>Transt  

| Tipo de oferta | Montra | Detalhes |   
|:---        |:---        | :--- |  
| Aplicativos Azure: App gerida | Azure Marketplace | Requisitos: Azure Marketplace: Transact: Aplicações Azure: App gerida |  
| Aplicativos Azure: Modelo de solução | Azure Marketplace | Requisitos: Azure Marketplace: Transact: Aplicações Azure: Modelo de solução |  
| Contentores | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: Contentor](#requirements-azure-marketplace-transact-container) |  
| App SaaS  | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: App SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Máquina virtual | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: Máquina virtual](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Requisitos: Azure Marketplace: Transact: Contentor  

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Apoie o modelo de faturação gratuito ou BYOL. |  
| Imagem baseada em Estivador | A sua imagem de contentor deve basear-se no formato de imagem do Docker e deve ser retirada dos registos de contentores do Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Requisitos: Azure Marketplace: Transact: App SaaS  

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Sua oferta tem um preço fixo mensal. Os preços baseados na utilização e as opções *de true-up* baseadas na utilização não são suportados neste momento. |  
| Cancelamento | A sua oferta é cancelada pelo cliente a qualquer momento. |  
| Página de aterragem de transações | Hospedar uma página de aterragem de transações de marca Azure. A sua página de aterragem permite aos seus clientes criar e gerir a sua conta de serviço SaaS. |  
| API de subscrição saas | Fornecer um serviço que interage com a Subscrição SaaS para criar, atualizar e eliminar uma conta de utilizador e plano de serviço. Todas as alterações críticas da API devem ser suportadas dentro de 24 horas. Quaisquer alterações não críticas da API são atualizadas periodicamente. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Requisitos: Azure Marketplace: Transact: Máquina virtual  

| Requisito | Detalhes |  
|:--- |:--- | 
| Faturação e medição | O seu VM deve apoiar a faturação mensal BYOL ou Pay-As-You-Go. |  
| Disco rígido virtual compatível com azure (VHD) | Os VMs devem ser construídos em Windows ou Linux.<ul> <li>Para obter mais informações sobre a criação de um VHD Linux, consulte [as distribuições linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD windows, consulte [Criar um VHD compatível com Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Passos seguintes
*   Visite a página do Guia da [Editora Azure Marketplace e AppSource.](./marketplace-publishers-guide.md)  

