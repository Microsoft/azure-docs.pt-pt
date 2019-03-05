---
title: Criar e gerir aplicações do Azure IoT Central como um CSP | Documentos da Microsoft
description: Como um CSP, como criar uma aplicação do Azure IoT Central em nome do seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2018
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 22e9954e66c05bfb2e425a01ffa6f9e3b93b761e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313723"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>Como um CSP, criar e gerir um aplicativo de Central de IoT do Azure em nome do seu cliente 

O programa de fornecedor de soluções Cloud (CSP) da Microsoft é um programa de Reseller da Microsoft. Qual a sua intenção é fornecer a nossos parceiros de canal com um programa de um único local para revenda todos os serviços comerciais Microsoft Online. Saiba mais sobre o [programa Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Como um CSP, pode criar e gerir aplicações do Microsoft Azure IoT Central em nome dos clientes através da [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando as aplicações do Azure IoT Central são criadas em nome dos clientes por CSPs, tal como com os outros CSP serviços geridos do Azure, CSPs gerenciar a cobrança aos clientes. Um custo para o Azure IoT Central irá aparecer na sua fatura total no Microsoft Partner Center.

Para começar a utilizar, início de sessão para a sua conta no Portal de parceiros Microsoft e selecionar um cliente para o qual pretende criar uma aplicação do Azure IoT Central. Navegue para a gestão de serviço para o cliente de navegação esquerda.

![Microsoft Partner Center, exibição do cliente](media/howto-create-application-asCSP/image1.png)

O Azure IoT Central é listado como um serviço disponível para administrar. Selecione a ligação do Azure IoT Central na página para criar novas aplicações ou gerir as aplicações existentes para este cliente.

![Centro de IoT do Azure disponíveis para gerir](media/howto-create-application-asCSP/image2.png)

É apresentado na página do Gestor de aplicações do Azure IoT Central. O Azure IoT Central mantém contexto que veio do Microsoft Partner Center e que veio para gerir esse cliente específico. Verá isto confirmados no cabeçalho da página do Gestor de aplicações. A partir daqui, pode optar por navegar para uma aplicação existente que tiver criado anteriormente para este cliente gerir ou crie uma nova aplicação do cliente.

![Criar Manager para CSPs](media/howto-create-application-asCSP/image3.png)

Para criar uma aplicação do Azure IoT Central, selecione o **nova aplicação** mosaico. Isso irá carregar a página de criação de aplicativos. Tem de concluir todos os campos nesta página e, em seguida, escolha **criar**. Encontrar mais informações sobre cada um dos campos abaixo.

![Criar a página do aplicativo para CSPs](media/howto-create-application-asCSP/image4.png)

![Criar a página do aplicativo para CSPs](media/howto-create-application-asCSP/image4-1.png)

## <a name="payment-plan"></a>Plano de pagamento

Só pode criar aplicativos de pay as you go como CSP. Para demonstrar o Azure IoT Central para o seu cliente, pode criar uma aplicação de avaliação em separado. Saiba mais sobre os aplicativos de versão de avaliação e pay as you go no [do Azure IoT Central página de preços](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nome da Aplicação

O nome da sua aplicação é apresentado na **Gerenciador de aplicativos** página e dentro de cada aplicação Azure IoT Central. Pode escolher qualquer nome para a sua aplicação do Azure IoT Central. Escolha um nome que faça sentido para si e para outras pessoas na sua organização.

## <a name="application-url"></a>URL da Aplicação

O URL da aplicação é a ligação à sua aplicação. Pode salvar um indicador para ele no seu browser ou partilhá-lo com outras pessoas.

Ao introduzir o nome para a sua aplicação, o seu URL da aplicação é gerado automaticamente. Se preferir, pode escolher um URL diferente para a sua aplicação. Cada URL de Central de IoT do Azure tem de ser exclusivo no Azure IoT Central. Verá uma mensagem de erro se o URL que escolher já foi atribuído.

## <a name="directory"></a>Diretório

Uma vez que o Azure IoT Central tem um contexto que veio para gerir o cliente que selecionou no Portal de parceiros Microsoft, verá apenas o inquilino do Azure Active Directory para que o cliente no campo de diretório. 

Um inquilino do Azure Active Directory contém as identidades dos utilizadores, credenciais e outras informações organizacionais. Várias subscrições do Azure podem ser associadas um único inquilino do Azure Active Directory.

Para obter mais informações, consulte [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subscrição do Azure

Uma subscrição do Azure permite-lhe criar instâncias de serviços do Azure. O Azure IoT Central automaticamente localiza todas as subscrições do Azure do cliente para o qual tem acesso e exibe-os numa lista suspensa na **Criar aplicação** página. Escolha uma subscrição do Azure para criar uma nova aplicação de IoT do Azure Central.

Se não tiver uma subscrição do Azure, pode criar um no Microsoft Partner Center. Depois de criar a subscrição, regresse à página **Create Application** (Criar Aplicação). A subscrição nova aparece no menu pendente **Azure Subscription** (Subscrição do Azure).

Para obter mais informações, consulte [subscrições do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Região

Selecione a região onde pretende criar a sua aplicação de Central de IoT do Azure. Normalmente, deve escolher a região mais próxima fisicamente aos seus dispositivos para obter um desempenho ideal.

Para obter mais informações, consulte [regiões do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Pode ver os em que regiões do Azure IoT Central está disponível na [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) página.

> [!Note]
> Depois de escolher a região, não pode mover a aplicação para outra posteriormente.

## <a name="application-template"></a>Modelo de aplicação

Pode escolher um dos modelos de aplicativos disponíveis para a sua nova aplicação do Azure IoT Central. Os modelos de aplicação podem conter itens predefinidos, como modelos de dispositivo e dashboards, que o ajudam a começar.

| Modelo de aplicação | Descrição |
| -------------------- | ----------- |
| Aplicação personalizada   | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
| Exemplo da Contoso       | Cria uma aplicação que inclui um modelo de dispositivo para um dispositivo conectado simple. Utilize este modelo para começar a explorar o Azure IoT Central. |
| Exemplo da Devkits       | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Utilize este modelo, se for um desenvolvedor de dispositivo fazer experiências com o código em um desses dispositivos. |

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar uma aplicação do Azure IoT Central como um CSP, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)