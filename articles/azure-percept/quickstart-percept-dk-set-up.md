---
title: Configurar o seu Azure Percept DK
description: Ligue o seu kit dev ao Azure e implemente o seu primeiro modelo de IA
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 3540204d66bb589c567514f92a9a8acb2159e343
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665644"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configure o seu Azure Percept DK e implemente o seu primeiro modelo de IA

Começa com o Azure Percept DK e o Azure Percept Studio utilizando a experiência de configuração Azure Percept DK para ligar o seu dispositivo ao Azure e para implementar o seu primeiro modelo de IA. Depois de verificar se a sua conta Azure é compatível com o Azure Percept Studio, completa a experiência de configuração para garantir que o seu Azure Percept DK está configurado para criar uma prova de conceitos edge AI.

Se tiver algum problema durante este Quick Start, consulte o guia [de resolução de problemas](./troubleshoot-dev-kit.md) para possíveis soluções.

## <a name="prerequisites"></a>Pré-requisitos

- Um Azure Percept DK.
- Um computador anfitrião baseado em Windows, Linux ou OS X com capacidade wi-fi e um navegador web.
- Uma conta Azure com uma subscrição ativa. [Criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A conta Azure deve ter a função "proprietário" ou "contribuinte" na subscrição. Saiba mais sobre definições de papel do Azure

### <a name="prerequisite-check"></a>Verificação pré-requisito

Para verificar se a sua conta Azure é um "proprietário" ou "contribuinte" na subscrição, seguindo estes passos.

1. Vá ao portal Azure e faça login com a mesma conta Azure que pretende utilizar com o Azure Percept Studio. 

    > [!NOTE]
    > Se tiver várias contas Azure, o seu navegador poderá cache credenciais de outra conta. Consulte o guia de resolução de problemas para obter mais informações sobre como garantir que está assinado com a conta correta.

1. Expanda o menu principal a partir do canto superior esquerdo do ecrã e clique em "Subscrições" ou selecione "Subscrições" a partir do menu de ícones na página inicial. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Selecione a sua subscrição da lista. Se não vir a sua subscrição na lista, certifique-se de que está assinado com a conta Azure correta. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Se desejar criar uma nova subscrição, siga [estes passos](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. No menu Subscrição selecione "Controlo de acesso (IAM)"
1. Clique no botão "Ver o meu acesso"
1. Verifique a função
    - Se mostrar o papel de "Reader" ou se receber uma mensagem que diga que não tem permissões para ver funções, terá de seguir o processo necessário na sua organização para elevar o seu papel de conta.
    - Se mostrar o papel de "proprietário" ou "contribuinte", a sua conta funcionará com o Azure Percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Lançar a experiência de configuração Azure Percept DK

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Ligue o computador anfitrião diretamente ao ponto de acesso wi-fi do kit dev. Isto é feito assim como ligar-se a qualquer outra rede wi-fi,
    - **nome da rede**: scz-xxxx (onde "xxxx" é os últimos quatro dígitos do endereço de rede MAC do kit dev)
    - **palavra-passe**: pode ser encontrado no Cartão de Boas-Vindas que veio com o kit dev

    > [!WARNING]
    > Enquanto está ligado ao ponto de acesso wi-fi Azure Percept DK, o computador anfitrião perderá temporariamente a sua ligação à Internet. Chamadas de videoconferência ativas, streaming web ou outras experiências baseadas em rede serão interrompidas até que o passo 3 da experiência de configuração Azure Percept DK esteja concluído.

1. Uma vez ligado ao ponto de acesso wi-fi do kit dev, o dispositivo anfitrião lançará automaticamente a experiência de configuração Azure Percept DK numa nova janela do navegador. Se não abrir automaticamente, pode lançá-lo manualmente abrindo uma janela do navegador e navegando para [http://10.1.1.1](http://10.1.1.1) . 

1. Agora que lançou a experiência de configuração Azure Percept, está pronto para prosseguir com a experiência de configuração. 

    > [!NOTE]
    > O serviço web de configuração Azure Percept DK será encerrado após 30 minutos de não utilização e no final da experiência de configuração. Quando isto acontece, recomenda-se reiniciar o kit dev para evitar problemas de conectividade com o ponto de acesso wi-fi do dev kit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Complete a experiência de configuração Azure Percept DK

1. Começar - Clique em **seguida** no ecrã welcome.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Página de boas-vindas.":::

1. Conecte-se ao wi-fi - Na página de ligação da Rede, clique em **Ligar a uma nova rede Wi-Fi** para ligar o seu devkit à sua rede wi-fi.

    Se já ligou este kit dev à sua rede wi-fi ou se já está ligado à experiência de configuração Azure Percept DK através da sua rede wi-fi, clique no link **Skip.**

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Ligue-se ao wi-fi.":::

1. Selecione a sua rede wi-fi a partir das ligações disponíveis e conecte-se. (Vai requerer a sua senha wi-fi local)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Selecione rede wi-fi."::: 

1. Copie o seu endereço IP - Uma vez que o seu devkit tenha sido conectado com sucesso à sua rede de escolha, escreva o **endereço IPv4** apresentado na página. **Você precisará deste endereço IP mais tarde neste guia de arranque rápido**. 

    > [!NOTE]
    > O endereço IP pode ser alterado com cada arranque do dispositivo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiar endereço IP.":::

1. Reveja e aceite o Contrato de Licença - Leia através do Contrato de Licença, **selecione eu li e concordo com o Contrato** de Licença (deve rolar para o fundo do contrato), e clique em **Seguinte**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Aceite a EULA.":::

1. Crie a sua conta de login SSH - Crie SSH para acesso remoto ao seu devkit. Crie um nome de utilizador SSH e uma palavra-passe. 

    > [!NOTE]
    > SSH (Secure Shell) é um protocolo de rede utilizado para uma comunicação segura entre o dispositivo de anfitrião e o kit dev. Para mais informações sobre o SSH, consulte [este artigo.](https://en.wikipedia.org/wiki/SSH_(Secure_Shell))

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="Criar conta SSH."::: 

1. Inicie o processo de ligação do dev kit - No ecrã seguinte, clique em **Ligar com um novo dispositivo** para iniciar o processo de ligação do seu kit dev ao Azure IoT Hub. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Ligue-se a Azure."::: 

1. Copie o código do dispositivo - Clique no link **Copy** para copiar o código do seu dispositivo. Em seguida, clique em **Iniciar sessão para Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Código do dispositivo de cópia."::: 

1. Cole o código do dispositivo - Um novo separador de navegador abrirá com uma janela que pede o código do dispositivo copiado. Cole o código na janela e clique em **Seguinte**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Introduza o código do dispositivo."::: 

1. Inscreva-se no Azure - A próxima janela requer que assine com a conta Azure que verificou no início do Quick Start. Introduza as credenciais de login e clique em **Seguinte**. 

    > [!NOTE]
    > O seu navegador pode cache automático outras credenciais. Verifique duas vezes se está a iniciar sessão com a conta correta.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Inscreva-se no Azure.":::
 
1. Não feche o separador de navegador de experiência de configuração neste passo - Após a sua assinatura será apresentado com um ecrã reconhecendo que assinou. Embora diga que muitos fecham a janela, **recomendamos que não feche nenhuma janela.** 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Sucesso de inscrição."::: 

1. Volte ao separador do navegador que hospeda a experiência de configuração.
1. Selecione a sua opção IoT Hub
    - Se já tem um Hub IoT e está listado nesta página, pode selecioná-lo e **saltar para o passo 17**.
    - Se não tiver um Hub IoT ou quiser criar um novo, vá para o fundo da lista e clique em **Criar um novo Azure IoT Hub**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selecione um Hub IoT."::: 

1. Crie o seu novo Hub IoT – Preencha todos os campos desta página. 
    - Selecione a subscrição Azure que utilizará com o Azure Percept Studio
    - Selecione um Grupo de Recursos existente. Se não existir, clique em "Criar novo" e siga as indicações. 
    - Selecione a região de Azure. 
    - Dê um nome ao seu novo Hub IoT
    - Selecione o nível de preços (normalmente corresponde à subscrição)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Criar um novo Hub IoT."::: 

1. Aguarde que o Hub IoT seja implantado – Pode demorar alguns minutos

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="Implementar o Hub IoT."::: 

1. Registar o seu kit dev - Quando a implementação estiver concluída, clique no botão **Registar**

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub implantado com sucesso."::: 

1. Nomeie o seu kit dev - Introduza um nome de dispositivo para o seu kit dev e clique em **Seguinte**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Diga o nome do dispositivo."::: 

1. Aguarde que os modelos de IA predefinidos descarreguem – isto levará alguns minutos

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Preparação final."::: 

1. Consulte a Visão AI em ação - O seu devkit foi ligado com sucesso ao seu Azure IoT Hub e recebeu o modelo de deteção de objetos de IA de visão padrão. A câmara Azure Percept Vision pode agora fazer a deteção de objetos inferencing sem uma ligação à nuvem. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Clique na saída de vídeo de pré-visualização."::: 
       
    > [!NOTE]
    > O processo de embarque e ligação ao dispositivo O acesso wi-fi ao computador anfitrião desliga-se neste momento, mas o seu kit dev permanecerá ligado à internet.   Pode reiniciar a experiência de embarque com um reboot de dev kit, que lhe permitirá voltar a navegar e reconectar o dispositivo a um outro hub IOT associado à mesma subscrição ou a um Azure diferente..

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Experimente a instalação, desconectar o aviso."::: 

1. Continue até ao Portal Azure – Volte à janela de experiência de configuração e clique no botão **Continuar para o portal Azure** para começar a criar os seus modelos de IA personalizados no Azure Percept Studio.

    > [!NOTE]
    > Verifique se o seu computador anfitrião já não está ligado ao ponto de acesso do dev kit nas definições wifi e está agora novamente ligado ao wifi local.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Vá ao Estúdio Azure Percept."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Veja o seu Dispositivo no Azure Percept Studio e implemente aplicações de amostras pré-construídas comuns

1. Consulte a sua lista de dispositivos a partir da página de visão geral do Azure Percept. A página Azure Percept Overview é o seu ponto de lançamento para aceder a muitos fluxos de trabalho diferentes tanto para o início como para o avançado AI Edge Model e Desenvolvimento de Soluções

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Veja a sua lista de dispositivos.":::
    
1. Verifique se o dispositivo que acabou de criar está ligado e clique nele.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selecione o seu dispositivo.":::

1. Veja o fluxo do seu dispositivo para ver o que a sua câmara de dev kit está a ver. Um modelo de deteção de objetos predefinidos é implantado fora da caixa e irá detetar uma série de objetos comuns.

    > [!NOTE]
    > na primeira vez que o fizer num novo dispositivo receberá a notificação de que um novo módulo está a ser implantado no canto superior direito.  Assim que isto for competido, poderás lançar a janela com o fluxo de vídeo da câmara. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Veja o seu vídeo.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Ver deteção de objetos.":::

1. Explore modelos de IA de amostra pré-construídos.   O Azure Precept Studio tem uma série de amostras pré-construídas comuns que podem ser implantadas com um único clique.   Selecione "Implementar um modelo de amostra" para visualizar e implementar estes.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Explore modelos pré-construídos.":::
    
1. Coloque uma nova amostra pré-construída no seu dispositivo conectado. Selecione uma amostra da biblioteca e clique em "Implementar para dispositivo"

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-1-select-prebuilt.png" alt-text="Selecione um pré-construído.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Ver deteção de objetos em ação.":::

## <a name="next-steps"></a>Passos seguintes

Pode seguir um fluxo semelhante para experimentar [modelos de fala pré-construídos.](./tutorial-no-code-speech.md)