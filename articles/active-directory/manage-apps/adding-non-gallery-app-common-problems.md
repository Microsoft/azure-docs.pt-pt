---
title: Problema de adicionar uma aplicação não-galeria / Microsoft Docs
description: Compreender problemas comuns que as pessoas enfrentam ao adicionar aplicações personalizadas não-galeria
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce103ca6c958b0524d753da25c2a79aadfb7c8b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760291"
---
# <a name="problem-adding-a-non-gallery-application"></a>Problema de adicionar uma aplicação não-galeria

Este artigo ajuda-o a compreender os problemas comuns que as pessoas enfrentam ao adicionar **aplicações personalizadas de não galeria e** o que pode fazer para resolvê-las. 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Cliquei no botão "adicionar" e a minha aplicação demorou muito tempo a aparecer

Em algumas circunstâncias, pode levar 1-2 minutos (e às vezes mais) para que uma aplicação apareça depois de a adicionar ao seu diretório. Embora este não seja o desempenho normal esperado, pode ver que a adição da aplicação está em andamento clicando no ícone **notificações** (o sino) no canto superior direito do [portal Azure](https://portal.azure.com/) e procurando uma **aplicação Create**em **andamento** ou **concluída.**

Se a sua aplicação nunca for adicionada, ou se encontrar um erro ao clicar no botão **Adicionar,** verá uma **Notificação** num estado **de Erro.** Se quiser mais detalhes sobre o erro para saber mais ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro seguindo os passos no Como ver os detalhes de uma secção [de notificação do portal.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Cliquei no botão "adicionar" e a minha aplicação não apareceu

Por vezes, devido a problemas transitórios, problemas de networking ou um bug, adicionando uma falha de aplicação. Pode dizer que isto acontece quando clica no ícone **'Notificações'** (a campainha) no canto superior direito do portal Azure e vê um ícone vermelho (!) ao lado da notificação de **aplicação Criar.** Isto indica que houve um erro ao criar a aplicação.

Se encontrar um erro ao clicar no botão **Adicionar,** verá uma **Notificação** num estado **de Erro.** Se quiser mais detalhes sobre o erro para saber mais ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro seguindo os passos no Como ver os detalhes de uma secção [de notificação do portal.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Não sei como configurar a minha candidatura depois de a ter adicionado.

Se precisar de ajuda para aprender sobre aplicações personalizadas, a [Azure AD Applications Document Library](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) ajuda-o a aprender mais sobre o súblio com o AD Azure e como funciona.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação do portal

Pode ver os detalhes de qualquer notificação do portal seguindo os passos abaixo:

1. clique no ícone **notificações** (o sino) no canto superior direito do portal Azure

2. Selecione qualquer notificação num estado **de Erro** (aqueles com um vermelho (!) ao seu lado).

   >[!NOTE]
   >Não é possível clicar em notificações num estado **de sucesso** ou **em progresso.**
   >
   >

4. Utilize as informações nos **Detalhes da Notificação** para obter mais detalhes sobre o problema.

5. Se ainda precisar de ajuda, também pode partilhar esta informação com um engenheiro de suporte ou com o grupo de produtos para obter ajuda com o seu problema.

6. Clique no ícone de **cópia** à direita da caixa de texto de **erro copy** para copiar todos os dados de notificação para partilhar com um engenheiro de grupo de suporte ou produto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda enviando detalhes de notificação a um engenheiro de suporte

É muito importante que partilhe **todos os detalhes listados abaixo** com um engenheiro de suporte se precisar de ajuda, para que possam ajudá-lo rapidamente. Pode fazê-lo facilmente **tirando uma imagem ou** clicando no **ícone de erro Copy**, encontrado à direita da caixa de texto de erro **de Cópia.**

## <a name="notification-details-explained"></a>Detalhes de notificação explicados

Consulte as seguintes descrições para mais detalhes sobre as notificações.

### <a name="essential-notification-items"></a>Itens essenciais de notificação

- **Título** – o título descritivo da notificação
  *  Exemplo – **Configurações de procuração de aplicativos**

- **Descrição** – a descrição do que ocorreu como resultado da operação

  *  Exemplo – **A url interna inserida já está a ser utilizada por outra aplicação**

- **ID de notificação** – o ID único da notificação

  *  Exemplo - **clienteNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **ID de pedido** do cliente – o ID de pedido específico feito pelo seu navegador

  *  Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Time Stamp UTC** – o carimbo de tempo durante o qual ocorreu a notificação, na UTC

  *  Exemplo – **2017-03-23T19:50:43.7583681Z**

- **ID de transação interna** – o ID interno que podemos usar para procurar o erro nos nossos sistemas

  *  Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – o utilizador que realizou a operação

  *  Exemplo - **tperkins \@ f128.info**

- **ID do inquilino** – o ID único do inquilino que o utilizador que realizou a operação era membro da

  *  Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **ID do objeto do utilizador** – o ID único do utilizador que realizou a operação

  *  Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhados

- **Nome do visor** – **(pode ser vazio)** um nome de exibição mais detalhado para o erro

  *  Exemplo – **Configurações de procuração de aplicativos**

- **Estado** – o estado específico da notificação

  *  Exemplo – **Falhado**

- **ID do objeto** – **(pode ser vazio)** o ID do objeto contra o qual a operação foi realizada

  *  Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

- **Detalhes** – a descrição detalhada do que ocorreu como resultado da operação

  *  Exemplo – **O url interno é `https://bing.com/` inválido uma vez que já está em uso**

- **Erro de cópia** – Clique no ícone de **cópia** à direita da caixa de texto de erro **de cópia** para copiar todos os dados de notificação para partilhar com um grupo de suporte ou produto 
- engenheiro

  *  Exemplo```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```




