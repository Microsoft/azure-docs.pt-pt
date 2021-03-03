---
title: O que é que está a ser forjada com o Azure Ative Directory? | Microsoft Docs
description: Descreve a visão geral do provisionamento de inter-directórios de identidade.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4246bc4c62fd8e5e73ff18e383b8bf115d25ede8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643847"
---
# <a name="what-is-inter-directory-provisioning"></a>O que é o aprovisionamento entre diretórios?

Um diretório é uma infraestrutura de informação partilhada, que é usada para localizar, gerir, administrar e organizar itens e recursos de rede.  Exemplos de aplicações que utilizam serviços de diretório são o Microsoft Ative Directory e o Azure AD.  Identidades ajudam os sistemas de diretório a fazer determinações como quem tem acesso ao quê e quem é autorizado a usar recursos específicos.

O provisionamento inter-directório está a forragem de identidade entre dois sistemas de serviços de diretórios diferentes.   O cenário mais comum para o provisionamento inter-directório é quando um utilizador já em Ative Directory é adcedido no Azure AD. Este provisionamento pode ser realizado por agentes como a sincronização Azure AD Connect ou o provisionamento em nuvem AD AD AD.

O provisionamento inter-directório permite-nos criar ambientes [de identidade híbrida.](../hybrid/whatis-hybrid-identity.md)


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Que tipos de provisionamento inter-directório faz o apoio da Azure AD

A Azure AD apoia atualmente três métodos para a realização do provisionamento inter-directório. Estes métodos são:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) - a ferramenta da Microsoft projetada para cumprir e realizar a sua identidade híbrida, incluindo o fornecimento inter-directório de Ative Directory a Azure AD.

- [Azure AD Connect Cloud Provisioning](../cloud-sync/what-is-cloud-sync.md) - um novo agente da Microsoft projetado para cumprir e cumprir os seus objetivos de identidade híbrida.  Proporciona uma experiência de provisionamento inter-directório leve entre o Ative Directory e o Azure AD.

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) - a solução de gestão de identidade e acesso da Microsoft que o ajuda a gerir os utilizadores, credenciais, políticas e acesso dentro da sua organização. Além disso, a MIM fornece provisões inter-directórios avançadas para alcançar ambientes de identidade híbrida para o Ative Directory, Azure AD e outros diretórios.

### <a name="key-benefits"></a>Principais vantagens

Esta capacidade de provisionamento inter-directório oferece os seguintes benefícios significativos para o negócio:

- [Sincronização de hash de palavra-passe](../hybrid/whatis-phs.md) - Um método de entrada que sincroniza um haxixe de um utilizador no local com Azure AD.
- [Autenticação pass-through](../hybrid/how-to-connect-pta.md) - Um método de entrada que permite aos utilizadores usar a mesma palavra-passe no local e na nuvem, mas não requer a infraestrutura adicional de um ambiente federado.
- [Integração da Federação](../hybrid/how-to-connect-fed-whatis.md) - pode ser usado para configurar um ambiente híbrido usando uma infraestrutura AD FS no local. Também fornece capacidades de gestão de FS AD, tais como renovação de certificados e implementações adicionais de servidores AD FS.
- [Sincronização](../hybrid/how-to-connect-sync-whatis.md) - Responsável pela criação de utilizadores, grupos e outros objetos.  Além disso, certificar-se de que as informações de identidade dos seus utilizadores e grupos no local estão a combinar com a nuvem.  Esta sincronização também inclui hashes de senha.
- [Health Monitoring](../hybrid/whatis-azure-ad-connect.md) - pode fornecer uma monitorização robusta e fornecer uma localização central no portal Azure para visualizar esta atividade. 


## <a name="next-steps"></a>Passos seguintes 
- [O que é a gestão do ciclo de vida da identidade](what-is-identity-lifecycle-management.md)
- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o fornecimento conduzido pela HR?](what-is-hr-driven-provisioning.md)
- [O que é o aprovisionamento de aplicações?](what-is-app-provisioning.md)