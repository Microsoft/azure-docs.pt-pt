---
title: Configurar micro focus CICS BankDemo para Micro Focus Enterprise Developer 4.0 em Máquinas Virtuais Azure
description: Executar a aplicação Micro Focus BankDemo em Máquinas Virtuais Azure (VMs) para aprender a usar Micro Focus Enterprise Server e Enterprise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411078"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configurar micro focus CICS BankDemo para Micro Focus Enterprise Developer 4.0 em Azure

Quando configurar o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure, pode testar as implementações das cargas de trabalho IBM z/OS. Este artigo mostra como configurar o CICS BankDemo, uma aplicação de amostra que vem com o Promotor Empresarial.

Os CICs representam o Sistema de Controlo de Informação do Cliente, a plataforma de transação utilizada por muitas das aplicações principais online. A aplicação BankDemo é ótima para aprender como o Enterprise Server e o Enterprise Developer operam e como gerir e implementar uma aplicação real completa com terminais de tela verde.

> [!NOTE]
> Em breve: Instruções para a instalação do [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) em VMs Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um VM com [Enterprise Developer](set-up-micro-focus-azure.md). Tenha em mente que o Enterprise Developer tem uma instância completa de Enterprise Server nele para fins de desenvolvimento e teste. Esta instância é a instância do Enterprise Server usado para a demonstração.

