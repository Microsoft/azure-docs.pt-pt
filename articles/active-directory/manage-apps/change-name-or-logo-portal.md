---
title: Alterar o nome ou o logotipo de um aplicativo empresarial no Azure AD
description: Como alterar o nome ou o logotipo de um aplicativo empresarial personalizado no Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3dfe0f8788275dd4403b6c9cad99a8eb09e479b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274722"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Alterar o nome ou o logotipo de um aplicativo empresarial no Azure Active Directory

É fácil alterar o nome ou o logotipo de um aplicativo empresarial personalizado no Azure Active Directory (Azure AD). Você deve ter as permissões apropriadas para fazer essas alterações e deve ser o criador do aplicativo personalizado.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Como fazer alterar o nome ou o logotipo de um aplicativo empresarial?

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do diretório. A página **Azure Active Directory centro de administração** é exibida.
2. No painel esquerdo, selecione **Aplicações empresariais**. A lista de seus aplicativos empresariais é exibida.
3. Selecione um aplicativo. A página Visão geral do aplicativo é exibida.
4. No painel Visão geral do aplicativo, sob o título **gerenciar** , selecione **Propriedades**. A página **Propriedades** é exibida.
5. Se você quiser alterar o nome, selecione a caixa **nome** , digite o novo nome e pressione **Enter**.
6. Se você quiser alterar o logotipo, localize o campo **logotipo** e selecione o ícone de pasta ao lado da caixa **selecionar um arquivo** , que está abaixo da imagem do logotipo atual do aplicativo.

   ![Selecionando o comando Propriedades](./media/change-name-or-logo-portal/change-logo.png)

   Caso contrário, se você não estiver alterando o logotipo, vá para a etapa 8.
7. No seletor de arquivos, selecione o arquivo desejado como o novo logotipo. O nome do arquivo aparece na caixa abaixo da imagem do logotipo atual.

   > [!NOTE]
   > O Azure exige que a imagem do logotipo seja um arquivo PNG e aplica limites de largura, altura e tamanho do arquivo.
8. Selecione **Guardar**. Se você escolher um novo logotipo, a imagem do campo de **logotipo** será alterada para refletir o novo arquivo de logotipo.

## <a name="next-steps"></a>Passos seguintes

* [Início rápido: exibir os grupos e membros da sua organização no Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Desabilitar entradas de usuário para um aplicativo empresarial](disable-user-sign-in-portal.md)
