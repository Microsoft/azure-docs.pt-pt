---
title: Configurar a Micro foco CICS BankDemo para Micro foco Enterprise Developer 4.0 no Azure | Documentos da Microsoft
description: Execute a aplicação de Micro foco BankDemo no Azure para aprender a utilizar o Micro Focus Enterprise Server e o Enterprise Developer.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 733649c2b7f27ec5e80cb77adba708a158fb2c22
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099472"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-in-azure"></a>Configurar a Micro foco CICS BankDemo para Micro foco Enterprise Developer 4.0 no Azure

Quando configurou Micro foco Enterprise Server 4.0 e 4.0 do programador empresarial no Azure, pode testar implementações das cargas de trabalho do IBM z/OS. Este artigo mostra como configurar a CICS BankDemo, um aplicativo de exemplo que acompanha o desenvolvedor empresariais.

CICs significa para o sistema de controlo de informações do cliente, a plataforma de transações usada por muitas pessoas os aplicativos de online mainframe. O aplicativo BankDemo é ótimo para aprender como funcionam o Enterprise Server e o Enterprise Developer e como gerenciar e implantar um aplicativo real, completo com terminais de tela verde.

## <a name="prerequisites"></a>Pré-requisitos

- Uma VM com [Enterprise Developer](set-up-micro-focus-on-azure.md). Tenha em atenção que o desenvolvedor de Enterprise tem uma instância completa do Enterprise Server no mesmo para efeitos de desenvolvimento e teste. Esta é a instância do servidor de Enterprise utilizado para a demonstração.

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Transfira e instale-a na VM de programador empresarial. Enterprise Server exige uma base de dados para o gerenciamento de regiões do CICS, e o aplicativo BankDemo também utiliza uma base de dados do SQL Server chamado BANKDEMO. Esta demonstração supõe que estiver a utilizar o SQL Server Express para ambas as bases de dados. Ao instalar, selecione a instalação básica.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS é utilizado para gerir as bases de dados e executar um script T-SQL. Transfira e instale-a na VM de programador empresarial.

