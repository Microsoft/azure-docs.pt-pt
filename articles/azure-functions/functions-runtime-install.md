---
title: Instalação do Azure Functions Runtime | Microsoft Docs
description: Como instalar a versão prévia do Azure Functions Runtime 2
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 0fbc76f3cb3fea84347ef55e40e1b2bed9b98152
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085503"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instalar a versão prévia do Azure Functions Runtime 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Se você quiser instalar o Azure Functions Runtime versão prévia 2, siga estas etapas:

1. Verifique se o computador passa pelos requisitos mínimos.
1. Baixe o [instalador do Azure Functions Runtime Preview](https://aka.ms/azafrv2).
1. Desinstale o Azure Functions Runtime Preview 1.
1. Instale a versão prévia do Azure Functions Runtime 2.
1. Conclua a configuração da versão prévia do Azure Functions Runtime 2.
1. Criar sua primeira função na visualização Azure Functions Runtime

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar a versão prévia do Azure Functions Runtime, você deve ter os seguintes recursos disponíveis:

1. Um computador executando o Microsoft Windows Server 2016 ou o Microsoft Windows 10 Creators Update (Professional ou Enterprise Edition).
1. Uma instância SQL Server em execução na sua rede.  A edição mínima necessária é SQL Server Express.

## <a name="uninstall-previous-version"></a>Desinstalar versão anterior

Se você já tiver instalado a versão prévia do Azure Functions Runtime, deverá desinstalar o antes de instalar a liberação mais recente.  Desinstale o Azure Functions Runtime Preview removendo o programa em Adicionar ou remover programas no Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalar a versão prévia do Azure Functions Runtime

O instalador de visualização do Azure Functions Runtime orienta você pela instalação das funções de trabalho e gerenciamento de Azure Functions Runtime visualização.  É possível instalar a função de gerenciamento e de trabalho no mesmo computador.  No entanto, à medida que você adiciona mais aplicativos de funções, você deve implantar mais funções de trabalho em computadores adicionais para poder dimensionar suas funções em vários trabalhos.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalar a função de gerenciamento e de trabalho no mesmo computador

1. Execute o instalador do Azure Functions Runtime Preview.

    ![Instalador do Azure Functions Runtime Preview][1]

1. Clique em **Seguinte**.
1. Depois de ler os termos do **EULA**, **marque a caixa** para aceitar os termos e clique em **Avançar** para avançar.
1. Selecione as funções que você deseja instalar nesta função de **Gerenciamento de funções** de computador e/ou **função de trabalho** do Functions e clique em **Avançar**.

    ![Instalador de Azure Functions Runtime Preview – seleção de função][3]

    > [!NOTE]
    > Você pode instalar a **função de trabalho** do Functions em muitas outras máquinas. Para fazer isso, siga estas instruções e selecione apenas **função de trabalho** do Functions no instalador.

1. Clique em **Avançar** para que o **Assistente de instalação do Azure Functions Runtime** inicie o processo de instalação em seu computador.
1. Depois de concluído, o assistente de instalação inicia a ferramenta de configuração de **Azure Functions Runtime** .

    ![Instalador do Azure Functions Runtime Preview concluído][6]

    > [!NOTE]
    > Se você estiver instalando o no **Windows 10** e o recurso de **contêiner** não tiver sido habilitado anteriormente, a **instalação do Azure Functions Runtime** solicitará que você reinicialize o computador para concluir a instalação.

## <a name="configure-the-azure-functions-runtime"></a>Configurar o Azure Functions Runtime

Para concluir a instalação do Azure Functions Runtime, você deve concluir a configuração.

1. A ferramenta de configuração de **Azure Functions Runtime** mostra quais funções estão instaladas em seu computador.

    ![Ferramenta de configuração de Azure Functions Runtime visualização][7]

1. Clique na guia **banco de dados** , insira os detalhes de conexão para sua instância de SQL Server, incluindo a especificação de uma [chave mestra de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)e clique em **aplicar**.  A conectividade com uma instância de SQL Server é necessária para que o Azure Functions Runtime crie um banco de dados para dar suporte ao tempo de execução.

    ![Azure Functions Runtime Visualizar a configuração do banco de dados][8]

1. Clique na guia **credenciais** .  Aqui, você deve criar duas novas credenciais para uso com um compartilhamento de arquivos para hospedar todos os seus aplicativos de funções.  Especifique as combinações de **nome de usuário** e **senha** para o proprietário do **compartilhamento de arquivos** e para o usuário de **compartilhamento de arquivos**e clique em **aplicar**.

    ![Azure Functions Runtime as credenciais de visualização][9]

1. Clique na guia **compartilhamento de arquivos** .  Aqui você deve especificar os detalhes do local do compartilhamento de arquivos.  O compartilhamento de arquivos pode ser criado para você ou você pode usar um compartilhamento de arquivos existente e clicar em **aplicar**.  Se você selecionar um novo local de compartilhamento de arquivos, deverá especificar um diretório para uso pelo Azure Functions Runtime.

    ![Azure Functions Runtime Visualizar o compartilhamento de arquivos][10]

1. Clique na guia **IIS** .  Essa guia mostra os detalhes dos sites no IIS que a ferramenta de configuração do Azure Functions Runtime cria.  Você pode especificar um nome DNS personalizado aqui para o Azure Functions Runtime portal de visualização.  Clique em **aplicar** para concluir.

    ![Azure Functions Runtime Visualizar IIS][11]

1. Clique na guia **Serviços** .  Essa guia mostra o status dos serviços em sua ferramenta de configuração de Azure Functions Runtime.  Se o **serviço de ativação do Host Azure Functions** não estiver em execução após a configuração inicial, clique em **Iniciar serviço**.

    ![Configuração de visualização do Azure Functions Runtime concluída][12]

1. Navegue até o **portal** de Azure Functions Runtime `https://<machinename>.<domain>/`como.

    ![Azure Functions Runtime o portal de visualização][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Criar sua primeira função na visualização Azure Functions Runtime

Para criar sua primeira função na visualização Azure Functions Runtime

1. Navegue até o **portal** de Azure Functions Runtime `https://<machinename>.<domain>` como por `https://mycomputer.mydomain.com`exemplo.

1. Você será solicitado a **fazer logon**, se implantado em um domínio, usar o nome de usuário e a senha da conta de domínio, caso contrário, use o nome de usuário e a senha da conta local para fazer logon no Portal.

    ![Azure Functions Runtime o logon no portal de visualização][14]

1. Para criar aplicativos de funções, você deve criar uma assinatura.  No canto superior esquerdo do portal, clique na **+** opção ao lado das assinaturas.

    ![Azure Functions Runtime Visualizar assinaturas do portal][15]

1. Escolha **defaultplan**, insira um nome para sua assinatura e clique em **criar**.

    ![Azure Functions Runtime plano e nome da assinatura do portal de visualização][16]

1. Todos os seus aplicativos de funções estão listados no painel esquerdo do Portal.  Para criar um novo aplicativo de funções, selecione a função de cabeçalho **aplicativos** e clique **+** na opção.

1. Insira um nome para seu aplicativo de funções, selecione a assinatura correta, escolha qual versão do Azure Functions Runtime você deseja programar e clique em **criar**

    ![Portal de visualização do Azure Functions Runtime novo aplicativo de funções][17]

1. Seu novo aplicativo de funções está listado no painel esquerdo do Portal.  Selecione funções e, em seguida, clique em **nova função** na parte superior do painel central no Portal.

    ![Azure Functions Runtime modelos de visualização][18]

1. Selecione a função gatilho de temporizador, no submenu à direita, nomeie sua função e altere o `*/5 * * * * *` agendamento para (essa expressão cron faz com que a função de temporizador seja executada a cada cinco segundos) e clique em **criar**

    ![Azure Functions Runtime Visualizar nova configuração de função de temporizador][19]

1. Sua função foi criada agora.  Você pode exibir o log de execução do seu aplicativo de funções expandindo o painel **log** na parte inferior do Portal.

    ![Azure Functions Runtime a execução da função de visualização][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
