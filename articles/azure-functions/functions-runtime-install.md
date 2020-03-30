---
title: Instalação de tempo de funcionamento das funções Azure
description: Como instalar as funções Azure Pré-visualização 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 7ad748aa9a5b45af10121648a668344548484cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226729"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instalar a pré-visualização do tempo de funcionamento das funções Azure 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Se quiser instalar a pré-visualização do Tempo de Funcionamento das Funções Azure 2, siga estes passos:

1. Certifique-se de que a sua máquina passa os requisitos mínimos.
1. Descarregue o instalador de [pré-visualização de funções Azure](https://aka.ms/azafrv2).
1. Desinstale a pré-visualização do tempo de funcionamento das funções Azure 1.
1. Instale a pré-visualização do tempo de funcionamento das funções Azure 2.
1. Complete a configuração das funções Azure Pré-visualização 2.
1. Crie a sua primeira função na pré-visualização do tempo de funcionamento das funções Azure

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar a pré-visualização do Tempo de Funcionamento das Funções Azure, deve dispor dos seguintes recursos disponíveis:

1. Uma máquina que executa o Microsoft Windows Server 2016 ou a Microsoft Windows 10 Creators Update (Professional ou Enterprise Edition).
1. Uma instância do Servidor SQL que funciona dentro da sua rede.  A edição mínima necessária é o SQL Server Express.

## <a name="uninstall-previous-version"></a>Desinstalar versão anterior

Se já instalou previamente a pré-visualização do Funcionamento das Funções Azure, tem de desinstalar antes de instalar a versão mais recente.  Desinstale a pré-visualização do tempo de funcionamento das funções Azure removendo o programa em Programas Add/Remove no Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instale a pré-visualização do tempo de funcionamento das funções Azure

O instalador de pré-visualização de funções Azure guia-o através da instalação das funções Azure Gedade de pré-visualização e funções de trabalhador.  É possível instalar o papel de Gestão e Trabalhador na mesma máquina.  No entanto, à medida que adiciona mais aplicações de função, deve implementar mais funções de trabalhador em máquinas adicionais para poder escalar as suas funções em vários trabalhadores.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instale a Função de Gestão e Trabalhador na mesma máquina

1. Executar o instalador de pré-visualização de funções Azure.

    ![Instalador de pré-visualização de funções Azure Runtime][1]

1. Clique em **Seguinte**.
1. Depois de ler os termos do **EULA,** **verifique a caixa** para aceitar os termos e clique em **Next** para avançar.
1. Selecione as funções que pretende instalar nesta função de gestão de **funções** da máquina e/ou **funções Funções Funções Função função do trabalhador** e clique em **Seguinte**.

    ![Instalador de pré-visualização funções Azure Funções - seleção de funções][3]

    > [!NOTE]
    > Pode instalar o **Funções Funções Funções Funções funções** em muitas outras máquinas. Para isso, siga estas instruções e selecione apenas **funções de trabalho** no instalador.

1. Clique em **seguida** para que o Assistente de Configuração de Funcionamento das **Funções Azure** inicie o processo de instalação na sua máquina.
1. Uma vez concluído, o assistente de configuração lança a ferramenta de configuração do Funcionamento do Funcionamento das **Funções Azure.**

    ![Instalador de pré-visualização de funções Azure Completo][6]

    > [!NOTE]
    > Se estiver a instalar no **Windows 10** e a funcionalidade **contentor não** tiver sido previamente ativada, a Configuração de tempo de funcionamento das **funções Azure** leva-o a reiniciar a sua máquina para concluir a instalação.

## <a name="configure-the-azure-functions-runtime"></a>Configure o tempo de funcionamento das funções azure

Para completar a instalação de funcionamento do funcionamento das funções Azure, deve completar a configuração.

1. A ferramenta de configuração do tempo de funcionação do **Azure** mostra quais as funções instaladas na sua máquina.

    ![Ferramenta de configuração de pré-visualização de funções Azure Runtime][7]

1. Clique no separador Base de **Dados,** introduza os dados de ligação para a sua instância do Servidor SQL, incluindo especificar uma chave principal da Base de [Dados,](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)e clique **em Aplicar**.  A conectividade com uma instância do Servidor SQL é necessária para que o Tempo de Funcionamento das Funções Azure crie uma base de dados para suportar o Tempo de Execução.

    ![Configuração da base de dados de pré-visualização de funções Azure][8]

1. Clique no separador **Credenciais.**  Aqui, você deve criar duas novas credenciais para uso com uma partilha de ficheiros para hospedar todas as suas aplicações de função.  Especifique as combinações de **nome de utilizador** e **palavra-passe** para o titular da partilha de **ficheiros** e para o utilizador da partilha de **ficheiros,** clique em **Aplicar**.

    ![Funções Azure Credenciais de pré-visualização do tempo de funcionamento][9]

1. Clique no separador Partilha de **Ficheiros.**  Aqui deve especificar os detalhes da localização da partilha de ficheiros.  A partilha de ficheiros pode ser criada para si ou pode utilizar uma Partilha de Ficheiros existente e clicar **em Aplicar**.  Se selecionar um novo local de Partilha de Ficheiros, deve especificar um diretório para utilização pelo Tempo de Funcionamento das Funções Azure.

    ![Funções Azure Executar partilha de ficheiros][10]

1. Clique no separador **IIS.**  Este separador mostra os detalhes dos websites no IIS que a ferramenta de configuração de funcionamento do Funcionamento das Funções Azure cria.  Pode especificar um nome DNS personalizado aqui para o portal de pré-visualização de funções Azure Runtime.  Clique **Em Aplicar** para completar.

    ![Funções Azure Pré-visualização IIS][11]

1. Clique no separador **Serviços.**  Este separador mostra o estado dos serviços na sua ferramenta de configuração de tempo de funcionamento das funções Azure.  Se o Serviço de **Ativação do Hospedeiro das Funções Azure** não estiver a funcionar após a configuração inicial, clique no **Serviço de Início**.

    ![Configuração de pré-visualização de prazo de funcionamento das funções Azure completa][12]

1. Navegue no Portal de Tempo de `https://<machinename>.<domain>/`Funcionamento das **Funções Azure** como .

    ![Portal de pré-visualização de funções Azure Runtime][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Crie a sua primeira função na pré-visualização do tempo de funcionamento das funções Azure

Para criar a sua primeira função na pré-visualização do tempo de funcionamento das funções Azure

1. Navegue no Portal de Tempo de `https://<machinename>.<domain>` Funcionamento das **Funções Azure** como, por exemplo. `https://mycomputer.mydomain.com`

1. É solicitado a **iniciar sessão**, se for implementado num domínio, utilize o nome de utilizador e a palavra-passe da sua conta de domínio, caso contrário utilize o nome de utilizador e a palavra-passe da sua conta local para iniciar sessão no portal.

    ![Início do portal de pré-visualização de funções Azure][14]

1. Para criar aplicações de função, tem de criar uma Subscrição.  No canto superior esquerdo do portal, **+** clique na opção ao lado das subscrições.

    ![Funções Azure Pré-visualização de assinaturas do portal][15]

1. Escolha **O Plano Padrão,** introduza um nome para a sua Subscrição e clique em **Criar**.

    ![Plano de subscrição do portal de pré-visualização do Portal de Funções Azure plano e nome][16]

1. Todas as suas aplicações de função estão listadas no painel esquerdo do portal.  Para criar uma nova App de Funções, **+** selecione as **Aplicações de Função** de título e clique na opção.

1. Introduza um nome para a sua aplicação de funções, selecione a Subscrição correta, escolha qual versão do tempo de execução das Funções Azure que deseja programar e clique em **Criar**

    ![Funções Azure Runtime pré-visualização novo aplicativo de função][17]

1. A sua nova aplicação de funções está listada no painel esquerdo do portal.  Selecione Funções e, em seguida, clique em **Nova Função** na parte superior do painel central no portal.

    ![Modelos de pré-visualização de funções azure Runtime][18]

1. Selecione a função Degatilho do Temporizador, no nome `*/5 * * * * *` de flyout à direita a sua função e altere o Horário para (esta expressão crona faz com que a função do temporizador execute a cada cinco segundos) e clique em **Criar**

    ![Funções Azure Pré-visualização de tempo de funcionamento novo configuração de função de temporizador][19]

1. A sua função foi agora criada.  Pode visualizar o registo de execução da sua aplicação Fun, expandindo o painel de **registo** na parte inferior do portal.

    ![Execução da função de pré-visualização de funções de pré-visualização de funções de tempo de funcionamento do funcionamento do tempo de][20]

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
