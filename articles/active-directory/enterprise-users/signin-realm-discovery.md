---
title: Pesquisa de nome de utilizador durante a inscrição - Azure Ative Directory / Microsoft Docs
description: Como as mensagens no ecrã refletem a procura do nome de utilizador durante a entrada no Azure Ative Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ec804029919f0b9c4be26b044cd7a3eeddf6690
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547836"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Descoberta do reino doméstico para páginas de inscrição do Azure Ative Directory

Estamos a mudar o nosso comportamento de inscrição no Azure Ative Directory (Azure AD) para dar espaço a novos métodos de autenticação e melhorar a usabilidade. Durante a entrada, a Azure AD determina onde um utilizador precisa de autenticar. A Azure AD toma decisões inteligentes através da organização de leitura e das definições do utilizador para o nome de utilizador introduzido na página de entrada. Este é um passo rumo a um futuro livre de palavras-passe que permite credenciais adicionais como o FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento de descoberta de reino doméstico

Historicamente, a descoberta do reino doméstico foi governada pelo domínio que é fornecido no início ou por uma política de Descoberta do Reino Doméstico para algumas aplicações antigas. Por exemplo, no nosso comportamento de descoberta, um utilizador do Azure Ative Directory poderia desajustar o seu nome de utilizador, mas ainda assim chegaria ao ecrã de recolha de credenciais da sua organização. Isto ocorre quando o utilizador fornece corretamente o nome de domínio da organização "contoso.com". Este comportamento não permite que a granularidade personalize experiências para um utilizador individual.

Para suportar uma ampla gama de credenciais e aumentar a usabilidade, o comportamento de procura do nome de utilizador da Azure Ative Directory durante o processo de inscrição é agora atualizado. O novo comportamento toma decisões inteligentes lendo definições de nível de organização e nível de utilizador com base no nome de utilizador introduzido na página de entrada. Para tornar isto possível, o Azure Ative Directory verificará se o nome de utilizador que está inserido na página de início de sintrodução existe no seu domínio especificado ou redireciona o utilizador para fornecer as suas credenciais.

Um benefício adicional deste trabalho é a melhoria das mensagens de erro. Aqui estão alguns exemplos da melhoria das mensagens de erro ao iniciar sessão numa aplicação que suporta apenas utilizadores do Azure Ative Directory.

- O nome de utilizador está mal marcado ou o nome de utilizador ainda não foi sincronizado com Azure AD:
  
    ![o nome de utilizador é mal assinalado ou não encontrado](./media/signin-realm-discovery/typo-username.png)
  
- O nome de domínio está mal digitado:
  
    ![o nome de domínio é mal digitado ou não encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- O utilizador tenta entrar com um domínio de consumo conhecido:
  
    ![iniciar s-in com um domínio de consumo conhecido](./media/signin-realm-discovery/consumer-domain.png)
  
- A palavra-passe está mal afinada, mas o nome de utilizador é preciso:  
  
    ![palavra-passe é mal-humorado com bom nome de utilizador](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esta funcionalidade pode ter um impacto nos domínios federados, confiando no antigo nível de domínio Home Realm Discovery para forçar a federação. Para obter atualizações sobre quando será adicionado o suporte de domínio federado, consulte [a descoberta do Reino Doméstico durante a entrada nos serviços da Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Entretanto, algumas organizações treinaram os seus colaboradores para assinarem com um nome de utilizador que não existe no Azure Ative Directory, mas que contém o nome de domínio adequado, porque o nome de domínio encaminha os utilizadores atualmente para o ponto final de domínio da sua organização. O novo comportamento de inscrição não permite isto. O utilizador é notificado para corrigir o nome de utilizador, e não está autorizado a iniciar súmis com um nome de utilizador que não exista no Diretório Azure Ative.
>
> Se você ou a sua organização têm práticas que dependem do comportamento antigo, é importante que os administradores da organização atualizem a documentação de inscrição e autenticação dos colaboradores e treinem os colaboradores para utilizarem o seu nome de utilizador Azure Ative Directory para se inscreverem.
  
Se tiver preocupações com o novo comportamento, deixe as suas observações na secção **de Feedback** deste artigo.  

## <a name="next-steps"></a>Passos seguintes

[Personalize a sua marca de sinalização](../fundamentals/add-custom-domain.md)
