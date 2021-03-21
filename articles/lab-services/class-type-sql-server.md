---
title: Crie um laboratório para gerir e desenvolver com a Azure SQL Database | Serviços de Laboratório Azure
description: Aprenda a criar um laboratório para gerir e desenvolver com a Base de Dados Azure SQL.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 50f71ee1ce59f5809fe8905c58f0399cf484f11a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659714"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Crie um laboratório para gerir e desenvolver com o SQL Server

Este artigo descreve como criar um laboratório para uma classe básica de gestão e desenvolvimento do SQL Server nos Serviços de Laboratório Azure.  Os conceitos de base de dados são um dos cursos introdutórios ministrados na maioria dos departamentos de Ciências da Computação na faculdade. A Linguagem De Consulta Estruturada (SQL) é um padrão internacional.  SQL é o idioma padrão para a gestão da base de dados de relação, incluindo a adição, acesso e gestão de conteúdos numa base de dados.  É mais notável pelo seu processamento rápido, fiabilidade comprovada, facilidade e flexibilidade de utilização.

Neste artigo, vamos mostrar como configurar um modelo de máquina virtual em laboratório com [Visual Studio 2019,](https://visualstudio.microsoft.com/vs/) [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), e [Azure Data Studio](https://github.com/microsoft/azuredatastudio).  Para este laboratório, usaremos uma base de dados de [servidores SQL](../azure-sql/database/sql-database-paas-overview.md) partilhada para todo o laboratório. [A Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) é a Plataforma como uma oferta de motor de base de dados de serviço (PaaS) da Azure.

## <a name="lab-configuration"></a>Configuração de laboratório

Para montar este laboratório, precisa de uma assinatura Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório.](./tutorial-setup-lab-account.md) Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Definições de conta de laboratório

Ativar as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como ativar imagens de marketplace, consulte [as imagens do Mercado Dese especificar disponíveis para os criadores de laboratório.](specify-marketplace-images.md)

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
| Imagem do Marketplace | Ativar a imagem 'Visual Studio 2019 Community (mais recente versão) no Windows 10 Enterprise N (x64)' para utilização na sua conta de laboratório. |

### <a name="shared-resource-configuration"></a>Configuração de recursos compartilhados

Para utilizar um recurso partilhado nos Serviços de Laboratório, primeiro precisa de criar a rede virtual e os recursos em si.  Para criar a rede virtual e conectá-la ao laboratório, siga [como criar um laboratório com um recurso partilhado nos Serviços Azure Lab](how-to-create-a-lab-with-shared-resource.md).  Lembre-se, quaisquer recursos externos aos Serviços de Laboratório serão cobrados separadamente e não serão incluídos nas estimativas de custos do laboratório.

>[!WARNING]
>Os recursos partilhados para um laboratório devem ser configurados antes do laboratório ser criado.  Se o vnet não for [espreitado para a conta do laboratório](how-to-connect-peer-virtual-network.md) *antes* do laboratório ser criado, o laboratório não terá acesso ao recurso partilhado.

Agora que o lado de rede das coisas é tratado, permite criar uma Base de Dados de Servidor SQL.  Vamos criar uma [única base de dados,](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) uma vez que é a opção de implementação mais rápida para a Base de Dados Azure SQL.  Para outras opções de implantação, crie uma [piscina elástica,](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal) [exemplo gerido,](../azure-sql/managed-instance/instance-create-quickstart.md)ou [máquina virtual SQL](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).

