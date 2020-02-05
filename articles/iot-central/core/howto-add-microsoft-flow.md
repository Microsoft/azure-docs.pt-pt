---
title: Utilize o conector Central Azure IoT no Microsoft Flow  Microsoft Docs
description: Utilize o conector IoT Central no Microsoft Flow para desencadear fluxos de trabalho e criar, obter, atualizar, eliminar dispositivos e executar comandos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: 42e79a03f5e3ba0e3e39cd37d9911b3a150cf175
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985463"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Construir fluxos de trabalho com o conector IoT Central no Microsoft Flow

*Este tópico se aplica a construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Utilize o Microsoft Flow para automatizar fluxos de trabalho em muitas aplicações e serviços em que os utilizadores de negócios dependem. Utilizando o conector IoT Central no Microsoft Flow, pode acionar fluxos de trabalho quando uma regra é ativada no IoT Central. Num fluxo de trabalho desencadeado pela IoT Central ou por qualquer outra aplicação, pode utilizar as ações no conector Central IoT para:
- Criar um dispositivo
- Obtenha informações sobre o dispositivo
- Atualizar as propriedades e configurações de um dispositivo
- Executar um comando em um dispositivo
- Apagar um dispositivo

Confira [estes modelos do Microsoft Flow](https://aka.ms/iotcentralflowtemplates) que ligam o IoT Central a outros serviços, como notificações móveis e Equipas Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação criada usando um plano de preços padrão
- Uma conta pessoal ou de trabalho ou escola da Microsoft para usar o Microsoft Flow ([saiba mais sobre os planos do Microsoft Flow)](https://aka.ms/microsoftflowplans)
- Uma conta de trabalho ou escola para usar o conector Central Azure IoT

## <a name="trigger-a-workflow"></a>Desencadear um fluxo de trabalho

Esta secção mostra-lhe como desencadear uma notificação móvel na aplicação móvel Flow quando uma regra dispara no IoT Central. Você pode construir todo este fluxo de trabalho dentro da sua aplicação IoT Central usando o designer incorporado Microsoft Flow.

1. Comece por [criar uma regra na IoT Central.](howto-create-telemetry-rules.md) Depois de guardar as condições de regra, selecione a **ação Microsoft Flow** como uma nova ação. Abrir-se-á uma janela de diálogo para configurar o seu fluxo de trabalho. A conta de utilizador IoT Central em que está assinado será usada para iniciar sessão no Microsoft Flow.

    ![Criar uma nova ação do Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Você verá uma lista de fluxos de trabalho a que você tem acesso e está anexado a esta regra IoT Central. Clique em **Explore modelos** ou **New > Crie a partir do modelo** e pode escolher entre qualquer um dos modelos disponíveis. 

    ![Modelos disponíveis do Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Será solicitado a assinar nos conectores no modelo que escolheu. 

    > [!NOTE]
    > Para utilizar o conector Central Azure IoT, deve assinar através de uma conta de Diretório Ativo Azure (conta de trabalho ou escola). Uma conta pessoal como abc@outlook.com ou abc@live.com não é suportada pelo conector Central Azure IoT.

    Assim que tiver assinado os conectores, irá aterrar em designer para construir o seu fluxo de trabalho. O fluxo de trabalho tem um gatilho Central IoT que tem a sua Aplicação e Regra já preenchidas.

1. Pode personalizar o fluxo de trabalho personalizando a informação passada para a ação e adicionando novas ações. Neste exemplo, a ação é **Notificações - Envie-me uma notificação móvel**. Pode incluir *conteúdo dinâmico* a partir da sua regra IoT Central, transmitindo informações importantes, como o nome do dispositivo e o carimbo temporal para a sua notificação.

    > [!NOTE]
    > Selecione o texto **Ver mais** texto na janela de conteúdo Dinâmico para obter valores de medição e propriedade que desencadearam a regra.

    ![Ação de edição de fluxo com painel dinâmico aberto](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Quando terminar de editar a sua ação, selecione **Guardar**. Será direcionado para a página geral do seu fluxo de trabalho. Aqui pode ver a história da corrida e partilhá-la com outros colegas.

    > [!NOTE]
    > Se quiser que outros utilizadores da sua aplicação IoT Central editem esta regra, deve partilhá-la com eles no Microsoft Flow. Adicione as suas contas Microsoft Flow como proprietários no seu fluxo de trabalho.

1. Se voltar à sua aplicação IoT Central, verá que esta regra tem uma ação do Microsoft Flow na área de Ações.

Também pode construir fluxos de trabalho utilizando o conector IoT Central diretamente do Microsoft Flow. Em seguida, pode escolher a qual aplicação IoT Central para se conectar.

## <a name="create-a-device-in-a-workflow"></a>Criar um dispositivo num fluxo de trabalho

Esta secção mostra-lhe como criar um novo dispositivo no IoT Central ao premir um botão num dispositivo móvel utilizando a aplicação móvel Microsoft Flow. Pode utilizar esta ação no Flow para integrar sistemas ERP como o Dynamics com o IoT Central, criando um novo dispositivo quando um dispositivo é adicionado em outro lugar.

1. Comece por criar um fluxo de trabalho em branco no Microsoft Flow.

1. Procure o **botão Flow para o telemóvel** como gatilho.

1. Neste gatilho, adicione uma entrada de texto chamada **Nome do Dispositivo**. Altere o texto de descrição para ser **O nome do dispositivo do seu novo dispositivo**.

1. Adicione uma nova ação. Pesquisa da **Central Azure IoT - Crie uma** ação do dispositivo.

1. Escolha a sua aplicação e escolha um modelo de dispositivo para criar um dispositivo nas quedas. Verá a ação expandir-se para mostrar todas as propriedades e configurações do dispositivo.

1. Selecione o campo Nome do Dispositivo. A partir do painel de conteúdo dinâmico, escolha **o Nome**do Dispositivo . Este valor é passado a partir da entrada que o utilizador entra através da aplicação móvel, e é o nome do seu novo dispositivo na IoT Central. Neste exemplo, o único campo necessário é o nome do dispositivo, indicado pelo asterisco vermelho. Outro modelo de dispositivo pode ter vários campos necessários que precisam de ser preenchidos para criar um novo dispositivo.

    ![Fluxo criar painel dinâmico de ação do dispositivo](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (Opcional) Preencha outros campos que achar adequado para a sua criação de novos dispositivos.

1. Finalmente, poupe o seu fluxo de trabalho.

1. Experimente o seu fluxo de trabalho na aplicação móvel Microsoft Flow. Vá ao separador **Botões** na aplicação. Deve ver o seu Botão -> Criar um novo fluxo de trabalho do dispositivo. Insira o nome do seu novo dispositivo e veja-o aparecer na IoT Central!

    ![Fluxo criar dispositivo screenshot móvel](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Atualizar um dispositivo num fluxo de trabalho

Esta secção mostra-lhe como atualizar as definições e propriedades do dispositivo no IoT Central ao premir um botão num dispositivo móvel utilizando a aplicação móvel Microsoft Flow.

1. Comece por criar um fluxo de trabalho em branco no Microsoft Flow.

1. Procure o **botão Flow para o telemóvel** como gatilho.

1. Neste gatilho, adicione uma entrada como uma entrada de texto de **Localização** que corresponda a uma definição ou propriedade que pretende alterar. Altere o texto de descrição.

1. Adicione uma nova ação. Procure a **Central Azure IoT - Atualize uma** ação do dispositivo.

1. Escolha o seu pedido da entrega. Agora vai precisar de uma identificação do dispositivo existente que pretende atualizar. 

    > [!NOTE] 
    > **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo encontrado na lista de dispositivos do explorador do dispositivo não é o certo para ser utilizado no Microsoft Flow.

    ![IoT Id Central de URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Pode atualizar o nome do dispositivo. Para atualizar qualquer uma das propriedades e configurações do dispositivo, deve selecionar o modelo de dispositivo do dispositivo que pretende atualizar no desfecho do Modelo de **Dispositivo.** O azulejo de ação expande-se para mostrar todas as propriedades e configurações que pode atualizar.

    ![Fluxo de trabalho do dispositivo de atualização de fluxo](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Selecione cada uma das propriedades e definições que pretende atualizar. A partir do painel de conteúdo dinâmico, escolha a entrada correspondente do gatilho. Neste exemplo, o valor de Localização é propagado para atualização da propriedade de Localização do dispositivo.

1. Finalmente, poupe o seu fluxo de trabalho.

1. Experimente o seu fluxo de trabalho na aplicação móvel Microsoft Flow. Vá ao separador **Botões** na aplicação. Deve ver o botão -> Atualizar um fluxo de trabalho do dispositivo. Introduza as inputs e veja o dispositivo atualizado no IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Obtenha informações do dispositivo num fluxo de trabalho

Pode obter informações do dispositivo através do seu ID utilizando a **Central Azure IoT - Obtenha uma** ação do dispositivo. 
> [!NOTE] 
> **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo encontrado na lista de dispositivos do explorador do dispositivo não é o certo para ser utilizado no Microsoft Flow.

Pode obter informações como nome do dispositivo, nome do modelo do dispositivo, valores de propriedade e valores de definições para passar para ações posteriores no seu fluxo de trabalho. Aqui está um fluxo de trabalho de exemplo que passa ao longo do valor de propriedade do Nome do Cliente de um dispositivo para Microsoft Teams.

   ![Fluxo obter fluxo de trabalho do dispositivo](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Executar um comando em um dispositivo em um fluxo de trabalho
Pode executar um comando num dispositivo especificado pelo seu ID utilizando a **Central Azure IoT - Executar uma** ação de comando. 

> [!NOTE] 
> **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo encontrado na lista de dispositivos do explorador do dispositivo não é o certo para ser utilizado no Microsoft Flow.
    
Pode escolher o comando para correr e passar nos parâmetros do comando através desta ação. Aqui está um fluxo de trabalho de exemplo que executa um comando de reinicialização do dispositivo a partir de um botão na aplicação móvel Microsoft Flow.

   ![Fluxo obter fluxo de trabalho do dispositivo](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Eliminar um dispositivo num fluxo de trabalho

Pode eliminar um dispositivo através do seu ID utilizando a **Central Azure IoT - Apagar uma** ação do dispositivo. 
> [!NOTE] 
> **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo encontrado na lista de dispositivos do explorador do dispositivo não é o certo para ser utilizado no Microsoft Flow.

Aqui está um fluxo de trabalho de exemplo que elimina um dispositivo ao premir um botão na aplicação móvel Microsoft Flow.

   ![Fluxo eliminar fluxo de trabalho do dispositivo](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Resolução de problemas

Se está com dificuldades em criar uma ligação ao conector Central Azure IoT, aqui ficam algumas dicas para o ajudar.

1. As contas pessoais da Microsoft (como @hotmail.com, @live.com, @outlook.com domínios) não são suportadas neste momento. Deve utilizar uma conta de trabalho ou escola de Diretório Ativo Azure (AD).

2. Para utilizar o conector IoT Central no Microsoft Flow, deve ter assinado a aplicação IoT Central pelo menos uma vez. Caso contrário, o pedido não aparecerá nas desistências da Aplicação.

3. Se estiver a receber um erro ao utilizar uma conta Azure AD, tente abrir o Windows PowerShell e executar os seguintes comandos como administrador.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o Microsoft Flow para construir fluxos de trabalho, o próximo passo sugerido é [gerir os dispositivos](howto-manage-devices.md).

