---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a7d0a899b4a2ef86153e82a4e3f04e7ec430a1bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184851"
---
1. Navegue até à [Consola do Google Cloud](https://console.developers.google.com/cloud-resource-manager) e inicie sessão com as credenciais da conta Google. 
2. Selecione **Criar Projeto** na barra de ferramentas. 
   
    ![Criar novo projeto](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. Para **Nome do projeto**, introduza um nome para o seu projeto e clique em **Criar**.
4. Selecione o botão **alertas** na barra de ferramentas e selecione o seu projeto na lista. Verá o dashboard para o seu projeto. Também pode navegar diretamente para o dashboard com o URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Selecionar o projeto nos alertas](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Anote o **Número do projeto** no mosaico **Informações do projeto** do dashboard. 

    ![ID do Projeto](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. No dashboard, no mosaico **APIs**, selecione **Aceder à descrição geral das APIs**. 

    ![Ligação da descrição geral da API](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Na página **API**, selecione **ATIVAR APIS E SERVIÇOS**. 

    ![Botão Ativar APIs e Serviços](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Procure e selecione **Google Cloud Messaging**. 

    ![Procure e selecione Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Para ativar o Google Cloud Messaging para o projeto, selecione **ATIVAR**.

    ![Ativar o Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Selecione **Criar credenciais** na barra de ferramentas. 

    ![Botão Criar credenciais](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Na página **Adicionar credenciais ao projeto**, selecione a ligação **Chave de API**. 

    ![Botão Criar credenciais](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Na página **Chave de API**, selecione **Criar/Guardar**. No exemplo seguinte, a opção **Endereços IP** está selecionada e é introduzido **0.0.0.0/0** para os endereços IP permitidos. Deverá restringir a chave de API adequadamente. 

    ![Chave de API - botão Criar](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Copie a **Chave de API** para a área de transferência e guarde-a algures. 

    ![Copiar a chave de API](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Vai utilizar este valor para permitir ao Azure a autenticação com o GCM e o envio de notificações push em nome da aplicação. Para navegue de volta para o dashboard do projeto, utilize o URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

