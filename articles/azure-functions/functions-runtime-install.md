---
title: Instalação de tempo de execução das funções do Azure | Documentos da Microsoft
description: Como instalar a pré-visualização do Runtime de funções do Azure 2
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: aae6bc41f3c2fc2c5f8cf63d07f6b4d79bb3564a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61023118"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instalar a pré-visualização do Runtime de funções do Azure 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Se gostaria de instalar o 2 da pré-visualização do Runtime de funções do Azure, siga estes passos:

1. Certifique-se de que sua máquina passa os requisitos mínimos.
1. Transfira o [instalador de pré-visualização do Runtime das funções do Azure](https://aka.ms/azafrv2).
1. Desinstale o 1 da pré-visualização do Runtime de funções do Azure.
1. Instale o 2 da pré-visualização do Runtime de funções do Azure.
1. Conclua a configuração do Runtime de funções do Azure preview 2.
1. Criar a primeira função em pré-visualização do Runtime de funções do Azure

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar a pré-visualização do Runtime de funções do Azure, tem de ter os seguintes recursos disponíveis:

1. Uma máquina com o Microsoft Windows Server 2016 ou o Microsoft Windows 10 Creators Update (Professional ou Enterprise Edition).
1. Uma instância de SQL Server em execução dentro da sua rede.  Edição mínima necessária é o SQL Server Express.

## <a name="uninstall-previous-version"></a>Desinstalar a versão anterior

Se tiver instalado anteriormente a pré-visualização do Runtime de funções do Azure, tem de desinstalar antes de instalar a versão mais recente.  Desinstale a pré-visualização do Runtime de funções do Azure ao remover o programa em Adicionar/remover programas no Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalar a visualização de tempo de execução de funções do Azure

O instalador de pré-visualização do tempo de execução de funções do Azure orienta-o ao longo da instalação do Runtime de funções do Azure preview gestão e funções de trabalho.  É possível instalar a função de gestão e de trabalho na mesma máquina.  No entanto, à medida que adiciona mais aplicações de função, tem de implementar mais funções de trabalho em máquinas adicionais para poder dimensionar as suas funções em várias funções de trabalho.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalar o gerenciamento e a função de trabalho na mesma máquina

1. Execute o instalador de pré-visualização do tempo de execução de funções do Azure.

    ![Instalador de pré-visualização do Runtime das funções do Azure][1]

1. Clique em **Seguinte**.
1. Assim que tiver lido os termos do **EULA**, **a caixa de verificação** para aceitar os termos e clique em **seguinte** para avançar.
1. Selecione as funções de que pretende instalar nesta máquina **função de gestão de funções** e/ou **função de trabalho de funções** e clique em **seguinte**.

    ![Instalador de pré-visualização de Runtime das funções do Azure - seleção da função][3]

    > [!NOTE]
    > Pode instalar o **função de trabalho de funções** em muitas outras máquinas. Para tal, siga estas instruções e selecionar apenas **função de trabalho de funções** no instalador.

1. Clique em **próxima** para ter a **Assistente de configuração de tempo de execução de funções do Azure** iniciar o processo de instalação no seu computador.
1. Depois de concluído, o Assistente de configuração é iniciada a **Runtime das funções de Azure** ferramenta de configuração.

    ![Conclusão do instalador de pré-visualização do Runtime das funções do Azure][6]

    > [!NOTE]
    > Se estiver a instalar num **Windows 10** e o **contentor** funcionalidade não foi ativada anteriormente, o **configuração de tempo de execução de funções do Azure** pede-lhe para reiniciar a máquina para concluir a instalação.

## <a name="configure-the-azure-functions-runtime"></a>Configurar o Runtime das funções do Azure

Para concluir a instalação do Runtime de funções do Azure, tem de concluir a configuração.

1. O **Runtime das funções de Azure** ferramenta de configuração mostra que funções são instaladas no seu computador.

    ![Ferramenta de configuração de pré-visualização de Runtime das funções do Azure][7]

1. Clique nas **base de dados** separador, introduza os detalhes de ligação para a sua instância do SQL Server, incluindo a especificação de um [chave mestra do banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)e clique em **aplicar**.  Conectividade a uma instância do SQL Server é necessária para o Runtime das funções do Azure criar uma base de dados para suportar o tempo de execução.

    ![Configuração de base de dados de pré-visualização de Runtime das funções do Azure][8]

1. Clique nas **credenciais** separador.  Aqui, tem de criar duas novas credenciais para utilização com uma partilha de ficheiros para o alojamento de todas as suas aplicações de função.  Especificar **nome de utilizador** e **palavra-passe** combinações para o **proprietário de partilha do ficheiro** e para o **utilizador da partilha de ficheiros**, em seguida, clique em **Aplicam-se**.

    ![Credenciais de pré-visualização do Runtime das funções do Azure][9]

1. Clique nas **partilha de ficheiros** separador.  Aqui tem de especificar os detalhes da localização de partilha de ficheiros.  A partilha de ficheiros pode ser criada por si ou pode utilizar uma partilha de ficheiros existentes e clique em **aplicar**.  Se selecionar uma nova localização de partilha de ficheiros, tem de especificar um diretório para utilização pelo tempo de execução de funções do Azure.

    ![Partilha de ficheiros de pré-visualização de Runtime das funções do Azure][10]

1. Clique nas **IIS** separador.  Este separador mostra os detalhes dos sites no IIS, que cria a ferramenta de configuração do Runtime de funções do Azure.  Pode especificar um nome DNS personalizado aqui para o portal de pré-visualização do Runtime de funções do Azure.  Clique em **aplicar** para concluir.

    ![Pré-visualização de Runtime das funções do Azure IIS][11]

1. Clique nas **serviços** separador.  Este separador mostra o estado dos serviços na sua ferramenta de configuração do Runtime de funções do Azure.  Se o **serviço de ativação de anfitrião de funções do Azure** é não está em execução após a configuração inicial, clique em **iniciar serviço**.

    ![Runtime das funções pré-visualização de configuração do Azure completa][12]

1. Navegue para o **Portal de tempo de execução das funções do Azure** como `https://<machinename>.<domain>/`.

    ![Portal de pré-visualização do Runtime das funções do Azure][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Criar a primeira função em pré-visualização do Runtime de funções do Azure

Para criar a sua primeira função em pré-visualização do Runtime de funções do Azure

1. Navegue para o **Portal de tempo de execução de funções do Azure** como `https://<machinename>.<domain>` por exemplo `https://mycomputer.mydomain.com`.

1. Deverá **iniciar sessão**, se implementada numa utilização de domínio a sua conta de domínio do nome de utilizador e palavra-passe, caso contrário, utilizam o seu nome de conta local e a palavra-passe para iniciar sessão no portal do.

    ![Logon de portal do Azure Runtime das funções pré-visualização][14]

1. Para criar aplicações de funções, tem de criar uma subscrição.  No canto superior esquerdo do portal, clique nas **+** opção ao lado de subscrições.

    ![Subscrições de portais do Azure Runtime das funções pré-visualização][15]

1. Escolher **DefaultPlan**, introduza um nome para a sua subscrição e clique em **criar**.

    ![O plano de subscrição de portal de pré-visualização de Runtime das funções do Azure e o nome][16]

1. Todas as suas aplicações de função estão listadas no painel esquerdo do portal.  Para criar uma nova aplicação de função, selecione o cabeçalho **aplicações Function App** e clique nas **+** opção.

1. Introduza um nome para a sua aplicação de função, selecione a subscrição correta, escolher a versão do runtime das funções do Azure que quer programar e clique em **Create**

    ![Azure Runtime das funções pré-visualização portal nova aplicação de funções][17]

1. A nova aplicação de funções consta no painel esquerdo do portal.  Selecione as funções e, em seguida, clique em **nova função** na parte superior do painel central no portal.

    ![Modelos de pré-visualização do Runtime das funções do Azure][18]

1. Selecione a função de Acionador de temporizador, no submenu da direita nomear sua função e alterar a agenda para `*/5 * * * * *` (esta expressão cron de menos faz com que sua função de temporizador executar a cada cinco segundos) e clique em **Create**

    ![Azure configuração de função de temporizador novo do Runtime das funções pré-visualização][19]

1. Sua função agora foi criada.  Pode ver o registo de execução da sua aplicação de função ao expandir a **log** painel na parte inferior do portal.

    ![Execução de função de pré-visualização de Runtime das funções do Azure][20]

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
