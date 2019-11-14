---
title: Pesquisa de nome de usuário durante a entrada-Azure Active Directory | Microsoft Docs
description: Como mensagens na tela refletem a pesquisa de nome de usuário durante a entrada no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024259"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Home Realm Discovery para páginas de entrada Azure Active Directory

Estamos a alterar o comportamento de início de sessão do Azure Active Directory (Azure AD) para introduzir novos métodos de autenticação e melhorar a usabilidade. Durante o início de sessão, o Azure AD determina onde um utilizador tem de se autenticar. O Azure AD toma decisões inteligentes ao ler as definições da organização e do utilizador relativamente ao nome de utilizador introduzido na página de início de sessão. Este é um passo em direção a um futuro sem palavras-passe que permite credenciais adicionais, como FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento da descoberta de realm inicial

Historicamente, a descoberta de realm inicial foi regida pelo domínio que é fornecido na entrada ou por uma política de descoberta de realm inicial para alguns aplicativos herdados. Por exemplo, em nosso comportamento de descoberta, um usuário Azure Active Directory poderia digitar o nome de usuário indigitadamente, mas ainda chegaria à tela de coleção de credenciais de sua organização. Isso ocorre quando o usuário fornece corretamente o nome de domínio da organização "contoso.com". Este comportamento não permite a granularidade para personalizar as experiências de um utilizador individual.

Para dar suporte a um intervalo mais amplo de credenciais e aumentar a usabilidade, Azure Active Directory comportamento de pesquisa de nome de usuário durante o processo de entrada agora é atualizado. O novo comportamento faz decisões inteligentes com a leitura de configurações de locatário e de nível de usuário com base no nome de usuários inserido na página de entrada. Para tornar isso possível, Azure Active Directory verificará se o nome de usuário inserido na página de entrada existe no domínio especificado ou redireciona o usuário para fornecer suas credenciais.

Um benefício adicional desse trabalho é o sistema de mensagens de erro aprimorado. Aqui estão alguns exemplos das mensagens de erro aprimoradas ao entrar em um aplicativo que dá suporte apenas a Azure Active Directory usuários.

- O nome de usuário está digitado Inou o nome de usuário ainda não foi sincronizado com o Azure AD:
  
    ![o nome de usuário está digitado ou não foi encontrado](./media/signin-realm-discovery/typo-username.png)
  
- O nome de domínio está digitado inparado:
  
    ![o nome de domínio está digitado ou não foi encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- O usuário tenta entrar com um domínio de consumidor conhecido:
  
    ![entrar com um domínio de consumidor conhecido](./media/signin-realm-discovery/consumer-domain.png)
  
- A senha está digitada incorretamente, mas o nome de usuário é preciso:  
  
    ![a senha está digitada incorretamente com um bom nome de usuário](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esse recurso pode ter um impacto em domínios federados que dependem da descoberta de realm inicial no nível de domínio antigo para forçar a Federação. Para obter atualizações sobre quando o suporte a domínio federado será adicionado, consulte [Home Realm Discovery durante a entrada para serviços de Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Enquanto isso, algumas organizações treinavam seus funcionários para entrar com um nome de usuário que não existe no Azure Active Directory, mas contém o nome de domínio apropriado, pois os nomes de domínio roteiam os usuários no momento para o ponto de extremidade de domínio da organização. O novo comportamento de entrada não permite isso. O usuário é notificado para corrigir o nome de usuário e não tem permissão para entrar com um nome de usuário que não existe no Azure Active Directory.
>
> Se você ou sua organização tiver práticas que dependem do comportamento antigo, é importante que os administradores da organização atualizem a documentação de entrada e autenticação de funcionários e treinem os funcionários a usarem seus Azure Active Directory nome de usuário para entrar.
  
Se você tiver preocupações com o novo comportamento, deixe seus comentários na seção de **comentários** deste artigo.  

## <a name="next-steps"></a>Passos seguintes

[Personalizar sua identidade visual de entrada](../fundamentals/add-custom-domain.md)
