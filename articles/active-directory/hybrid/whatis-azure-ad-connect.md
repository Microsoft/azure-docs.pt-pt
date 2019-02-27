---
title: O que é o Azure AD Connect e Connect Health. | Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorizar o seu ambiente no local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a29a00c34d4c60e6ba2b4fd6b0b413873301af5c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888377"
---
# <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect?

O Azure AD Connect é a ferramenta da Microsoft concebida para satisfazer e atingir os seus objetivos de identidade híbrida.  Proporciona as seguintes funcionalidades:
    
- [Sincronização de hash de palavra-passe](whatis-phs.md) -um método de início de sessão que sincroniza um hash de utilizadores no local palavras-passe do AD com o Azure AD.
- [Autenticação pass-through](how-to-connect-pta.md) -um método de início de sessão que permite aos utilizadores utilizar a mesma palavra-passe no local e na cloud, mas não requer a infraestrutura adicional de um ambiente federado.
- [Integração de Federação](how-to-connect-fed-whatis.md) -Federation é uma parte opcional do Azure AD Connect e pode ser utilizado para configurar um ambiente híbrido utilizando uma local infraestrutura do AD FS. Ele também fornece capacidades de gestão do AD FS, tais como implementações de servidor do AD FS adicionais e renovação de certificado.
- [Sincronização](how-to-connect-sync-whatis.md) - responsável pela criação de utilizadores, grupos e outros objetos.  Bem como a tornar-se de que informações de identidade para seus usuários locais e grupos é correspondência na nuvem.  Essa sincronização também inclui os hashes de palavra-passe.
-   [Monitorização do Estado de Funcionamento ](whatis-hybrid-identity-health.md) - o Azure AD Connect Health pode proporcionar uma monitorização robusta e uma localização central no portal do Azure para visualizar esta atividade. 


![O que é o Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>O que é o Azure AD Connect Health?

O Azure Active Directory (Azure AD) Connect Health fornece uma monitorização robusta de sua infraestrutura de identidade no local. Permite-lhe manter uma ligação fiável ao Office 365 e do Microsoft Online Services.  Este confiabilidade é conseguida ao fornecer capacidades de monitorização para os componentes de identidade chave. Além disso, torna os pontos de dados chave sobre estes componentes facilmente acessíveis.

As informações são apresentadas no [portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). Utilize o portal do Azure AD Connect Health para ver alertas, monitorização de desempenho, análise de utilização e outras informações. O Azure AD Connect Health ativa a lente única do estado de funcionamento dos componentes de identidade chave num único local.

![O que é o Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Porquê utilizar o Azure AD Connect?
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. Os utilizadores e as organizações podem tirar partido de:

* Os utilizadores podem utilizar uma identidade única para acederem às aplicações no local e aos serviços na nuvem, como o Office 365.
* Uma ferramenta única para proporcionar uma experiência fácil de implementação para sincronização e início de sessão.
* Fornece as capacidades mais recentes para os seus cenários. O Azure AD Connect substitui as versões anteriores das ferramentas de integração de identidade, como o DirSync e o Azure AD Sync. Para obter mais informações, consulte [Comparação das ferramentas de integração de diretórios de identidade híbrida](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Porquê utilizar o Azure AD Connect Health?
Quando com o Azure AD, os utilizadores sejam mais produtivos porque existe uma identidade comum para aceder aos recursos na cloud e no local. Garantir que o ambiente é confiável, para que os utilizadores podem aceder estes recursos, torna-se um desafio.  O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a infraestrutura de identidade no local, que garante a confiabilidade de neste ambiente. É tão simples como instalar um agente em cada um dos servidores de identidade no local.

O Azure AD Connect Health para AD FS suporta o AD FS 2.0 no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. Suporta também a monitorização de servidores proxy do AD FS ou de aplicações Web que fornecem suporte de autenticação para acesso à extranet. Com uma instalação fácil e rápida do Agente de Estado de Funcionamento, o Azure AD Connect Health para AD FS fornece um conjunto de capacidades principais.

Principais vantagens e as práticas recomendadas:

|Principais vantagens|Melhores práticas|
|-----|-----|
|Segurança avançada|[Tendências de bloqueio de extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Relatório de inícios de sessão falhados](how-to-connect-health-adfs-risky-ip.md)</br>[Em privacidade em conformidade](reference-connect-health-user-privacy.md)|
|Receba alertas no [todos críticos problemas de sistema do AD FS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Configuração e disponibilidade do servidor</br>[Desempenho e conectividade](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Manutenção regular|
|Facilidade de implementação e gestão|[Instalação do agente rápida](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Atualização automática do agente para a versão mais recente</br>Dados disponíveis no portal em alguns minutos|
Rich [métrica de utilização](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|Utilização principal das aplicações</br>Localizações de rede e ligação TCP</br>Pedidos de token por servidor|
|Grande experiência de usuário|Modo de dashboard do portal do Azure</br>[Alertas por e-mail](how-to-connect-health-adfs.md#alerts-for-ad-fs)|




## <a name="next-steps"></a>Passos Seguintes

- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições rápidas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)
- [Instalar os agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
