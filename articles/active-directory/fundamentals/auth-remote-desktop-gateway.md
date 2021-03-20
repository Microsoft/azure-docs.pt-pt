---
title: Serviços remotos de gateway de desktop com diretório ativo Azure
description: Orientação arquitetónica sobre a obtenção de serviços de gateway remoto de desktop com diretório ativo Azure.
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
ms.openlocfilehash: 4baaf2de6fbe4a56f64d449644b8594217dc432c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172742"
---
# <a name="remote-desktop-gateway-services"></a>Serviços remotos de gateway de desktop

Uma implementação padrão de Serviços de Ambiente de Trabalho Remoto (RDS) inclui vários [serviços de função de ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) em execução no Windows Server. A implementação rds com Azure Ative Directory (Azure AD) Application Proxy tem uma ligação de saída permanente a partir do servidor que executa o serviço de conector. Outras implementações deixam as ligações de entrada abertas através de um equilibrador de carga. Este padrão de autenticação permite-lhe oferecer mais tipos de aplicações publicando aplicações no local através de Serviços de Desktop Remoto. Também reduz a superfície de ataque da sua implantação utilizando o Proxy da Aplicação AD Azure.

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

* [Publique desktop remoto com Proxy de aplicação AD Azure](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) 

* [Adicione uma aplicação no local para acesso remoto através de Aplicação Proxy em Azure AD](../manage-apps/application-proxy-add-on-premises-application.md)

