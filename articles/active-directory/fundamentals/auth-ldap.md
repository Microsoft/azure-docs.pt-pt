---
title: Autenticação LDAP com Diretório Ativo Azure
description: Orientação arquitetónica para a obtenção da autenticação LDAP com diretório Azure Ative.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168717"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Autenticação LDAP com Diretório Ativo Azure

O Protocolo de Acesso ao Diretório Leve (LDAP) é um protocolo de aplicação para trabalhar com vários serviços de diretório. Serviços de diretório, tais como Diretório Ativo, [informações de utilizadores e contas](https://www.dnsstuff.com/active-directory-service-accounts)de loja, e informações de segurança como palavras-passe. O serviço permite então que a informação seja partilhada com outros dispositivos da rede. Aplicações empresariais como e-mail, gestores de relacionamento com o cliente (CRMs) e software de Recursos Humanos (HR) podem usar LDAP para autenticar, aceder e encontrar informações. 

O Azure Ative Directory (Azure AD) suporta este padrão através dos Serviços de Domínio AD Azure (DS AD). Permite que as organizações que estão a adotar uma estratégia de primeira nuvem modernizem o seu ambiente, deslocando os seus recursos LDAP para a nuvem. Os benefícios imediatos serão: 

* Integrado com Azure AD. A adições de utilizadores e grupos, ou alterações de atributos aos seus objetos são automaticamente sincronizadas do seu inquilino AD AZure para AD DS. Alterações em objetos no local O Ative Directory é sincronizado para Azure AD e, em seguida, para DS AD.

* Simplificar as operações. Reduz a necessidade de manter e remendar manualmente as infraestruturas no local. 

* De confiança. Você é gerido, serviços altamente disponíveis 

## <a name="use-when"></a>Utilizar quando

É necessário que uma aplicação ou serviço utilize a autenticação LDAP.

![Diagrama de arquitetura](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador**: Acede a aplicações dependentes do LDAP através de um browser.

* **Web Browser**: A interface com a que o utilizador interage para aceder ao URL externo da aplicação.

* **Rede Virtual**: Uma rede privada em Azure através da qual a aplicação legacy pode consumir serviços LDAP. 

* **Aplicações antigas**: Aplicações ou cargas de trabalho de servidor que requerem LDAP implantado numa rede virtual em Azure, ou que tenham visibilidade para IPs de instância de AD por instância através de rotas de rede. 

* **Azure AD**: Sincroniza a informação de identidade do diretório da organização através do Azure AD Connect.

* **Azure AD Domain Services (AD DS)**: Realiza uma sincronização unidirecional a partir da Azure AD para fornecer acesso a um conjunto central de utilizadores, grupos e credenciais. A instância DS da AD é atribuída a uma rede virtual. Aplicações, serviços e VMs em Azure que se conectam à rede virtual atribuída a DS AD podem usar funcionalidades comuns de DS AD tais como LDAP, união de domínio, política de grupo, Kerberos e autenticação NTLM.
   > [!NOTE]
   >  Em ambientes onde a organização não consegue sincronizar hashes de palavra-passe, ou os utilizadores iniciarem a sposição usando cartões inteligentes, recomendamos que utilize uma floresta de recursos em DS AD. 

* **Azure AD Connect**: Uma ferramenta para sincronizar informações de identidade do Microsoft Azure AD. O assistente de implantação e as experiências guiadas ajudam-no a configurar os pré-requisitos e componentes necessários para a ligação, incluindo sincronização e inscrição do Ative Directory para Azure AD. 

* **Diretório Ativo**: Serviço de diretório que armazena [no local informações de identidade, tais como informações de utilizador e conta,](https://www.dnsstuff.com/active-directory-service-accounts)e informações de segurança como palavras-passe.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Implementar a autenticação LDAP com Azure AD

* [Criar e configurar uma instância AD DS Azure](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Configurar a rede virtual para uma instância AD DS Azure](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Configure Secure LDAP para um domínio gerido Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Criar uma confiança florestal de saída para um domínio no local em Azure AD DS](../../active-directory-domain-services/tutorial-create-forest-trust.md)

