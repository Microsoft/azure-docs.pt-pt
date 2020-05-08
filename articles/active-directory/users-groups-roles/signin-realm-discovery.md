---
title: Lookup username durante o sign-in - Azure Ative Diretório / Microsoft Docs
description: Como as mensagens no ecrã refletem a procura de nome de utilizador durante o início de sessão no Diretório Ativo do Azure
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
ms.openlocfilehash: c1f27c7b91a78da8944c23fd353d3b6791b3e015
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582550"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Descoberta do reino doméstico para páginas de inscrição do Diretório Ativo Azure

Estamos a mudar o nosso comportamento de entrada de sign-in Azure Ative Directory (Azure AD) para dar espaço a novos métodos de autenticação e melhorar a usabilidade. Durante o início de sessão, a Azure AD determina onde um utilizador precisa de autenticar. A Azure AD toma decisões inteligentes lendo as definições de organização e utilizador para o nome de utilizador introduzido na página de entrada. Este é um passo para um futuro livre de palavras-passe que permite credenciais adicionais como o FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento de descoberta do reino doméstico

Historicamente, a descoberta do reino doméstico foi governada pelo domínio que é fornecido no sign-in ou por uma política home realm discovery para algumas aplicações antigas. Por exemplo, no nosso comportamento de descoberta, um utilizador do Azure Ative Directory poderia escrever mal o seu nome de utilizador, mas ainda chegaria ao ecrã de recolha credencial da sua organização. Isto ocorre quando o utilizador fornece corretamente o nome de domínio da organização "contoso.com". Este comportamento não permite que a granularidade personalize experiências para um utilizador individual.

Para suportar uma gama mais alargada de credenciais e aumentar a usabilidade, o comportamento de pesquisa de nome de utilizador do Azure Ative Directory durante o processo de início de sessão é agora atualizado. O novo comportamento toma decisões inteligentes lendo as definições de nível de organização e de nível de utilizador com base no nome de utilizador introduzido na página de entrada. Para que isso seja possível, o Azure Ative Directory verificará se o nome de utilizador introduzido na página de entrada existe no seu domínio especificado ou redireciona o utilizador para fornecer as suas credenciais.

Um benefício adicional deste trabalho é a melhoria das mensagens de erro. Aqui estão alguns exemplos da melhoria das mensagens de erro ao iniciar sessão numa aplicação que suporta apenas utilizadores do Azure Ative Directory.

- O nome de utilizador está enevoado ou o nome de utilizador ainda não foi sincronizado com a AD Azure:
  
    ![o nome de utilizador é mistyed ou não encontrado](./media/signin-realm-discovery/typo-username.png)
  
- O nome de domínio está enevoado:
  
    ![o nome de domínio é mistyed ou não encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- O utilizador tenta iniciar sessão com um domínio de consumo conhecido:
  
    ![iniciar sessão com um domínio de consumo conhecido](./media/signin-realm-discovery/consumer-domain.png)
  
- A palavra-passe está enevoado, mas o nome de utilizador é preciso:  
  
    ![palavra-passe é enevoado com bom nome de utilizador](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esta funcionalidade pode ter um impacto em domínios federados que dependem da antiga Descoberta do Reino Doméstico de domínio para forçar a federação. Para obter atualizações sobre quando será adicionado o suporte de domínio federado, consulte a descoberta do Reino Doméstico durante o [início de sessão para os serviços da Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Entretanto, algumas organizações treinaram os seus colaboradores para se inscreverem com um nome de utilizador que não existe no Azure Ative Directory, mas que contém o nome de domínio adequado, porque os nomes de domínio saem dos utilizadores atualmente para o ponto final de domínio da sua organização. O novo comportamento de inscrição não permite isto. O utilizador é notificado para corrigir o nome de utilizador e não está autorizado a iniciar sessão com um nome de utilizador que não existe no Diretório Ativo do Azure.
>
> Se você ou sua organização têm práticas que dependem do comportamento antigo, é importante para os administradores da organização atualizar documentação de inscrição e autenticação dos colaboradores e formar funcionários para usar o seu nome de utilizador Azure Ative Directory para iniciar sessão.
  
Se tiver preocupações com o novo comportamento, deixe os seus comentários na secção **feedback** deste artigo.  

## <a name="next-steps"></a>Passos seguintes

[Personalize a sua marca de sessão](../fundamentals/add-custom-domain.md)
