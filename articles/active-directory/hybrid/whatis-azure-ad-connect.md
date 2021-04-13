---
title: O que é Azure AD Connect e Connect Health. | Microsoft Docs
description: Saiba mais sobre as ferramentas usadas para sincronizar e monitorizar o ambiente no local com a Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: afc3d1d84d7b50cdaa7031230d7085e9f611c6c6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304975"
---
# <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect?

O Azure AD Connect é a ferramenta da Microsoft concebida para satisfazer e atingir os seus objetivos de identidade híbrida.  Proporciona as seguintes funcionalidades:
     
- [Sincronização de hash de palavra-passe](whatis-phs.md) - Um método de entrada que sincroniza um haxixe de um utilizador no local com Azure AD.
- [Autenticação pass-through](how-to-connect-pta.md) - Um método de entrada que permite aos utilizadores usar a mesma palavra-passe no local e na nuvem, mas não requer a infraestrutura adicional de um ambiente federado.
- [Integração da](how-to-connect-fed-whatis.md) Federação - A Federação é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura AD FS no local. Também fornece capacidades de gestão de FS AD, tais como renovação de certificados e implementações adicionais de servidores AD FS.
- [Sincronização](how-to-connect-sync-whatis.md) - Responsável pela criação de utilizadores, grupos e outros objetos.  Além disso, certificar-se de que as informações de identidade dos seus utilizadores e grupos no local estão a combinar com a nuvem.  Esta sincronização também inclui hashes de senha.
- [Monitorização de Saúde](whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) - Azure AD Connect Health pode fornecer uma monitorização robusta e fornecer uma localização central no portal Azure para visualizar esta atividade. 


![O que é o Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>O que é o Azure AD Connect Health?

O Azure Ative Directory (Azure AD) Connect Health proporciona uma monitorização robusta da sua infraestrutura de identidade no local. Permite-lhe manter uma ligação fiável aos Serviços Microsoft 365 e Microsoft Online.  Esta fiabilidade é conseguida fornecendo capacidades de monitorização para os seus principais componentes de identidade. Além disso, torna os principais pontos de dados sobre estes componentes facilmente acessíveis.

As informações são apresentadas no [portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). Utilize o portal Azure AD Connect Health para visualizar alertas, monitorização de desempenho, análise de utilização e outras informações. O Azure AD Connect Health ativa a lente única do estado de funcionamento dos componentes de identidade chave num único local.

![O que é o Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Porquê utilizar o Azure AD Connect?
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. Os utilizadores e organizações podem aproveitar:

* Os utilizadores podem usar uma única identidade para aceder a aplicações no local e serviços na nuvem, como o Microsoft 365.
* Uma ferramenta única para proporcionar uma experiência fácil de implementação para sincronização e início de sessão.
* Fornece as capacidades mais recentes para os seus cenários. O Azure AD Connect substitui versões mais antigas de ferramentas de integração de identidade, tais como DirSync e Azure AD Sync. Para obter mais informações, consulte [a comparação de ferramentas de integração de diretórios de identidade híbrida.](plan-hybrid-identity-design-considerations-tools-comparison.md)

## <a name="why-use-azure-ad-connect-health"></a>Porquê utilizar o Azure AD Connect Health?
Ao autenticar com a Azure AD, os seus utilizadores são mais produtivos porque existe uma identidade comum para aceder tanto aos recursos em nuvem como no local. Garantir que o ambiente é fiável, para que os utilizadores possam aceder a estes recursos, torna-se um desafio.  O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a sua infraestrutura de identidade no local, garantindo assim a fiabilidade deste ambiente. É tão simples como instalar um agente em cada um dos servidores de identidade no local.

O Azure AD Connect Health para AD FS suporta o AD FS 2.0 no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. Suporta também a monitorização de servidores proxy do AD FS ou de aplicações Web que fornecem suporte de autenticação para acesso à extranet. Com uma instalação fácil e rápida do Agente de Estado de Funcionamento, o Azure AD Connect Health para AD FS fornece um conjunto de capacidades principais.

Principais benefícios e boas práticas:

|Principais vantagens|Melhores práticas|
|-----|-----|
|Segurança avançada|[Tendências de bloqueio de extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Relatório de inícios de sessão falhados](how-to-connect-health-adfs-risky-ip.md)</br>[Em conformidade com a privacidade](reference-connect-health-user-privacy.md)|
|Receba alerta sobre [todos os problemas críticos do sistema ADFS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Configuração e disponibilidade do servidor</br>[Desempenho e conectividade](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Manutenção regular|
|Fácil de implementar e gerir|[Instalação de agente rápido](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs)</br>Atualização automática do agente para a versão mais recente</br>Dados disponíveis no portal em alguns minutos|
Métricas de utilização [avançadas](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|Utilização principal das aplicações</br>Localizações de rede e ligação TCP</br>Pedidos de token por servidor|
|Experiência de utilizador excecional|Modo de dashboard do portal do Azure</br>[Alertas por e-mail](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Requisitos de licença para utilização do Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Requisitos de licença para utilização de Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições expressas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)
- [Instalar os agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md)
