---
title: Azure AD assegura acesso híbrido com | F5 Microsoft Docs
description: F5 BIG-IP Access Policy Manager e integração do Azure Ative Directory para acesso híbrido seguro
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8210e00824d7680f4eecde2f0b299dfcdc93b90
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730576"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Policy Manager e integração do Azure Ative Directory para acesso híbrido seguro

A proliferação da mobilidade e o cenário de ameaças em evolução estão a colocar um escrutínio extra no acesso e governação dos recursos, colocando [a Zero Trust](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) na frente e no centro de todos os programas de modernização.
Na Microsoft e F5, percebemos que esta transformação digital é tipicamente uma viagem de vários anos para qualquer negócio, potencialmente deixando recursos críticos expostos até ser modernizado. A génese por trás do F5 BIG-IP e do Azure Ative Directory Secure Hybrid Access (SHA) visa não só melhorar o acesso remoto às aplicações no local, mas também reforçar a postura de segurança global destes serviços vulneráveis.

Para o contexto, a investigação estima que 60-80% das aplicações no local são de natureza de natureza, ou seja, incapazes de ser integradas diretamente com o Azure Ative Directory (AD). O mesmo estudo também indicou que uma grande parte destes sistemas funciona em versões de baixo nível de SAP, Oracle, SAGE e outras cargas de trabalho bem conhecidas que fornecem serviços críticos.

A SHA aborda este ponto cego, permitindo que as organizações continuem a utilizar os seus investimentos F5 para uma rede superior e entrega de aplicações. Combinado com Azure AD, faz a ponte entre a paisagem heterogénea da aplicação com o moderno plano de controlo de identidade.

Ter o Azure AD pré-autenticar o acesso aos serviços publicados BIG-IP proporciona muitos benefícios:

- Autenticação sem palavra-passe através do [Windows Hello,](/windows/security/identity-protection/hello-for-business/hello-overview) [Autenticador MS,](../user-help/user-help-auth-app-download-install.md) [Chaves Fast Identity Online (FIDO)](../authentication/howto-authentication-passwordless-security-key.md)e [autenticação baseada em certificados](../authentication/active-directory-certificate-based-authentication-get-started.md)

- Acesso [Condicional](../conditional-access/overview.md) Preventivo e [autenticação de vários fatores (MFA)](../authentication/concept-mfa-howitworks.md)

- [Proteção de Identidade](../identity-protection/overview-identity-protection.md) - Controlo adaptativo através do perfil de risco de utilizador e sessão


- [Deteção de credenciais vazadas](../identity-protection/concept-identity-protection-risks.md)

- [Reset da palavra-passe de autosserviço (SSPR)](../authentication/tutorial-enable-sspr.md)

- [Colaboração de parceiros](../governance/entitlement-management-external-users.md) - Gestão de direitos para acesso de hóspedes governado

- [Cloud App Security (CASB)](/cloud-app-security/what-is-cloud-app-security) - Para a completa descoberta e controlo de aplicações

- Monitorização de ameaças - [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) para análise de ameaças avançadas

