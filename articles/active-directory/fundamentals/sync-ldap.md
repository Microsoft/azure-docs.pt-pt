---
title: Sincronização LDAP com Diretório Ativo Azure
description: Orientação arquitetónica para alcançar a sincronização LDAP com o Azure Ative Directory.
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
ms.openlocfilehash: e617d7ccc14e65c18eb86877b1c7fb1aeef74cd0
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578897"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Sincronização LDAP com Diretório Ativo Azure

O Protocolo de Acesso ao Diretório Leve (LDAP) é um protocolo de serviço de diretório que funciona na pilha TCP/IP. Fornece um mecanismo usado para ligar, pesquisar e modificar diretórios de internet. O serviço de diretório LDAP baseia-se num modelo de servidor de clientes e a sua função é permitir o acesso a um diretório existente. Muitas empresas dependem de servidores LDAP no local para armazenar utilizadores e grupos para as suas aplicações empresariais críticas. 

O Azure Ative Directory (Azure AD) pode substituir a sincronização LDAP por Azure AD Connect. O serviço de sincronização Azure AD Connect realiza todas as operações relacionadas com a sincronização de dados de identidade entre os ambientes no local e o Azure AD. 

## <a name="use-when"></a>Utilizar quando

Precisa de sincronizar dados de identidade entre os seus diretórios LDAP v3 e Azure AD. 

![diagrama arquitetónico](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador** : Acede a uma aplicação que se baseia na utilização de um diretório LDAP v3 para classificar utilizadores e senhas.

* **Web browser** : O componente com o que o utilizador interage para aceder ao URL externo da aplicação

* **Aplicação Web** : Aplicação com dependências de diretórios LDAP v3.

* **Azure AD** : Azure AD sincroniza informações de identidade (utilizadores, grupos, passwords) dos diretórios LDAP da organização através do Azure AD Connect. 

* **Azure AD Connect:** é uma ferramenta para ligar em infraestruturas de identidade do Microsoft Azure AD. O assistente e as experiências guiadas ajudam a implementar e configurar os pré-requisitos e componentes necessários para a ligação. 

* **Conector personalizado** : Um Conector LDAP genérico permite-lhe integrar o serviço de sincronização Azure AD Connect com um servidor LDAP v3. Fica no Azure AD Connect.

* **Ative Directory** : Ative Directory é um serviço de diretório incluído na maioria dos sistemas operativos Windows Server. Os servidores que executam os Serviços de Diretório Ativo são chamados controladores de domínio e autenticam e autorizam todos os utilizadores e computadores num domínio Windows.

* **Servidor LDAP v3** : Diretório compatível com protocolo lDAP que armazena utilizadores corporativos e palavras-passe utilizadas para autenticação de serviços de diretório.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementar a sincronização LDAP com Azure AD

* [Ferramentas híbridas de integração de diretórios de identidade](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Roteiro de instalação Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap) 

* [Visão geral e criação de um Conector LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > Os Conectores LDAP são uma configuração avançada que requer alguma familiaridade com o Gestor de Identidade da Vanguarda e/ou o Gestor de Identidade da Microsoft. Se for utilizado na produção, aconselhamos que as perguntas sobre esta configuração passem pelo [Premier Support](https://support.microsoft.com/premier) ou pela Microsoft Partner Network.

 
