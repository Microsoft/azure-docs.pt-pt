---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: ce49e9e26160017d64d745c9c7ad5402bc3ae2ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641568"
---
#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Selecione **aplicativos**e, em seguida, selecione o aplicativo Web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Selecione **acesso à API**e, em seguida, selecione **Adicionar**.
1. Na lista suspensa **selecionar API** , selecione a API à qual o aplicativo Web deve receber acesso. Por exemplo, *webapi1*.
1. Na lista suspensa **selecionar escopos** , selecione os escopos que você definiu anteriormente. Por exemplo, *demo. Read* e *demo. Write*.
1. Selecione **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione o aplicativo Web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Em **gerenciar**, selecione **permissões de API**.
1. Em **permissões configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **minhas APIs** .
1. Selecione a API à qual o aplicativo Web deve receber acesso. Por exemplo, *webapi1*.
1. Em **permissão**, expanda **demonstração**e selecione os escopos que você definiu anteriormente. Por exemplo, *demo. Read* e *demo. Write*.
1. Selecione **adicionar permissões**. Conforme indicado, aguarde alguns minutos antes de prosseguir para a próxima etapa.
1. Selecione **conceder consentimento de administrador para (seu nome de locatário)** .
1. Selecione sua conta de administrador conectada no momento ou entre com uma conta em seu locatário Azure AD B2C que tenha sido atribuído pelo menos à função *administrador de aplicativos de nuvem* .
1. Selecione **Aceitar**.
1. Selecione **Atualizar**e, em seguida, verifique se "concedido para..." aparece em **status** para ambos os escopos. Pode levar alguns minutos para que as permissões se propaguem.