- [Visual Studio 2017](https://azure.microsoft.com/downloads/) com o service pack mais recente ou [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), que pode ser baixado gratuitamente.

- Rumba a área de trabalho ou outro emulador 3270 direcionado.

## <a name="configure-the-windows-environment"></a>Configurar o ambiente do Windows

Depois de instalar Enterprise Developer 4.0 na VM, tem de configurar a instância do servidor de Enterprise que vem com ele. Para fazer isso, precisa instalar alguns recursos adicionais do Windows da seguinte forma.

1. Utilize o RDP para iniciar sessão no Enterprise Server 4.0 VM que criou.

2. Clique a **pesquisa** ícone junto aos **iniciar** botão e o tipo **funcionalidades do Windows**. O Gestor de servidor adicionar funções e funcionalidades assistente é aberto.

3. Selecione **função de servidor Web (IIS)** e, em seguida, verifique o seguinte:

    - Ferramentas de gestão Web
    - Compatibilidade de gestão do IIS 6 (selecione todas as funcionalidades disponíveis)
    - Consola de gestão do IIS
    - Ferramentas e Scripts de gestão do IIS
    - Serviço de gestão do IIS

4. Selecione **serviços da World Wide Web**e verifique o seguinte:

     Recursos de desenvolvimento do aplicativo:
    - Extensibilidade .NET
    - ASP.NET
    - Funcionalidades HTTP comuns: Adicionar todas as funcionalidades disponíveis
    - Estado de funcionamento e diagnóstico: Adicionar todas as funcionalidades disponíveis
    - Segurança:
        - Autenticação Básica
        - Autenticação do Windows

5. Selecione **serviço de ativação de processos do Windows** e todos os respetivos subordinados.

6. Para **funcionalidades**, verifique **Microsoft .NET framework 3.5.1**e verifique o seguinte:

    - Windows Communication Foundation HTTP Activation
    - Ativação não HTTP do Windows Communication Foundation

7. Para **funcionalidades**, verifique **Microsoft .NET framework 4.6**e verifique o seguinte:

   - Ativação de Pipe nomeado
   - Ativação de TCP
   - Partilha de portas TCP

     ![Assistente para adicionar funções e funcionalidades: Serviços de função](media/01-demo-roles.png)

8. Depois de ter adicionado todas as opções, clique em **seguinte** para instalar.

9. Depois das funcionalidades do Windows, aceda a **painel de controlo \> sistema e segurança \> ferramentas administrativas**e selecione **serviços**. Desloque para baixo e certificar-se de que os seguintes serviços estão em execução e definido como **automática**:

    - **NetTcpPortSharing**
    - **Adaptador de escuta NET. pipe**
    - **Adaptador de escuta NET. TCP**

10. Para configurar o suporte IIS e WAS, no menu localize **linha de comandos do Programador de Enterprise de foco de Micro (64 bits)** e execute como **administrador**.

11. Tipo **wassetup – i** e prima **Enter**.

12. Depois do script é executado, pode fechar a janela.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configurar a conta sistema local para o SQL Server

Alguns processos de Enterprise Server tem de conseguir iniciar sessão no servidor de SQL e criar bases de dados e outros objetos. Estes processos utilizam a conta de sistema local, para que a autoridade do administrador do sistema tem de dar a essa conta.

1. Iniciar o **SSMS** e clique em **Connect** para ligar ao servidor SQLEXPRESS local através da autenticação do Windows. Deve estar disponível na **nome do servidor** lista.

2. No lado esquerdo, expanda o **Security** pasta e selecione **inícios de sessão**.

3. Selecione **NT AUTHORITY\\SYSTEM** e selecione **propriedades**.

4. Selecione **funções de servidor** e verifique **sysadmin**.

     ![Janela do Explorador de objetos do SSMS: Propriedades do Início de Sessão](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Criar a base de dados BankDemo e todos os seus objetos

1. Open **Windows Explorer** e navegue para **c:\\utilizadores\\pública\\documentos\\Micro Focus\\Enterprise Developer\\ Exemplos\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL**.

2. Copie o conteúdo do **BankDemoCreateAll.SQL** ficheiro na sua área de transferência.

3. Open **SSMS**. À direita, clique em **servidor** e selecione **nova consulta**.

4. Cole o conteúdo da área de transferência para o **nova consulta** caixa.

5. Executar o SQL clicando **Execute** partir a **comando** separador a consulta acima.

A consulta deve ser executado sem erros. Quando estiver terminado, terá a base de dados de exemplo para a aplicação de BankDemo.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Certifique-se de que as tabelas de base de dados e objetos foram criados

1. Com o botão direito a **BANKDEMO** da base de dados e selecione **atualizar**.

2. Expanda a **base de dados** e selecione **tabelas**. Deverá ver algo semelhante ao seguinte.

     ![Tabela BANKDEMO expandida no Object Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Criar a aplicação no Enterprise Developer

1. Abra o Visual Studio e inicie sessão.

2. Sob o **arquivo** opção de menu, selecione **abrir projeto/solução**, navegue para **c:\\utilizadores\\pública\\documentos\\Micro Foco\\Enterprise Developer\\amostras\\Mainframe\\CICS\\DotNet\\BankDemo**e selecione o **sln**ficheiro.

3. Demora algum tempo para examinar os objetos. Programas de COBOL são apresentados no Explorador de soluções com a extensão CBL juntamente com CopyBooks (CPY) e JCL.

4. Com o botão direito a **BankDemo2** projeto e selecione **Set as Startup Project**.

    > [!NOTE]
    > O projeto de BankDemo faz uso de HCOSS (opção de compatibilidade de anfitrião para o SQL Server), que não é utilizada para esta demonstração.

5. Na **Explorador de soluções**, clique com botão direito a **BankDemo2** projeto e selecione **criar**.

    > [!NOTE]
    > Criação no nível da solução resulta em erros, que não tenha sido configurada HCOSS.

6. Quando o projeto é criado, examine o **saída** janela. Ele deve ter um aspeto semelhante à imagem abaixo.

     ![Janela de saída que mostra a compilação bem-sucedida](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Implementar a aplicação de BankDemo no banco de dados de região

1. Abra uma linha de comandos do Programador de Enterprise (64 bits) como administrador.

2. Navegue para o **% pública %\\documentos\\Micro Focus\\Enterprise Developer\\exemplos\\Mainframe\\CICS\\DotNet\\ BankDemo**.

3. No prompt de comando, execute **bankdemodbdeploy** e inclua o parâmetro para a base de dados implementar, por exemplo:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Certifique-se de usar uma barra (/), não uma barra invertida (\\). Este script é executado durante algum tempo.

![Administração: Janela de linha de comandos do Programador de Enterprise](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Criar a região de BankDemo no administrador de empresa para .NET

1. Abra o **Enterprise Server para a administração de .NET** interface do Usuário.

2. Para iniciar o snap-in MMC a partir do Windows **começar** menu, escolha **Micro foco Enterprise Developer \> configuração \> servidor empresarial para administração de .NET**. (Para o Windows Server, escolha **Micro foco Enterprise Developer \> Enterprise Server para o administrador de .NET**).

3. Expanda a **regiões** contentor no painel esquerdo e, em seguida, botão direito do mouse **CICS**.

4. Selecione **definir região** para criar uma nova região de CICS chamado **BANKDEMO**e hospedada no banco de dados (local).

5. Fornecer a instância de servidor de base de dados, clique em **próxima**e, em seguida, introduza o nome da região **BANKDEMO**.

     ![Definir a caixa de diálogo de região](media/07-demo-cics.png)

6. Para selecionar o ficheiro de definição de região para a base de dados entre regiões, localize **região\_bankdemo\_db.config** na **c:\\utilizadores\\pública\\ Documentos\\Micro Focus\\Enterprise Developer\\exemplos\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Defina região - nome da região: BANKDEMO](media/08-demo-cics.png)

7. Clique em **Concluir**.

## <a name="create-xa-resource-definitions"></a>Criar definições de recursos XA

1. No painel esquerdo dos **Enterprise Server para a administração de .NET** interface do Usuário, expanda **System**e, em seguida **definições de recursos XA**. Esta definição define como a região interopera com Enterprise Server e as bases de dados do aplicativo.

2. Com o botão direito no **definições de recursos XA** e selecione **adicionar instância de servidor**.

3. Na caixa de lista pendente, selecione **instância de serviço de base de dados**. É o SQLEXPRESS de computador local.

4. Selecione a instância no âmbito da **definições de recursos XA (machinename\\sqlexpress)** contentor e clique em **Add**.

5. Selecione **definição de recurso do banco de dados XA** e, em seguida, escreva **BANKDEMO** para o **nome** e **região**.

     ![Novo ecrã de definição de recurso XA de base de dados](media/09-demo-xa.png)

6. Clique nas reticências (**...** ) para abrir o Assistente de cadeia de ligação. Para **nome do servidor**, tipo **(local)\\SQLEXPRESS**. Para **início de sessão**, selecione **autenticação do Windows**. Para o nome de base de dados, escreva **BANKDEMO**

     ![Ecrã de cadeia de ligação de edição](media/10-demo-string.png)

7. Teste a ligação.

## <a name="start-the-bankdemo-region"></a>Iniciar a região BANKDEMO

> [!NOTE]
> A primeira etapa é importante: Tem de definir a região a utilizar a definição do recurso XA que acabou de criar.

1. Navegue para o **BANDEMO CICS região** sob a **regiões contentor**e, em seguida, selecione **Editar ficheiro de arranque de região** do **ações** painel. Desloque para baixo para as propriedades SQL e introduza **bankdemo** para o **nome do recurso XA** , ou utilize o botão de reticências para selecioná-lo.

2. Clique nas **guardar** ícone para guardar as alterações.

3. Com o botão direito **BANKDEMO CICS região** no **consola** painel e selecione **região iniciar/parar**.

4. Na parte inferior a **região iniciar/parar** caixa que aparece no painel do meio, selecione **iniciar**. Após alguns segundos, a região é iniciado.

     ![Caixa de SQL iniciar/parar](/media/11-demo-sql.png)

     ![Região do CICS BANKDEMO - ecrã de introdução](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Criar um serviço de escuta

Tem de criar um serviço de escuta de sessões de TN3270 que aceder à aplicação BankDemo.

1. No painel esquerdo, expanda **editores de configuração** e selecione **serviço de escuta**.

2. Clique nas **abrir o ficheiro** ícone e selecione o **seelistener.exe.config** ficheiro. Este ficheiro vai ser editado e é carregado, sempre que inicia do Enterprise Server.

3. Tenha em atenção que as duas regiões definido anteriormente (ESDEMO e JCLDEMO).

4. Para criar uma nova região para BANKDEMO, com o botão direito **regiões**e selecione **região adicionar**.

5. Selecione **BANKDEMO região**.

6. Adicionar um canal de TN3270 clicando **região BANKDEMO** e selecionando **canal adicionar**.

7. Para **Name**, introduza **TN3270**. Para **porta**, introduza **9024**. (Observe que a aplicação de ESDEMO utiliza a porta 9230 por isso terá de utilizar uma porta diferente.)

8. Para guardar o ficheiro, clique nas **salvar** ícone ou escolha **ficheiro** \> **guardar**.

9. Para iniciar o serviço de escuta, clique nas **iniciar o serviço de escuta** ícone ou escolha **opções** \> **iniciar o serviço de escuta**.

     ![Janelas do Editor de configuração do serviço de escuta](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configurar Rumba para aceder à aplicação BankDemo

O último ponto que precisa fazer é configurar uma sessão de 3270 usando Rumba, um emulador 3270. Este passo permite-lhe aceder à aplicação de BankDemo através do serviço de escuta que acabou de criar.

1. Partir do Windows **iniciar** menu, iniciar Rumba Desktop.

2. Sob o **conexões** item de menu, selecione **TN3270**.

3. Clique em **inserir** e escreva **127.0.0.1** para o endereço IP e **9024** para a porta definido pelo utilizador.

4. Na parte inferior da caixa de diálogo, clique em **Connect**. É apresentado um ecrã preto do CICS.

5. Tipo **banco** para exibir a tela de 3270 inicial para a aplicação de BankDemo.

6. Para o ID de utilizador, escreva **B0001** e a palavra-passe, escreva qualquer coisa. A primeira tela BANK20 abre.

![Ecrã de boas-vindas exibição de mainframe](media/14-demo.png)
![ecrã de demonstração do subsistema de apresentação de Mainframe - Rumba -](media/15-demo.png)

Parabéns! Está agora a executar um aplicativo do CICS no Azure utilizando Micro Focus Enterprise Server.

## <a name="learn-more"></a>Saiba mais

- [Migração de mainframe - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Resolução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando do mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
