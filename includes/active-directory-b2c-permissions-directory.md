---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 43bcd1f11eb228bd1454b2ad0f2addb851029f2f
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800041"
---
#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Na página Visão geral do **aplicativo registrado** , selecione **configurações**.
1. Em **acesso à API**, selecione **permissões necessárias**.
1. Selecione **Azure Active Directory do Windows**.
1. Em **permissões do aplicativo**, selecione **ler e gravar dados do diretório**.
1. Selecione **Guardar**.
1. Selecione **conceder permissões**e, em seguida, selecione **Sim**. Pode levar alguns minutos para que as permissões se propaguem totalmente.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Em **gerenciar**, selecione **permissões de API**.
1. Em **permissões configuradas**, selecione **Adicionar uma permissão**.
1. Selecione **Azure Active Directory grafo**.
1. Selecione **permissões de aplicativo**.
1. Expanda **diretório** e marque a caixa de seleção **Directory. ReadWrite. All** .
1. Selecione **adicionar permissões**. Conforme indicado, aguarde alguns minutos antes de prosseguir para a próxima etapa.
1. Selecione **conceder consentimento de administrador para (seu nome de locatário)** .
1. Selecione uma conta de administrador de locatários.
1. Selecione **Aceitar**.
1. Selecione **Atualizar**e, em seguida, verifique se "concedido para..." aparece em **status**. Pode levar alguns minutos para que as permissões se propaguem.