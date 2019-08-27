---
title: Crie fluxos de trabalho com o conector de IoT Central do Azure no Microsoft Flow | Microsoft Docs
description: Use o conector de IoT Central no Microsoft Flow para disparar fluxos de trabalho e criar, obter, atualizar, excluir dispositivos e executar comandos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: a972ab52f64a37ac6876194202324b4380460817
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050575"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Crie fluxos de trabalho com o conector de IoT Central no Microsoft Flow

*Este tópico se aplica a construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Use Microsoft Flow para automatizar fluxos de trabalho em vários aplicativos e serviços dos quais os usuários corporativos dependem. Usando o conector de IoT Central no Microsoft Flow, você pode disparar fluxos de trabalho quando uma regra é disparada no IoT Central. Em um fluxo de trabalho disparado por IoT Central ou qualquer outro aplicativo, você pode usar as ações no conector de IoT Central para:
- Criar um dispositivo
- Obter informações do dispositivo
- Atualizar as propriedades e configurações de um dispositivo
- Executar um comando em um dispositivo
- Excluir um dispositivo

Confira [esses Microsoft Flow modelos](https://aka.ms/iotcentralflowtemplates) que conectam IOT central a outros serviços, como notificações móveis e Microsoft Teams.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo pago conforme o uso
- Uma conta pessoal ou corporativa ou de estudante da Microsoft para usar Microsoft Flow ([saiba mais sobre planos de Microsoft Flow](https://aka.ms/microsoftflowplans))
- Uma conta corporativa ou de estudante para usar o conector de IoT Central do Azure

## <a name="trigger-a-workflow"></a>Disparar um fluxo de trabalho

Esta seção mostra como disparar uma notificação móvel no aplicativo móvel do Flow quando uma regra é disparada no IoT Central. Você pode criar todo o fluxo de trabalho em seu aplicativo IoT Central usando o designer de Microsoft Flow incorporado.

1. Comece [criando uma regra no IOT central](howto-create-telemetry-rules.md). Depois de salvar as condições de regra, selecione a **ação Microsoft Flow** como uma nova ação. Uma janela de diálogo será aberta para que você configure seu fluxo de trabalho. A conta de usuário IoT Central à qual você está conectado será usada para entrar no Microsoft Flow.

    ![Criar uma nova ação de Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Você verá uma lista de fluxos de trabalho aos quais você tem acesso e está anexado a esta regra de IoT Central. Clique em **explorar modelos** ou em **novo > criar a partir do modelo** e você pode escolher entre qualquer um dos modelos disponíveis. 

    ![Modelos de Microsoft Flow disponíveis](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Você será solicitado a entrar nos conectores no modelo escolhido. 

    > [!NOTE]
    > Para usar o conector de IoT Central do Azure, você deve entrar usando uma conta de Azure Active Directory (conta corporativa ou de estudante). Uma conta pessoal, como abc@outlook.com ou abc@live.com , não é suportada pelo conector do IOT central do Azure.

    Depois de entrar nos conectores, você será levado ao designer para criar seu fluxo de trabalho. O fluxo de trabalho tem um gatilho IoT Central que tem seu aplicativo e a regra já preenchidos.

1. Você pode personalizar o fluxo de trabalho Personalizando as informações passadas para a ação e adicionando novas ações. Neste exemplo, a ação é **notificações-envie-me uma notificação móvel**. Você pode incluir *conteúdo dinâmico* de sua regra de IOT central, passando informações importantes, como o nome do dispositivo e o carimbo de data/hora para sua notificação.

    > [!NOTE]
    > Selecione a janela **Ver mais** texto no conteúdo dinâmico para obter valores de medição e propriedade que dispararam a regra.

    ![Ação de edição de fluxo com painel dinâmico aberto](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Quando terminar de editar sua ação, selecione **salvar**. Você será direcionado para a página de visão geral do fluxo de trabalho. Aqui você pode ver o histórico de execuções e compartilhá-lo com outros colegas.

    > [!NOTE]
    > Se você quiser que outros usuários em seu aplicativo IoT Central editem essa regra, você deve compartilhá-la com eles em Microsoft Flow. Adicione suas contas de Microsoft Flow como proprietários em seu fluxo de trabalho.

1. Se voltar para seu aplicativo IoT Central, você verá que essa regra tem uma ação Microsoft Flow na área ações.

Você também pode criar fluxos de trabalho usando o conector de IoT Central diretamente do Microsoft Flow. Em seguida, você pode escolher a qual aplicativo IoT Central se conectar.

## <a name="create-a-device-in-a-workflow"></a>Criar um dispositivo em um fluxo de trabalho

Esta seção mostra como criar um novo dispositivo no IoT Central por push de um botão em um dispositivo móvel usando o aplicativo Microsoft Flow Mobile. Você pode usar essa ação no flow para integrar sistemas ERP como o Dynamics com IoT Central criando um novo dispositivo quando um dispositivo é adicionado em outro lugar.

1. Comece criando um fluxo de trabalho em branco no Microsoft Flow.

1. Procure o **botão de fluxo para dispositivos móveis** como um gatilho.

1. Nesse gatilho, adicione uma entrada de texto chamada **nome do dispositivo**. Altere o texto de descrição para **Inserir o nome do dispositivo do novo dispositivo**.

1. Adicione uma nova ação. Pesquise a ação **IOT central do Azure – criar um dispositivo** .

1. Escolha seu aplicativo e escolha um modelo de dispositivo no qual criar um dispositivo nos menus suspensos. Você verá a ação expandir para mostrar todas as propriedades e configurações do dispositivo.

1. Selecione o campo nome do dispositivo. No painel de conteúdo dinâmico, escolha **nome do dispositivo**. Esse valor é passado da entrada que o usuário insere por meio do aplicativo móvel e é o nome do novo dispositivo no IoT Central. Neste exemplo, o único campo obrigatório é o nome do dispositivo, indicado pelo asterisco vermelho. Outro modelo de dispositivo pode ter vários campos obrigatórios que precisam ser preenchidos para criar um novo dispositivo.

    ![Painel dinâmico de ação do dispositivo criar fluxo](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. Adicional Preencha outros campos como desejar para a criação de novos dispositivos.

1. Por fim, salve seu fluxo de trabalho.

1. Teste o fluxo de trabalho no aplicativo móvel Microsoft Flow. Vá para a guia **botões** no aplicativo. Você deve ver seu botão – > criar um novo fluxo de trabalho de dispositivo. Insira o nome do novo dispositivo e observe que ele aparece no IoT Central!

    ![Captura de tela criar dispositivo móvel do Flow](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Atualizar um dispositivo em um fluxo de trabalho

Esta seção mostra como atualizar as configurações e as propriedades do dispositivo no IoT Central por push de um botão em um dispositivo móvel usando o aplicativo Microsoft Flow Mobile.

1. Comece criando um fluxo de trabalho em branco no Microsoft Flow.

1. Procure o **botão de fluxo para dispositivos móveis** como um gatilho.

1. Nesse gatilho, adicione uma entrada como uma entrada de texto de **local** que corresponda a uma configuração ou propriedade que você deseja alterar. Altere o texto de descrição.

1. Adicione uma nova ação. Pesquise o **IOT central do Azure-atualizar uma ação do dispositivo** .

1. Escolha seu aplicativo na lista suspensa. Agora você precisará de uma ID do dispositivo existente que deseja atualizar. 

    > [!NOTE] 
    > **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.

    ![ID de IoT Central da URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Você pode atualizar o nome do dispositivo. Para atualizar as propriedades e configurações do dispositivo, você deve selecionar o modelo de dispositivo do dispositivo que deseja atualizar na lista suspensa modelo de **dispositivo** . O bloco ação se expande para mostrar todas as propriedades e configurações que você pode atualizar.

    ![Fluxo de trabalho do dispositivo de atualização de fluxo](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Selecione cada uma das propriedades e configurações que você deseja atualizar. No painel de conteúdo dinâmico, escolha a entrada correspondente do gatilho. Neste exemplo, o valor de local é propagado para baixo para atualizar a propriedade de local do dispositivo.

1. Por fim, salve seu fluxo de trabalho.

1. Teste o fluxo de trabalho no aplicativo móvel Microsoft Flow. Vá para a guia **botões** no aplicativo. Você deve ver o botão > atualizar um fluxo de trabalho de dispositivo. Insira as entradas e veja o dispositivo ser atualizado no IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Obter informações do dispositivo em um fluxo de trabalho

Você pode obter informações de dispositivo por sua ID usando a **IOT central do Azure-obter uma** ação de dispositivo. 
> [!NOTE] 
> **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.

Você pode obter informações como nome do dispositivo, nome do modelo de dispositivo, valores de propriedade e valores de configurações para passar para ações posteriores no fluxo de trabalho. Veja um exemplo de fluxo de trabalho que passa o valor da propriedade Customer Name de um dispositivo para o Microsoft Teams.

   ![Fluxo de trabalho do dispositivo obter fluxo](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Executar um comando em um dispositivo em um fluxo de trabalho
Você pode executar um comando em um dispositivo especificado por sua ID usando o **IOT central do Azure-executar uma** ação de comando. 

> [!NOTE] 
> **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.
    
Você pode escolher o comando a ser executado e passar os parâmetros do comando por meio desta ação. Veja um exemplo de fluxo de trabalho que executa um comando de reinicialização de dispositivo de um botão no aplicativo Microsoft Flow móvel.

   ![Fluxo de trabalho do dispositivo obter fluxo](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Excluir um dispositivo em um fluxo de trabalho

Você pode excluir um dispositivo por sua ID usando a ação **IOT central do Azure-excluir um dispositivo** . 
> [!NOTE] 
> **Você deve usar a ID encontrada na URL** na página de detalhes do dispositivo do dispositivo que você deseja atualizar. A ID do dispositivo encontrada na lista de dispositivos do Gerenciador de dispositivos não é a correta a ser usada no Microsoft Flow.

Aqui está um exemplo de fluxo de trabalho que exclui um dispositivo no envio de um botão no aplicativo móvel Microsoft Flow.

   ![Fluxo de trabalho do dispositivo de exclusão de fluxo](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Resolução de problemas

Se você estiver tendo problemas para criar uma conexão com o conector de IoT Central do Azure, aqui estão algumas dicas para ajudá-lo.

1. As contas pessoais da Microsoft ( @hotmail.comcomo @live.com, @outlook.com por exemplo, domínios) não têm suporte no momento. Você deve usar uma conta corporativa ou de estudante do Azure Active Directory (AD).

2. Para usar o conector de IoT Central no Microsoft Flow, você deve ter entrado no aplicativo IoT Central pelo menos uma vez. Caso contrário, o aplicativo não aparecerá nos menus suspensos do aplicativo.

3. Se você estiver recebendo um erro ao usar uma conta do Azure AD, tente abrir o Windows PowerShell e execute o commandlets a seguir como administrador.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a usar Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerenciar dispositivos](howto-manage-devices.md).

