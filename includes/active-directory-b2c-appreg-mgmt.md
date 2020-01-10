---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 0ab9302909f86ff4559ece217f8d702710733691
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75368011"
---
Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
1. Em **gerenciar**, selecione **registros de aplicativo (Herdado)** .
1. Selecione **Novo registo de aplicação**.
1. Introduza um nome para a aplicação. Por exemplo, *managementapp1*.
1. Para **tipo de aplicativo**, selecione **aplicativo Web/API**.
1. Insira qualquer URL válida na **URL de logon**. Por exemplo, `https://localhost`. O ponto de extremidade não precisa estar acessível, mas deve ser uma URL válida.
1. Selecione **Criar**.
1. Registre a **ID do aplicativo** que aparece na página Visão geral do **aplicativo registrado** . Você usará esse valor em uma etapa posterior.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *managementapp1*.
1. Selecione **contas somente neste diretório organizacional**.
1. Em **permissões**, desmarque a caixa de seleção *conceder consentimento de administrador às permissões OpenID e offline_access* .
1. Selecione **Registar**.
1. Registre a **ID do aplicativo (cliente)** que aparece na página Visão geral do aplicativo. Você usará esse valor em uma etapa posterior.