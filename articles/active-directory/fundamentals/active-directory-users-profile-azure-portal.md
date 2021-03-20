---
title: Adicionar ou atualizar informações sobre o perfil do utilizador - Azure AD
description: Instruções sobre como adicionar informações ao perfil de um utilizador no Azure Ative Directory, incluindo uma imagem e detalhes do trabalho.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27896bac6f5436fb6717e4bc1ab0a1f9381904ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370921"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Adicionar ou atualizar as informações do perfil de um utilizador com o Azure Active Directory
Adicione informações sobre o perfil do utilizador, incluindo uma imagem de perfil, informações específicas do trabalho e algumas definições utilizando o Azure Ative Directory (Azure AD). Para obter mais informações sobre a adição de novos utilizadores, consulte [Como adicionar ou eliminar utilizadores no Azure Ative Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Adicionar ou alterar informações de perfil
Como pode ver, há mais informação disponível no perfil de um utilizador do que o que é capaz de adicionar durante a criação do utilizador. Toda esta informação adicional é opcional e pode ser adicionada conforme necessário pela sua organização.

## <a name="to-add-or-change-profile-information"></a>Para adicionar ou alterar informações de perfil
1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador do Utilizador para a organização.

2. Selecione **Azure Ative Directory**, selecione **Utilizadores** e, em seguida, selecione um utilizador. Por exemplo, _Alain Caronte._

    Aparece a página **Alain Charon - Perfil.**

    ![Página de perfil do utilizador, incluindo informações editáveis](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. **Selecione Editar** opcionalmente para adicionar ou atualizar as informações incluídas em cada uma das secções disponíveis.

    ![Página de perfil do utilizador, mostrando as áreas editáveis](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Imagem de perfil.** Selecione uma imagem de miniatura para a conta do utilizador. Esta imagem aparece no Azure Ative Directory e nas páginas pessoais do utilizador, como a página myapps.microsoft.com.

    - **A identidade.** Adicione ou atualize um valor de identidade adicional para o utilizador, como um apelido casado. Pode definir este nome independentemente dos valores do Primeiro Nome e do Apelido. Por exemplo, pode usá-lo para incluir iniciais, um nome da empresa, ou para alterar a sequência de nomes mostrados. Noutro exemplo, para dois utilizadores cujos nomes são 'Chris Green' podes usar a cadeia identidade para definir os seus nomes para 'Chris B. Green' 'Chris R. Green (Contoso)."

    - **Informação de trabalho.** Adicione qualquer informação relacionada com o trabalho, como o título de emprego, departamento ou gestor do utilizador.

    - **Configurações.** Decida se o utilizador pode fazer o seu súmis no Azure Ative Directory. Também pode especificar a localização global do utilizador.

    - **Informação de contato.** Adicione qualquer informação de contacto relevante para o utilizador, com exceção de algumas informações de contacto telefónico ou móvel de alguns utilizadores (apenas um administrador global pode atualizar para os utilizadores em funções de administrador).

    - **Informação de contacto de autenticação.** Verifique estas informações para se certificar de que existe um número de telefone ativo e um endereço de e-mail para o utilizador. Estas informações são utilizadas pelo Azure Ative Directory para se certificar de que o utilizador é realmente o utilizador durante a sinstrução. As informações de contacto de autenticação só podem ser atualizadas por um administrador global.

4. Selecione **Guardar**.

    Todas as suas alterações são guardadas para o utilizador.

    >[!Note]
    >Tem de utilizar o Windows Server Ative Directory para atualizar a identidade, informações de contacto ou informações de trabalho para utilizadores cuja fonte de autoridade é o Windows Server Ative Directory. Depois de completar a atualização, tem de esperar que o próximo ciclo de sincronização esteja concluído antes de ver as alterações.

## <a name="next-steps"></a>Passos seguintes
Depois de atualizar os perfis dos seus utilizadores, pode realizar os seguintes processos básicos:

- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou pode executar outras tarefas de gestão de utilizadores, como atribuir delegados, usar políticas e partilhar contas de utilizador. Para obter mais informações sobre outras ações disponíveis, consulte [a documentação de gestão de utilizadores do Azure Ative Directory](../enterprise-users/index.yml).
