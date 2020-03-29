---
title: Adicionar ou atualizar informações sobre o perfil do utilizador - Azure AD
description: Instruções sobre como adicionar informações ao perfil de um utilizador no Diretório Ativo Azure, incluindo uma imagem e detalhes de trabalho.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba51b0ad7f314058f34092dff4e26411020086a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422880"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Adicionar ou atualizar as informações do perfil de um utilizador com o Azure Active Directory
Adicione informações sobre o perfil do utilizador, incluindo uma imagem de perfil, informações específicas para o trabalho e algumas definições utilizando o Azure Ative Directory (Azure AD). Para mais informações sobre a adição de novos utilizadores, consulte [como adicionar ou excluir utilizadores no Diretório Ativo do Azure](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Adicionar ou alterar informações sobre o perfil
Como pode ver, há mais informação disponível no perfil de um utilizador do que o que é capaz de adicionar durante a criação do utilizador. Toda esta informação adicional é opcional e pode ser adicionada conforme necessário pela sua organização.

## <a name="to-add-or-change-profile-information"></a>Para adicionar ou alterar informações de perfil
1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de utilizador para a organização.

2. Selecione **Diretório Ativo Azure,** selecione **Utilizadores**e, em seguida, selecione um utilizador. Por exemplo, _Alain Charon._

    A página **alain Charon - Perfil** aparece.

    ![Página de perfil do utilizador, incluindo informações editáveis](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Selecione **Editar** para adicionar ou atualizar opcionalmente as informações incluídas em cada uma das secções disponíveis.

    ![Página de perfil do utilizador, mostrando as áreas editáveis](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Imagem de perfil.** Selecione uma imagem de miniatura para a conta do utilizador. Esta imagem aparece no Diretório Ativo do Azure e nas páginas pessoais do utilizador, como a página myapps.microsoft.com.

    - **A identidade.** Adicione ou atualize um valor de identidade adicional para o utilizador, como um sobrenome casado. Pode definir este nome de forma independente a partir dos valores de Primeiro nome e Apelido. Por exemplo, pode usá-lo para incluir iniciais, um nome de empresa ou para alterar a sequência de nomes mostrados. Noutro exemplo, para dois utilizadores cujos nomes são 'Chris Green' pode usar a cadeia Identidade para definir os seus nomes para 'Chris B. Green' 'Chris R. Green (Contoso).'

    - **Informação de trabalho.** Adicione qualquer informação relacionada com o trabalho, como o cargo de utilizador, departamento ou gestor.

    - **Configurações.** Decida se o utilizador pode iniciar sessão no Azure Ative Directory tenant. Também pode especificar a localização global do utilizador.

    - **Informações de contacto.** Adicione quaisquer informações de contacto relevantes para o utilizador, com exceção das informações de contacto de algum utilizador ou telemóvel (apenas um administrador global pode atualizar para os utilizadores nas funções de administrador).

    - **Informação de contacto de autenticação.** Verifique esta informação para se certificar de que existe um número de telefone ativo e endereço de e-mail para o utilizador. Estas informações são utilizadas pelo Azure Ative Directory para garantir que o utilizador é realmente o utilizador durante o início de sessão. As informações de contacto de autenticação só podem ser atualizadas por um administrador global.

4. Selecione **Guardar**.

    Todas as suas alterações são guardadas para o utilizador.

    >[!Note]
    >Deve utilizar o Diretório Ativo do Windows Server para atualizar a identidade, informações de contacto ou informações de trabalho para utilizadores cuja fonte de autoridade é o Diretório Ativo do Servidor do Windows. Depois de completar a atualização, deve esperar que o próximo ciclo de sincronização termine antes de ver as alterações.

## <a name="next-steps"></a>Passos seguintes
Depois de atualizar os perfis dos seus utilizadores, pode realizar os seguintes processos básicos:

- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou pode executar outras tarefas de gestão de utilizadores, tais como atribuir delegados, usar políticas e partilhar contas de utilizadores. Para obter mais informações sobre outras ações disponíveis, consulte a documentação de [gestão de utilizadores do Azure Ative Directory.](../users-groups-roles/index.yml)
