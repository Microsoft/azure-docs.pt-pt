---
title: Políticas de segurança de dados e privacidade para conectores google
description: Conheça o impacto que as políticas de segurança e privacidade da Google têm nos conectores da Google, como o Gmail, em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400762"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps

A partir de 1 de maio de **2020,** as alterações devido à segurança de [dados e políticas](https://www.blog.google/technology/safety-security/project-strobe/) de privacidade da Google podem afetar os fluxos de trabalho das suas aplicações lógicas que utilizam o conector do [Gmail.](/connectors/gmail/) Se as suas aplicações lógicas utilizarem o conector do Gmail com uma conta de consumidor do Gmail (endereço de e-mail que termina com @gmail.com @googlemail.com ou), as suas aplicações lógicas podem usar [apenas gatilhos, ações e conectores específicos aprovados pela Google.](#approved-connectors)

> [!NOTE]
> Se as suas aplicações lógicas utilizarem o conector do Gmail com uma conta de negócios G-Suite (endereço de e-mail com um domínio personalizado), as suas aplicações lógicas não são afetadas e não têm restrições na utilização do conector do Gmail.

## <a name="affected-logic-apps"></a>Aplicativos lógicos afetados

Se tiver aplicações lógicas que usam o conector do Gmail, receberá um e-mail sobre aplicações lógicas potencialmente afetadas. No entanto, a partir de 15 de junho de **2020,** quaisquer fluxos de trabalho não conformes serão desativados. Pode tomar qualquer uma destas ações:

* Atualize as aplicações lógicas [afetadas seguindo os passos neste tópico.](#update-affected-workflows) Precisa de criar uma aplicação para clientes do Google, que fornece um ID de cliente e um segredo de cliente que utiliza para autenticação no seu gatilho ou ação do Gmail.

* Atualize as aplicações lógicas afetadas para que utilizem apenas os [conectores aprovados pela Google](#approved-connectors) antes de voltar a ativar as aplicações lógicas desativadas.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Conectores aprovados pelo Google

Ao abrigo desta política, quando utilizar uma conta de consumo do Gmail, pode utilizar o conector gmail apenas com serviços específicos aprovados pela Google, que estão sujeitos a alterações. As nossas equipas de engenharia continuam a trabalhar com a Google para adicionar mais serviços a esta lista. Por enquanto, aqui estão os gatilhos, ações e conectores aprovados pela Google que pode utilizar no mesmo fluxo de trabalho de aplicações lógicas com o conector gmail quando utilizar uma conta de consumo do Gmail:

* Aplicativos e ações incorporadas: Lote, Controlo, Operações de Dados, Hora da Data, Arquivo Plano, Líquido, Pedido, Agenda, Variáveis e XML

  Os gatilhos e ações incorporadas que não são aprovados pela Google, tais como HTTP, Azure Functions, Azure Logic Apps, entre outros, tornam uma aplicação lógica incompatível com o conector do Gmail porque a aplicação pode enviar ou receber dados de qualquer lugar.

* Serviços Google: Gmail, Google Calendar, Google Contacts, Google Drive, Google Sheets e Google Tasks

* Serviços aprovados da Microsoft: Dynamics 365, Excel Online, Microsoft Teams, Microsoft 365, OneDrive e SharePoint Online

* Conectores para fontes de dados geridas pelo cliente: FTP, RSS, SFTP, SMTP e SQL Server

## <a name="non-compliant-examples"></a>Exemplos não conformes

Aqui estão alguns exemplos que usam o conector gmail com gatilhos e ações incorporados ou conectores geridos que não são aprovados pela Google:

* Esta aplicação lógica utiliza o conector Gmail com o gatilho INCORPORADO HTTP:

  ![Aplicação lógica não conforme - Exemplo 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  A aplicação lógica também utiliza o conector Do Calendário do Google, que é aprovado.

* Esta aplicação lógica utiliza o conector Gmail com o conector Azure Blob Storage:

  ![Aplicação lógica não conforme - Exemplo 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Esta aplicação lógica utiliza o conector gmail com o conector do Twitter:

  ![Aplicação lógica não conforme - Exemplo 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

Para obter informações mais recentes, consulte a [documentação técnica de referência do conector do Gmail.](/connectors/gmail/)

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Passos para aplicações lógicas afetadas

Se tiver de utilizar o conector do Gmail com uma conta de consumo do Gmail e conectores não aprovados pela Google numa aplicação lógica, pode criar a sua própria aplicação Google para uso pessoal ou interno na sua empresa. Para este cenário, aqui estão os passos de alto nível que precisa de tomar:

1. Crie uma aplicação para clientes da Google utilizando a [Consola API do Google.](https://console.developers.google.com)

1. No seu conector Gmail, utilize o ID do cliente e os valores secretos do cliente a partir da sua aplicação de cliente Google.

Para mais informações, consulte a [documentação técnica de referência do conector do Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application)

### <a name="create-google-client-app"></a>Criar aplicativo de cliente google

Para configurar um projeto para a sua aplicação cliente, utilize o assistente de consola google [API](https://console.developers.google.com/start/api?id=gmail&credential=client_key) e siga as instruções. Ou, para etapas detalhadas, reveja as instruções na [documentação técnica de referência do conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

Quando terminar, o seu ecrã parece este exemplo, exceto que terá os seus próprios valores de **ID** do Cliente e **cliente,** que mais tarde utiliza na sua aplicação lógica.

![ID do cliente e segredo do cliente para a sua aplicação de clientes google](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Use as definições de aplicativo do cliente na aplicação lógica

Para utilizar o ID do cliente e o segredo do cliente a partir da sua aplicação de cliente Google no seu gatilho ou ação do Gmail, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Se adicionar um novo gatilho ou ação do Gmail e criar uma ligação inteiramente nova, continue para o próximo passo. Caso contrário, no gatilho ou ação do Gmail, selecione **Alterar a ligação**  >  **Adicione novos**, por exemplo:

   ![Selecione "Alterar ligação" > "Adicionar novo"](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Forneça as suas informações de ligação, por exemplo:

   ![Fornecer informações de conexão](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Tipo de autenticação** | **Traga a sua própria aplicação** | Especifica que utilizará a sua própria aplicação de cliente para autenticação. |
   | **ID de Cliente** | <*iD cliente*> | O ID do cliente da sua aplicação de clientes google |
   | **Segredo do Cliente** | <*cliente-segredo*> | O segredo do cliente a partir da sua aplicação de clientes do Google |
   ||||

1. Quando terminar, **selecione Iniciar s-se-ão.**

   Aparece uma página que mostra a aplicação do cliente que criou. Se estiver a utilizar uma conta de consumo do Gmail, poderá receber uma página que mostre que a sua aplicação de clientes não é verificada pela Google e que lhe pede que permita primeiro o acesso à sua conta Google.

   ![Solicitação para acesso à sua conta google](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Se necessário, **selecione Permitir**.

   Pode agora utilizar o conector gmail sem restrições na sua aplicação lógica.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [conector](/connectors/gmail/) do Gmail