- O [portal AD AZure](https://azure.microsoft.com/features/azure-portal/) - Um único plano de controlo para governar a identidade e o acesso

## <a name="scenario-description"></a>Descrição do cenário

Como Controlador de Entrega de Aplicações (ADC) e SSL-VPN, um sistema BIG-IP fornece acesso local e remoto a todos os tipos de serviços, incluindo:

- Aplicações web modernas e antigas

- Aplicações não baseadas na web

- Serviços DE API DA WEB REST e SOAP

O seu Gestor de Tráfego Local (LTM) permite a publicação segura de serviços através da funcionalidade de procuração inversa. Ao mesmo tempo, um sofisticado Gestor de Políticas de Acesso (APM) estende o BIG-IP com um conjunto mais rico de capacidades, permitindo a federação e o único sign-on (SSO).

A integração baseia-se numa confiança padrão da federação entre a APM e a AZure AD, comum à maioria dos casos de utilização de SHA que inclui o [cenário SSL-VPN.](f5-aad-password-less-vpn.md) Os recursos de Markup Language (SAML), OAuth e Open ID Connect (OIDC) também não são exceção, uma vez que também podem ser protegidos para acesso remoto. Também pode haver cenários em que um BIG-IP se torne um ponto de estrangulamento para o acesso do Zero Trust a todos os serviços, incluindo aplicações SaaS.

A capacidade de integração de um BIG-IP com AD Azure é o que permite a transição do protocolo necessária para garantir serviços integrados em AD legado ou não-Azure com controlos modernos, tais como [autenticação sem palavra-passe](https://www.microsoft.com/security/business/identity/passwordless) e [acesso condicional.](../conditional-access/overview.md) Neste cenário, um BIG-IP continua a cumprir o seu papel de procuração inversa, ao mesmo tempo que entrega a pré-autenticação e autorização à Azure AD, por conta de serviço.

Os passos 1-4 do diagrama ilustram a troca de pré-autenticação frontal entre um utilizador, um BIG-IP e AD AZure, num fluxo iniciado por um prestador de serviços. Os passos 5-6 mostram o enriquecimento subsequente da sessão APM e sSO para serviços individuais de backend.

![A imagem mostra a arquitetura de alto nível](./media/f5-aad-integration/integration-flow-diagram.png)

| Passo | Descrição |
|:------|:-----------|
| 1. | O utilizador seleciona um ícone de aplicação no portal, resolvendo o URL para o SAML SP (BIG-IP) |
| 2. | O BIG-IP redireciona o utilizador para o IDP SAML (Azure AD) para pré-autenticação|
| 3. | Azure AD processa políticas de acesso condicional e [controlos de sessão](../conditional-access/concept-conditional-access-session.md) para autorização|
| 4. | O utilizador redireciona de volta para a BIG-IP apresentando as reclamações da SAML emitidas pela Azure AD |
| 5. | A BIG-IP solicita qualquer informação adicional da sessão para incluir no controlo de acesso baseado em [SSO](../hybrid/how-to-connect-sso.md) e [Role (RBAC)](../../role-based-access-control/overview.md) ao serviço publicado |
| 6. | A BIG-IP remete o pedido do cliente para o serviço de backend

## <a name="user-experience"></a>Experiência do utilizador

Quer seja um colaborador direto, afiliado ou consumidor, a maioria dos utilizadores já estão familiarizados com a experiência de login do Office 365, pelo que o acesso aos serviços BIG-IP via SHA permanece amplamente familiar.

Os utilizadores encontram agora os seus serviços publicados BIG-IP consolidados nos plataformas de lançamento  [MyApps](../user-help/my-apps-portal-end-user-access.md) ou [O365,](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) juntamente com as capacidades de self-service para um conjunto mais alargado de serviços, independentemente do tipo de dispositivo ou localização. Os utilizadores podem mesmo continuar a aceder aos serviços publicados diretamente através do portal webtop BIG-IPs proprietário, se preferir. Ao iniciar sessão, a SHA garante que uma sessão de utilizadores seja terminada em ambas as extremidades, o BIG-IP e o AD Azure, garantindo que os serviços permanecem totalmente protegidos do acesso não autorizado.  

As imagens fornecidas são do portal de aplicações AD AZure que os utilizadores acedem de forma segura para encontrar os seus serviços publicados BIG-IP e para gerir as suas propriedades de conta.  

![A imagem mostra woodgrove myapps gallery](media/f5-aad-integration/woodgrove-app-gallery.png)

![A imagem mostra woodgrove myaccounts página de self-service](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Insights e análises

O papel de um BIG-IP é fundamental para qualquer empresa, pelo que as instâncias BIG-IP implementadas devem ser monitorizadas para garantir que os serviços publicados estão altamente disponíveis, tanto a nível de SHA como a nível operacional.

Existem várias opções para registar eventos locais ou remotamente através de uma solução de Gestão de Informação de Segurança e Eventos (SIEM), permitindo o armazenamento e processamento fora da caixa de telemetria. Uma solução altamente eficaz para monitorizar a atividade específica do Azure AD e sha-specific, é utilizar [o Azure Monitor](../../azure-monitor/overview.md) e [o Azure Sentinel,](../../sentinel/overview.md)oferecendo em conjunto:

- Visão geral detalhada da sua organização, potencialmente através de várias nuvens, e locais no local, incluindo infraestrutura BIG-IP

- Plano de controlo único que proporciona uma visão combinada de todos os sinais, evitando a dependência de ferramentas complexas e díspares

![A imagem mostra o fluxo de monitorização](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Pré-requisitos

A integração do F5 BIG-IP com a Ad Azure para sha tem os seguintes pré-requisitos:

- Um caso F5 BIG-IP em execução em qualquer uma das seguintes plataformas:

  - Aparelho físico

  - Edição Virtual hipervisor como Microsoft Hyper-V, VMware ESXi, Linux KVM e Citrix Hypervisor

  - Cloud Virtual Edition como Azure, VMware, KVM, Community Xen, MS Hyper-V, AWS, OpenStack e Google Cloud

    A localização real de uma instância BIG-IP pode ser no local ou qualquer plataforma de nuvem suportada, incluindo a Azure, desde que tenha conectividade com a Internet, recursos sendo publicados, e quaisquer outros serviços necessários, como o Ative Directory.  

- Uma licença APM F5 BIG-IP ativa, através de uma das seguintes opções:

   - F5 BIG-IP® Melhor pacote (ou)

   - F5 BIG-IP Access Policy Manager™ licença autónoma

   - F5 BIG-IP Access Policy Manager™ (APM) licença de complemento em um BIG-IP F5 BIG-IP existente® Gestor de Tráfego Local™ (LTM)

   - Uma [licença](https://www.f5.com/trial/big-ip-trial.php) de 90 dias do Big-IP Access Policy Manager™ (APM)

- Licenciamento Azure AD através de qualquer uma das seguintes opções:

   - Uma [subscrição gratuita](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) da Azure AD fornece os requisitos fundamentais mínimos para a implementação do SHA com autenticação sem palavra-passe

   - Uma [subscrição Premium](https://azure.microsoft.com/pricing/details/active-directory/) fornece todos os valores adicionais delineados no prefácio, incluindo [Acesso Condicional,](../conditional-access/overview.md) [MFA](../authentication/concept-mfa-howitworks.md)e [Proteção de Identidade](../identity-protection/overview-identity-protection.md)

Nenhuma experiência anterior ou conhecimento de F5 BIG-IP é necessário para implementar SHA, mas recomendamos familiarizar-se com a terminologia F5 BIG-IP. A rica base de [conhecimento](https://www.f5.com/services/resources/glossary) da F5 também é um bom lugar para começar a construir conhecimentos BIG-IP.

## <a name="deployment-scenarios"></a>Cenários de implementação

Os seguintes tutoriais fornecem orientações detalhadas sobre a implementação de alguns dos padrões mais comuns para BIG-IP e Azure AD SHA:

- [F5 BIG-IP em azure implantação walk-through](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM e Azure AD SSO para aplicações Kerberos](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM e Azure AD SSO para aplicações baseadas em cabeçalho](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Segurança F5 BIG-IP SSL-VPN com Azure AD SHA](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Recursos adicionais

- [O fim das senhas, ir sem palavras-passe](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Ative Directy assegura acesso híbrido](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Quadro do Microsoft Zero Trust para permitir o trabalho remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Começando com Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Próximos passos

Considere executar uma prova de conceito SHA (POC) utilizando a sua infraestrutura BIG-IP existente, ou implantando uma instância experimental. [A implementação de um VM virtual BIG-IP (VE) em Azure](f5-bigip-deployment-guide.md) demora aproximadamente 30 minutos, altura em que terá:

- Uma plataforma totalmente segura para modelar uma prova de conceito SHA

- Uma instância de pré-produção, plataforma totalmente segura para usar para testar novas atualizações e hotfixes do sistema BIG-IP

Ao mesmo tempo, deverá identificar uma ou duas aplicações que podem ser direcionadas para a publicação através do BIG-IP e proteger com a SHA.  

A nossa recomendação é começar com uma aplicação que ainda não foi publicada através de um BIG-IP, de modo a evitar possíveis perturbações nos serviços de produção. As diretrizes mencionadas neste artigo ajudarão-no a conhecer o procedimento geral para criar os vários objetos de configuração BIG-IP e configurar a SHA. Uma vez concluído, você deve ser capaz de fazer o mesmo com quaisquer outros novos serviços, além de também ter conhecimento suficiente para converter os serviços publicados BIG-IP existentes para a SHA com o mínimo de esforço.

O guia abaixo interativo percorre o procedimento de alto nível para implementar SHA e ver a experiência do utilizador final.

[![A imagem mostra a capa de guia interativo](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
