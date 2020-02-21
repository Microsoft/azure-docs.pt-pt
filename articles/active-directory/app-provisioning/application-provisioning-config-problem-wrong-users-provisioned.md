---
title: Conjunto errado de utilizadores são provisionados para uma app Azure AD Gallery
description: Saiba como descobrir por que um conjunto diferente de utilizadores estão sendo provisionados para uma aplicação do que aqueles que você esperava
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c94388011605da73666e82011bb8ef56d2af8d30
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522786"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Conjunto errado de utilizadores estão sendo provisionados para uma aplicação da Galeria AD Azure

Quais os utilizadores que são aprovisionados para a aplicação é essencialmente impulsionado pelo qual utilizadores e grupos foram **atribuídos** à aplicação.

Utilize os seguintes recursos para saber quais os utilizadores e grupos que foram atribuídos a uma aplicação dentro do Diretório Ativo do Azure.

## <a name="assign-a-user-directly-as-an-administrator"></a>Atribuir um utilizador diretamente como administrador

Para atribuir diretamente a um ou mais utilizadores uma aplicação, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que a aplicação for carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Para abrir o painel **adicionar** atribuição, clique no botão **Adicionar** em cima da lista de **Utilizadores e Grupos.**

9. clique nos Utilizadores e no seletor de **grupos** a partir do painel **adicionar atribuição.**

10. Digite o **nome completo** ou endereço de **e-mail** do utilizador que está interessado em atribuir na caixa de pesquisa de endereços de correio eletrónico **Pesquisar pelo nome ou pelo endereço de e-mail.**

11. Passe por cima do **utilizador** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço de **e-mail** na caixa de pesquisa de endereços de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar os utilizadores, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Se o fornecimento estiver configurado e já estiver em execução para uma aplicação, os novos utilizadores devem ser aprovisionados para uma aplicação em aproximadamente 10 minutos. Verifique os **Registos de Auditoria** para obter mais detalhes.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Atribuir um grupo diretamente a um pedido como administrador

Para atribuir um ou mais grupos a uma aplicação diretamente, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que a aplicação for carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Para abrir o painel **adicionar** atribuição, clique no botão **Adicionar** em cima da lista de **Utilizadores e Grupos.**

9. clique nos Utilizadores e no seletor de **grupos** a partir do painel **adicionar atribuição.**

10. Digite o **nome completo** do grupo que está interessado em atribuir na caixa de pesquisa de endereços de pesquisa de Pesquisa por nome ou endereço de **e-mail.**

11. Paire sobre o **grupo** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do grupo para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um grupo**, digite outro nome de grupo **completo** na caixa de pesquisa de endereços de pesquisa de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este grupo à lista **Selecionada.**

13. Quando terminar de selecionar grupos, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos grupos selecionados.

Se o fornecimento estiver configurado e já estiver em execução para uma aplicação, os novos utilizadores contidos no grupo devem ser aprovisionados para uma aplicação em aproximadamente 10 minutos. Verifique os **Registos de Auditoria** para obter mais detalhes.

>[!IMPORTANT]
>Fornecimento do nome do grupo e dos detalhes do grupo, para além dos membros, se apoiados em algumas candidaturas. Pode ativar ou desativar esta funcionalidade, permitindo ou desativando o **Mapeamento** para objetos de grupo mostrados no separador **Provisioning.** 
>
>

Se os grupos de aprovisionamento estiverem ativados, certifique-se de rever os mapeamentos do atributo para garantir que está a ser utilizado um campo adequado para o "ID correspondente". Este ID correspondente pode ser o nome de exibição ou pseudónimo de e-mail. O grupo e os seus membros não são provisionados se a propriedade correspondente estiver vazia ou não povoada para um grupo em Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)