1. A partir do menu do portal Azure, escolha **Criar novo recurso.**
2. Escolha **a Base de Dados SQL** e clique no botão **Criar.**
3. No **separador Básico do** formulário de base de **dados Create SQL,** selecione o grupo de recursos para a base de dados.  Usaremos *sqldb-rg.*
4. Para **o nome da base de dados**, insira o *classlabdb*.
5. Na definição do **Servidor,** clique em **Criar novo** para criar um novo servidor para manter a base de dados.
6. No **novo voo do servidor,** insira o nome do Servidor.  Usaremos *o classlabdbserver.*  O nome do servidor deve ser globalmente único.
7. Introduza *o azureuser* para o **login de administração do Servidor**.
8. Insira uma senha memorável.  A palavra-passe deve ter pelo menos oito caracteres de comprimento e conter caracteres especiais.
9. Escolha a região para a **localização.**  Se possível, entre no mesmo local que a conta do laboratório e espre trabalhou na vnet para minimizar a latência.
10. Clique **em OK** para voltar ao formulário Criar Base de **Dados SQL.**
11. Clique em Configurar o link **de base de dados** sob a definição de armazenamento **Compute +** .
12. Modifique as definições da base de dados conforme necessário para a classe.  Pode escolher entre opções Provisioned e Serverless.  Para este exemplo, usaremos a opção Serverless de escala automática com max vCores de 4, min vCores de 1. Vamos manter a regulação da autopausa no mínimo de 1 hora. Clique em **Aplicar**.
13. Clique **em seguida: Botão de networking.**
14. No separador 'Rede', escolha ponto final privado para o **método conectividade**.
15. Na secção **ponto final privado,** clique **em Adicionar ponto final privado**.
16. No flyout **de ponto final privado Create,** escolha o mesmo grupo de recursos que a sua rede virtual espreitada para a conta de laboratório.
17. Para **localização,** escolha o mesmo local que a rede virtual.
18. Para **citar nome,** *insira labsql-endpt*.
19. Deixe o subresource target definido para SqlServer.
20. Para **a rede Virtual,** escolha a mesma rede virtual espreitada para a conta de laboratório.
21. Para **a Sub-rede,** escolha a sub-rede onde pretende que o ponto final seja alojado.  O IP atribuído ao ponto final será do intervalo atribuído a essa sub-rede.
22. Conjunto **Integrar-se com DNS privado** a **Nº**. Para simplificar, usaremos o DNS da Azure em vez da própria zona privada de DNS ou dos nossos próprios servidores DNS.
23. Clique em **OK**.
24. Clique **em seguida: Definições adicionais**.
25. Para a **definição de dados existente,** escolha **amostra**.  Os dados da base de dados AdventureWorksLT serão utilizados quando a base de dados for criada.
26. Clique em **Rever + criar**.
27. Clique em **Criar**.

Assim que a implementação da Base de Dados SQL estiver concluída com sucesso, podemos criar o laboratório e instalar software na máquina de modelo de laboratório.

### <a name="lab-settings"></a>Configurações de laboratório

Utilize as definições na tabela abaixo ao configurar um laboratório de sala de aula. Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [a criação de um tutorial de laboratório em sala de aula.](tutorial-setup-classroom-lab.md)

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
| Tamanho da máquina virtual | Média. Este tamanho é mais adequado para bases de dados relacionais, caching na memória e análise. |
| Imagem de máquina virtual | Visual Studio 2019 Community (mais recente lançamento) no Windows 10 Enterprise N (x64) |

Agora que o nosso laboratório foi criado, vamos modificar a máquina de modelos com o software de que precisamos.

## <a name="visual-studio"></a>Visual Studio

