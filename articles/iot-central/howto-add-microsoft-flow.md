---
title: Criar fluxos de trabalho com o conector do Azure IoT Central no Microsoft Flow | Documentos da Microsoft
description: Utilizar o conector de IoT Central no Microsoft Flow para acionar fluxos de trabalho e criar, obter, atualizar, eliminar dispositivos e executar comandos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: c0a03b70c6e5e4742e03d4892b2b5f97c908ab9c
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467912"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Criar fluxos de trabalho com o conector do Centro de IoT no Microsoft Flow

*Este tópico aplica-se para criadores e administradores.*

Utilize o Microsoft Flow para automatizar fluxos de trabalho entre os vários aplicativos e serviços que dependem os utilizadores empresariais. Utilizar o conector do Centro de IoT no Microsoft Flow, pode acionar fluxos de trabalho quando é acionada uma regra no Centro de IoT. Num fluxo de trabalho acionado por centro de IoT ou qualquer outro aplicativo, pode usar as ações no conector do Centro de IoT para:
- Criar um dispositivo
- Obter informações do dispositivo
- Atualizar as propriedades de um dispositivo e definições
- Executar um comando num dispositivo
- Eliminar um dispositivo

Confira [estes modelos do Microsoft Flow](https://aka.ms/iotcentralflowtemplates) IoT Central que se ligar a outros serviços como notificações móveis e o Microsoft Teams.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo de pay as you go
- Uma conta escolar ou Microsoft pessoal ou profissional para iniciar sessão no Flow ([Saiba mais sobre os planos do Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Acionar um fluxo de trabalho

Esta secção mostra-lhe como acionar uma notificação por telemóvel na aplicação móvel do Flow quando uma regra é acionada no Centro de IoT. Pode criar este fluxo de trabalho completo na sua aplicação de centro de IoT com o designer incorporado do Microsoft Flow.

1. Comece por [criar uma regra no Centro de IoT](howto-create-telemetry-rules.md). Depois de guardar as condições de regra, selecione o **ação do Microsoft Flow** como uma nova ação. Uma janela de caixa de diálogo será aberta para que possa configurar o seu fluxo de trabalho. A conta de utilizador do Centro de IoT que tem sessão iniciada no será utilizada para iniciar sessão no Microsoft Flow.

    ![Criar uma nova ação do Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Verá uma lista de fluxos de trabalho tha têm acesso a e estão ligados a esta regra de IoT Central. Clique em **explorar modelos** ou **New > criar do modelo** e pode escolher a partir de qualquer um dos modelos disponíveis. 

    ![Modelos disponíveis do Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Será solicitado a iniciar sessão nos conectores no modelo que escolheu. Depois dos conectores são conectados, será direcionado no designer para criar o seu fluxo de trabalho. O fluxo de trabalho tem um acionador de IoT Central que tem a sua aplicação e a regra já preenchidos.

1. Pode personalizar o fluxo de trabalho ao personalizar as informações transmitidas para a ação e a adição de novas ações. Neste exemplo, é a ação **notificações - enviar-me uma notificação por telemóvel**. Pode incluir *conteúdo dinâmico* da sua regra de IoT Central, passando informações importantes, como o nome do dispositivo e timestamp para a notificação.

    > [!NOTE]
    > Selecione o **ver mais** texto na janela de conteúdo dinâmico para obter valores de propriedade e de medição que disparou a regra.

    ![Fluxo de edição de ação com o painel dinâmico aberto](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Quando tiver concluído a ação de edição, selecione **guardar**. Será redirecionado à página de descrição geral do seu fluxo de trabalho. Aqui pode ver o histórico de execuções e partilhá-lo com outros colegas.

    > [!NOTE]
    > Se pretender que outros utilizadores na sua aplicação do Centro de IoT para editar esta regra, tem de partilhá-lo com os mesmos no Microsoft Flow. Adicione as suas contas do Microsoft Flow como proprietários no fluxo de trabalho.

1. Se voltar à sua aplicação IoT Central, verá que esta regra tem uma ação do Microsoft Flow na área de ações.

Também pode criar fluxos de trabalho utilizando o conector do Centro de IoT diretamente a partir do Microsoft Flow. Em seguida, pode escolher qual aplicação Centro de IoT para ligar ao.

## <a name="create-a-device-in-a-workflow"></a>Criar um dispositivo num fluxo de trabalho

Esta secção mostra-lhe como criar um novo dispositivo no Centro de IoT no envio de um botão num dispositivo móvel com a aplicação móvel Microsoft Flow. Pode utilizar esta ação no Flow para integrar sistemas ERP, como o Dynamics com o IoT Central através da criação de um novo dispositivo quando um dispositivo é adicionado em outro lugar.

1. Comece por criar um fluxo de trabalho em branco no Microsoft Flow.

1. Procure **botão de fluxo para dispositivos móveis** como acionador.

1. Neste acionador, adicione uma entrada de texto chamada **nome do dispositivo**. Alterar o texto de descrição seja **introduza o nome de dispositivo do seu dispositivo novo**.

1. Adicione uma nova ação. Procure o **Centro de IoT do Azure - criar um dispositivo** ação.

1. Escolha a sua aplicação e escolha um modelo de dispositivo para criar um dispositivo a partir nas listas pendentes. Verá a ação se expandem para mostrar todas as propriedades e definições do dispositivo.

1. Selecione o campo de nome do dispositivo. No painel de conteúdo dinâmico, escolha **nome do dispositivo**. Este valor é passado da entrada, o usuário digita através da aplicação móvel e é o nome do seu novo dispositivo na IoT Central. Neste exemplo, o único campo obrigatório é o nome do dispositivo, indicado pelo asterisco vermelho. Outro modelo de dispositivo pode ter vários campos necessários que tem de ser preenchidos para criar um novo dispositivo.

    ![Fluxo de criar o painel de dinâmica de ação de dispositivo](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

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

1. Escolha a sua aplicação na lista pendente. Agora terá um ID do dispositivo existente que pretende atualizar. 

    > [!NOTE] 
    > **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo se encontrar na lista do Explorador de dispositivos de dispositivos não é a correta para utilizar no Microsoft Flow.

    ![ID do Centro de IoT partir do URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Pode atualizar o nome do dispositivo. Para atualizar qualquer uma das propriedades e definições do dispositivo, tem de selecionar o modelo de dispositivo do dispositivo que pretende atualizar o **modelo de dispositivo** lista pendente. O mosaico de ação expande para mostrar todas as propriedades e definições que pode atualizar.

    ![Fluxo de trabalho de dispositivos de atualização de fluxo](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Selecione cada uma das propriedades e definições que pretende atualizar. A partir do painel de conteúdo dinâmico, escolha a entrada correspondente a partir do acionador. Neste exemplo, o valor de localização é propagado para baixo para atualizar a propriedade de localização do dispositivo.

1. Por fim, guarde o seu fluxo de trabalho.

1. Tente o seu fluxo de trabalho na aplicação móvel do Microsoft Flow. Vá para o **botões** separador na aplicação. Deverá ver o botão -> atualização um fluxo de trabalho do dispositivo. Introduza as entradas e ver o dispositivo for atualizado no Centro de IoT!

## <a name="get-device-information-in-a-workflow"></a>Obter informações de dispositivo num fluxo de trabalho

Pode obter informações de dispositivo utilizando o seu ID da **Centro de IoT do Azure - obter um dispositivo** ação. 
> [!NOTE] 
> **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo se encontrar na lista do Explorador de dispositivos de dispositivos não é a correta para utilizar no Microsoft Flow.

Pode obter informações, tais como o nome do dispositivo, o nome de modelo do dispositivo, os valores das propriedades e valores de definições para passar para as ações posteriores no fluxo de trabalho. Aqui está um fluxo de trabalho de exemplo que transmite o valor da propriedade de nome do cliente de um dispositivo para o Microsoft Teams.

   ![O fluxo de trabalho do fluxo get dispositivos](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Executar um comando num dispositivo num fluxo de trabalho
Pode executar um comando num dispositivo especificado pelo respetivo ID com o **do Azure IoT Central - executar um comando** ação. 

> [!NOTE] 
> **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo se encontrar na lista do Explorador de dispositivos de dispositivos não é a correta para utilizar no Microsoft Flow.
    
Pode selecionar o comando para executar e passar os parâmetros do comando esta ação. Aqui está um fluxo de trabalho de exemplo que executa um comando de reinício de dispositivo a partir de um botão na aplicação móvel do Microsoft Flow.

   ![O fluxo de trabalho do fluxo get dispositivos](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Eliminar um dispositivo num fluxo de trabalho

Pode eliminar um dispositivo pelo respetivo ID com o **do Azure IoT Central - eliminar um dispositivo** ação. 
> [!NOTE] 
> **Tem de utilizar o ID encontrado no URL** na página de detalhes do dispositivo do dispositivo que pretende atualizar. O ID do dispositivo se encontrar na lista do Explorador de dispositivos de dispositivos não é a correta para utilizar no Microsoft Flow.

Aqui está um fluxo de trabalho de exemplo que exclui um dispositivo com o envio de um botão na aplicação móvel do Microsoft Flow.

   ![O fluxo de trabalho do fluxo de eliminação de dispositivos](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Resolução de problemas

Se estiver a ter problemas ao criar uma ligação para o conector do Azure IoT Central, aqui estão algumas dicas para ajudá-lo.

1. Contas pessoais da Microsoft (tal como @hotmail.com, @live.com, @outlook.com domínios) não são suportadas neste momento. Tem de utilizar um trabalho do Azure Active Directory (AD) ou conta escolar.

2. Para utilizar o conector do Centro de IoT no Microsoft Flow, deve ter assinado no aplicativo IoT Central, pelo menos, uma vez. Caso contrário, a aplicação não será apresentado nas listas pendentes aplicação.

3. Se estiver a receber um erro ao utilizar uma conta do Azure AD, tente abrir o Windows PowerShell e execute os seguintes commandlets como administrador.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar o Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerir dispositivos](howto-manage-devices.md).

