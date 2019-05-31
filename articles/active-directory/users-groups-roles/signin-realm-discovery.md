---
title: Pesquisa de nome de utilizador durante a autenticação início de sessão - Azure Active Directory | Documentos da Microsoft
description: Como no ecrã de mensagens refletem a pesquisa de nome de utilizador durante o início de sessão
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db627359b75aa0ea19e30a8d22bcacaa3409cb4a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418192"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Deteção de realm inicial para páginas de início de sessão no Azure Active Directory

Estamos a alterar o comportamento de início de sessão do Azure Active Directory (Azure AD) para introduzir novos métodos de autenticação e melhorar a usabilidade. Durante o início de sessão, o Azure AD determina onde um utilizador tem de se autenticar. O Azure AD toma decisões inteligentes ao ler as definições da organização e do utilizador relativamente ao nome de utilizador introduzido na página de início de sessão. Este é um passo em direção a um futuro sem palavras-passe que permite credenciais adicionais, como FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento de deteção de realm inicial

Historicamente, deteção de realm inicial foi controlada pelo domínio em que está disponível no início de sessão ou por uma política de deteção de Realm Inicial para alguns aplicativos herdados. Por exemplo, no nosso comportamento de deteção um utilizador do Azure Active Directory poderia digitar seu nome de utilizador, mas ainda seria chegar ao ecrã de coleção de credenciais da sua organização. Isto ocorre quando o utilizador fornece corretamente o nome de domínio "contoso.com" da organização. Este comportamento não permite a granularidade para personalizar as experiências de um utilizador individual.

Para suportar um maior número de credenciais e aumentar a usabilidade, o comportamento de pesquisa de nome de utilizador do Azure Active Directory durante o processo de início de sessão está agora atualizado. O novo comportamento toma decisões inteligentes com a leitura de definições de nível inquilino e o utilizador com base no nome de utilizador introduzido na página de início de sessão. Para tornar isso possível, o Azure Active Directory irá verificar para ver se o nome de utilizador que é introduzido na página de início de sessão existe no respetivo domínio especificado ou redirecionará o usuário de fornecer as credenciais.

Uma vantagem adicional desse trabalho é de erro melhoradas de mensagens. Aqui estão alguns exemplos de erro melhoradas, mensagens quando iniciar sessão a uma aplicação que suporta apenas a utilizadores do Azure Active Directory.

- O nome de utilizador está incorreto ou o nome de utilizador ainda não tenha sido sincronizado com o Azure AD:
  
    ![o nome de utilizador é escrito incorretamente ou não encontrado](./media/signin-realm-discovery/typo-username.png)
  
- O nome de domínio está escrito incorretamente:
  
    ![o nome de domínio está escrito incorretamente ou não encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- O utilizador tenta iniciar sessão com um domínio de consumidor conhecidos:
  
    ![Inicie sessão com um domínio de consumidor conhecidos](./media/signin-realm-discovery/consumer-domain.png)
  
- A palavra-passe é escrito incorretamente, mas o nome de utilizador é preciso:  
  
    ![palavra-passe é escrito incorretamente com bom nome de utilizador](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esta funcionalidade pode ter um impacto nos domínios Federados da entidade confiadora na deteção de Realm Inicial para forçar o Federação no nível do domínio antigo. Para obter atualizações sobre o quando será adicionado suporte de domínio federado, consulte [deteção de realm inicial durante o início de sessão para serviços do Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Entretanto, algumas organizações têm formação aos seus funcionários para iniciar sessão com um nome de utilizador que não existe no Azure Active Directory, mas contém o nome de domínio adequado, porque os nomes de domínio encaminha os utilizadores atualmente ao ponto final de domínio da sua organização. O novo comportamento de início de sessão não permite isso. O utilizador é notificado para corrigir o nome de utilizador, e eles não têm permissão para iniciar sessão com um nome de utilizador que não existe no Azure Active Directory.
>
> Se ou sua organização tiver práticas que dependem do comportamento antigo, é importante para os administradores da organização para atualizar a documentação de início de sessão e autenticação de funcionários e para preparar os funcionários para usarem o respetivo nome de utilizador do Azure Active Directory para iniciar sessão.
  
Se tiver problemas com o novo comportamento, deixe os seus comentários na **comentários** seção deste artigo.  

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o seu início de sessão de imagem corporativa](../fundamentals/add-custom-domain.md)
