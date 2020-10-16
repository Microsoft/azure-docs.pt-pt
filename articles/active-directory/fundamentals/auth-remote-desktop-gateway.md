---
title: Serviços remotos de gateway de desktop com diretório ativo Azure
description: Orientação arquitetónica para alcançar este padrão de autenticação
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
ms.openlocfilehash: ff099d41970ad4d78d5c6035a60f448f29ab24b1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114355"
---
# <a name="remote-desktop-gateway-services"></a>Serviços remotos de gateway de desktop

Uma implementação padrão de Serviços de Ambiente de Trabalho Remoto (RDS) inclui vários [serviços de função de ambiente de trabalho remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) em execução no Windows Server. A implementação rds com Azure Ative Directory (Azure AD) Application Proxy tem uma ligação de saída permanente a partir do servidor que executa o serviço de conector. Outras implementações deixam as ligações de entrada abertas através de um equilibrador de carga. Este padrão de autenticação permite-lhe oferecer mais tipos de aplicações publicando aplicações no local através de Serviços de Desktop Remoto. Também reduz a superfície de ataque da sua implantação utilizando o Proxy da Aplicação AD Azure.

## <a name="use-when"></a>Utilizar quando

Tem de fornecer acesso remoto e proteger a sua implementação de Serviços de Ambiente de Trabalho Remoto com pré-autenticação.

![diagrama arquitetónico](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador:** Acede RDS servido por Application Proxy.

* **Web browser**: O componente com o que o utilizador interage para aceder ao URL externo da aplicação.

* **Azure AD**: Autentica o utilizador. 

* **Serviço de procuração de aplicação**: Atua como procuração inversa para encaminhar o pedido do utilizador para RDS. O Application Proxy também pode impor quaisquer políticas de Acesso Condicional. 

* **Serviços remotos de desktop**: Funciona como uma plataforma para aplicações virtualizadas individuais, fornecendo acesso seguro ao ambiente de trabalho móvel e remoto, e proporcionando aos utilizadores finais a capacidade de executar as suas aplicações e desktops a partir da nuvem. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Implementar serviços remote desktop gateway com Azure AD

* [Publique desktop remoto com Proxy de aplicação AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [Adicione uma aplicação no local para acesso remoto através de Aplicação Proxy em Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
