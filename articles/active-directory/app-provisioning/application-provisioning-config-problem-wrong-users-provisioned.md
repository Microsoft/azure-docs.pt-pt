---
title: Conjunto errado de utilizadores são aprovisionados para uma aplicação da Galeria AD Azure
description: Saiba como descobrir por que um conjunto diferente de utilizadores estão sendo a provisionados para uma aplicação do que aqueles que você esperava
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 5a109f1a06e7ee8aff3e455c009217ff670d2781
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84782216"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>O conjunto errado de utilizadores está a ser a provisionado para uma aplicação da Galeria AD Azure

Quais os utilizadores que são aprovisionados na aplicação é essencialmente impulsionado pelo qual utilizadores e grupos foram **atribuídos** à aplicação.

Utilize os seguintes recursos para saber como verificar quais utilizadores e grupos foram atribuídos a uma aplicação dentro do Azure Ative Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Atribuir um utilizador diretamente como administrador

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação a que pretende atribuir um utilizador da lista.

7. Assim que a aplicação estiver carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Para abrir o painel **de atribuição de adicionar,** clique no botão **Adicionar** no topo da lista **de Utilizadores e Grupos.**

9. clique no seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

10. Digite o **nome completo** ou endereço de **e-mail** do utilizador que está interessado em atribuir na caixa de pesquisa **de endereço de e-mail por nome ou e-mail.**

11. Passe por cima do **utilizador** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se pretender **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço **de e-mail** na caixa de pesquisa **de endereço de nome ou endereço de e-mail** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar utilizadores, clique no botão **Selecionar** para os adicionar à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **'Escolha's Role** in the **Add Assignment(Sessão)** para selecionar uma função a atribuir aos utilizadores selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Se o provisionamento estiver configurado e já em execução para uma app, os novos utilizadores devem ser aprovisionados para uma aplicação em aproximadamente 10 minutos. Verifique os **Registos de Auditoria** para obter mais detalhes.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Atribuir um grupo diretamente a uma aplicação como administrador

Para atribuir um ou mais grupos a uma aplicação diretamente, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação a que pretende atribuir um utilizador da lista.

7. Assim que a aplicação estiver carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Para abrir o painel **de atribuição de adicionar,** clique no botão **Adicionar** no topo da lista **de Utilizadores e Grupos.**

9. clique no seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

10. Digite o **nome completo** do grupo que está interessado em atribuir na caixa de pesquisa **de endereços de e-mail para procurar nome ou e-mail.**

11. Passe por cima do **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do grupo para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um grupo**, digite outro nome de grupo **completo** na caixa de pesquisa de endereço de nome **ou e-mail** e clique na caixa de verificação para adicionar este grupo à lista **Selecionada.**

13. Quando terminar de selecionar grupos, clique no botão **Selecionar** para os adicionar à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **'Escolha's Role** in the **Add Assignment(Sessão)** para selecionar uma função a atribuir aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos grupos selecionados.

Se o provisionamento estiver configurado e já em execução para uma app, os novos utilizadores contidos no grupo devem ser aprovisionados para uma aplicação em aproximadamente 10 minutos. Verifique os **Registos de Auditoria** para obter mais detalhes.

>[!IMPORTANT]
>Provisionamento do nome do grupo e detalhes do grupo, além dos membros, se apoiados em algumas candidaturas. Pode ativar ou desativar esta funcionalidade ativando ou desativando o **Mapeamento** de objetos de grupo indicados no **separador Provisioning.** 
>
>

Se os grupos de provisionamento estiverem habilitados, certifique-se de rever os mapeamentos de atributos para garantir que um campo adequado está a ser utilizado para o "ID correspondente". Este ID correspondente pode ser o nome de exibição ou pseudónimo de e-mail. O grupo e os seus membros não são a provisionados se a propriedade correspondente estiver vazia ou não povoada para um grupo em Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automatizar o Provisionamento de Utilizadores e Deprovisionamento para aplicações saas com diretório ativo Azure](user-provisioning.md)
