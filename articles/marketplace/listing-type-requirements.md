---
title: Requisitos de listando tipo | Azure
description: Este artigo descreve os critérios de qualificação e, de publicação de requisitos de parceiros, que tentar entender como publicar aplicações no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: f88c7b9350d104241524284950865e1f2ca6f559
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729411"
---
# <a name="requirements-by-listing-type"></a>Requisitos de listando tipo  
Os requisitos de conteúdo técnicos e marketing variam de acordo com a loja, o tipo de oferta e o tipo de lista. Reveja as seguintes especificações para verificar a sua conformidade.  
1. Vitrine requisitos:  
    *   [AppSource](#storefront-requirements-appSource)  
    *   [O Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Tipo de listagem e requisitos de tipo de oferta:  
    *   Para obter mais informações sobre os tipos de listagem e tipos de oferta, visite a determinar o tipo de listagem para a página de sua solução localizado em [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Vitrine requisitos: AppSource  
A tabela seguinte descreve os requisitos necessários para publicação no AppSource.  

| Requisito | Detalhes | Obrigatório ou recomendado |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | A aplicação tem de permitir Azure Active Directory, federada início de sessão único (SSO federado do Azure AD) com consentimento ativado.<ul> <li>Para obter mais informações sobre como ativar o Azure AD de SSO federado, visite a configurar início de sessão único para aplicações que não estão no Azure Active Directory página da Galeria de aplicações localizada em [docs.microsoft.com/azure/active-directory/ Active-directory--personalizada-aplicações saas](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Necessário |   
| ***Integração com os serviços de nuvem da Microsoft*** | A aplicação deve integrar com outros serviços Cloud da Microsoft, como serviços do Microsoft Power BI, o Cortana Intelligence ou o Microsoft Azure.<ul> <li>Um exemplo de um serviço Cloud da Microsoft é a Internet das coisas.</li> </ul> | Recomendado |  
| ***Público-alvo*** | A aplicação tem de ser para utilizadores de linha de negócio e os proprietários de empresas. | Necessário | 
| ***Software como uma aplicação de serviço (SaaS) para empresas*** | A aplicação tem de cumprir os seguintes requisitos.<ul> <li>Uma aplicação SaaS de linha de negócio</li> <li>Concentra-se de processo comercial</li> <li>Direcionado a clientes empresariais</li> <li>Permitir que os utilizadores usem suas credenciais de trabalho para iniciar sessão como o nome de utilizador e palavra-passe</li> </ul> | Necessário |  
| ***Período de avaliação gratuita e a experiência de avaliação*** | A aplicação têm de incluir um as seguintes opções para que um cliente utilizar a sua aplicação gratuitamente por um período limitado.<ul> <li>Fornecer um `try` método, para que os clientes podem iniciar uma avaliação da sua aplicação no AppSource</li> <li>Fornecer um `request trial` opção no AppSource, para que os clientes podem solicitar uma versão de avaliação da sua aplicação</li> </ul>A avaliação gratuita, que fornece deve oferecer ao cliente uma quantidade predefinida de tempo de testar seu aplicativo sem custos adicionais. | Necessário |  
| ***Solução facilmente configurável e prontos a utilizar*** | A aplicação tem de ser fácil e rápida configurar e configurar com nenhuma personalização necessária. | Necessário |  
| ***Gestão de oportunidades potenciais*** | Ative o seu sistema de CRM aceitar dados de oportunidades potenciais antes de poder receber leva a partir da loja.<ul> <li>Exemplos de CRMs são Marketo, o Microsoft Dynamics ou o Salesforce</li> </ul> | Necessário |  
| ***Política de privacidade e termos de utilização*** | A aplicação tem de fornecer um link para sua página de política de privacidade através de um URL público. Os termos de utilização tem de ser fornecidos durante a publicação como texto. | Necessário |  
| ***Suporte*** | A aplicação tem de fornecer um link para sua página de suporte de cliente através de um URL público. Se a aplicação for uma versão de avaliação, em seguida, tem de suportar sem custos adicionais durante o período de avaliação. | Necessário |  

## <a name="storefront-requirements-azure-marketplace"></a>Vitrine requisitos: Azure Marketplace  
Seguem-se os requisitos necessários para listar os tipos no Azure Marketplace.  

| Requisito | Detalhes | Tipo de lista |  
|:--- |:--- |:--- |  
| ***Políticas de participação*** | A aplicação tem de seguir as políticas de participação do Azure Marketplace.<ul> <li>Para obter mais informações sobre as políticas de participação, visite as políticas de participação do Azure Marketplace página localizada em [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Transact<br />trial |  
| ***Integração com a Microsoft*** | A oferta deve usar ou estender os tipos de serviço do Microsoft Azure, como computação, rede ou armazenamento. A oferta deve ser alinhado a uma categoria existente do Azure Marketplace, como bases de dados, segurança ou funcionamento em rede.<ul> <li>Para obter mais informações sobre as ofertas do Marketplace, visite a página localizada em aplicações do Marketplace [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />Transact<br />trial |  
| ***Público-alvo*** | A oferta tem de ser para os profissionais de TI, desenvolvedores de nuvem ou outras funções de técnicas de cliente. | list<br />Transact<br />trial |  
| ***Gestão de oportunidades potenciais*** | Ative o CRM (Marketo, Microsoft Dynamics ou Salesforce) aceitar dados de oportunidades potenciais antes de poder receber leva a partir da loja. | list<br />Transact<br />trial |  
| ***Política de privacidade e termos de utilização*** | A aplicação tem de fornecer um link para sua página de política de privacidade através de um URL público. Os termos de utilização tem de ser fornecidos durante a publicação como texto. | list<br />Transact<br />trial |  
| ***Suporte*** | A oferta tem de fornecer um link para sua página de suporte de cliente através de um URL público. Se a sua oferta é uma versão de avaliação, em seguida, tem de suportar sem custos adicionais durante o período de avaliação. | Transact<br />trial |    

## <a name="non-transact-listings"></a>As listagens de não transact  
Esta secção descreve todos os tipos de oferta que não utilizam o Transact listagem tipo. 

### <a name="list"></a>Lista  
A listagem Tipo de lista inclui os seguintes tipos de oferta sobre as lojas no marketplace.  

| Tipo de oferta | Loja | Detalhes |  
|:---        |:---        |:---     |  
| Serviços de Consultoria | AppSource | Requisitos: AppSource: Lista de: Serviços de Consultoria |  
| Serviços de Consultoria | Azure Marketplace | Requisitos: Azure Marketplace: Lista de: Serviços de Consultoria |  
| Contactar-me | AppSource | [](#) |  
| Contactar-me | Azure Marketplace | Requisitos: AppSource: Lista de: Contactar-me |  

#### <a name="requirements-appsource-list-consulting-service"></a>Requisitos: AppSource: Lista de: Serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características da oferta de serviço | O serviço de consultoria têm de cumprir os seguintes critérios.<ul> <li>Fornecer um compromisso de escopo fixo, duração foi corrigido, com preços fixos (ou gratuito).</li> <li>Orientar principalmente para pré-vendas.</li> <li>Limite de um único cliente.</li> <li>Conduzir no site.</li> </ul> |  
| Requisitos de parceiros dos serviços de consultoria | Cumpre os critérios na área de relevante para o seu serviço.<table><tr><th>Área de Solução</th><th>Critérios</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Tem a competência Silver ou Gold Cloud Customer Relationship Management.</td></tr><tr><td>Dynamics 365 para finanças e operações, Enterprise edition</td><td>Ter competência Silver ou Gold Enterprise Resource Planning e a receita de suas operações na cloud em 12 meses de US $25.000 ou mais à direita.</td></tr><tr><td>Dynamics 365 para finanças e operações, Business edition</td><td>Servir como fornecedor de serviços Cloud (CSP) ou o parceiro de registo Digital (DPOR) para um ou mais clientes.</td></tr><tr><td>Power BI</td><td>Cumprem os critérios de parceiro de solução.</td></tr><tr><td>PowerApps</td><td>Ter uma solução de Showcase de parceiros.</td></tr></table><ul> <li>Para obter mais informações sobre a gestão de relação do cliente, visite a Cloud Customer Relationship Management página localizada em [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Para obter mais informações sobre o planeamento de recursos, visite o Enterprise Resource Planning, página localizada em [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Para obter mais informações sobre o CSP, visite o fornecedor de serviços Cloud página localizada em [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Para obter mais informações sobre como DPOR, visite o parceiro Digital de registo e a página de associação de parceiro localizado em [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Para obter mais informações sobre critérios de parceiro de solução, visite a descrição de geral do parceiro de solução e documento incentivos localizado em [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Para obter mais informações sobre o showcase de parceiros, visite o Showcase de parceiros página localizada em [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Requisitos: Azure Marketplace: Lista de: Serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características da oferta de serviço | O serviço de consultoria têm de cumprir os seguintes critérios.<ul> <li>Fornecer um compromisso de escopo fixo, duração foi corrigido, com preços fixos (ou gratuito).</li> <li>Orientar principalmente para pré-vendas.</li> <li>Limite de um único cliente.</li> <li>Conduzir no site.</li> </ul> |  
| Requisitos de parceiros dos serviços de consultoria | Tem de ter gold ou silver dos seguintes competências na área de relevante para o seu serviço. <table><tr><th>Área de Solução</th><th>Competência</th></tr><td>Plataforma em nuvem e infraestrutura</td><td>Plataforma na cloud<br />Data Center</td><tr><td>Desenvolvimento de aplicativos e de ISV</td><td>Desenvolvimento de Aplicações<br />Integração de Aplicações<br />DevOps</td></tr><tr><td>Gestão de dados e análise</td><td>Análise de Dados<br />Data Platform</td></tr></table><ul> <li>Para obter mais informações sobre competências, visite competências através do Microsoft Partner Network página localizada em [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Para obter mais informações sobre a listagem, visite o Azure Marketplace dos serviços de consultoria página localizada em [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Avaliação  

| Tipo de oferta | Loja | Detalhes |  
|:---        |:---        |:---     |  
| Gratuitos / avaliação SaaS | AppSource | Requisitos de tipo de listagem: Avaliação |  
| Gratuitos / avaliação SaaS | Azure Marketplace | Requisitos: Azure Marketplace: Versão de avaliação: Avaliação gratuita / avaliação SaaS |  
| Demo interativa | AppSource | Requisitos de tipo de listagem: Avaliação |  
| Demo interativa | Azure Marketplace | [Requisitos: Azure Marketplace: Versão de avaliação: Demonstração interativa](#requirements-azure-marketplace-trial-interactive-demo) |  
| Versão de teste | AppSource | Requisitos de tipo de listagem: Avaliação |  
| Versão de teste | Azure Marketplace | [Requisitos: Azure Marketplace: Versão de avaliação: Versão de teste](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Requisitos: Azure Marketplace: Avaliação  

| Requisito | Detalhes |  
|:--- |:--- |  
| Período de avaliação gratuita e a experiência de avaliação | Seu cliente pode utilizar a sua aplicação gratuitamente por um período limitado.<br /><br />Seu cliente não é necessário pagar quaisquer taxas de licenciamento ou de subscrição para a sua oferta ou a aplicação. Seu cliente não é necessário para pagar o produto de original Microsoft subjacente ou serviço. Todas as opções de avaliação são implementadas para a sua subscrição do Azure. Tem a versão de avaliação de controlo único da otimização de custos e a gestão.<br /><br />Pode escolher uma versão de avaliação gratuita, demonstração interativa ou faça o test drive. Não importa o que escolha, a sua avaliação gratuita deve oferecer ao cliente uma quantidade predefinida de tempo para experimentar a aplicação sem custos adicionais.<ul> <li>Para iniciar o processo de criação de uma versão de teste, envie um e-mail para [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Nota: Experiências de avaliação de Marketplace SaaS do Azure têm de permitir aos clientes utilizar credenciais de trabalho para iniciar sessão.<ul> <li>Para obter mais informações, visite o AppSource secção de experiências de avaliação localizada em [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Solução facilmente configurável e prontos a utilizar | A aplicação tem de ser fácil e rápida configurar e configurar. |  
| Disponibilidade / tempo de atividade | A aplicação de SaaS ou plataforma tem de ter um tempo de atividade de, pelo menos, 99,9%. |  
| Azure Active Directory | A oferta tem de permitir a que Azure Active Directory (Azure AD) federadas início de sessão único (SSO) (SSO federado do Azure AD) com o consentimento ativado. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Requisitos: Azure Marketplace: Versão de avaliação: Avaliação gratuita / avaliação SaaS  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que um cliente experimentar seu produto antes de comprar com um método automatizado para a conversão para paga a utilização. Também permite que provas de conceito para o cliente e envolvimento conjunto com equipas de vendas da Microsoft. | A sua solução é uma máquina virtual ou modelo de solução.<br /><br />A sua solução é uma oferta de SaaS e oferecer um produto de SaaS multi-inquilino.<br /><br />Tem uma experiência de primeira execução para um cliente se pôr em funcionamento rapidamente.<br /><br />Tem um único inquilino, mas estiver a adicionar clientes como os utilizadores convidados. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Requisitos: Azure Marketplace: Versão de avaliação: Demo interativa  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite aos seus clientes ver a sua solução em ação sem a complexidade da configuração. | A solução requer definições complexas que seriam difíceis de alcançar dentro do período de avaliação. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Requisitos: Azure Marketplace: Versão de avaliação: Versão de teste  

| Vantagem | Requisito |  
|:--- |:--- |  
| Permite que um cliente experimentar seu produto antes que compraram.<br /><br />Fornece uma experiência orientada da sua solução com as definições pré-configuradas.<br /><br />Seguem-se benefícios adicionais ao utilizar uma versão de teste.<ul> <li>27% das pesquisas de utilizador no marketplace são refinadas de acordo com os utilizadores para além de mostrar oferecer com versões de teste.</li> <li>As ofertas versões de teste geram oportunidades potenciais de 38% mais do que ofertas sem.</li> <li>% de 36, de aquisições de cliente de novo no marketplace são provenientes os clientes que demorou um test drive.</li> <li>Versões de teste permitem vendedores no terreno Microsoft compreender melhor o seu produto para os esforços de venda conjunta.</li> </ul> | A solução é uma máquina virtual, o modelo de solução ou a aplicação SaaS com um único inquilino ou é complicada para aprovisionar. <br /><br />Não tem um método para converter a versão de avaliação numa oferta paga. |  

---

## <a name="transact-specific-listings"></a>Listagens de Transact-específicas

### <a name="transact"></a>Transact  

| Tipo de oferta | Loja | Detalhes |   
|:---        |:---        | :--- |  
| Aplicações do Azure: Aplicação gerida | Azure Marketplace | Requisitos: Azure Marketplace: Transact: Aplicações do Azure: Aplicação gerida |  
| Aplicações do Azure: Modelo de solução | Azure Marketplace | Requisitos: Azure Marketplace: Transact: Aplicações do Azure: Modelo de solução |  
| Contentores | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| Aplicação SaaS  | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: Aplicação SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Máquina virtual | Azure Marketplace | [Requisitos: Azure Marketplace: Transact: Máquina virtual](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Requisitos: Azure Marketplace: Transact: Contentor  

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Suporta qualquer um dos gratuito ou modelo de faturação de BYOL. |  
| Imagem do docker | A imagem de contentor têm de ser baseada no formato de imagem de Docker e têm de ser extraída de registos de contentores do Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Requisitos: Azure Marketplace: Transact: Aplicação SaaS  

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | A oferta é o preço de uma taxa fixa mensal. Baseada na utilização e baseada na utilização de preços *verdadeiramente* opções não são suportadas neste momento. |  
| Cancelamento | A oferta é pode ser cancelada pelo cliente em qualquer altura. |  
| Página de destino de transação | Aloje uma página de destino de transação de associação de marcas do Azure. A página de destino permite aos seus clientes criar e gerir a sua conta de serviço SaaS. |  
| Subscrição de SaaS API | Fornece um serviço que interage com a subscrição de SaaS para criar, atualizar e eliminar um plano de serviço e de conta de utilizador. Todas as alterações de API críticas têm de ser suportadas dentro de 24 horas. Quaisquer alterações de API não críticas são atualizadas periodicamente. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Requisitos: Azure Marketplace: Transact: Máquina virtual  

| Requisito | Detalhes |  
|:--- |:--- | 
| Faturação e medição | A VM tem de suportar uma faturação mensal BYOL ou pay as you go. |  
| Compatível com o Azure de disco rígido virtual (VHD) | As VMs devem ser criadas no Windows ou Linux.<ul> <li>Para obter mais informações sobre a criação de um VHD do Linux, consulte [distribuições do Linux apoiadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD do Windows, consulte [criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Passos Seguintes
*   Visite o [Azure Marketplace e guia de publicador do AppSource](./marketplace-publishers-guide.md) página.  

