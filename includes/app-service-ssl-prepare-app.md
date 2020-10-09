---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4c5c0197ab6cecbba4bac4c0bff5ef76de24b6de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78301012"
---
## <a name="prepare-your-web-app"></a>Preparar a sua aplicação Web

Para criar encadernações de segurança personalizadas ou ativar certificados de cliente para a sua aplicação de Serviço de [Aplicações,](https://azure.microsoft.com/pricing/details/app-service/) o seu plano de Serviço de Aplicações deve estar no nível **Básico,** **Standard,** **Premium**ou **Isolado.** Neste passo, vai confirmar que a aplicação Web está no escalão de preço suportado.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o [portal do Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navegue até à sua aplicação Web

Procure e selecione **Serviços de Aplicações.**

![Selecione Serviços de Aplicações](./media/app-service-ssl-prepare-app/app-services.png)

Na página **serviços de aplicações,** selecione o nome da sua aplicação web.

![Navegação do portal para a aplicação do Azure](./media/app-service-ssl-prepare-app/select-app.png)

Aterrou na página de gestão da sua aplicação web.  

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação Web, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)**.

![Menu de aumento vertical](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Confirme que a aplicação Web não está no escalão **F1** ou **D1**. O escalão atual da aplicação é realçado com uma caixa azul-escura.

![Verificar o escalão de preço](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

O SSL personalizado não é suportado nos escalões **F1** ou **D1**. Se precisar de aumentar verticalmente, siga os passos na secção seguinte. Caso contrário, feche a página **'Escala',** e ignore a escala da [secção do plano do Serviço de Aplicações.](#scale-up-your-app-service-plan)

### <a name="scale-up-your-app-service-plan"></a>Aumentar verticalmente o seu plano do Serviço de Aplicações

Selecione qualquer uma das camadas não gratuitas (**B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Escolher um escalão de preço](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

![Notificação para “aumentar verticalmente”](./media/app-service-ssl-prepare-app/scale-notification.png)