A imagem escolhida acima inclui [a Comunidade Visual Studio 2019.](https://visualstudio.microsoft.com/vs/community/)  Todas as cargas de trabalho e conjuntos de ferramentas já estão instalados na imagem.  Utilize o Instalador de Estúdio Visual para [instalar as ferramentas opcionais](/visualstudio/install/modify-visual-studio?view=vs-2019) que desejar.  [Inscreva-se no Visual Studio](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio) para desbloquear a edição comunitária.

O Visual Studio inclui o conjunto **de ferramentas de armazenamento e processamento de dados,** que inclui ferramentas de dados do sql servidor (SSDT).  Para obter mais informações sobre as capacidades do SSDT, consulte a visão geral das [Ferramentas de Dados do Servidor SQL](/sql/ssdt/sql-server-data-tools?view=sql-server-ver15).  Para verificar se a ligação ao SqL Server partilhado para a classe será bem sucedida, consulte [a ligação a uma base de dados e navegue nos objetos existentes](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15). Se solicitado, adicione o IP da máquina de modelo à [lista de computadores autorizados](../azure-sql/database/firewall-configure.md) que podem ligar-se à sua instância do SQL Server.

O Visual Studio suporta várias cargas de trabalho, incluindo **web & cloud** e desktop & cargas de trabalho **móveis.**  Ambas as cargas de trabalho suportam o SQL Server como fonte de dados. Para obter mais informações utilizando ASP.NET Core to SQL Server, consulte [a construção de uma aplicação de Base de Dados Core e SQL ASP.NET no tutorial do Azure App Service.](../app-service/tutorial-dotnetcore-sqldb-app.md)  Use a biblioteca [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) para ligar a uma Base de Dados SQL a partir de uma aplicação [Xamarin.](/xamarin)

## <a name="install-azure-data-studio"></a>Instalar o Azure Data Studio

[O Azure Data Studio](https://github.com/microsoft/azuredatastudio) é um ambiente de desktop multi-base e plataformas cruzadas para profissionais de dados que utilizam a família de plataformas de dados em nuvem no Windows, macOS e Linux.

1. Descarregue o instalador do [ *sistema* Azure Data Studio para windows](https://go.microsoft.com/fwlink/?linkid=2127432). Para encontrar instaladores para outros sistemas operativos suportados, aceda à página de descarregamento do [Azure Data Studio.](/sql/azure-data-studio/download)
2. Na página Do **Contrato de Licença,** selecione **aceito o acordo.** Clique em **Seguinte**.
3. Na página **Selecionar localização de destino**, clique em **Seguinte**.
4. Na página **Selecionar Pasta do Menu Iniciar**, clique em **Seguinte**.
5. Na página **'Selecionar Tarefas Adicionais',** verifique **criar um ícone de ambiente de trabalho** se quiser um ícone de ambiente de trabalho.  Clique em **Seguinte**.
6. No **Pronto a Instalar,** clique **em Seguinte**.
7. Aguarde que o instalador corra.  Clique em **Concluir**.

Agora que temos o Azure Data Studio instalado, vamos configurar a ligação à Base de Dados Azure SQL.

1. Na página **Welcome** for Azure Data Studio, clique no link **New Connection.**
2. Na caixa **'Detalhes de Ligação',** preencha as informações necessárias.
    - Definir **servidor** para *classlabdbserver.database.windows.net*
    - Definir **nome de utilizador** para *azureuser*
    - Desafine a **palavra-passe** para a palavra-passe utilizada para criar a base de dados.
    - Verifique **a palavra-passe de Se lembrar**.
    - Para **a base de dados**, selecione *classlabdb*.
3. Clique em **Ligar**.

## <a name="install-sql-server-management-studio"></a>Instalar o SQL Server Management Studio

[O SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) é um ambiente integrado para a gestão de qualquer infraestrutura SQL.  SSMS é uma ferramenta usada pelos administradores de bases de dados para implantar, monitorizar e atualizar a infraestrutura de dados.

1. [Baixar Sql Server Management Studio](https://aka.ms/ssmsfullsetup). Uma vez descarregado, inicie o instalador.
2. Na página **Welcome,** clique **em Instalar**.
3. Na página **Configuração Concluída,** clique em **Fechar**.
4. Inicie o Sql Server Management Studio.  
5. Na página **de processo de configuração de dependência,** clique em **Fechar**.

Não que o SSMS esteja instalado, pode [ligar e consultar um SqL Server](/sql/ssms/tutorials/connect-query-sql-server). Ao configurar a ligação, utilize os seguintes valores:

- Tipo de servidor: Motor de base de dados
- Nome do servidor: *classlabdbserver.database.windows.net*
- Autenticação: Autenticação do Servidor SQL
- Login: *azureuser*
- Palavra-passe: palavra-passe utilizada para criar a base de dados.

## <a name="cost-estimate"></a>Estimativa de custos

Vamos cobrir uma possível estimativa de custos para esta aula. A estimativa não inclui o custo de funcionamento do SQL Server.  Consulte [os preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database) para obter detalhes atuais sobre os preços da base de dados.

Usaremos uma turma de 25 alunos. Há 20 horas de horário de aula. Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora do horário de aulas programado. O tamanho da máquina virtual que escolhemos era médio, que é 42 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custos para esta classe:

25 alunos \* (20 horas programadas \+ 10 horas de quota) * 0,42 USD por hora = 315,00 USD

>[!IMPORTANT]
>A estimativa de custos é apenas para fins. Para obter detalhes atuais sobre os preços, consulte [o Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar, gerir e publicar um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)