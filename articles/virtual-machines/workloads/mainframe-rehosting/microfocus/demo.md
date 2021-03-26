---
title: Configurar Micro Focus CICS BankDemo para Micro Focus Enterprise Developer 4.0 em Azure Virtual Machines
description: Executar a aplicação Micro Focus BankDemo em Azure Virtual Machines (VMs) para aprender a usar o Micro Focus Enterprise Server e o Enterprise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 9ef9dd27183b6c23762dcdc2d5a844123d35559a
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045965"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configurar Micro Focus CICS BankDemo para Micro Focus Enterprise Developer 4.0 em Azure

Quando configurar o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure, pode testar implementações de cargas de trabalho IBM z/OS. Este artigo mostra como criar o CICS BankDemo, uma aplicação de amostra que vem com o Enterprise Developer.

Os CICs representam o Sistema de Controlo de Informação do Cliente, a plataforma de transação utilizada por muitas das aplicações mainframe online. A aplicação BankDemo é ótima para aprender como o Enterprise Server e o Enterprise Developer operam e como gerir e implementar uma aplicação real completa com terminais de ecrã verde.

> [!NOTE]
> Em breve: Instruções para configurar [o Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) em VMs Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um VM com [Enterprise Developer](set-up-micro-focus-azure.md). Tenha em mente que o Enterprise Developer tem uma instância completa do Enterprise Server nele para fins de desenvolvimento e teste. Este caso é o caso do Enterprise Server usado para a demonstração.

