---
title: Instalação de tempo de execução de funções Azure
description: Como instalar a pré-visualização do tempo de execução das funções Azure 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 73b9533fa00c783e1cfb85270198f5c00c66afd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86540388"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instale a pré-visualização do tempo de execução das funções Azure 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Se pretender instalar a pré-visualização do tempo de execução Azure Functions 2, siga estes passos:

1. Certifique-se de que a sua máquina passa os requisitos mínimos.
1. Descarregue o [Instalador de Pré-visualização de tempo de execução Azure Functions](https://aka.ms/azafrv2).
1. Desinstalar a pré-visualização do tempo de execução das funções Azure 1.
1. Instale a pré-visualização do tempo de execução Azure Functions 2.
1. Preencha a configuração da pré-visualização do tempo de execução Azure Functions 2.
1. Crie a sua primeira função na pré-visualização do tempo de execução de funções Azure Functions

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar a pré-visualização do tempo de execução Azure Functions, tem de ter os seguintes recursos disponíveis:

1. Uma máquina com funcionamento do Microsoft Windows Server 2016 ou a Atualização de Criadores do Microsoft Windows 10 (Edição Profissional ou Empresarial).
1. Uma instância SQL Server a decorrer dentro da sua rede.  A edição mínima necessária é SQL Server Express.

## <a name="uninstall-previous-version"></a>Desinstalar versão anterior

Se já instalou previamente a pré-visualização do tempo de execução do Azure Functions, tem de desinstalar antes de instalar o último lançamento.  Desinstale a pré-visualização do tempo de execução das funções Azure removendo o programa em Programas de Adicionar/Remover no Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instale a pré-visualização do tempo de execução das funções Azure

O Instalador de Pré-visualização de tempo de execução Azure Funções orienta-o através da instalação da gestão de pré-visualização do tempo de execução de funções Azure Functions e funções de trabalhador.  É possível instalar o papel de Gestão e Trabalhador na mesma máquina.  No entanto, à medida que adiciona mais aplicações de função, tem de implementar mais funções de trabalhador em máquinas adicionais para poder escalar as suas funções em vários trabalhadores.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instale a Função de Gestão e Trabalhador na mesma máquina

1. Executar o Instalador de Pré-visualização de tempo de execução de funções Azure.

    ![Azure Functions Instalador de pré-visualização de tempo de execução][1]

1. Clique em **Seguinte**.
1. Depois de ler os termos do **EULA,** **consulte a caixa** para aceitar os termos e clique em **Seguir** para avançar.
1. Selecione as funções que pretende instalar nesta máquina **Funções Funções Função e/ou** Funções Funções Função Trabalho **e/ou** **Funções Funções Função Trabalhador** Role e/ou

    ![Azure Functions Instalador de pré-visualização de tempo de execução - seleção de funções][3]

    > [!NOTE]
    > Pode instalar o Papel de Trabalhador de Funções em **muitas** outras máquinas. Para tal, siga estas instruções e selecione **apenas funções Funções Funções Papel de Trabalhador** no instalador.

1. Clique **em seguida** para que o Assistente de **Configuração de Tempo de execução de funções Azure** inicie o processo de instalação na sua máquina.
1. Uma vez concluído, o assistente de configuração lança a ferramenta de configuração **de tempo de execução Azure Functions.**

    ![Instalação de pré-visualização de tempo de execução Azure Functions concluída][6]

    > [!NOTE]
    > Se estiver a instalar no **Windows 10** e a função **Contentor** não tiver sido previamente ativada, a **Configuração do tempo de execução de funções Azure** solicita-lhe que reinicie a sua máquina para concluir a instalação.

## <a name="configure-the-azure-functions-runtime"></a>Configure o tempo de execução das funções Azure

Para completar a instalação do tempo de execução Azure Functions, tem de completar a configuração.

1. A ferramenta de configuração **de tempo de execução Azure Functions** mostra quais as funções instaladas na sua máquina.

    ![Ferramenta de pré-visualização de tempo de execução de funções Azure Functions][7]

1. Clique no **separador Base de Dados,** introduza os dados de ligação para a sua instância SQL Server, incluindo especificar uma [chave master database](/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), e clique em **Aplicar**.  A conectividade com uma instância do SQL Server é necessária para que o tempo de execução das funções Azure crie uma base de dados para suportar o tempo de execução.

    ![Configuração da base de dados de pré-visualização do tempo de execução Azure Functions][8]

1. Clique no **separador Credenciais.**  Aqui, deve criar duas novas credenciais para uso com uma partilha de ficheiros para hospedar todas as suas aplicações de função.  Especifique o **nome de utilizador** e as combinações de **palavras-passe** para o **proprietário da partilha de ficheiros** e para o **utilizador da partilha de ficheiros,** em seguida, clique em **Aplicar**.

    ![Credenciais de pré-visualização do tempo de execução de funções Azure Functions][9]

1. Clique no separador **'Partilhar** ficheiros'.  Aqui deve especificar os detalhes da localização da partilha de ficheiros.  A partilha de ficheiros pode ser criada para si ou pode utilizar uma Partilha de Ficheiros existente e clicar em **Aplicar.**  Se selecionar uma nova localização de Partilha de Ficheiros, deve especificar um diretório para utilização pelo tempo de execução das funções Azure.

    ![Partilha de ficheiros de pré-visualização de tempo de execução Azure Functions][10]

1. Clique no separador **IIS.**  Este separador mostra os detalhes dos websites no IIS que a ferramenta de configuração de tempo de execução Azure Functions cria.  Pode especificar um nome DNS personalizado aqui para o portal de pré-visualização do tempo de execução de funções Azure.  Clique **em Aplicar** para completar.

    ![Azure Functions Runtime preview IIS][11]

1. Clique no separador **Serviços.**  Este separador mostra o estado dos serviços na sua ferramenta de configuração de tempo de execução Azure Functions.  Se o  **Serviço de Ativação do Anfitrião de Funções Azure** não estiver a funcionar após a configuração inicial, clique em **Iniciar Serviço**.

    ![Configuração de pré-visualização de tempo de execução de funções Azure Functions completa][12]

1. Navegue pelo Portal de **Tempo de Execução de Funções Azure** como `https://<machinename>.<domain>/` .

    ![Portal de pré-visualização do tempo de execução de funções Azure Functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Crie a sua primeira função na pré-visualização do tempo de execução de funções Azure Functions

Para criar a sua primeira função na pré-visualização do tempo de execução de funções Azure Functions

1. Navegue pelo Portal de **Tempo de Execução de Funções Azure** como `https://<machinename>.<domain>` por exemplo `https://mycomputer.mydomain.com` .

1. É-lhe pedido que **faça login**, se implementado num domínio utilize o nome de utilizador e a palavra-passe da sua conta de domínio, de outra forma utilize o nome de utilizador e a palavra-passe da conta local para iniciar sessão no portal.

    ![Início do portal de pré-visualização do prazo de execução de funções Azure Functions][14]

1. Para criar aplicações de função, tem de criar uma Subscrição.  No canto superior esquerdo do portal, clique na **+** opção ao lado das subscrições.

    ![Azure Functions Subscrições do portal de pré-visualização do tempo de execução][15]

1. Escolha **o Plano Padrão,** introduza um nome para a sua Subscrição e clique em **Criar**.

    ![Plano de subscrição do portal de pré-visualização do portal de pré-visualização de funções Azure Functions][16]

1. Todas as suas aplicações de função estão listadas no painel esquerdo do portal.  Para criar uma nova App de função, selecione o título **'Funções Apps'** e clique na **+** opção.

1. Introduza um nome para a sua aplicação de função, selecione a Subscrição correta, escolha qual a versão do tempo de execução das Funções Azure que deseja programar e clique em **Criar**

    ![Azure Functions Runtime portal novo portal de pré-visualização nova aplicação][17]

1. A sua nova aplicação de funções está listada no painel esquerdo do portal.  Selecione Funções e, em seguida, clique em **Nova Função** na parte superior do painel central no portal.

    ![Modelos de pré-visualização do tempo de execução de funções Azure Functions][18]

1. Selecione a função Detonador temporizador, no nome de voo do lado direito a sua função e altere a Agenda para `*/5 * * * * *` (esta expressão cron faz com que a função do temporizador seja executada a cada cinco segundos) e clique em **Criar**

    ![Azure Functions Pré-visualização de nova configuração da função do temporizador][19]

1. A sua função foi agora criada.  Pode visualizar o registo de execução da sua aplicação Função expandindo o painel de **registo** na parte inferior do portal.

    ![Funções Azure Executar função de pré-visualização][20]

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
