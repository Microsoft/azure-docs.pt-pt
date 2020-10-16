---
title: Inscreva-se na pré-visualização dos cadernos Azure
description: Configure a sua conta de utilizador para Azure Notebooks usando uma conta Microsoft ou uma conta trabalho/escola.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 929a21785b5b48c81c2d2016874feed61021d3c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85832190"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>A sua conta de utilizador para visualização de Cadernos Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Pode utilizar os Cadernos Azure com ou sem iniciar sessão com uma conta de utilizador:

- Sem iniciar sessão, pode criar e executar cadernos, mas não pode reter cadernos ou ficheiros de dados como parte de projetos. Os utilizadores que recebem um link para um Azure Notebook, por exemplo, podem desfrutar do portátil sem necessidade de iniciar sação.
- Quando se inscreve, a Azure Notebooks mantém todos os seus projetos na sua conta. Os utilizadores inscritos também têm um ID de utilizador que lhes permite partilhar os seus projetos e cadernos com outros.
- Quando a conta utilizada para os Cadernos Azure também está associada a uma Subscrição Azure, você ganha benefícios adicionais, como executar cadernos em servidores mais poderosos, criar cadernos privados e conceder permissões a cadernos a utilizadores individuais.

A inscrição em Azure Notebooks requer uma conta Microsoft ou uma conta "Trabalho ou Escola". É solicitado para a sua conta ao selecionar o comando **Sign In** no lado superior direito da página Cadernos:

![Assine no comando para cadernos Azure](media/accounts/sign-in-command.png)

Todo o trabalho que faz nos Cadernos Azure está associado à conta que usa para iniciar scontabilidade. Cada conta também deve ter um ID de utilizador único no seu [perfil de utilizador.](azure-notebooks-user-profile.md) Como resultado, pode inscrever-se em Cadernos Azure com contas diferentes se precisar de manter conjuntos separados de projetos e identidades separadas. Por exemplo, cada membro de uma equipa de ciência de dados pode ter ambas as contas individuais ao lado de uma conta de grupo partilhada que eles usam para apresentar o seu trabalho a pessoas fora da sua empresa. Os instrutores, da mesma forma, podem manter uma conta para o seu papel de professor que é diferente de uma conta usada em consultoria externa ou trabalho de código aberto.

## <a name="microsoft-accounts"></a>Contas microsoft

As contas da Microsoft são usadas para iniciar súmitos de produtos e serviços da Microsoft, tais como Windows, Azure, outlook.com, OneDrive e XBox Live. Se utilizar algum desses serviços, é provável que já tenha uma Conta Microsoft que possa utilizar com os Azure Notebooks.

Se não tiver a certeza, selecione o comando **Criar Um** na origem da conta. Pode criar uma nova conta Microsoft utilizando qualquer endereço de e-mail de qualquer fornecedor.

![Comando para criar uma nova conta microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Se tentar criar uma nova conta com um endereço de e-mail que já tenha uma conta associada a ela, poderá ver a mensagem: "Não pode inscrever-se aqui com um endereço de e-mail de trabalho ou escola. Use um e-mail pessoal, como o Gmail ou o Yahoo!, ou obtenha um novo e-mail do Outlook." Nesse caso, tente iniciar sessão com o endereço de e-mail de trabalho sem criar uma nova conta.

Para contas infantis, o acesso aos Cadernos Azure está bloqueado por defeito. A inscrição com uma conta para criança mostra o erro abaixo mostrado:

![Erro ao tentar entrar com uma conta de criança: algo correu mal, o seu pai bloqueou o acesso](media/accounts/child-account-error.png)

Para permitir o acesso, um progenitor deve fazer os seguintes passos:

1. Visite `https://account.live.com/mk` e inscreva-se com uma conta de pai.
1. Na secção para a criança em questão, **selecione Gerir o acesso desta criança a aplicações de terceiros**.
1. Na página seguinte, selecione **Ativar o acesso**.
1. Quando a conta da criança for usada para iniciar sessão em Azure Notebooks, selecione **Sim** na solicitação de permissões que aparece.

> [!Warning]
> Permitir o acesso a apps de terceiros para Azure Notebooks também permite o acesso a todas as outras aplicações de terceiros. Os pais são aconselhados a usar a discrição ao permitir o acesso e podem querer monitorizar mais de perto a atividade dos seus filhos.

## <a name="work-or-school-accounts"></a>Contas escolares ou profissionais

Uma conta de trabalho ou escola é criada por um administrador de uma organização para permitir que um membro da organização aceda a serviços na nuvem da Microsoft, como o Microsoft 365, e também como uma conta para assinar no Windows num computador ligado a domínios. Uma conta de trabalho ou escola normalmente usa um endereço de e-mail organizacional, como any-user@contoso.com .

A assinatura em Cadernos Azure com uma conta de trabalho ou escola pode requerer o consentimento do administrador porque os Cadernos Azure recolhem ou utilizam (mas não divulgam) informações como o endereço de e-mail da conta e as informações do navegador do utilizador. (Os dados do navegador são usados para otimizar funcionalidades de acordo com o uso popular.)

O administrador de uma conta organizacional deve fornecer consentimento em nome dos utilizadores se os utilizadores estiverem impedidos de consentir individualmente. Neste caso, os utilizadores vêem a mensagem "Não pode aceder a esta aplicação":

!["Não pode aceder a esta aplicação" mensagem ao usar uma conta de trabalho ou escola](media/accounts/consent-permissions-denied.png)

Para fornecer o seu consentimento como administrador, utilize a página de consentimento do [administrador,](https://notebooks.azure.com/account/adminConsent)que o acompanha através do processo.

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Edite o seu perfil e iD do utilizador](azure-notebooks-user-profile.md)
