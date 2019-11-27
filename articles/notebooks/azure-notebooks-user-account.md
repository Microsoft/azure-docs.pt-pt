---
title: Inicie sessão em blocos de notas do Azure
description: Configure a sua conta de utilizador para blocos de notas do Azure utilizando uma conta Microsoft ou uma conta de trabalho/escola.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: a2d8c8180dfb5dc31e273c7953a57f40cf31238d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277628"
---
# <a name="your-user-account-for-azure-notebooks"></a>Sua conta de utilizador para blocos de notas do Azure

Pode usar blocos de notas do Azure, com ou sem início de sessão com uma conta de utilizador:

- Sem entrar, pode criar e executar blocos de notas, mas não é possível manter os blocos de notas ou dados de ficheiros como parte dos projetos. Os utilizadores que recebem uma ligação para um bloco de notas do Azure, por exemplo, podem desfrutar o bloco de notas sem ser necessário iniciar sessão.
- Quando conectado, blocos de notas do Azure mantém todos os seus projetos com a sua conta. Utilizadores com sessão iniciada também tem um ID de utilizador que permita que sejam compartilhar seus projetos e blocos de notas com outras pessoas.
  - Quando a conta utilizada para blocos de notas do Azure está também associada uma subscrição do Azure, obtém os benefícios adicionais como a execução de blocos de notas em servidores mais poderosos, criação de blocos de notas privados e conceder permissões para blocos de notas para utilizadores individuais.

A iniciar sessão blocos de notas do Azure requer uma Account Microsoft ou uma conta de "Profissional ou escolar". Você será solicitado a fornecer sua conta ao selecionar o comando **entrar** no canto superior direito da página blocos de anotações:

![Inicie sessão no comando para blocos de notas do Azure](media/accounts/sign-in-command.png)

Todo o trabalho que fazer em blocos de notas do Azure está associado com a conta que utiliza para iniciar sessão. Cada conta também deve ter uma ID de usuário exclusiva em seu [perfil de usuário](azure-notebooks-user-profile.md). Como resultado, poderá entrar em blocos de notas do Azure com contas diferentes se precisar de manter conjuntos separados de projetos e identidades separadas. Por exemplo, cada membro de uma equipe de ciência de dados pode ter ambas as contas individuais em conjunto com como uma conta de grupo compartilhadas que podem utilizar para apresentar o seu trabalho para pessoas fora da sua empresa. Os instrutores, da mesma forma, podem manter uma conta para a respetiva função de ensino que difere de uma conta utilizada na consultoria externo ou de trabalho de código-fonte aberto.

## <a name="microsoft-accounts"></a>Contas Microsoft

Contas da Microsoft são utilizadas para iniciar sessão em qualquer número de produtos da Microsoft e serviços, como o Windows Azure, outlook.com, OneDrive e XBox Live. Se utilizar qualquer um desses serviços, é provável que já tem uma Account Microsoft que pode utilizar com blocos de notas do Azure.

Se não tiver certeza, selecione o comando **criar um** no prompt da conta. Pode criar uma nova conta Microsoft com qualquer endereço de e-mail a partir de qualquer fornecedor de serviços.

![Comando para criar uma nova conta Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Se você tentar criar uma nova conta com um endereço de email que já tenha uma conta associada a ela, você poderá ver a mensagem "não é possível inscrever-se aqui com um endereço de email corporativo ou de estudante. Use um email pessoal, como Gmail ou Yahoo!, ou obtenha um novo email do Outlook. Nesse caso, tente entrar com o endereço de email de trabalho sem criar uma nova conta.

Para contas de criança, o acesso aos blocos de notas do Azure é bloqueado por predefinição. Iniciar sessão com uma conta de menor apresenta o erro mostrado abaixo:

![Ocorreu um erro ao tentar iniciar sessão com uma conta de menor: Ocorreu um erro, bloqueou o acesso](media/accounts/child-account-error.png)

Para ativar o acesso, um elemento principal terá de efetuar os seguintes passos:

1. Visite `https://account.live.com/mk` e entre com uma conta pai.
1. Na seção para o filho em questão, selecione **gerenciar o acesso deste filho a aplicativos**de terceiros.
1. Na página seguinte, selecione **habilitar acesso**.
1. Quando a conta filho for usada em seguida para fazer logon no Azure Notebooks, selecione **Sim** no prompt de permissões que aparece.

> [!Warning]
> Também é ativar o acesso a aplicações de terceiros para blocos de notas do Azure permite o acesso a todas as outras aplicações de terceiros. Elementos principais são aconselhados a utilizar o critério ao ativar a aceder e podem querer monitorizar atividade do seu filho mais de perto.

## <a name="work-or-school-accounts"></a>Contas escolares ou profissionais

Uma conta escolar ou profissional é criada pelo administrador da organização para permitir um membro da organização aceder a serviços cloud da Microsoft, como o Office 365 bem como uma conta para iniciar sessão no Windows num computador associado a um domínio. Uma conta corporativa ou de estudante geralmente usa um endereço de email organizacional, como any-user@contoso.com.

Iniciar sessão no Azure blocos de notas com uma conta escolar ou profissional pode exigir o consentimento de administrador, porque os blocos de notas do Azure recolhe ou utiliza (mas não divulga a) informações tais como endereço de e-mail da conta e informações de browser do utilizador. (Os dados de browser são utilizados para otimizar as funcionalidades, de acordo com a utilização popular.)

O administrador de uma conta organizacional tem de fornecer consentimento em nome dos utilizadores se os utilizadores são impedidos de consentir individualmente. Neste caso, os utilizadores verão a mensagem "Não pode aceder a esta aplicação":

![Mensagem "Não pode aceder a esta aplicação" ao utilizar uma conta escolar ou profissional](media/accounts/consent-permissions-denied.png)

Para fornecer consentimento como administrador, use a [página de consentimento do administrador](https://notebooks.azure.com/account/adminConsent), que o orientará durante o processo.

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Editar seu perfil e ID de usuário](azure-notebooks-user-profile.md)