- [Edição Expresso SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-editions-express). Faça o download e instale-o no Enterprise Developer VM. O Enterprise Server requer uma base de dados para a gestão das regiões do CICS, e a aplicação BankDemo também utiliza uma base de dados do SQL Server chamada BANKDEMO. Esta demonstração pressupõe que está a utilizar o SQL Server Express para ambas as bases de dados. Ao instalar, selecione a instalação básica.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). O SSMS é utilizado para gerir as bases de dados e executar um script T-SQL. Faça o download e instale-o no Enterprise Developer VM.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) com o mais recente pacote de serviços ou [Visual Studio Community,](https://visualstudio.microsoft.com/vs/community/)que você pode baixar gratuitamente.

- Rumba Desktop ou outro emulador 3270.

## <a name="configure-the-windows-environment"></a>Configure o ambiente windows

Depois de instalar o Enterprise Developer 4.0 no VM, tem de configurar a instância do Enterprise Server que o acompanha. Para isso, é necessário instalar algumas funcionalidades adicionais do Windows da seguinte forma.

1. Utilize RDP para iniciar sessão no Enterprise Server 4.0 VM que criou.

2. Clique no ícone **'Procurar'** ao lado do botão **Iniciar** e digite **as funcionalidades do Windows**. O gestor do servidor adiciona funções e o assistente de funcionalidades abre.

3. Selecione **o Web Server (IIS) Função** e, em seguida, verifique as seguintes opções:

    - Ferramentas de Gestão Web
    - IIS 6 Compatibilidade de Gestão (selecione todas as funcionalidades disponíveis)
    - Consola de Gestão do IIS
    - Scripts e ferramentas de gestão do IIS
    - Serviço de Gestão IIS

4. Selecione **World Wide Web Services** e verifique as seguintes opções:

     Características de desenvolvimento de aplicações:
    - Extensibilidade .NET
    - ASP.NET
    - Funcionalidades HTTP Comuns: Adicionar todas as funcionalidades disponíveis
    - Saúde e Diagnósticos: Adicione todas as funcionalidades disponíveis
    - Segurança:
        - Autenticação Básica
        - Autenticação do Windows

5. Selecione **o Serviço de Ativação do Processo do Windows** e todas as suas crianças.

6. Para **funcionalidades**, verifique **o quadro microsoft .NET 3.5.1**, e verifique as seguintes opções:

    - Ativação HTTP Da Fundação de Comunicação do Windows
    - Ativação não HTTP da Fundação de Comunicação do Windows

7. Para **funcionalidades**, verifique **o quadro microsoft .NET 4.6**, e verifique as seguintes opções:

   - Ativação de tubos nomeado
   - Ativação TCP
   - Partilha de portas TCP

     ![Adicionar funções e funcionalidades Assistente: Serviços de função](media/01-demo-roles.png)

8. Quando tiver selecionado todas as opções, clique em **Seguinte** para instalar.

9. Depois das funcionalidades do Windows, aceda ao **Sistema de Controlo e \> \> Ferramentas Administrativas de Segurança** e selecione **Serviços**. Desloque-se para baixo e certifique-se de que os seguintes serviços estão em execução e definidos para **Automático:**

    - **NetTcpPortSharing**
    - **Adaptador de ouvintes net.pipe**
    - **Adaptador de ouvintes net.tcp**

10. Para configurar o suporte IIS e WAS, a partir do menu localizar o **Micro Focus Enterprise Developer Command Prompt (64 bit)** e funcionar como **Administrador**.

11. Tipo **wassetup (i)** e pressione **Enter**.

12. Depois do guião ser executado, pode fechar a janela.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configure a conta do sistema local para o SQL Server

Alguns processos do Enterprise Server precisam de ser capazes de assinar no SQL Server e criar bases de dados e outros objetos. Estes processos utilizam a conta do sistema local, pelo que deve dar autoridade a essa conta.

1. Lance o **SSMS** e clique em **Ligar** para ligar ao Servidor SQLEXPRESS local utilizando a autenticação do Windows. Deve estar disponível na lista **de Nomes** do Servidor.

2. À esquerda, expanda a pasta **De Segurança** e selecione **Logins**.

3. Selecione **NT AUTHORITY \\ SYSTEM** e selecione **Propriedades.**

4. Selecione **Funções do Servidor** e verifique **sysadmin**.

     ![Janela SSMS Object Explorer: Propriedades de login](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Criar a base de dados BankDemo e todos os seus objetos

1. Abra **o Windows Explorer** e navegue para **C: \\ Documentos Públicos dos Utilizadores \\ Micro Focus Enterprise Developer Samples \\ \\ \\ \\ \\ Mainframe \\ CICS \\ DotNet \\ BankDemo \\ SQL**.

2. Copie o conteúdo do **ficheiro .SQL BankDemoCreateAll** na sua área de transferência.

3. **SSMS abertos**. À direita, clique em **Server** e selecione **Nova Consulta**.

4. Cole o conteúdo da prancheta na caixa **New Query.**

5. Execute o SQL clicando **executar** a partir do separador **Comando** acima da consulta.

A consulta deve ser executada sem erros. Quando estiver concluída, tem a base de dados de amostras para a aplicação BankDemo.

![Produção .sql SQLQuery1](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verifique se as tabelas e objetos da base de dados foram criados

1. Clique com o botão direito na base **de dados BANKDEMO** e selecione **Refresh**.

2. Expandir a **Base de Dados** e selecionar **tabelas.** Devia ver algo como o seguinte.

     ![Tabela BANKDEMO expandida no Explorador de Objetos](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Construir a aplicação no Enterprise Developer

1. Abra o Estúdio Visual e inscreva-se.

2. Sob a opção de menu **de ficheiros,** selecione **Open Project/Solution,** navegue para **C: \\ Documentos Públicos dos Utilizadores \\ Micro Focus Enterprise Developer Samples \\ \\ \\ \\ \\ Mainframe \\ CICS \\ DotNet \\ BankDemo**, e selecione o ficheiro **sln.**

3. Leve algum tempo para examinar os objetos. Os programas COBOL são mostrados no Solution Explorer com a extensão CBL juntamente com CopyBooks (CPY) e JCL.

4. Clique com o botão direito no Projeto **BankDemo2** e selecione **set as Startup Project**.

    > [!NOTE]
    > O Projeto BankDemo utiliza o HCOSS (Opção de Compatibilidade do Anfitrião para O Servidor SQL), que não é utilizado para esta demonstração.

5. No **Solution Explorer,** clique à direita no Projeto **BankDemo2** e selecione **Build**.

    > [!NOTE]
    > A construção ao nível da solução resulta em erros, uma vez que o HCOSS não foi configurado.

6. Quando o Projeto for construído, examine a janela **de saída.** Deverá ser semelhante à imagem abaixo.

     ![Janela de saída mostrando construção bem sucedida](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Implementar a aplicação BankDemo na base de dados da Região

1. Abra um pedido de comando do Enterprise Developer (64 bits) como Administrador.

2. Navegue para os **%PUBLIC% \\ Documentos \\ Micro Focus Enterprise Developer samples \\ \\ \\ Mainframe \\ CICS \\ DotNet \\ BankDemo**.

3. Na origem do comando, execute a **deplora bancária** e inclua o parâmetro para a base de dados a implementar para, por exemplo:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Certifique-se de que utiliza um corte para a frente (/) e não um corte para \\ trás. Este guião dura por um tempo.

![Administração: Janela de solicitação do comando do desenvolvedor da empresa](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Criar a Região BankDemo em Administrador Empresarial para .NET

1. Abra o **Servidor Empresarial para UI da Administração .NET.**

2. Para iniciar o snap-in MMC, a partir do menu Windows **Start,** escolha o **Micro Focus Enterprise Developer Configuration Enterprise Server para \> \> .NET Admin**. (Para Windows Server, escolha **Micro Focus Enterprise Developer Enterprise Server para \> .NET Admin**).

3. Expanda o recipiente **regiões** no painel esquerdo e, em seguida, clique com o botão direito **CICS**.

4. Selecione **a Região De Defini** para criar uma nova Região CICS chamada **BANKDEMO,** hospedada na base de dados (local).

5. Forneça a instância do servidor de base de dados, clique em **Seguinte** e, em seguida, insira o nome da região **BANKDEMO**.

     ![Definir caixa de diálogo da região](media/07-demo-cics.png)

6. Para selecionar o ficheiro de definição da região para a base de dados cross-region, localize **adb.configbancária da região \_ \_** em **C: \\ Documentos Públicos utilizadores \\ Micro Focus Enterprise Developer Samples \\ \\ \\ \\ \\ Mainframe \\ CICS \\ DotNet \\ BankDemo**.

     ![Defini região - Nome da região: BANKDEMO](media/08-demo-cics.png)

7. Clique em **Concluir**.

## <a name="create-xa-resource-definitions"></a>Criar Definições de Recursos XA

1. No painel esquerdo do **Enterprise Server para .NET Administration** UI, expanda o **Sistema** e, em seguida, **as Definições de Recursos XA**. Esta definição define como a Região interopera com o Enterprise Server e as bases de dados de aplicações.

2. Clique com o botão direito nas **definições de recursos XA** e selecione **Add Server Instance**.

3. Na caixa de entrega, selecione **Database Service Instance**. Será a máquina local SQLEXPRESS.

4. Selecione a instância a partir do recipiente **XA Definições de Recursos (machinename \\ sqlexpress)** e clique em **Adicionar**.

5. Selecione **a Definição de Recursos XA da Base de Dados** e, em seguida, **escreva BANKDEMO** para o **Nome** e **Região.**

     ![Novo ecrã de definição de recursos XA de base de dados](media/09-demo-xa.png)

6. Clique nas elipses **(...**) para trazer o assistente de cadeia de ligação. Para **o nome do servidor,** **escreva (local) \\ SQLEXPRESS**. Para **logon**, selecione **A autenticação do Windows**. Para o nome da base de **dados, escreva BANKDEMO**

     ![Editar ecrã de cadeia de ligação](media/10-demo-string.png)

7. Teste a ligação.

## <a name="start-the-bankdemo-region"></a>Inicie a Região BANKDEMO

> [!NOTE]
> O primeiro passo é importante: deve definir a Região para utilizar a Definição de Recursos XA que acabou de criar.

1. Navegue para a **Região BANDEMO CICS** sob o **Contentor regiões,** e, em seguida, selecione EditAr Ficheiro de **Arranque da Região** a partir do painel **de Ações.** Desloque-se até às propriedades SQL e **introduza** a caixa bancária para obter o **nome de recurso XA,** ou use a elipse para selecioná-la.

2. Clique no ícone **Guardar** para guardar as suas alterações.

3. Clique à direita **na região CICS BANKDEMO** no painel de **consola** e selecione **a Região iniciar/parar**.

4. Na parte inferior da caixa **'Região início/paragem'** que aparece no painel central, selecione **Start**. Após alguns segundos, a região começa.

     ![SQL Start/Stop box](media/11-demo-sql.png)

     ![CICS Região BANKDEMO - Ecrã iniciado](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Criar um ouvinte

Crie um ouvinte para as sessões TN3270 que aceda à aplicação BankDemo.

1. No painel esquerdo, expanda os **Editores de Configuração** e selecione **o Ouvinte**.

2. Clique no ícone **'Ficheiro Aberto'** e selecione o ficheiro **seelistener.exe.config.** Este ficheiro será editado e é carregado sempre que o Enterprise Server começa.

3. Note as duas Regiões previamente definidas (ESDEMO e JCLDEMO).

4. Para criar uma nova Região para O BANCO, clique à direita **Regiões,** e selecione **Add Region**.

5. Selecione **região BANKDEMO**.

6. Adicione um canal TN3270 clicando na **região bankdemo** e selecionando **o Add Channel**.

7. Para **citar nome,** insira **O TN3270**. Para **o Porto**, insira **9024**. A aplicação ESDEMO utiliza a porta 9230, pelo que é necessário utilizar uma porta diferente.

8. Para guardar o ficheiro, clique no ícone **Guardar** ou escolha **Guardar** \> **ficheiros.**

9. Para iniciar o ouvinte, clique no ícone **Iniciar o ouvinte** ou escolha **Opções** \> **Start Listener**.

     ![Janelas do editor de configuração do ouvinte](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configure Rumba para aceder à aplicação BankDemo

A última coisa que precisa fazer é configurar uma sessão de 3270 usando Rumba, um emulador 3270. Este passo permite-lhe aceder à aplicação BankDemo através do ouvinte que criou.

1. A partir do menu Windows **Start,** lance o Rumba Desktop.

2. No item do menu **'Ligações',** selecione **TN3270**.

3. Clique **em Inserir** e tipo **127.0.0.1** para o endereço IP e **9024** para a porta definida pelo utilizador.

4. Na parte inferior da caixa de diálogo, clique em **Connect**. Aparece um ecrã CICS preto.

5. Digite **banco** para exibir o ecrã inicial 3270 para a aplicação BankDemo.

6. Para iD do utilizador, tipo **B0001** e para a palavra-passe, digite qualquer coisa. O primeiro ecrã BANK20 abre.

![Ecrã principal Display Ecrã ](media/14-demo.png)
 ![ principal De boas-vindas - Rumba - Ecrã de demonstração do subsistema](media/15-demo.png)

Parabéns! Está agora a executar uma aplicação CICS em Azure utilizando o Micro Focus Enterprise Server.

## <a name="next-steps"></a>Passos seguintes

- [Executar servidor de empresa em contentores Docker em Azure](run-enterprise-server-container.md)
- [Migração do Computador Central - Portal](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Máquinas Virtuais](../../../linux/overview.md)
- [Resolução de problemas](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [Desmistificar o mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)