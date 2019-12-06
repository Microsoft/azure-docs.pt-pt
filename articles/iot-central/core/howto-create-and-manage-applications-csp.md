---
title: Criar e gerenciar aplicativos de IoT Central do Azure no portal do CSP | Microsoft Docs
description: Como um CSP, como criar um aplicativo de IoT Central do Azure em nome do seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: d74bd67dedb70308061e54c05d23a4bb63adc8f8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851337"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Criar e gerenciar um aplicativo de IoT Central do Azure no portal do CSP

O programa CSP (provedor de soluções Microsoft Cloud) é um programa de revendedor da Microsoft. Sua intenção é fornecer aos nossos parceiros de canal um programa One-stop para revender todos os serviços online comerciais da Microsoft. Saiba mais sobre o [programa provedor de soluções na nuvem](https://partner.microsoft.com/cloud-solution-provider).

Como CSP, você pode criar e gerenciar Microsoft Azure IoT Central aplicativos em nome de seus clientes por meio do [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando os aplicativos IoT Central do Azure são criados em nome dos clientes por CSPs, assim como com outros serviços gerenciados do Azure CSP, os CSPs gerenciam a cobrança para os clientes. Um encargo para o Azure IoT Central será exibido em sua fatura total no Microsoft Partner Center.

Para começar, entre em sua conta no portal de parceiros da Microsoft e selecione um cliente para o qual você deseja criar um aplicativo de IoT Central do Azure. Navegue até gerenciamento de serviços para o cliente na barra de navegação à esquerda.

![Microsoft Partner Center, exibição de clientes](media/howto-create-application-csp/image1.png)

O IoT Central do Azure está listado como um serviço disponível para administrar. Selecione o link IoT Central do Azure na página para criar novos aplicativos ou gerenciar aplicativos existentes para esse cliente.

![IoT Central do Azure disponível para gerenciar](media/howto-create-application-csp/image2.png)

Você é levado para a página do Gerenciador de aplicativos IoT Central do Azure. O Azure IoT Central mantém o contexto que você veio do Microsoft Partner Center e que você veio a gerenciar esse cliente específico. Você verá isso confirmado no cabeçalho da página do Gerenciador de aplicativos. A partir daqui, você pode navegar para um aplicativo existente que você criou anteriormente para que este cliente gerencie ou crie um novo aplicativo para o cliente.

![Criar Gerenciador para CSPs](media/howto-create-application-csp/image3.png)

Para criar um aplicativo de IoT Central do Azure, selecione **Compilar** no menu à esquerda. Escolha um dos modelos do setor ou escolha **aplicativo personalizado** para criar um aplicativo do zero. Isso carregará a página de criação do aplicativo. Você deve concluir todos os campos nesta página e, em seguida, escolher **criar**. Você encontrará mais informações sobre cada um dos campos abaixo.

![Criar página de aplicativo para CSPs](media/howto-create-application-csp/image4.png)

![Criar página de aplicativo para CSPs](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>Plano de pagamento

Você só pode criar aplicativos pagos conforme o uso como um CSP. Para demonstrar o Azure IoT Central ao seu cliente, você pode criar um aplicativo de avaliação separadamente. Saiba mais sobre os aplicativos de avaliação e pagamento conforme o uso na página de [preços do Azure IOT central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nome da Aplicação

O nome do seu aplicativo é exibido na página **Gerenciador de aplicativos** e em cada aplicativo IOT central do Azure. Você pode escolher qualquer nome para seu aplicativo de IoT Central do Azure. Escolha um nome que faça sentido para você e para outras pessoas em sua organização.

## <a name="application-url"></a>URL da Aplicação

A URL do aplicativo é o link para seu aplicativo. Você pode salvar um indicador no seu navegador ou compartilhá-lo com outras pessoas.

Quando você insere o nome para seu aplicativo, a URL do aplicativo é gerada automaticamente. Se preferir, você pode escolher uma URL diferente para seu aplicativo. Cada URL de IoT Central do Azure deve ser exclusiva dentro do IoT Central do Azure. Você verá uma mensagem de erro se a URL escolhida já tiver sido executada.

## <a name="directory"></a>Diretório

Como o Azure IoT Central tem um contexto que você veio para gerenciar o cliente selecionado no portal de parceiros da Microsoft, você vê apenas o locatário Azure Active Directory para esse cliente no campo diretório. 

Um locatário Azure Active Directory contém identidades de usuário, credenciais e outras informações organizacionais. Várias assinaturas do Azure podem ser associadas a um único locatário de Azure Active Directory.

Para saber mais, consulte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subscrição do Azure

Uma subscrição do Azure permite-lhe criar instâncias de serviços do Azure. O Azure IoT Central localiza automaticamente todas as assinaturas do Azure do cliente aos quais você tem acesso e as exibe em uma lista suspensa na página **criar aplicativo** . Escolha uma assinatura do Azure para criar um novo aplicativo de IoT Central do Azure.

Se você não tiver uma assinatura do Azure, poderá criar uma no Microsoft Partner Center. Depois de criar a subscrição, regresse à página **Create Application** (Criar Aplicação). A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).

Para saber mais, confira [assinaturas do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Região

Escolha a região ou [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo de IOT central do Azure. Normalmente, você deve escolher a região mais próxima fisicamente em seus dispositivos para obter o desempenho ideal.

> [!NOTE]
> Os modelos de aplicativo de visualização atualmente só estão disponíveis nos locais da **Europa** e **Estados Unidos** .

Para saber mais, confira [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) e [geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Você pode ver as regiões nas quais o Azure IoT Central está disponível na página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) .

> [!Note]
> Depois de escolher a região, não pode mover a aplicação para outra posteriormente.

## <a name="application-template"></a>Modelo de aplicação

Você pode escolher um dos modelos de aplicativo disponíveis para seu novo aplicativo de IoT Central do Azure. Os modelos de aplicação podem conter itens predefinidos, como modelos de dispositivo e dashboards, que o ajudam a começar.

| Modelo de aplicação | Descrição |
| -------------------- | ----------- |
| Aplicação personalizada   | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
| Exemplo da Contoso       | Cria um aplicativo que inclui um modelo de dispositivo para um dispositivo conectado simples. Utilize este modelo para começar a explorar o Azure IoT Central. |
| Exemplo da Devkits       | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Use este modelo se você for um desenvolvedor de dispositivos experimentando o código em um desses dispositivos. |

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como criar um aplicativo de IoT Central do Azure como um CSP, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administre seu aplicativo](howto-administer.md)
