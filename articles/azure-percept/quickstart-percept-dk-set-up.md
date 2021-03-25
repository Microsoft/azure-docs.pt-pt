---
title: Configurar o seu Azure Percept DK
description: Ligue o seu kit dev ao Azure e implemente o seu primeiro modelo de IA
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 8cecd1b57395fa51fd95c824e88885d9c3ae3f09
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023304"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configure o seu Azure Percept DK e implemente o seu primeiro modelo de IA

Complete a experiência de configuração Azure Percept DK para configurar o seu kit dev e implementar o seu primeiro modelo de IA. Depois de verificar se a sua conta Azure é compatível com a Azure Percept, irá:

- Ligue o seu kit dev a uma rede Wi-Fi
- Configurar um login SSH para acesso remoto ao seu kit dev
- Criar um novo Hub IoT para usar com Azure Percept
- Ligue o seu kit dev à sua conta IoT Hub e Azure

Se tiver algum problema durante este processo, consulte o [guia de resolução de problemas de configuração](./how-to-troubleshoot-setup.md) para possíveis soluções.

## <a name="prerequisites"></a>Pré-requisitos

- Um Azure Percept DK (dev kit).
- Um computador anfitrião baseado em Windows, Linux ou OS X com capacidade Wi-Fi e um navegador web.
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A conta Azure deve ter a **função de proprietário** ou **contribuinte** dentro da subscrição. Siga os passos abaixo para verificar a sua função de conta Azure. Para obter mais informações sobre as definições de funções da Azure, consulte a [documentação de controlo de acesso baseada em funções da Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

    > [!CAUTION]
    > Se tiver várias contas Azure, o seu navegador poderá cache credenciais de outra conta. Para evitar confusões, recomenda-se que feche todas as janelas do navegador não usurram e inicie o login no [portal Azure](https://portal.azure.com/) antes de iniciar a experiência de configuração. Consulte o [guia de resolução de problemas de configuração](./how-to-troubleshoot-setup.md) para obter informações adicionais sobre como garantir que está assinado com a conta correta.

### <a name="check-your-azure-account-role"></a>Verifique a sua função de conta Azure

Para verificar se a sua conta Azure é um "proprietário" ou "contribuinte" dentro da subscrição, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com/) e faça login com a mesma conta Azure que pretende utilizar com a Azure Percept.

1. Clique no ícone **Subscrições** (parece uma chave amarela).

1. Selecione a sua subscrição da lista. Se não vir a sua subscrição, certifique-se de que está assinado com a conta Azure correta. Se desejar criar uma nova subscrição, siga [estes passos](../cost-management-billing/manage/create-subscription.md).

1. A partir do menu Assinatura, selecione **Access control (IAM)**.
1. Clique **em Ver o meu acesso.**
1. Verifique a função:
    - Se o seu papel estiver listado como **Reader** ou se receber uma mensagem que diga que não tem permissão para ver funções, terá de seguir o processo necessário na sua organização para elevar o seu papel de conta.
    - Se a sua função estiver listada como **proprietário** ou **colaborador,** a sua conta funcionará com a Azure Percept, podendo prosseguir com a experiência de configuração.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Lançar a experiência de configuração Azure Percept DK

1. Ligue o computador anfitrião diretamente ao ponto de acesso Wi-Fi do dev. Como ligar-se a qualquer outra rede Wi-Fi, abrir as definições de rede e internet no seu computador, clicar na seguinte rede e introduzir a palavra-passe de rede quando solicitado:

    - **Nome da rede**: dependendo da versão do sistema operativo do seu dev kit, o nome do ponto de acesso Wi-Fi é **scz-xxxx** ou **apd-xxxx** (onde "xxxx" é os últimos quatro dígitos do endereço MAC do kit dev)
    - **Senha**: pode ser encontrado no Cartão de Boas-Vindas que veio com o kit dev

    > [!WARNING]
    > Enquanto está ligado ao Azure Percept DK Wi-Fi ponto de acesso, o computador anfitrião perderá temporariamente a sua ligação à Internet. Chamadas de videoconferência ativas, streaming web ou outras experiências baseadas em rede serão interrompidas.

1. Uma vez ligado ao ponto de acesso Wi-Fi do dev, o computador anfitrião lançará automaticamente a experiência de configuração numa nova janela do navegador com o **seu.novo dispositivo/** na barra de endereços. Se o separador não abrir automaticamente, lance a experiência de configuração indo para [http://10.1.1.1](http://10.1.1.1) . Certifique-se de que o seu navegador está assinado com as mesmas credenciais de conta Azure que pretende utilizar com a Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Página de boas-vindas.":::

    > [!CAUTION]
    > O serviço web de experiência de configuração será desligado após 30 minutos de não utilização. Se isso acontecer, reinicie o kit dev para evitar problemas de conectividade com o ponto de acesso Wi-Fi do dev kit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Complete a experiência de configuração Azure Percept DK

1. Clique em **seguida** no ecrã **Welcome.**

1. Na página **de ligação 'Rede',** clique **em Ligar para uma nova rede Wi-Fi**.

    Se já ligou o seu kit dev à sua rede de Wi-Fi, clique em **Skip**.

1. Selecione a sua rede Wi-Fi a partir da lista de redes disponíveis e clique em **ligar**. Introduza a sua palavra-passe de rede quando solicitada.

1. Uma vez que o seu kit dev tenha sido conectado com sucesso à sua rede de escolha, a página mostrará o endereço IPv4 atribuído ao seu kit dev. **Escreva o endereço IPv4 apresentado na página.** Necessitará do endereço IP ao ligar o seu dev kit ao SSH para resolução de problemas e atualizações de dispositivos.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiar endereço IP.":::

    > [!NOTE]
    > O endereço IP pode ser alterado com cada arranque do dispositivo.

1. Leia o Contrato de Licença, **selecione eu li e concordo com o Contrato de Licença** (você deve rolar para a parte inferior do contrato), e clique em **Seguinte**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Aceite a EULA.":::

1. Introduza o nome e a palavra-passe da conta SSH e **escreva as suas informações de login para posterior utilização**.

    > [!NOTE]
    > SSH (Secure Shell) é um protocolo de rede que lhe permite ligar-se remotamente ao kit dev através de um computador anfitrião.

1. Na página seguinte, clique em **Configuração como um novo dispositivo** para criar um novo dispositivo dentro da sua conta Azure.

1. Clique **em Copiar** para copiar o código do dispositivo. Em seguida, clique em **Iniciar sessão para Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Código do dispositivo de cópia.":::

1. Um novo separador de navegador abrirá com uma janela que diz **Enter code**. Cole o código na janela e clique em **Seguinte**. NÃO feche o separador **Welcome** com a experiência de configuração.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Introduza o código do dispositivo.":::

1. Inscreva-se no Azure Percept utilizando as credenciais de conta Azure que utilizará com o seu kit dev. Deixe o separador do navegador aberto quando estiver completo.

    > [!CAUTION]
    > O seu navegador pode cache automático outras credenciais. Verifique duas vezes se está a iniciar sessão com a conta correta.

    Depois de iniciar sessão com sucesso no Azure Percent no dispositivo, volte ao separador **Welcome** para continuar a experiência de configuração.

1. Quando o dispositivo atribuir o seu dispositivo à sua página **Azure IoT Hub** aparecer no separador **Welcome,** tome uma das seguintes ações:

    - Se já tem um Hub IoT que gostaria de utilizar com o Azure Percept e está listado nesta página, selecione-o e salte para o passo 15.
    - Se não tiver um Hub IoT ou quiser criar um novo, clique em **Criar um novo Azure IoT Hub**.

    > [!IMPORTANT]
    > Se tem um Hub IoT, mas não está na lista, pode ter assinado em Azure Percept com as credenciais erradas. Consulte o [guia de resolução de problemas de configuração](./how-to-troubleshoot-setup.md) para obter ajuda.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selecione um Hub IoT.":::

1. Para criar um novo Hub IoT, complete os seguintes campos:

    - Selecione a subscrição Azure que utilizará com a Azure Percept.
    - Selecione um Grupo de Recursos existente. Se um não existir, clique em **Criar novo** e siga as indicações.
    - Selecione a região Azure mais próxima da sua localização física.
    - Dê um nome ao seu novo Hub IoT.
    - Selecione o nível de preços S1 (standard).

    > [!NOTE]
    > Se acabar por necessitar de uma [produção](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) de mensagem mais alta para as suas aplicações de IA de ponta, poderá [atualizar o seu Hub IoT para um nível padrão mais elevado](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) no Portal Azure a qualquer momento. Os níveis B e F NÃO suportam O Azure Percept.

1. A implantação do IoT Hub pode demorar alguns minutos. Quando a implementação estiver concluída, clique em **Registar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub implantado com sucesso.":::

1. Introduza o nome do dispositivo para o seu kit dev e clique em **Seguinte**.

1. Aguarde que os módulos do dispositivo descarreguem – isto levará alguns minutos.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Preparação final.":::

1. Quando vir a configuração do **Dispositivo concluída!** página, o seu dev kit tem ligado com sucesso ao seu IoT Hub e descarregou o software necessário. O seu kit dev desligar-se-á automaticamente do ponto de acesso Wi-Fi, resultando nestas duas notificações:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Experimente a instalação, desconectar o aviso.":::

1. Ligue o computador anfitrião à rede Wi-Fi a que o seu devkit está ligado no Passo 2.

1. Clique **Em Continuar a ler O portal Azure.**

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Vá ao Estúdio Azure Percept.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Veja o seu stream de vídeo dev kit e implemente um modelo de amostra

1. A [página Azure Percept Studio Overview](https://go.microsoft.com/fwlink/?linkid=2135819) é o seu ponto de lançamento para aceder a muitos fluxos de trabalho diferentes para o desenvolvimento de soluções de IA de ponta inicial e avançada. Para começar, clique em **Dispositivos** a partir do menu esquerdo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Veja a sua lista de dispositivos.":::

1. Verifique se o seu kit dev está listado como **Conectado** e clique nele para ver a página do dispositivo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selecione o seu dispositivo.":::

1. Clique **em Ver o fluxo do seu dispositivo**. Se esta for a primeira vez que visualiza o fluxo de vídeo do seu dispositivo, verá uma notificação de que um novo modelo está a ser implantado no canto superior direito. Esta operação poderá demorar alguns minutos.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Veja o seu vídeo.":::

    Uma vez que o modelo tenha sido implantado, receberá outra notificação com um link **de stream do View.** Clique no link para ver o fluxo de vídeo da sua câmara Azure Percept Vision numa nova janela do navegador. O dev kit é pré-carregado com um modelo de IA que executa automaticamente a deteção de objetos de muitos objetos comuns.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Ver deteção de objetos.":::

1. O Azure Percept Studio também tem uma série de modelos de IA de amostra. Para implementar um modelo de amostra no seu kit dev, volte à página do seu dispositivo e clique em **Implementar um modelo de amostra**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Explore modelos pré-construídos.":::

1. Selecione um modelo de amostra da biblioteca e clique em **Implementar para o dispositivo**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Ver deteção de objetos em ação.":::

1. Uma vez que o modelo tenha sido implementado com sucesso, verá uma notificação com uma ligação **de fluxo De visualização** no canto superior direito do ecrã. Para ver o modelo inferencing em ação, clique no link na notificação ou volte à página do dispositivo e clique em **Ver o fluxo do seu dispositivo**. Quaisquer modelos anteriormente em execução no dev kit serão agora substituídos pelo novo modelo.

## <a name="video-walkthrough"></a>Instruções de vídeo

Para uma passagem visual dos passos acima descritos, consulte o seguinte vídeo (a experiência de configuração começa às 0:50):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma solução de visão sem código](./tutorial-nocode-vision.md)