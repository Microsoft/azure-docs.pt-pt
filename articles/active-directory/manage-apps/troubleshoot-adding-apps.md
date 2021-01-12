---
title: Resolução de problemas problemas comuns adicionando ou removendo uma aplicação ao Azure Ative Directory
description: Resolver problemas comuns que as pessoas enfrentam ao adicionar ou remover uma aplicação ao Azure Ative Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.openlocfilehash: f873608d07785e6b99bd8e0fcdadf64a8899d490
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108420"
---
# <a name="troubleshoot-common-problem-adding-or-removing-an-application-to-azure-active-directory"></a>Resolução de problemas problemas comuns adicionando ou removendo uma aplicação ao Azure Ative Directory
Este artigo ajuda-o a compreender os problemas comuns que as pessoas enfrentam ao adicionar ou remover uma aplicação ao Azure Ative Directory.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Cliquei no botão "adicionar" e a minha aplicação demorou muito tempo a aparecer
Em algumas circunstâncias, pode levar 1-2 minutos (e às vezes mais) para que uma aplicação apareça depois de a adicionar ao seu diretório. Embora este não seja o desempenho normal esperado, pode ver que a adição da aplicação está em andamento clicando no ícone **notificações** (o sino) no canto superior direito do [portal Azure](https://portal.azure.com/) e procurando uma aplicação de adicionar com rótulo **em andamento** ou **concluída.** 

Se a sua aplicação nunca for adicionada, ou se encontrar um erro ao clicar no botão **Adicionar,** verá uma **Notificação** num estado **de Erro.** Se quiser mais detalhes sobre o erro para saber mais ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro seguindo os passos no Como ver os detalhes de uma secção [de notificação do portal.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Cliquei no botão "adicionar" e a minha aplicação não apareceu
Por vezes, devido a problemas transitórios, problemas de networking ou um bug, adicionar uma aplicação falha. Pode dizer que isto acontece quando clica no ícone **'Notificações'** (a campainha) no canto superior direito do portal Azure e vê um ícone vermelho (!) ao lado da notificação **da aplicação de adicionar.** Isto indica que houve um erro ao criar a aplicação.

Se encontrar um erro ao clicar no botão **Adicionar,** verá uma **Notificação** num estado **de Erro.** Se quiser mais detalhes sobre o erro para saber mais ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro seguindo os passos no Como ver os detalhes de uma secção [de notificação do portal.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Não sei como configurar a minha candidatura depois de a ter adicionado.
Se precisa de ajuda para aprender sobre aplicações, a [Lista de Tutoriais sobre Como Integrar aplicações saas com](../saas-apps/tutorial-list.md) o artigo do Azure Ative Directory é um bom ponto de partida.

Além disso, a [Azure AD Applications Document Library](./what-is-application-management.md) ajuda-o a aprender mais sobre o sign-on único com a AD Azure e como funciona.

## <a name="i-want-to-delete-an-application-but-the-delete-button-is-disabled"></a>Quero apagar uma aplicação, mas o botão de eliminar está desativado

O botão de eliminação será desativado nos seguintes cenários:

- Para aplicações sob aplicação da Enterprise, se não tiver uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.

- Para a aplicação da Microsoft, não poderá eliminá-las da UI independentemente do seu papel.

- Para serviçosPrincipals que correspondem a uma identidade gerida. Os principais do serviço de identidades geridas não podem ser eliminados na lâmina de aplicações da Enterprise. Tens de ir ao recurso Azure para o gerires. Saiba mais sobre [Identidade Gerida](../managed-identities-azure-resources/overview.md)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação do portal
Pode ver os detalhes de qualquer notificação do portal seguindo os passos abaixo:
1.  Selecione o ícone **notificações** (o sino) no canto superior direito do portal Azure
2.  Selecione qualquer notificação num estado **de Erro** (aqueles com um vermelho (!) ao seu lado).
    >[!NOTE]
    >Não é possível clicar em notificações num estado **de sucesso** ou **em progresso.**
4.  Utilize as informações nos **Detalhes da Notificação** para obter mais detalhes sobre o problema.
5.  Se ainda precisar de ajuda, também pode partilhar esta informação com um engenheiro de suporte ou com o grupo de produtos para obter ajuda com o seu problema.
6.  Selecione o ícone de **cópia** à direita da caixa de texto **de erro copy** para copiar todos os dados de notificação para partilhar com um engenheiro de grupo de suporte ou produto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda enviando detalhes de notificação a um engenheiro de suporte
É importante que partilhe **todos os detalhes listados abaixo** com um engenheiro de suporte se precisar de ajuda, para que possam ajudá-lo rapidamente. **Faça uma imagem** ou selecione o **ícone de erro copy**, encontrado à direita da caixa de texto de erro de **cópia.**

## <a name="notification-details-explained"></a>Detalhes de notificação explicados
Consulte as seguintes descrições para mais detalhes sobre as notificações.

### <a name="essential-notification-items"></a>Itens essenciais de notificação
- **Título** – o título descritivo da notificação
  * Exemplo – **Configurações de procuração de aplicativos**
- **Descrição** – a descrição do que ocorreu como resultado da operação
  -   Exemplo – **A url interna inserida já está a ser utilizada por outra aplicação**
- **ID de notificação** – o ID único da notificação
  -   Exemplo - **clienteNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**
- **ID de pedido** do cliente – o ID de pedido específico feito pelo seu navegador
  -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**
- **Time Stamp UTC** – o carimbo de tempo durante o qual ocorreu a notificação, na UTC
  -   Exemplo – **2017-03-23T19:50:43.7583681Z**
- **ID de transação interna** – o ID interno que podemos usar para procurar o erro nos nossos sistemas
  -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**
- **UPN** – o utilizador que realizou a operação
  -   Exemplo - **tperkins \@ f128.info**
- **ID do inquilino** – o ID único do inquilino que o utilizador que realizou a operação era membro da
  -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**
- **ID do objeto do utilizador** – o ID único do utilizador que realizou a operação
  -   Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhados
-   **Nome do visor** – **(pode ser vazio)** um nome de exibição mais detalhado para o erro
    -   Exemplo – **Configurações de procuração de aplicativos**
-   **Estado** – o estado específico da notificação
    -   Exemplo – **Falhado**
-   **ID do objeto** – **(pode ser vazio)** o ID do objeto contra o qual a operação foi realizada
    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**
-   **Detalhes** – a descrição detalhada do que ocorreu como resultado da operação
    -   Exemplo – **O url interno é `https://bing.com/` inválido uma vez que já está em uso**
-   **Erro de cópia** – Selecione o ícone de **cópia** à direita da caixa de texto de erro **de cópia** para copiar todos os dados de notificação para partilhar com um grupo de suporte ou produto 
-   engenheiro
    -   Exemplo ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```
