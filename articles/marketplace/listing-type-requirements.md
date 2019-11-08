---
title: Requisitos por listagem tipo | Azure
description: Este artigo descreve os critérios de qualificação e os parceiros de requisitos de publicação que estão tentando entender como publicar aplicativos no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: b9a2e8b37f746eb86b2e0fe6a7304cd888a293e1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823039"
---
# <a name="requirements-by-listing-type"></a>Requisitos por tipo de listagem  
Os requisitos de conteúdo técnico e de marketing variam por vitrine, tipo de oferta e tipo de listagem. Examine as especificações a seguir para verificar sua conformidade.  
1. Requisitos de vitrine:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Requisitos de tipo de listagem e tipo de oferta:  
    *   Para obter mais informações sobre tipos de listagem e tipos de oferta, visite a página determinar o tipo de listagem para sua solução, localizado em [docs.Microsoft.com/Azure/Marketplace/determine-Your-Listing-Type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Requisitos de vitrine: AppSource  
A tabela a seguir descreve os requisitos de pré-requisito para a publicação no AppSource.  

| Requisito | Detalhes | Obrigatório ou recomendado |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Seu aplicativo deve permitir logon único federado Azure Active Directory (SSO Federado do Azure AD) com consentimento habilitado.<ul> <li>Para obter mais informações sobre como habilitar o SSO Federado do Azure AD, visite a página Configurando logon único para aplicativos que não estão na Azure Active Directory Galeria de aplicativos localizada em [docs.Microsoft.com/Azure/Active-Directory/Active-Directory-SaaS-Custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Necessário |   
| ***Integração com serviços de Microsoft Cloud*** | Seu aplicativo deve ser integrado a outros serviços de Microsoft Cloud, como Microsoft Power BI, Cortana Intelligence ou serviços Microsoft Azure.<ul> <li>Um exemplo de um serviço de Microsoft Cloud é Internet das Coisas.</li> </ul> | Recomendado |  
| ***Platéia*** | Seu aplicativo deve ser para usuários de linha de negócios e proprietários de negócios. | Necessário | 
| ***Aplicativo SaaS (software como serviço) para negócios*** | Seu aplicativo deve atender aos seguintes requisitos.<ul> <li>Um aplicativo SaaS de linha de negócios</li> <li>Foco nos processos de negócios</li> <li>Direcionado a clientes de negócios</li> <li>Permitir que os usuários usem suas credenciais de trabalho para entrar, como nome de usuário e senha</li> </ul> | Necessário |  
| ***Período de avaliação gratuita e experiência de avaliação*** | Seu aplicativo deve incluir uma das opções a seguir para que um cliente use seu aplicativo gratuitamente por um período limitado.<ul> <li>Forneça um método `try`, para que os clientes possam iniciar uma avaliação de seu aplicativo no AppSource</li> <li>Forneça uma opção de `request trial` no AppSource, para que os clientes possam solicitar uma versão de avaliação do seu aplicativo</li> </ul>A avaliação gratuita que você fornece deve oferecer ao cliente uma quantidade predefinida de tempo para experimentar seu aplicativo sem custo adicional. | Necessário |  
| ***Solução facilmente configurável e pronta para uso*** | Seu aplicativo deve ser fácil e rápido de configurar e configurar sem a necessidade de personalização. | Necessário |  
| ***Gestão de oportunidades potenciais*** | Habilite seu CRM para aceitar dados de Lead antes de receber clientes potenciais da vitrine.<ul> <li>Exemplos de CRMs são Marketo, Microsoft Dynamics ou Salesforce</li> </ul> | Necessário |  
| ***Política de privacidade e termos de uso*** | Seu aplicativo deve fornecer um link para sua página de política de privacidade usando uma URL pública. Os termos de uso devem ser fornecidos durante a publicação como texto. | Necessário |  
| ***Suporte*** | Seu aplicativo deve fornecer um link para a página de suporte ao cliente usando uma URL pública. Se seu aplicativo for uma avaliação, você deverá oferecer suporte sem nenhum custo adicional durante o período de avaliação. | Necessário |  

## <a name="storefront-requirements-azure-marketplace"></a>Requisitos de vitrine: Azure Marketplace  
Veja a seguir os requisitos de pré-requisito para a listagem de tipos no Azure Marketplace.  

| Requisito | Detalhes | Tipo de listagem |  
|:--- |:--- |:--- |  
| ***Políticas de participação*** | Seu aplicativo deve seguir as políticas de participação do Azure Marketplace.<ul> <li>Para obter mais informações sobre as políticas de participação, visite a página de políticas de participação do Azure Marketplace localizada em [Azure.Microsoft.com/support/legal/Marketplace/participation-Policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />comercializa<br />trial |  
| ***Integração com a Microsoft*** | Sua oferta deve usar ou estender Microsoft Azure tipos de serviço como computação, rede ou armazenamento. Sua oferta deve ser alinhada a uma categoria existente do Azure Marketplace, como bancos de dados, segurança ou rede.<ul> <li>Para obter mais informações sobre ofertas do Marketplace, visite a página de aplicativos do Marketplace localizada em [azuremarketplace.Microsoft.com/Marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />comercializa<br />trial |  
| ***Platéia*** | Sua oferta deve ser para profissionais de ti, desenvolvedores de nuvem ou outras funções de cliente técnico. | list<br />comercializa<br />trial |  
| ***Gestão de oportunidades potenciais*** | Habilite seu CRM (Marketa, Microsoft Dynamics ou Salesforce) para aceitar dados de Lead antes de receber clientes potenciais da vitrine. | list<br />comercializa<br />trial |  
| ***Política de privacidade e termos de uso*** | Seu aplicativo deve fornecer um link para sua página de política de privacidade usando uma URL pública. Os termos de uso devem ser fornecidos durante a publicação como texto. | list<br />comercializa<br />trial |  
| ***Suporte*** | Sua oferta deve fornecer um link para a página de suporte ao cliente usando uma URL pública. Se sua oferta for uma avaliação, você deverá oferecer suporte sem nenhum custo adicional durante o período de avaliação. | comercializa<br />trial |    

## <a name="non-transact-listings"></a>Listagens não transactveis  
Esta seção descreve todos os tipos de oferta que não usam o tipo de listagem do Transact. 

### <a name="list"></a>Lista  
O tipo de listagem de lista inclui os seguintes tipos de oferta nas vitrines do Marketplace.  

| Tipo de oferta | Loja | Detalhes |  
|:---        |:---        |:---     |  
| Serviços de consultoria | AppSource | Requisitos: AppSource: lista: serviços de consultoria |  
| Serviços de consultoria | Azure Marketplace | Requisitos: Azure Marketplace: lista: serviços de consultoria |  
| Entre em contato comigo | AppSource | [](#) |  
| Entre em contato comigo | Azure Marketplace | Requisitos: AppSource: lista: entre em contato comigo |  

#### <a name="requirements-appsource-list-consulting-service"></a>Requisitos: AppSource: lista: serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características da oferta de serviço | Seu serviço de consultoria deve atender aos critérios a seguir.<ul> <li>Forneça um compromisso de escopo fixo, de duração fixa, de preço fixo (ou gratuito).</li> <li>Oriente-se principalmente para pré-vendas.</li> <li>Limite para um único cliente.</li> <li>Conduta no site.</li> </ul> |  
| Requisitos de parceiro para serviços de consultoria | Você atende aos critérios na área relevante para seu serviço.<table><tr><th>Área da solução</th><th>Critérios</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Tenha competência de gerenciamento de relacionamento com o cliente de nuvem prata ou ouro.</td></tr><tr><td>Dynamics 365 para finanças e operações, Enterprise Edition</td><td>Tenha competência de planejamento de recursos empresariais prata ou ouro e receita de suas operações de nuvem nos últimos 12 meses de $25000 ou mais.</td></tr><tr><td>Dynamics 365 para finanças e operações, Business Edition</td><td>Servir como CSP (provedor de serviços de nuvem) ou DPOR (parceiro de registro digital) para um ou mais clientes.</td></tr><tr><td>Power BI</td><td>Atenda aos critérios de parceiro da solução.</td></tr><tr><td>PowerApps</td><td>Ter uma solução de demonstração de parceiro.</td></tr></table><ul> <li>Para obter mais informações sobre o gerenciamento de relacionamento com o cliente, visite a página de gerenciamento de relacionamento com o cliente de nuvem localizada em [Partner.Microsoft.com/Membership/Cloud-Customer-Relationship-Management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Para obter mais informações sobre o planejamento de recursos, visite a página de planejamento de recursos empresariais localizada em [Partner.Microsoft.com/Membership/Enterprise-Resource-Planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Para obter mais informações sobre o CSP, visite a página do provedor de serviços de nuvem localizada em [Partner.Microsoft.com/Cloud-Solution-Provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Para obter mais informações sobre DPOR, visite a página parceiro digital de registro e Associação de parceiro localizada em [Partner.Microsoft.com/Membership/digital-Partner-of-Record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Para obter mais informações sobre os critérios de parceiro de solução, visite o documento visão geral do parceiro de solução e incentivos localizado em [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?sourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Para obter mais informações sobre o Partner Showcase, visite a página de demonstração de parceiro localizada em [powerapps.Microsoft.com/Partner-Showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Requisitos: Azure Marketplace: lista: serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características da oferta de serviço | Seu serviço de consultoria deve atender aos critérios a seguir.<ul> <li>Forneça um compromisso de escopo fixo, de duração fixa, de preço fixo (ou gratuito).</li> <li>Oriente-se principalmente para pré-vendas.</li> <li>Limite para um único cliente.</li> <li>Conduta no site.</li> </ul> |  
| Requisitos de parceiro para serviços de consultoria | Você deve ter prata ou ouro em uma das competências a seguir na área relevante para seu serviço. <table><tr><th>Área da solução</th><th>Capacita</th></tr><td>Infraestrutura e plataforma de nuvem</td><td>Plataforma de nuvem<br />Data Center</td><tr><td>Desenvolvimento de aplicativos e ISV</td><td>Desenvolvimento de Aplicações<br />Integração de Aplicações<br />DevOps</td></tr><tr><td>Gerenciamento de Dados e análise</td><td>Análise de Dados<br />Data Platform</td></tr></table><ul> <li>Para obter mais informações sobre competências, visite a página competências por meio de Microsoft Partner Network localizada em [Partner.Microsoft.com/Membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Para obter mais informações sobre a listagem, visite a página de serviços de consultoria do Azure Marketplace localizada em [docs.Microsoft.com/Azure/Marketplace/Consulting-Services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Avaliação  

| Tipo de oferta | Loja | Detalhes |  
|:---        |:---        |:---     |  
| Avaliação gratuita/SaaS | AppSource | Listando requisitos de tipo: avaliação |  
| Avaliação gratuita/SaaS | Azure Marketplace | Requisitos: Azure Marketplace: avaliação: avaliação gratuita/versão de avaliação de SaaS |  
| Demo interativa | AppSource | Listando requisitos de tipo: avaliação |  
| Demo interativa | Azure Marketplace | [Requisitos: Azure Marketplace: avaliação: demonstração interativa](#requirements-azure-marketplace-trial-interactive-demo) |  
| Versão de teste | AppSource | Listando requisitos de tipo: avaliação |  
| Versão de teste | Azure Marketplace | [Requisitos: Azure Marketplace: avaliação: Test Drive](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Requisitos: Azure Marketplace: avaliação  

| Requisito | Detalhes |  
|:--- |:--- |  
| Período de avaliação gratuita e experiência de avaliação | Seu cliente pode usar seu aplicativo gratuitamente por um período limitado.<br /><br />Seu cliente não precisa pagar nenhuma licença ou taxa de assinatura para sua oferta ou aplicativo. Seu cliente não precisa pagar pelo produto ou serviço subjacente da Microsoft. Todas as opções de avaliação são implantadas em sua assinatura do Azure. Você tem uma única avaliação de controle da otimização e do gerenciamento de custos.<br /><br />Você pode escolher uma avaliação gratuita, demonstração interativa ou test drive. Não importa o que você escolher, sua avaliação gratuita deve oferecer ao cliente uma quantidade predefinida de tempo para experimentar o aplicativo sem custo adicional.<ul> <li>Para iniciar o processo de criação de um test drive, envie um email para [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Observação: as experiências de avaliação do SaaS do Azure Marketplace devem permitir que os clientes usem as credenciais de trabalho para entrar.<ul> <li>Para obter mais informações, visite a seção experiências de avaliação do AppSource localizada em [docs.Microsoft.com/Azure/Active-Directory/develop/Active-Directory-devhowto-appsource-Certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Solução facilmente configurável e pronta para uso | Seu aplicativo deve ser fácil e rápido de configurar e configurar. |  
| Disponibilidade/tempo de atividade | Seu aplicativo ou plataforma SaaS deve ter um tempo de atividade de pelo menos 99,9%. |  
| Azure Active Directory | Sua oferta deve permitir o Azure Active Directory (SSO (logon único) federado (SSO) do Azure AD (Azure Active Directory) com consentimento habilitado. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Requisitos: Azure Marketplace: avaliação: avaliação gratuita/versão de avaliação de SaaS  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que um cliente Experimente seu produto antes de comprar com um método automatizado para converter para uso pago. Também permite provas de conceito para o cliente e o envolvimento conjunto com as equipes de vendas da Microsoft. | Sua solução é uma máquina virtual ou modelo de solução.<br /><br />Sua solução é uma oferta de SaaS e você oferece um produto SaaS multilocatário.<br /><br />Você tem uma experiência de primeira execução para colocar um cliente em funcionamento rapidamente.<br /><br />Você tem um único locatário, mas está adicionando clientes como usuários convidados. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Requisitos: Azure Marketplace: avaliação: demonstração interativa  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que seus clientes vejam sua solução em ação sem a complexidade de configurar. | Sua solução requer configurações complexas que seriam difíceis de alcançar dentro do período de avaliação. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Requisitos: Azure Marketplace: avaliação: Test Drive  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que um cliente Experimente seu produto antes de comprar.<br /><br />Fornece uma experiência guiada de sua solução usando configurações pré-configuradas.<br /><br />Veja a seguir os benefícios adicionais ao usar um test drive.<ul> <li>27% das pesquisas de usuário no Marketplace são refinadas pelos usuários para mostrar apenas ofertas com unidades de teste.</li> <li>Ofertas com unidades de teste geram 38% a mais clientes potenciais do que as ofertas sem.</li> <li>36% das novas aquisições de clientes no Marketplace são provenientes de clientes que levaram um test drive.</li> <li>As unidades de teste permitem que os vendedores de campo da Microsoft entendam melhor seu produto para os esforços de venda conjunta.</li> </ul> | Sua solução é uma máquina virtual, um modelo de solução ou um aplicativo SaaS com um único locatário ou é complicado de provisionar. <br /><br />Você não tem um método para converter sua avaliação em uma oferta paga. |  

---

## <a name="transact-specific-listings"></a>Listagens específicas do Transact

### <a name="transact"></a>Transact  

| Tipo de oferta | Loja | Detalhes |   
|:---        |:---        | :--- |  
| Aplicativos do Azure: aplicativo gerenciado | Azure Marketplace | Requisitos: Azure Marketplace: Transact: aplicativos do Azure: aplicativo gerenciado |  
| Aplicativos do Azure: modelo de solução | Azure Marketplace | Requisitos: Azure Marketplace: Transact: aplicativos do Azure: modelo de solução |  
| Contentores | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| Aplicativo SaaS  | Azure Marketplace | [Requisitos: Azure Marketplace: aplicativo Transact: SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Máquina virtual | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: máquina virtual](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Requisitos: Azure Marketplace: Transact: Container  

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Suporte ao modelo de cobrança gratuito ou BYOL. |  
| Imagem baseada em Docker | A imagem de contêiner deve ser baseada no formato de imagem do Docker e deve ser extraída dos registros de contêiner do Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Requisitos: Azure Marketplace: aplicativo Transact: SaaS  

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Sua oferta é cobrada a uma taxa fixa mensal. Não há suporte para os preços baseados em uso e as opções de *verdadeiro* com base no uso no momento. |  
| Cancelamento | Sua oferta é cancelável pelo cliente a qualquer momento. |  
| Página de aterrissagem da transação | Hospede uma página de aterrissagem de transação de marca do Azure. Sua página de aterrissagem permite que seus clientes criem e gerenciem sua conta de serviço SaaS. |  
| API de assinatura de SaaS | Forneça um serviço que interaja com a assinatura de SaaS para criar, atualizar e excluir uma conta de usuário e um plano de serviço. Todas as alterações de API críticas devem ter suporte dentro de 24 horas. Todas as alterações de API não críticas são atualizadas periodicamente. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Requisitos: Azure Marketplace: Transact: máquina virtual  

| Requisito | Detalhes |  
|:--- |:--- | 
| Cobrança e medição | Sua VM deve dar suporte a cobrança mensal BYOL ou paga conforme o uso. |  
| VHD (disco rígido virtual) compatível com o Azure | As VMs devem ser criadas no Windows ou no Linux.<ul> <li>Para obter mais informações sobre como criar um VHD do Linux, consulte [distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre como criar um VHD do Windows, consulte [criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Passos seguintes
*   Visite a página do [Guia do Azure Marketplace e do AppSource Publisher](./marketplace-publishers-guide.md) .  

