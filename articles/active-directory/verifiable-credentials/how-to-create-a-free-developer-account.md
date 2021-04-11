---
title: Como criar um inquilino de desenvolvimento gratuito do Azure Ative Directory
description: Este artigo mostra-lhe como criar uma conta de programador
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1894ffb7ca75a283e3a74d17c3e73de51fc46d8d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170249"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Como criar um inquilino de desenvolvimento gratuito do Azure Ative Directory

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Enquanto em Pré-visualização é necessária uma licença P2. 

Existem duas maneiras fáceis de criar um Diretório Ativo Azure gratuito com uma licença de teste P2 para que possa instalar o serviço de emitente de credencial verificável e pode testar a criação e validação de credenciais verificáveis:

- [Junte-se](https://aka.ms/o365devprogram) ao Programa de Desenvolvedores gratuito do Microsoft 365 e obtenha uma caixa de areia, ferramentas e outros recursos gratuitos como um Azure Ative Directory com licenças P2. Utilizadores configurados, Grupos, caixas de correio, etc.
- Crie um novo [inquilino](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) e ative um [teste gratuito](https://azure.microsoft.com/trial/get-started-active-directory/) de Azure AD Premium P1 ou P2 no seu novo inquilino.

Se decidir inscrever-se no programa de desenvolvimento gratuito da Microsoft 365, tem de seguir alguns passos fáceis:

1. Clique no botão 'Juntar-se' agora no ecrã

2. Faça sing com uma nova Conta Microsoft ou use uma conta (trabalho) existente que já tenha.

3. Na página de inscrição selecione a sua região, insira um nome de empresa e aceite os termos e condições do programa antes de clicar em seguida

4. Clique na subscrição configurada. Especifique a região onde pretende criar o seu novo inquilino, crie um nome de utilizador, domínio e introduza uma senha. Isto vai criar um novo inquilino e o primeiro administrador do inquilino

5. Insira as informações de segurança necessárias para proteger a conta do administrador do seu novo inquilino. Isto irá configurar a autenticação do MFA para a conta


Neste momento, criou um inquilino com 25 licenças de utilizador E5. As licenças E5 incluem licenças Azure AD P2. Opcionalmente, pode adicionar pacotes de dados de amostra com utilizadores, grupos, correio e SharePoint para ajudá-lo a testar no seu ambiente de desenvolvimento. Para o serviço de emissão de credenciais verificáveis, não são necessários.

Para sua conveniência, você poderia adicionar a sua própria conta de trabalho como [hóspede](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) no inquilino recém-criado e usar essa conta para administrar o inquilino. Se quiser que a conta do cliente seja capaz de gerir o Serviço credencial verificável, tem de atribuir a função 'Administrador Global' a esse utilizador.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma conta de programador, pode experimentar o nosso [primeiro tutorial](get-started-verifiable-credentials.md) para saber mais sobre credenciais verificáveis.