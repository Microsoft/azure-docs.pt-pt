---
title: Políticas de segurança de dados e privacidade para conectores do Google
description: Saiba o impacto que as políticas de segurança e privacidade do Google têm nos conectores do Google, como o Gmail, nas Aplicações Lógicas Do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 01121612c5574544ef6985d33f56f3e334f8d37c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120655"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Políticas de segurança de dados e privacidade para conectores da Google em Aplicações Lógicas Azure

A partir de 1 de maio de **2020**, as alterações devido às políticas de [segurança de dados e privacidade](https://www.blog.google/technology/safety-security/project-strobe/) da Google podem afetar os fluxos de trabalho da sua aplicação lógica que utilizam o [conector Gmail](https://docs.microsoft.com/connectors/gmail/). Se as suas aplicações lógicas utilizarem o conector @gmail.com @googlemail.comGmail com uma conta de consumo do Gmail (endereço de e-mail que termina com ou), as suas aplicações lógicas só podem utilizar [gatilhos, ações e conectores aprovados pelo Google específicos.](#approved-connectors) 

> [!NOTE]
> Se as suas aplicações lógicas utilizarem o conector Gmail com uma conta de negócios G-Suite (endereço de e-mail com um domínio personalizado), as suas aplicações lógicas não são afetadas e não têm restrições na utilização do conector Gmail.

## <a name="affected-logic-apps"></a>Aplicativos lógicos afetados

Se tiver aplicações lógicas que utilizem o conector gmail, receberá um e-mail sobre aplicações lógicas potencialmente afetadas. No entanto, a partir de 15 de junho de **2020,** quaisquer fluxos de trabalho não conformes serão desativados. Pode tomar qualquer uma destas ações:

* Atualize as aplicações lógicas afetadas [seguindo os passos neste tópico.](#update-affected-workflows) Precisa de criar uma aplicação de cliente da Google, que fornece um ID do cliente e um segredo de cliente que utiliza para autenticação no gatilho ou ação do Gmail.

* Atualize as aplicações lógicas afetadas para que utilizem apenas os [conectores aprovados pela Google](#approved-connectors) antes de reativar as aplicações lógicas desativadas.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Conectores aprovados pela Google

De acordo com esta política, quando utiliza uma conta de consumo do Gmail, pode utilizar o conector Gmail apenas com serviços específicos aprovados pela Google, que estão sujeitos a alterações. As nossas equipas de engenharia continuam a trabalhar com a Google para adicionar mais serviços a esta lista. Por enquanto, aqui estão os gatilhos, ações e conectores aprovados pela Google que pode utilizar no mesmo fluxo de trabalho de aplicações lógicas com o conector Gmail quando utiliza uma conta de consumo do Gmail:

* Aplicativos lógicos incorporados gatilhos e ações: Lote, Controlo, Operações de Dados, Hora de Data, Ficheiro Plano, Liquid, Pedido, Agenda, Variáveis e XML

* Serviços google: Gmail, Google Calendar, Google Contacts, Google Drive, Google Sheets e Google Tasks

* Serviços aprovados da Microsoft: Dynamics 365, Excel Online, Microsoft Teams, Office 365, OneDrive e SharePoint Online

* Conectores para fontes de dados geridas pelo cliente: FTP, HTTP, RSS, SFTP, SMTP e SQL Server

Para obter as informações mais recentes, consulte a [documentação técnica de referência do conector Gmail.](https://docs.microsoft.com/connectors/gmail/)

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Passos para aplicações lógicas afetadas

Se tiver de utilizar o conector Gmail com uma conta de consumo do Gmail e conectores não aprovados pela Google numa aplicação lógica, pode criar a sua própria aplicação google para uso pessoal ou interno na sua empresa. Para este cenário, aqui estão os passos de alto nível que você precisa tomar:

1. Crie uma aplicação de cliente da Google utilizando a [consola Google API](https://console.developers.google.com).

1. No seu conector Gmail, utilize o ID do cliente e os valores secretos do cliente da sua aplicação de cliente google.

Para mais informações, consulte a [documentação técnica de referência do conector Gmail.](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)

### <a name="create-google-client-app"></a>Criar aplicativo de cliente do Google

Para configurar um projeto para a sua aplicação de cliente, use o assistente de [consola google API](https://console.developers.google.com/start/api?id=gmail&credential=client_key) e siga as instruções. Ou, para etapas detalhadas, reveja as instruções na [documentação técnica de referência do conector Gmail.](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)

Quando terminar, o seu ecrã parece este exemplo, exceto que terá o seu próprio **CLIENTE ID** e valores **secretos do Cliente,** que mais tarde utiliza na sua aplicação lógica.

![ID do cliente e segredo do cliente para a sua aplicação de cliente google](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Utilize as definições de aplicativos do cliente na aplicação lógica

Para utilizar o ID do cliente e o segredo do cliente da sua aplicação de cliente google no seu gatilho ou ação do Gmail, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Se estiver a adicionar um novo gatilho ou ação do Gmail e a criar uma ligação inteiramente nova, continue até ao próximo passo. Caso contrário, no gatilho ou ação do Gmail, selecione **Ligação** > alterar**Adicionar novo,** por exemplo:

   ![Selecione "Alterar a ligação" > "Adicionar novo"](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Forneça as suas informações de ligação, por exemplo:

   ![Fornecer informações de ligação](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Tipo de autenticação** | **Traga a sua própria aplicação** | Especifica que utilizará a sua própria aplicação de cliente para autenticação. |
   | **ID do cliente** | <*cliente-ID*> | O ID do cliente da sua aplicação de cliente do Google |
   | **Segredo de Cliente** | <*segredo de cliente*> | O segredo do cliente da sua aplicação de cliente do Google |
   ||||

1. Quando terminar, selecione **Iniciar sessão**.

   Aparece uma página que mostra a aplicação do cliente que criou. Se estiver a usar uma conta de consumo do Gmail, poderá obter uma página que mostre que a aplicação do seu cliente não é verificada pela Google e pede-lhe que permita primeiro o acesso à sua conta google.

   ![Solicitação para acesso à sua conta Google](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Se necessário, **selecione Permitir**.

   Agora pode utilizar o conector Gmail sem restrições na sua aplicação lógica.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [conector Gmail](https://docs.microsoft.com/connectors/gmail/)