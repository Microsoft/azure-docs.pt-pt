---
title: Criar e gerir aplicações Azure IoT Central a partir do portal CSP | Microsoft Docs
description: Como CSP, como criar uma aplicação Azure IoT Central em nome do seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: f3293ada549351cc7273847cde48c0531f06f028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675812"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Criar e gerir uma aplicação Azure IoT Central a partir do portal CSP

O programa Microsoft Cloud Solution Provider (CSP) é um programa de revendedor da Microsoft. A sua intenção é fornecer aos nossos parceiros de canal um programa único para revender todos os Serviços Online Comerciais da Microsoft. Saiba mais sobre o [programa Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Como CSP, pode criar e gerir aplicações da Microsoft Azure IoT Central em nome dos seus clientes através do [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando as aplicações Azure IoT Central são criadas em nome dos clientes por CSPs, tal como acontece com outros serviços geridos pela CSP Azure, os CSPs gerem a faturação para os clientes. Uma taxa para a Azure IoT Central aparecerá na sua conta total no Microsoft Partner Center.

Para começar, inicie a sua conta no Portal do Parceiro microsoft e selecione um cliente para o qual pretende criar uma aplicação Azure IoT Central. Navegue para Gestão de Serviços para o cliente a partir da navegação esquerda.

![Microsoft Partner Center, vista para o cliente](media/howto-create-and-manage-applications-csp/image1.png)

A Azure IoT Central está listado como um serviço disponível para administrar. Selecione o link Azure IoT Central na página para criar novas aplicações ou gerir as aplicações existentes para este cliente.

![Azure IoT Central disponível para gerir](media/howto-create-and-manage-applications-csp/image2.png)

Aterra na página do Diretor de Aplicações Central Azure IoT. A Azure IoT Central mantém o contexto de que veio do Microsoft Partner Center e que veio gerir esse cliente em particular. Vê isto reconhecido no cabeçalho da página do Gestor de Aplicações. A partir daqui, pode navegar para uma aplicação existente que criou anteriormente para este cliente gerir ou criar uma nova aplicação para o cliente.

![Criar Gestor para CSPs](media/howto-create-and-manage-applications-csp/image3.png)

Para criar uma aplicação Azure IoT Central, selecione **Construir** no menu esquerdo. Escolha um dos modelos da indústria ou escolha **a app Custom** para criar uma aplicação de raiz. Isto irá carregar a página De Criação de Aplicações. Tem de completar todos os campos desta página e, em seguida, escolher **Criar**. Encontre mais informações sobre cada um dos campos abaixo.

![Screenshot que mostra a página "Construa a sua aplicação IoT" com o botão "Construir" selecionado.](media/howto-create-and-manage-applications-csp/image4.png)

![Criar página de aplicação para CSPs](media/howto-create-and-manage-applications-csp/image4-1.png)

![Criar página de aplicação para informações de faturação de CSPs](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Plano de preços

Só é possível criar aplicações que utilizem um plano de preços padrão como CSP. Para mostrar a Azure IoT Central ao seu cliente, pode criar uma aplicação que utilize o plano de preços gratuitos separadamente. Saiba mais sobre os planos de preços gratuitos e padrão na página de preços da [Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Só é possível criar aplicações que utilizem um plano de preços padrão como CSP. Para mostrar a Azure IoT Central ao seu cliente, pode criar uma aplicação que utilize o plano de preços gratuitos separadamente. Saiba mais sobre os planos de preços gratuitos e padrão na página de preços da [Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nome da aplicação

O nome da sua aplicação é apresentado na página **do Gestor de Aplicações** e em cada aplicação Azure IoT Central. Pode escolher qualquer nome para a sua aplicação Azure IoT Central. Escolha um nome que faça sentido para si e para os outros da sua organização.

## <a name="application-url"></a>URL da Aplicação

O URL de aplicação é o link para a sua aplicação. Pode guardar um marcador no seu navegador ou partilhá-lo com outros.

Quando introduz o nome da sua aplicação, o URL da sua aplicação é autogerado. Se preferir, pode escolher um URL diferente para a sua aplicação. Cada URL Central Azure IoT deve ser único dentro da Azure IoT Central. Veja uma mensagem de erro se o URL que escolher já tiver sido tirado.

## <a name="directory"></a>Diretório

Uma vez que a Azure IoT Central tem o contexto de que veio gerir o cliente que selecionou no Portal do Parceiro Microsoft, vê apenas o inquilino do Azure Ative Directory para esse cliente na área do Diretório. 

Um inquilino do Azure Ative Directory contém identidades de utilizador, credenciais e outras informações organizacionais. As subscrições multiple Azure podem ser associadas a um único inquilino do Azure Ative Directory.

Para saber mais, consulte [o Diretório Ativo Azure.](../../active-directory/index.yml)

## <a name="azure-subscription"></a>Subscrição do Azure

Uma subscrição do Azure permite-lhe criar instâncias de serviços do Azure. O Azure IoT Central encontra automaticamente todas as Subscrições Azure do cliente a que tem acesso e exibe-as num dropdown na página **'Aplicação Criar'.** Escolha uma subscrição Azure para criar uma nova Aplicação Central Azure IoT.

Se não tiver uma subscrição do Azure, pode criar uma no Microsoft Partner Center. Depois de criar a subscrição, regresse à página **Create Application** (Criar Aplicação). A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).

Para saber mais, consulte [as assinaturas Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Localização

**A localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde gostaria de criar a aplicação. Normalmente, deve escolher o local mais próximo fisicamente dos seus dispositivos para obter um desempenho ideal. Atualmente, você pode criar uma aplicação IoT Central nas geografias da **Austrália**, **Ásia Pacífico,** **Europa,** **Estados Unidos,** **Reino Unido** e **Japão.** Uma vez que você escolhe um local, você não pode mais tarde mover a sua aplicação para um local diferente.

## <a name="application-template"></a>Modelo de aplicação

Escolha o modelo de aplicação que deseja utilizar para a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar uma aplicação Azure IoT Central como CSP, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)