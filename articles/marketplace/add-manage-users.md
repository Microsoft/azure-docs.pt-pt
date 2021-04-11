---
title: Adicionar e gerir utilizadores para o mercado comercial - Azure Marketplace
description: Saiba como gerir os utilizadores no programa de marketplace comercial para uma conta de marketplace comercial da Microsoft no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108412"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Adicionar e gerir utilizadores para o mercado comercial

**Funções adequadas**

- Proprietário
- Gestor

A secção de **Utilizadores** do Partner Center (em **Definições de Conta)** permite-lhe utilizar a Azure AD para gerir os utilizadores, grupos e aplicações AD Azure que tenham acesso à sua conta Partner Center. A sua conta deve ter permissões ao nível do Gestor para a conta de [trabalho (inquilino Azure AD)](company-work-accounts.md) nas quais pretende adicionar ou editar utilizadores. Para gerir os utilizadores dentro de uma conta de trabalho/inquilino diferente, terá de se assinar e depois voltar a entrar como utilizador com permissões **do Manager** nessa conta de trabalho/inquilino.

Depois de ter assinado com a sua conta de trabalho (inquilino Azure AD), pode adicionar e gerir utilizadores.

## <a name="add-existing-users"></a>Adicionar utilizadores existentes

Para adicionar utilizadores à sua conta Partner Center que já existe na conta de trabalho da sua empresa [(Azure AD inquilino)](company-work-accounts.md):

1. Aceda aos **Utilizadores** (nas **definições de Conta)** e selecione **Adicionar utilizadores**.
1. Selecione um ou mais utilizadores da lista que aparece. Pode utilizar a caixa de pesquisa para procurar utilizadores específicos. *Se selecionar mais do que um utilizador para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários utilizadores com diferentes funções/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
1. Quando terminar de escolher os utilizadores, **selecione Adicionar selecionado**.
1. Na secção **Funções,** especifique as funções ou permissões personalizadas para os utilizadores selecionados.
1. Selecione **Guardar**.

## <a name="create-new-users"></a>Criar novos utilizadores

Para criar novas contas de utilizador, tem de ter uma conta com permissões [de administrador global.](/azure/active-directory/roles/permissions-reference)

1. Ir para **os Utilizadores** (nas **definições de Conta),** selecionar **Adicionar utilizadores,** em seguida, escolher **Criar novos utilizadores**.
1. Introduza um nome próprio, apelido e nome de utilizador para cada novo utilizador.
1. Se pretender que o novo utilizador tenha uma conta de administrador global no diretório da sua organização, verifique a caixa com a etiqueta **Faça deste utilizador um administrador Global no seu AD Azure, com controlo total sobre todos os recursos do diretório.** Isto dará ao utilizador acesso total a todas as funcionalidades administrativas do Azure AD da sua empresa. Eles poderão adicionar e gerir utilizadores na conta de trabalho da sua organização (inquilino Azure AD), embora não no Partner Center, a menos que você conceda à conta o papel/permissões apropriados.
1. Se verificou a caixa para **fazer deste utilizador um administrador Global,** terá de fornecer um e-mail de *recuperação de passwords* para que o utilizador recupere a sua palavra-passe, se necessário.
1. Na secção **de membros do Grupo,** selecione quaisquer grupos aos quais pretenda que o novo utilizador pertença.
1. Na secção **Peças,** especifique as funções ou permissões personalizadas para o utilizador.
1. Selecione **Guardar**.

A criação de um novo utilizador no Partner Center também criará uma conta para esse utilizador na conta de trabalho (Azure AD inquilina) à qual está inscrito. Fazer alterações no nome de um utilizador no Partner Center fará as mesmas alterações na conta de trabalho da sua organização (inquilino Azure AD).

## <a name="invite-new-users-by-email"></a>Convidar novos utilizadores por e-mail

Para convidar utilizadores que não fazem parte da sua conta de trabalho da empresa (inquilino Azure AD) por e-mail, você deve ter uma conta com permissões [de administrador global.](/azure/active-directory/roles/permissions-reference)

1. Aceda aos **Utilizadores** (nas **definições de Conta),** selecione **Adicionar utilizadores,** em seguida, escolha **Convidar os utilizadores por e-mail**.
1. Insira um ou mais endereços de e-mail (até 10), separados por vírgulas ou pontos-e-vírgulas.
1. Na secção **Peças,** especifique as funções ou permissões personalizadas para o utilizador.
1. Selecione **Guardar**.

Os utilizadores que convidou receberão um convite por e-mail para se juntarem à sua conta partner Center. Uma nova conta de utilizador de hóspedes será criada na sua conta de trabalho (Azure AD inquilino). Cada utilizador terá de aceitar o seu convite antes de poder aceder à sua conta.

Se precisar de reensirar um convite, visite a página *dos Utilizadores,* encontre o convite na lista de utilizadores, selecione o seu endereço de e-mail (ou o texto que diz *Convite pendente).* Em seguida, na parte inferior da página, selecione **o convite de Resend**.

Se a sua organização utilizar a integração de [diretórios](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) para sincronizar o serviço de diretório no local com o seu AD Azure, não poderá criar novos utilizadores, grupos ou aplicações AD AZure no Partner Center. Você (ou outro administrador no seu diretório no local) terá de criá-los diretamente no diretório no local antes de poder vê-los e adicioná-los no Partner Center.

## <a name="remove-a-user"></a>Remover um utilizador

Para remover um utilizador da sua conta de trabalho (inquilino Azure AD), vá aos **Utilizadores** (nas **definições de Conta),** selecione o utilizador que pretende remover utilizando a caixa de verificação na coluna de extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover os utilizadores selecionados.

## <a name="change-a-user-password"></a>Alterar uma palavra-passe do utilizador

Se um dos seus utilizadores precisar de alterar a sua palavra-passe, pode fazê-lo se tiver fornecido um *e-mail de recuperação de passwords* ao criar a conta de utilizador. Também pode atualizar a palavra-passe de um utilizador seguindo os passos abaixo. Para alterar a palavra-passe de um utilizador na sua conta de trabalho da empresa (inquilino Azure AD), tem de ser inscrito numa conta com permissões [de administrador global.](/azure/active-directory/roles/permissions-reference) Isto irá alterar a palavra-passe do utilizador no seu inquilino Azure AD, juntamente com a palavra-passe que usam para aceder ao Partner Center.

1. A partir da página **Utilizadores** (nas **definições de Conta),** selecione o nome da conta de utilizador que pretende editar.
1. Selecione o botão **de palavra-passe Reset** na parte inferior da página.
1. Uma página de confirmação aparecerá para mostrar as informações de login para o utilizador, incluindo uma senha temporária. Certifique-se de imprimir ou copiar esta informação e fornetilá-la ao utilizador, uma vez que não poderá aceder à senha temporária depois de sair desta página.
