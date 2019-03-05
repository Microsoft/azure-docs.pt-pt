---
title: Criar fluxos de trabalho com o conector do Centro de IoT no Microsoft Flow | Documentos da Microsoft
description: Utilizar o conector de IoT Central no Microsoft Flow para acionar fluxos de trabalho e criar, atualizar e eliminar dispositivos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: e0c4b90511da78ac5a8e454f653ab2a3819697c0
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317599"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Criar fluxos de trabalho com o conector do Centro de IoT no Microsoft Flow

*Este tópico aplica-se para criadores e administradores.*

Utilize o Microsoft Flow para automatizar fluxos de trabalho entre os vários aplicativos e serviços que dependem os utilizadores empresariais. Utilizar o conector do Centro de IoT no Microsoft Flow, pode acionar fluxos de trabalho quando é acionada uma regra no Centro de IoT. Num fluxo de trabalho acionado por centro de IoT ou qualquer outro aplicativo, pode utilizar as ações no conector do Centro de IoT para criar um dispositivo, atualizar as propriedades e definições de um dispositivo ou eliminar um dispositivo. Confira [estes modelos do Microsoft Flow](https://aka.ms/iotcentralflowtemplates) IoT Central que se ligar a outros serviços como notificações móveis e o Microsoft Teams.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo de pay as you go
- Uma conta escolar ou Microsoft pessoal ou profissional para iniciar sessão no Flow ([Saiba mais sobre os planos do Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Acionar um fluxo de trabalho quando é acionada uma regra

Esta secção mostra-lhe como acionar uma notificação por telemóvel na aplicação móvel do Flow, quando é acionada uma regra no Centro de IoT.

1. Comece por [criar uma regra no Centro de IoT](howto-create-telemetry-rules.md). Depois de guardar as condições de regra, selecione o **ação do Microsoft Flow** como uma nova ação. Um novo separador ou janela deve ser aberto no browser, a levá-lo no Microsoft Flow.

    ![Criar uma nova ação do Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.PNG)

1. Inicie sessão no Microsoft Flow. Isso não tem de ser a mesma conta que aquela que utiliza no Centro de IoT. Acessará uma página de descrição geral que mostra um conector do Centro de IoT ligar a uma ação personalizada.

1. Inicie sessão para o conector do Centro de IoT e selecione **continuar**. É direcionado para o estruturador do Microsoft Flow para criar o seu fluxo de trabalho. O fluxo de trabalho tem um acionador de IoT Central que tem a sua aplicação e a regra já preenchidos.

1. Escolher **+ novo passo** e **adicionar uma ação**. Neste momento é possível adicionar qualquer ação que pretende o fluxo de trabalho. Por exemplo, vamos enviar uma notificação por telemóvel. Procure **notificação**e escolha **notificações - enviar-me uma notificação por telemóvel**.

1. Na ação, preencha o campo de texto com o que deseja que a notificação a dizer. Pode incluir *conteúdo dinâmico* da sua regra de IoT Central, passando informações importantes, como o nome do dispositivo e timestamp para a notificação.

    > [!NOTE]
    > Selecione o **ver mais** texto na janela de conteúdo dinâmico para obter valores de propriedade e de medição que disparou a regra.

    ![Fluxo de edição de ação com o painel dinâmico aberto](./media/howto-add-microsoft-flow/flowdynamicpane.PNG)

1. Quando tiver concluído a ação de edição, selecione **guardar**. Será redirecionado à página de descrição geral do seu fluxo de trabalho. Aqui pode ver o histórico de execuções e partilhá-lo com outros colegas.

    > [!NOTE]
    > Se pretender que outros utilizadores na sua aplicação do Centro de IoT para editar esta regra, tem de partilhá-lo com os mesmos no Microsoft Flow. Adicione as suas contas do Microsoft Flow como proprietários no fluxo de trabalho.

1. Se voltar à sua aplicação IoT Central, verá que esta regra tem uma ação do Microsoft Flow sob a área de ações.

Sempre que pode começar a criar um fluxo de trabalho com o conector de IoT Central no Microsoft Flow. Em seguida, pode escolher que aplicação IoT Central e quais regras para ligar a.

## <a name="create-a-device-in-a-workflow"></a>Criar um dispositivo num fluxo de trabalho

Esta secção mostra-lhe como criar um novo dispositivo no Centro de IoT no envio de um botão num dispositivo móvel com a aplicação móvel Microsoft Flow. Pode utilizar esta ação no Flow para integrar sistemas ERP, como o Dynamics com o IoT Central através da criação de um novo dispositivo quando um dispositivo é adicionado em outro lugar.

1. Comece por criar um fluxo de trabalho em branco no Microsoft Flow.

1. Procure **botão de fluxo para dispositivos móveis** como acionador.

1. Neste acionador, adicione uma entrada de texto chamada **nome do dispositivo**. Alterar o texto de descrição seja **introduza o nome de dispositivo do seu dispositivo novo**.

1. Adicione uma nova ação. Procure o **Centro de IoT do Azure - criar um dispositivo** ação.

1. Escolha a sua aplicação e escolha um modelo de dispositivo para criar um dispositivo a partir nas listas pendentes. Verá a ação se expandem para mostrar todas as propriedades e definições do dispositivo.

1. Selecione o campo de nome do dispositivo. No painel de conteúdo dinâmico, escolha **nome do dispositivo**. Este valor vai ser transmitida a partir da entrada do usuário através da aplicação móvel e vai ser o nome do seu dispositivo novo na IoT Central. Neste exemplo, o único campo obrigatório é o nome do dispositivo, indicado pelo asterisco vermelho. Outro modelo de dispositivo pode ter vários campos necessários que tem de ser preenchidos para criar um novo dispositivo.

    ![Fluxo de criar o painel de dinâmica de ação de dispositivo](./media/howto-add-microsoft-flow/flowcreatedevice.PNG)
1. (Opcional) Preencha outros campos conforme achar adequado para os criação de novos dispositivos.

1. Por fim, guarde o seu fluxo de trabalho.

1. Tente o seu fluxo de trabalho na aplicação móvel do Microsoft Flow. Vá para o **botões** separador na aplicação. Deverá ver o botão -> criar um novo fluxo de trabalho do dispositivo. Introduza o nome do seu novo dispositivo e vê-lo a aparecer no Centro de IoT!

    ![Fluxo criar dispositivo móvel captura de ecrã](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Atualizar um dispositivo num fluxo de trabalho

Esta secção mostra-lhe como atualizar as definições e propriedades no Centro de IoT no envio de um botão num dispositivo móvel com a aplicação móvel Microsoft Flow.

1. Comece por criar um fluxo de trabalho em branco no Microsoft Flow.

1. Procure **botão de fluxo para dispositivos móveis** como acionador.

1. Neste acionador, adicione uma entrada, como um **localização** introdução de texto que corresponde a uma definição ou propriedade que pretende alterar. Altere o texto de descrição.

1. Adicione uma nova ação. Procure o **do Azure IoT Central - atualizar um dispositivo** ação.

1. Escolha a sua aplicação na lista pendente. Agora terá um ID do dispositivo existente que pretende atualizar. Pode obter o ID do dispositivo IoT Central no URL do navegador.

    ![ID de dispositivo do Explorador de dispositivo do IoT Central](./media/howto-add-microsoft-flow/iotcdeviceid.PNG)

1. Pode atualizar o nome do dispositivo. Para atualizar qualquer uma das propriedades e definições do dispositivo, tem de selecionar o modelo de dispositivo do dispositivo que pretende atualizar o **modelo de dispositivo** lista pendente. O mosaico de ação expande para mostrar todas as propriedades e definições que pode atualizar.

    ![Fluxo de trabalho de dispositivos de atualização de fluxo](./media/howto-add-microsoft-flow/flowupdatedevice.PNG)

1. Selecione cada uma das propriedades e definições que pretende atualizar. A partir do painel de conteúdo dinâmico, escolha a entrada correspondente a partir do acionador. Neste exemplo, o valor de localização é propagado para baixo para atualizar a propriedade de localização do dispositivo.

1. Por fim, guarde o seu fluxo de trabalho.

1. Tente o seu fluxo de trabalho na aplicação móvel do Microsoft Flow. Vá para o **botões** separador na aplicação. Deverá ver o botão -> atualização um fluxo de trabalho do dispositivo. Introduza as entradas e ver o dispositivo for atualizado no Centro de IoT!

## <a name="delete-a-device-in-a-workflow"></a>Eliminar um dispositivo num fluxo de trabalho

Pode eliminar um dispositivo ao seu ID de dispositivo com o **do Azure IoT Central - eliminar um dispositivo** ação. Aqui está um fluxo de trabalho de exemplo que exclui um dispositivo com o envio de um botão na aplicação móvel do Microsoft Flow.

   ![O fluxo de trabalho do fluxo de eliminação de dispositivos](./media/howto-add-microsoft-flow/flowdeletedevice.PNG)
    
## <a name="troubleshooting"></a>Resolução de problemas

Se estiver a ter problemas ao criar uma ligação para o conector do Azure IoT Central, aqui estão algumas dicas para ajudá-lo.

1. Contas pessoais da Microsoft (tal como @hotmail.com, @live.com, @outlook.com domínios) não são suportadas neste momento. Deve usar um trabalho AAD conta escolar ou profissional.

2. Para utilizar o conector do Centro de IoT no Microsoft Flow, deve ter assinado no aplicativo IoT Central, pelo menos, uma vez. Caso contrário, a aplicação não será apresentado nas listas pendentes aplicação.

3. Se estiver a receber um erro ao utilizar uma conta do AAD, tente abrir o Windows PowerShell e execute os seguintes commandlets como administrador.
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu como utilizar o Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerir dispositivos](howto-manage-devices.md).