- [Edição Expresso SQL Server 2017.](https://www.microsoft.com/sql-server/sql-server-editions-express) Descarregue e instale-o no VM do Desenvolvedor da Empresa. O Enterprise Server requer uma base de dados para a gestão das regiões cics, e a aplicação BankDemo também utiliza uma base de dados do SQL Server chamada BANKDEMO. Esta demonstração pressupõe que está a utilizar o SQL Server Express para ambas as bases de dados. Ao instalar, selecione a instalação básica.

- [Estúdio de Gestão de Servidores SQL](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). O SSMS é utilizado para gerir as bases de dados e executar um script T-SQL. Descarregue e instale-o no VM do Desenvolvedor da Empresa.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) com o mais recente pacote de serviços ou [Visual Studio Community,](https://visualstudio.microsoft.com/vs/community/)que você pode baixar gratuitamente.

- Rumba Desktop ou outro emulador 3270.

## <a name="configure-the-windows-environment"></a>Configure o ambiente windows

Depois de instalar o Enterprise Developer 4.0 no VM, tem de configurar a instância do Enterprise Server que o acompanha. Para isso, é necessário instalar algumas funcionalidades adicionais do Windows da seguinte forma.

1. Utilize RDP para iniciar sessão no Enterprise Server 4.0 VM que criou.

2. Clique no ícone **'Procurar'** ao lado do botão **Iniciar** e digite **as funcionalidades do Windows**. O Gestor do Servidor adiciona funções e funcionalidades que o Assistente abre.

3. Selecione **Função do Servidor Web (IIS)** e, em seguida, verifique as seguintes opções:

    - Ferramentas de Gestão Web
    - Compatibilidade de Gestão IIS 6 (selecione todas as funcionalidades disponíveis)
    - Consola de Gestão do IIS
    - Scripts e Ferramentas de Gestão IIS
    - Serviço de Gestão IIS

4. Selecione **World Wide Web Services**e verifique as seguintes opções:

     Características de desenvolvimento de aplicações:
    - Extensibilidade .NET
    - ASP.NET
    - Características comuns do HTTP: Adicione todas as funcionalidades disponíveis
    - Saúde e Diagnósticos: Adicione todas as funcionalidades disponíveis
    - Segurança:
        - Autenticação Básica
        - Autenticação do Windows

5. Selecione O Serviço de **Ativação do Processo do Windows** e todas as suas crianças.

6. Para **funcionalidades,** consulte a **estrutura Microsoft .NET 3.5.1**e verifique as seguintes opções:

    - Ativação da Fundação de Comunicação do Windows HTTP
    - Ativação não HTTP da Fundação de Comunicação do Windows

7. Para **funcionalidades,** consulte a **estrutura Microsoft .NET 4.6**e verifique as seguintes opções:

   - Ativação de tubos nomeados
   - Ativação do TCP
   - Partilha de portas TCP

     ![Adicionar papéis e funcionalidades assistente: serviços de funções](media/01-demo-roles.png)

8. Quando tiver selecionado todas as opções, clique em **Next** para instalar.

9. Depois das funcionalidades do Windows, vá ao ** \> Sistema de Controlo e ferramentas administrativas \> **de segurança, e selecione **Serviços**. Desloque-se para baixo e certifique-se de que os seguintes serviços estão em execução e definidos para **Automático:**

    - **NetTcpPortSharing**
    - **Adaptador de ouvido net.pipe**
    - **Adaptador de ouvinte net.tcp**

10. Para configurar o suporte iIS e WAS, a partir do menu localize **o Micro Focus Enterprise Developer Command Prompt (64 bits)** e corra como **Administrador**.

11. Digite **wassetup - i** e prima **Enter**.

12. Depois do guião, podefechar a janela.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configure a conta do sistema local para o Servidor SQL

Alguns processos do Enterprise Server precisam de ser capazes de assinar no SQL Server e criar bases de dados e outros objetos. Estes processos utilizam a conta do sistema local, pelo que deve dar autoridade de sisadmina a essa conta.

1. Lance o **SSMS** e clique em **Ligar-se** ao Servidor SQLEXPRESS local utilizando a Autenticação do Windows. Deve estar disponível na lista de Nomes do **Servidor.**

2. À esquerda, expanda a pasta **De Segurança** e selecione **Logins**.

3. Selecione **NT\\AUTHORITY SYSTEM** e selecione **Propriedades**.

4. Selecione **Funções de Servidor** e verifique **a sisadmina**.

     ![Janela do Explorador de Objetos SSMS: Propriedades de Login](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Crie a base de dados BankDemo e todos os seus objetos

1. Abra **o Windows Explorer** e navegue para **C:\\Utilizadores\\\\Documentos\\\\Públicos\\Micro\\Focus Enterprise Developer\\Samples\\Mainframe\\CICS DotNet BankDemo\\SQL**.

2. Copie o conteúdo do ficheiro **BankDemoCreateAll.SQL** na sua área de redação.

3. **SSMS abertos.** À direita, clique em **Server** e selecione **New Query**.

4. Colhe o conteúdo da pasta na caixa **New Consulta.**

5. Execute o SQL clicando em **Executar** a partir do separador **Comando** acima da consulta.

A consulta deve ser executada sem erros. Quando estiver concluído, tem a base de dados da amostra para a aplicação BankDemo.

![Saída SQLQuery1.sql](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verifique se foram criadas as tabelas e objetos da base de dados

1. Clique na base de dados **BANKDEMO** e selecione **Refresh**.

2. Expandir a Base de **Dados** e selecionar **Tabelas**. Devia ver algo como o seguinte.

     ![Tabela BANKDEMO expandida no Object Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Construa a aplicação em Enterprise Developer

1. Open Visual Studio e inscreva-se.

2. Sob a opção de menu **de ficheiros,** selecione **Open Project/Solution**, navegue para **C:\\\\Documentos públicos\\\\micro foco\\de utilizadores Amostras\\\\de desenvolvimento do MAINFrame\\CICS\\DotNet\\BankDemo,** e selecione o ficheiro **SLN.**

3. Tire algum tempo para examinar os objetos. Os programas COBOL são mostrados no Solution Explorer com a extensão CBL juntamente com CopyBooks (CPY) e JCL.

4. Clique à direita no Projeto **BankDemo2** e selecione **set como Projeto de Arranque**.

    > [!NOTE]
    > O Projeto BankDemo utiliza o HCOSS (Opção de Compatibilidade do Anfitrião para o Servidor SQL), que não é utilizado para esta demonstração.

5. No **Solution Explorer,** clique à direita no Projeto **BankDemo2** e selecione **Build**.

    > [!NOTE]
    > A construção ao nível da solução resulta em erros, uma vez que o HCOSS não foi configurado.

6. Quando o Projeto for construído, examine a janela **output.** Deverá ser semelhante à imagem abaixo.

     ![Janela de saída mostrando construção bem sucedida](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Implementar a aplicação BankDemo na base de dados da Região

1. Abra um pedido de comando enterprise developer (64 bits) como Administrador.

2. Navegue para os **\\documentos\\%PUBLIC%\\\\Micro\\Focus Enterprise\\\\Developer amostras\\Mainframe CICS DotNet BankDemo**.

3. No pedido de comando, execute a **bankdemodbdeploy** e inclua o parâmetro para que a base de dados se desloque, por exemplo:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Certifique-se de que utiliza um corte para\\a frente (/) e não um corte para trás. Este guião dura por um tempo.

![Administração: Janela rápida de comando de desenvolvimento de empresa](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Criar a Região bankDemo em Administrador Empresarial para .NET

1. Abra o **Servidor Empresarial para .NET Administration** UI.

2. Para iniciar o snap-in do MMC, a partir do menu Windows **Start,** escolha **o Micro Focus Enterprise Developer \> Enterprise \> Server para .NET Admin**. (Para windows server, escolha **Micro Focus Enterprise Developer \> Enterprise Server para .NET Admin**).

3. Expanda o recipiente **das Regiões** no painel esquerdo e, em seguida, clique à direita **NO CICS**.

4. Selecione **Região de Definição** para criar uma nova região CICS chamada **BANKDEMO,** alojada na base de dados (local).

5. Forneça a instância do servidor de base de dados, clique **em Seguinte**, e, em seguida, introduza o nome de região **BANKDEMO**.

     ![Definir caixa de diálogo da região](media/07-demo-cics.png)

6. Para selecionar o ficheiro de definição da região para a base de dados transversal, localize a **região\_\_bankdemo db.config** em **C:\\Utilizadores\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Região de Definição - Nome da região: BANKDEMO](media/08-demo-cics.png)

7. Clique em **Concluir**.

## <a name="create-xa-resource-definitions"></a>Criar Definições de Recursos XA

1. No painel esquerdo do **Servidor Empresarial para .NET Administration** UI, expanda o **System**, e, em seguida, as Definições de **Recursos XA**. Esta definição define como a Região interopera com o Enterprise Server e as bases de dados de aplicações.

2. Clique à direita nas Definições de **Recursos XA** e selecione **Adicionar A Instância do Servidor**.

3. Na caixa de entrega, selecione **Database Service Instance**. Será a máquina local SQLEXPRESS.

4. Selecione a instância no recipiente **XA\\Resource Definitions (machinename sqlexpress)** e clique em **Adicionar**.

5. Selecione **Base de Dados XA Definição** de recursos e, em seguida, **digite BANKDEMO** para o **Nome** e **Região**.

     ![Novo ecrã de definição de recursos XA da base de dados](media/09-demo-xa.png)

6. Clique nas elipses**para**elevar o assistente de corda de ligação. Para **nome do servidor,** escreva **(local)\\SQLEXPRESS**. Para **iniciar sessão,** selecione **A autenticação do Windows**. Para nome da base de dados, escreva **BANKDEMO**

     ![Editar tela de corda de ligação](media/10-demo-string.png)

7. Teste a ligação.

## <a name="start-the-bankdemo-region"></a>Iniciar a Região BANKDEMO

> [!NOTE]
> O primeiro passo é importante: Tem de definir a Região para utilizar a Definição de Recursos XA que acabou de criar.

1. Navegue para a **região DO CICS BANDEMO** sob o contentor das **regiões**e, em seguida, selecione Editar O Ficheiro de Arranque da **Região** a partir do painel **de ações.** Desloque-se até às propriedades SQL e introduza **bankdemo** para o nome de **recurso XA,** ou use a elipse para selecioná-la.

2. Clique no ícone **Guardar** para guardar as suas alterações.

3. Clique direito **bankdemo CICS Região** no painel de **consolas** e selecione **Região de Início/Paragem**.

4. Na parte inferior da caixa **Start/Stop Region** que aparece no painel médio, selecione **Iniciar**. Após alguns segundos, a região começa.

     ![Caixa de arranque/paragem SQL](media/11-demo-sql.png)

     ![REGIÃO CICS BANKDEMO - Tela iniciada](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Criar um ouvinte

Crie um ouvinte para as sessões TN3270 que acedam à aplicação do BankDemo.

1. No painel esquerdo, expanda **os Editores** de Configuração e selecione **Listener**.

2. Clique no ícone **Open File** e selecione o ficheiro **seelistener.exe.config.** Este ficheiro será editado e é carregado sempre que o Enterprise Server começar.

3. Note as duas Regiões previamente definidas (ESDEMO e JCLDEMO).

4. Para criar uma nova Região para BANKDEMO, clique à direita **regiões,** e **selecione Adicionar Região**.

5. Selecione **REGIÃO BANKDEMO**.

6. Adicione um canal TN3270 clicando na região de **BANKDEMO** e selecionando **Add Channel**.

7. Para **Nome,** insira **TN3270**. Para **o Porto,** insira **9024**. A aplicação ESDEMO utiliza a porta 9230, pelo que necessita de utilizar uma porta diferente.

8. Para guardar o ficheiro, clique no ícone **Guardar** ou escolha **Guardar** **Ficheiros** \> .

9. Para iniciar o ouvinte, clique no ícone **Iniciar Ouvintes** ou escolha **Opções** \> **Iniciar Ouvintes**.

     ![Janelas do Editor de Configuração do Ouvinte](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configure Rumba para aceder à aplicação BankDemo

A última coisa que precisa fazer é configurar uma sessão 3270 usando Rumba, um emulador 3270. Este passo permite-lhe aceder à aplicação BankDemo através do ouvinte que criou.

1. A partir do menu Windows **Start,** lance Rumba Desktop.

2. No ponto do menu **Conexões,** selecione **TN3270**.

3. Clique em **Inserir** e tipo **127.0.0.1** para o endereço IP e **9024** para a porta definida pelo utilizador.

4. Na parte inferior da caixa de diálogo, clique em **Ligar**. Aparece um ecrã CICS preto.

5. Digite **banco** para exibir o ecrã inicial 3270 para a aplicação BankDemo.

6. Para id do utilizador, digite **B0001** e para a palavra-passe, digite qualquer coisa. Abre o primeiro ecrã BANK20.

![Ecrã de exibição principal Ecrã de boas-vindas](media/14-demo.png)
![Ecrã principal - Rumba - Ecrã de demonstração do subsistema](media/15-demo.png)

Parabéns! Está agora a executar uma aplicação CICS em Azure utilizando o Micro Focus Enterprise Server.

## <a name="next-steps"></a>Passos seguintes

- [Executar o Servidor Empresarial em contentores Docker em Azure](run-enterprise-server-container.md)
- [Migração mainframe - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Resolução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando o mainframe para a migração de Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
