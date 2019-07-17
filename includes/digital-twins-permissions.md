---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 06/28/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 324f41055cf333081f308a3ff533ff7df6b33038
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "67479233"
---
>[!NOTE]
>Esta secção fornece instruções para o [novo registo de aplicação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Se ainda tiver o registo de aplicações nativas legadas, pode usá-lo, desde que é suportado. Além disso, se por algum motivo, a nova forma de registation de aplicação não está trabalhando em sua configuração, pode tentar criar uma aplicação nativa herdada do AAD. Leia [registar a aplicação de duplos Digital do Azure no legado do Azure Active Directory](../articles/digital-twins/how-to-use-legacy-aad.md) para obter mais instruções. 

1. Na [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel à esquerda e, em seguida, abra a **registos das aplicações** painel. Selecione o **novo registo** botão.

    ![Aplicação registada](./media/digital-twins-permissions/aad-app-register.png)

1. Dê um nome amigável para este registo de aplicações no **nome** caixa. Sob o **URI de redirecionamento (opcional)** secção, escolha **cliente público (móveis e de ambiente de trabalho)** na lista suspensa à esquerda e introduza `https://microsoft.com` na caixa de texto à direita. Selecione **Registar**.

    ![Criar Painel](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Para se certificar de que [o aplicativo está registrado como um *aplicação nativa*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), abra a **autenticação** painel para o seu registo de aplicações e desloque-se para baixo nesse painel. Na **predefinido a tipo de cliente** secção, escolha **Sim** para **tratar o aplicativo como um cliente público**. 

    ![Predefinição nativo](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Abra o **descrição geral** painel do seu aplicativo registrado e copie os valores de entidades a seguir num arquivo temporário. Irá utilizar estes valores para configurar o aplicativo de exemplo nas seções a seguir.

    - **ID da aplicação (cliente)**
    - **ID de diretório (inquilino)**

    ![ID da aplicação do Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Abra o **permissões API** painel para o seu registo de aplicação. Selecione **adicionar uma permissão** botão. Na **permissões de pedido de API** painel, selecione a **APIs utiliza a minha organização** separador e, em seguida, procure **espaços inteligente do Azure**. Selecione o **Azure Smart espaços serviço** API.

    ![API de Pesquisa](./media/digital-twins-permissions/aad-app-search-api.png)

1. A API de selecionado aparece como **duplos Digital do Azure** no mesmo **permissões de pedido de API** painel. Selecione o **leitura (1)** menu pendente e, em seguida, selecione **Read.Write** caixa de verificação. Selecione o **adicionar permissões** botão.

    ![Adicionar permissões de API](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. Dependendo das definições da sua organização, poderá ter de efetuar passos adicionais para conceder acesso de administrador para esta API. Contacte o seu administrador para obter mais informações. Depois do acesso de administrador for aprovado, o **administrador consentimento necessários** coluna na **permissões API** painel mostrará semelhante ao seguinte para as suas APIs:

    ![Adicionar permissões de API](./media/digital-twins-permissions/aad-app-admin-consent.png)

