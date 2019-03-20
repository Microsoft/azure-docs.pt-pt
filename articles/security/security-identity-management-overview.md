---
title: Funcionalidades de segurança do Azure que o ajudam a gestão de identidades | Documentos da Microsoft
description: " Este artigo fornece uma descrição geral das principais funcionalidades de segurança do Azure que o ajudam a gestão de identidades. Microsoft identidades e acessos gestão soluções ajudar a TI a proteger o acesso aos recursos e aplicações em todo o datacenter Corporativo e para a cloud, permitindo níveis adicionais de validação, como o multi-factor Authentication e o acesso condicional políticas. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: 29c04fc04b5d277e982a37402a128b2dbe787e2c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898504"
---
# <a name="azure-identity-management-security-overview"></a>Descrição geral da segurança de gestão de identidade do Azure

 Gestão de identidades é o processo de autenticação e autorização [entidades de segurança](https://docs.microsoft.com/windows/security/identity-protection/access-control/security-principals). Ela também envolve a controlar informações sobre os principais (identidades). Entidades de segurança (identidades) podem incluir serviços, aplicações, utilizadores, grupos, etc. Microsoft identidades e acessos gestão soluções ajudar a TI a proteger o acesso aos recursos e aplicações em todo o datacenter Corporativo e na nuvem. Essa proteção permite níveis adicionais de validação, como o multi-factor Authentication e políticas de acesso condicional. Monitorização de atividade suspeita por meio de segurança avançadas, relatórios, auditoria e alertas ajuda a mitigar potenciais problemas de segurança. [O Azure Active Directory Premium](../active-directory/active-directory-editions.md) fornece logon único (SSO) a milhares de software na cloud, como aplicações de serviço (SaaS) e acesso a aplicações web que executa no local.
 
Ao tirar partido dos benefícios de segurança do Azure Active Directory (Azure AD), pode:

* Criar e gerir uma identidade única para cada utilizador em toda a empresa híbrida, manter os utilizadores, grupos e dispositivos em sincronização. 
* Fornece acesso SSO às suas aplicações, incluindo milhares de aplicações de SaaS previamente integradas.
* Ativar a segurança de acesso de aplicação através da imposição de regras com base no multi-factor Authentication para ambos os locais e aplicações na cloud.
* Aprovisionar o acesso remoto seguro a aplicativos da web no local através do Proxy de aplicações do Azure AD.

O objetivo deste artigo é fornecer uma visão geral das principais funcionalidades de segurança do Azure que o ajudam a gestão de identidades. Também fornecemos links para artigos que fornecem detalhes de cada funcionalidade, para que possa saber mais.  

O artigo se concentra nos recursos de gerenciamento de identidade do Azure core seguintes:

* Início de sessão único
* Proxy inverso
* Multi-Factor Authentication
* Controlo de acesso baseado em funções (RBAC)
* Monitorização de segurança, alertas e relatórios baseados em aprendizagem automática
* Gestão de acesso e identidade do consumidor
* Registo de dispositivo
* Privileged Identity Management
* Proteção de identidade
* Hybrid identity management/Azure AD connect
* Revisões de acesso do Azure AD

## <a name="single-sign-on"></a>Início de sessão único

SSO significa que pode aceder a todas as aplicações e recursos de que precisa para fazer negócios, ao iniciar sessão apenas uma vez com uma conta de utilizador único. Depois de iniciar sessão, pode aceder a todos os aplicativos que necessita sem ter que está a ser necessária para autenticar (por exemplo, escreva uma palavra-passe) uma segunda vez.

Muitas organizações contam com aplicações de SaaS, como o Office 365, a caixa e o Salesforce para produtividade do usuário. Historicamente, a equipe de TI necessários para individualmente criar e atualizar as contas de utilizador em cada aplicação SaaS, e os utilizadores tinham de memorizar uma palavra-passe para cada aplicação SaaS.

O Azure AD estende ambientes do Active Directory no local para a cloud, permitindo que os usuários utilizam suas contas institucionais principal para iniciar sessão, não apenas aos seus dispositivos associados a domínios e recursos da empresa, mas também para todas as aplicações de SaaS e web que precisam para as suas tarefas.

Não só os utilizadores não têm de gerir vários conjuntos de nomes de utilizador e palavras-passe, pode aprovisionar ou desaprovisionar acesso à aplicação automaticamente, com base nos seus grupos organizacionais e o respetivo estado de funcionários. O Azure AD introduz a segurança e controlos de governação de acesso com a qual pode gerenciar centralmente o acesso dos utilizadores em aplicações SaaS.

Saiba mais:

* [Descrição geral do início de sessão único](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [What is application access and single sign-on with Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Integrar o Azure Active Directory início de sessão único em aplicações de SaaS](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Proxy inverso

Proxy de aplicações do Azure AD permite-lhe publicar aplicações no local, tal como [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) sites, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), e [IIS](https://www.iis.net/)-com base em aplicações dentro da rede privada Fornece acesso seguro aos utilizadores fora da rede. Proxy de aplicações fornece acesso remoto e SSO para muitos tipos de locais aplicativos web com milhares de aplicações SaaS suportadas pelo Azure AD. Os funcionários poderem iniciar sessão para as suas aplicações de casa em seus próprios dispositivos e autenticar-se através deste proxy baseado na nuvem.

Saiba mais:

* [Ativar o Proxy de aplicações do Azure AD](../active-directory/manage-apps/application-proxy-enable.md)
* [Publicar aplicações com o Proxy de aplicações do Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Início de sessão único com o Proxy de aplicações](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Trabalhar com o acesso condicional](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

O Azure multi-factor Authentication é um método de autenticação que requer a utilização de mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos inícios de sessão de utilizador e de transações. Multi-factor Authentication ajuda a salvaguardar o acesso a dados e aplicações, satisfazendo um processo de início de sessão simples. Proporciona uma autenticação segura através de um conjunto de opções de verificação: chamadas telefónicas, mensagens de texto, ou notificações da aplicação móvel ou códigos de verificação e tokens de OAuth de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é o Multi-Factor Authentication do Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Como funciona o Azure multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

RBAC é um sistema de autorização incorporado no Azure Resource Manager que fornece a gestão de acesso detalhado dos recursos no Azure. RBAC permite-lhe granularmente controlar o nível de acesso dos utilizadores. Por exemplo, pode limitar a um utilizador para gerir apenas as redes virtuais e de outro utilizador para gerir todos os recursos num grupo de recursos. Azure inclui várias funções incorporadas que pode utilizar. São apresentadas em seguida quatro funções incorporadas fundamentais. As três primeiras aplicam-se a todos os tipos de recursos.

Saiba mais:

* [O que é o controlo de acesso baseado em funções (RBAC)?](../role-based-access-control/overview.md)
* [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md) (Funções incorporadas para recursos do Azure)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorização de segurança, alertas e relatórios baseados em aprendizagem automática

Monitorização de segurança, alertas e relatórios baseados em aprendizagem de máquina identificam padrões de acesso inconsistentes podem ajudar a proteger a sua empresa. Pode utilizar o acesso do Azure AD e relatórios de utilização para obter visibilidade sobre a integridade e segurança de diretório da sua organização. Com essas informações, um administrador do diretório pode determinar melhor onde podem existir possíveis riscos de segurança para que eles podem planear adequadamente atenuar esses riscos.

No portal do Azure, os relatórios se enquadram nas seguintes categorias:

* **Relatórios de anomalias**: Conter os eventos de início de sessão que encontramos para ser anómalas. Nosso objetivo é conscientizá-lo de tal atividade e permitem-lhe determinar se um evento é suspeito.
* **Integrado relatórios de aplicação**: Fornece informações sobre como as aplicações na cloud estão a ser utilizadas na sua organização. O Azure AD oferece integração com milhares de aplicações na cloud.
* **Relatórios de erros**: Indica os erros que possam ocorrer quando aprovisionar contas em aplicativos externos.
* **Relatórios de utilizador específico**: Exibir dados de atividade de início de sessão de dispositivo para um utilizador específico.
* **Registos de atividades**: Conter um registo de todos os eventos auditados no últimas 24 horas, últimos 7 dias ou últimos 30 dias e alterações de atividade de grupo e atividade de registo e reposição de palavra-passe.

Saiba mais:

* [Ver os relatórios de acesso e utilização](../active-directory/active-directory-view-access-usage-reports.md)
* [Começar a utilizar com os relatórios do Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
* [Guia de relatórios do Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gestão de acesso e identidade do consumidor

O Azure AD B2C é um serviço de gestão de identidades global altamente disponível para aplicações voltadas para consumidores que se dimensiona para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão todas as suas aplicações através de experiências personalizáveis através das respetivas contas de redes sociais existentes ou criando novas credenciais.

No passado, os desenvolvedores de aplicativos que desejavam Inscreva-se os clientes e iniciar sessão-las às suas aplicações teriam de escrever seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure AD B2C oferece uma maneira melhor de integrar a gestão de identidades de consumidor nas aplicações com a ajuda de uma plataforma segura e baseada em normas e um grande conjunto de políticas extensíveis de sua organização.

Quando utiliza o Azure AD B2C, os consumidores podem inscrever-se para as suas aplicações através das respetivas contas de redes sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e palavra-passe).

Saiba mais:

* [O que é o Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Pré-visualização do Azure do Active Directory B2C: Inscreva-se e iniciar sessão em consumidores nas suas aplicações](../active-directory-b2c/active-directory-b2c-overview.md)
* [Pré-visualização do Azure Active Directory B2C: Tipos de aplicativos](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registo de dispositivo

Registo de dispositivos do Azure AD é a base com base no dispositivo [acesso condicional](../active-directory/active-directory-conditional-access-device-registration-overview.md) cenários. Quando um dispositivo é registado, o registo de dispositivos do Azure AD fornece ao dispositivo uma identidade que utiliza para autenticar o dispositivo quando um utilizador inicia sessão. O dispositivo autenticado e os atributos do dispositivo, em seguida, podem ser utilizados para impor políticas de acesso condicional para aplicações alojadas na cloud e no local.

Quando combinada com uma solução de gestão de dispositivos móveis, como o Intune, os atributos do dispositivo no Azure AD são atualizados com informações adicionais sobre o dispositivo. Em seguida, pode criar regras de acesso condicional que impõem o acesso a partir de dispositivos para cumprir as normas de segurança e conformidade.

Saiba mais:

* [Introdução ao registo de dispositivos do Azure AD](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Registo automático de dispositivos com o Azure AD para dispositivos associados a um domínio do Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Configurar o registo automático do Windows dispositivos associados a um domínio com o Azure AD](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Com o Azure AD Privileged Identity Management, pode gerir, controlar e monitorizar as identidades privilegiadas e aceder a recursos no Azure AD, bem como outros serviços online da Microsoft, como o Office 365 e o Microsoft Intune.

Às vezes, os utilizadores têm de realizar operações privilegiadas nos recursos do Azure ou do Office 365 ou noutras aplicações de SaaS. Essa necessidade, muitas vezes, significa que as organizações têm para lhe dar acesso permanente com privilégios de utilizadores no Azure AD. Esse acesso é um risco de segurança de cada vez maior para os recursos de alojamento na cloud, uma vez que as organizações suficientemente não podem monitorizar o que os utilizadores estão a fazer com seus privilégios de administrador. Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, que uma violação pode afetar a segurança de cloud global da organização. O Azure AD Privileged Identity Management ajuda a diminuir esse risco.

Com o Azure AD Privileged Identity Management, pode:

* Veja quais usuários são administradores do Azure AD.
* Ative a pedido just-in-time (JIT) acesso administrativo e aos serviços da Microsoft, como o Office 365 e Intune.
* Obter relatórios sobre o histórico de acesso de administrador e as alterações em atribuições de administrador.
* Obtenha alertas sobre o acesso a uma função com privilégios.

Saiba mais:

* [O que é o Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)
* [Atribuir funções de diretório do Azure AD no PIM](../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Proteção de identidade

O Azure AD Identity Protection é um serviço de segurança que fornece uma vista consolidada de eventos de risco e potenciais vulnerabilidades que afetam as identidades da sua organização. Proteção de identidade tira partido das capacidades de deteção de anomalias do Azure AD existentes, que estão disponíveis por meio de relatórios de atividade anómala de AD do Azure. Proteção de identidade também apresenta novos tipos de eventos de risco que podem detetar anomalias em tempo real.

Saiba mais:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: O Azure AD e mostrar de identidade: Pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybrid identity management/Azure AD connect

As soluções de identidade da Microsoft abrangem capacidades baseadas na cloud e no local, ao criar uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização. Chamamos-lhe identidade híbrida. O Azure AD Connect é a ferramenta da Microsoft concebida para satisfazer e atingir os seus objetivos de identidade híbrida. Isto permite-lhe fornecer uma identidade comum para o utilizadores das aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD. Proporciona as seguintes funcionalidades:

* Sincronização
* Integração do AD FS e o Federação
* Pass-through authentication
* Monitorização do Estado de Funcionamento

Saiba mais:

* [White paper sobre identidade híbrida](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blogue da equipa do Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Revisões de acesso do Azure AD

As revisões de acesso do Azure Active Directory (Azure AD) permitem que as organizações façam uma gestão eficiente das associações a grupos, acesso às aplicações empresariais e atribuições de funções privilegiadas.

Saiba mais:

* [Revisões de acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)
* [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)
