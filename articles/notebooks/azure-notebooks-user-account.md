---
title: Assine na Pré-visualização de Cadernos Azure
description: Configure a sua conta de utilizador para os Cadernos Azure utilizando uma conta Microsoft ou uma conta de trabalho/escola.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646301"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>A sua conta de utilizador para pré-visualização de cadernos Azure

Pode utilizar Cadernos Azure com ou sem fazer sessão com uma conta de utilizador:

- Sem iniciar sessão, pode criar e executar cadernos, mas não pode reter cadernos ou ficheiros de dados como parte de projetos. Os utilizadores que recebem um link para um Caderno Azure, por exemplo, podem desfrutar do caderno sem precisar em sessão.
- Quando assinado, a Azure Notebooks mantém todos os seus projetos com a sua conta. Os utilizadores de sutique satisfaçam também um ID de utilizador que lhes permite partilhar os seus projetos e cadernos com outros.
- Quando a conta utilizada para os Cadernos Azure também está associada a uma Subscrição Azure, você ganha benefícios adicionais, como executar cadernos em servidores mais poderosos, criar cadernos privados e conceder permissões a cadernos para utilizadores individuais.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

A inscrição nos Cadernos Azure requer uma conta Microsoft ou uma conta "Work or School". É solicitado para a sua conta ao selecionar o comando **Sign In** no lado superior direito da página Dos Cadernos:

![Inscreva-se no comando dos Cadernos Azure](media/accounts/sign-in-command.png)

Todo o trabalho que faz nos Cadernos Azure está associado à conta que usa para iniciar sessão. Cada conta também deve ter um ID único no seu perfil de [utilizador](azure-notebooks-user-profile.md). Como resultado, pode assinar em Cadernos Azure com contas diferentes se precisar de manter conjuntos separados de projetos e identidades separadas. Por exemplo, cada membro de uma equipa de ciência de dados pode ter ambas as contas individuais ao lado de uma conta partilhada em grupo que eles usam para apresentar o seu trabalho a pessoas fora da sua empresa. Os instrutores, da mesma forma, podem manter uma conta pelo seu papel pedagógico que é diferente de uma conta usada em consultoria externa ou trabalho de código aberto.

## <a name="microsoft-accounts"></a>Contas da Microsoft

As contas da Microsoft são usadas para iniciar sessão em vários produtos e serviços da Microsoft, tais como Windows, Azure, outlook.com, OneDrive e XBox Live. Se utilizar algum desses serviços, é provável que já tenha uma Conta Microsoft que possa utilizar com cadernos Azure.

Se não tiver a certeza, selecione o comando **Create One** no pedido de conta. Pode criar uma nova conta Microsoft utilizando qualquer endereço de e-mail de qualquer fornecedor.

![Comando para criar uma nova conta Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Se tentar criar uma nova conta com um endereço de e-mail que já tenha uma conta associada à si, poderá ver a mensagem: "Não se pode inscrever aqui com um endereço de e-mail de trabalho ou escola. Use um e-mail pessoal, como gmail ou Yahoo!, ou obtenha um novo e-mail do Outlook." Nesse caso, tente iniciar sessão com o endereço de e-mail de trabalho sem criar uma nova conta.

Para contas infantis, o acesso aos Cadernos Azure é bloqueado por padrão. A inscrição com uma conta infantil apresenta o erro abaixo indicado:

![Erro ao tentar entrar com uma conta de criança: algo correu mal, o seu pai bloqueou o acesso](media/accounts/child-account-error.png)

Para permitir o acesso, um progenitor deve fazer os seguintes passos:

1. Visite `https://account.live.com/mk` e inscreva-se com uma conta dos pais.
1. Na secção para a criança em questão, selecione **Gerir o acesso desta criança a aplicações de terceiros**.
1. Na página seguinte, selecione **Aceder**.
1. Quando a conta da criança for usada para entrar nos Cadernos Azure, selecione **Sim** no pedido de permissões que aparece.

> [!Warning]
> Permitir o acesso a apps de terceiros para O Notebooks Azure também permite o acesso a todas as outras aplicações de terceiros. Os pais são aconselhados a usar a discrição ao permitir o acesso e podem querer monitorizar mais de perto a atividade dos seus filhos.

## <a name="work-or-school-accounts"></a>Contas escolares ou profissionais

Uma conta de trabalho ou escola é criada pelo administrador de uma organização para permitir que um membro da organização aceda a serviços na nuvem da Microsoft, como o Office 365, e também como uma conta para assinar no Windows num computador de domínio. Uma conta de trabalho ou escola normalmente usa any-user@contoso.comum endereço de e-mail organizacional, como .

A inscrição nos Cadernos Azure com uma conta de trabalho ou escola pode exigir o consentimento do administrador porque os Cadernos Azure recolhem ou utilizam (mas não divulgam) informações como o endereço de e-mail da conta e as informações do navegador do utilizador. (Os dados do navegador são utilizados para otimizar funcionalidades de acordo com o uso popular.)

O administrador de uma conta organizacional deve fornecer o consentimento em nome dos utilizadores se os utilizadores estiverem impedidos de consentir individualmente. Neste caso, os utilizadores vêem a mensagem "Não pode aceder a esta aplicação":

![Mensagem "Não pode aceder a esta aplicação" ao utilizar uma conta de trabalho ou escola](media/accounts/consent-permissions-denied.png)

Para fornecer o consentimento como administrador, utilize a página de consentimento do [administrador,](https://notebooks.azure.com/account/adminConsent)que o acompanha durante o processo.

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Editar o seu perfil e ID do utilizador](azure-notebooks-user-profile.md)
