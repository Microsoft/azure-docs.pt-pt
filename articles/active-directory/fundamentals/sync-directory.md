---
title: Sincronização do diretório com diretório Azure Ative Directory
description: Orientação arquitetónica para a sincronização do diretório com o Azure Ative Directory.
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
ms.openlocfilehash: 748f91b2fe77667969e9736f8084a9dd24018425
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172474"
---
# <a name="directory-synchronization"></a>Sincronização de diretórios

Muitas organizações têm uma infraestrutura híbrida que abrange tanto no local como componentes em nuvem. Sincronizar as identidades dos utilizadores entre diretórios locais e em nuvem permite que os utilizadores acedam aos recursos com um único conjunto de credenciais. 

Sincronização é o processo de 

* criando um objeto baseado em certas condições
* manter o objeto atualizado
* remover o objeto quando as condições já não estiverem reunidas. 

O provisionamento no local envolve o fornecimento de fontes no local (como o Ative Directory) ao Azure Ative Directory (Azure AD). 

## <a name="use-when"></a>Utilizar quando

Precisa de sincronizar os dados de identidade dos ambientes do Ative Directory para a Azure AD.

![diagrama arquitetónico](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador:** Acede a uma aplicação utilizando a Azure AD.

* **Web browser**: O componente com o que o utilizador interage para aceder ao URL externo da aplicação.

* **Aplicação**: Aplicação Web que se baseia na utilização do Azure AD para efeitos de autenticação e autorização.

* **Azure AD**: Sincroniza a informação de identidade do diretório da organização através do Azure AD Connect. 

* **Azure AD Connect**: Uma ferramenta para ligar em infraestruturas de identidade do Microsoft Azure. O assistente e as experiências guiadas ajudam-no a implementar e a configurar os pré-requisitos e componentes necessários para a ligação, incluindo sincronização e inscrição de Ative Directies para Azure AD. 

* **Ative Directory**: Ative Directory é um serviço de diretório incluído na maioria dos sistemas operativos Windows Server. Os servidores que executam serviços de domínio de diretório ativo (DS AD) são chamados controladores de domínio. Autenticam e autorizam todos os utilizadores e computadores do domínio.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Implementar sincronização de diretórios com Azure AD

* [O que é o aprovisionamento de identidades?](../cloud-provisioning/what-is-provisioning.md) 

* [Ferramentas híbridas de integração de diretórios de identidade](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Roteiro de instalação Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md)