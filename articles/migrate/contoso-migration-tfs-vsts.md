---
title: Refatorizar uma implantação do Team Foundation Server para Visual Studio Team Services (VSTS) no Azure | Documentos da Microsoft
description: Saiba como Contoso revisasse, encontrasse sua implantação do TFS no local ao migrá-lo para Visual Studio Team Services (VSTS) no Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 05340c8504150ed568e0d5ce5c8250127e59bca0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003242"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-visual-studio-team-services-vsts"></a>Migração de Contoso: Refatorizar uma implantação do Team Foundation Server para Visual Studio Team Services (VSTS)

Este artigo mostra como Contoso são refatoração a implantação do Team Foundation Server (TFS) no local ao migrá-lo para Visual Studio Team Services (VSTS) no Azure. Equipe de desenvolvimento da Contoso usaram o TFS para colaboração em equipe e o controlo de origem nos últimos cinco anos. Agora, pretende mover para uma solução baseada na cloud para o trabalho de desenvolvimento e teste e para controlo de origem. VSTS desempenharão uma função que mudar para um modelo de DevOps e desenvolver novas aplicações nativas da cloud.

Este documento é a décima primeira de uma série de artigos que mostram como a empresa fictícia Contoso migra os respetivos recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade e vamos adicionar artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Fornece uma descrição geral da estratégia de migração da Contoso, a série de artigos e as aplicações de exemplo que usamos. | Disponível
[Artigo 2: Implementar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como o Contoso prepara a sua infraestrutura do Azure e no local, para a migração. A mesma infra-estrutura é utilizada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar a recursos no local](contoso-migration-assessment.md)  | Mostra como Contoso é executado uma avaliação da sua aplicação de SmartHotel de duas camadas do local em execução no VMware. Avaliar a VMs de aplicação com o [do Azure Migrate](migrate-overview.md) serviço e a base de dados do SQL Server do aplicativo com o [Assistente de migração de base de dados do Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Realojamento para VMs do Azure e uma instância gerida de SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como a Contoso migra a aplicação de SmartHotel para o Azure. Estes migrar a aplicação web VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)e a base de dados de aplicação com o [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) serviço, para migrar para uma instância gerida de SQL. | Disponível
[Artigo 5: Realojar em VMs do Azure](contoso-migration-rehost-vm.md) | Mostra como o Contoso migrar seus SmartHotel para VMs de IaaS do Azure, com o serviço Site Recovery.
[Artigo 6: Realojar em VMs do Azure e grupos de disponibilidade do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra a aplicação de SmartHotel. Utilizam o Site Recovery para migrar as VMs da aplicação e o serviço de migração de base de dados para migrar a base de dados de aplicação para um grupo de disponibilidade do SQL Server. | Disponível
[Artigo 7: Realojar uma aplicação do Linux para VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como a Contoso migra a aplicação de Linux osTicket para VMs de IaaS do Azure com o Azure Site Recovery.
[Artigo 8: Realojar uma aplicação do Linux para VMs do Azure e o servidor MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra a aplicação de Linux osTicket. Utilizam o Site Recovery para a migração de VM e o MySQL Workbench para migrar para uma instância do servidor MySQL do Azure. | Disponível
[Artigo 9: Refatorizar uma aplicação para uma aplicação Web do Azure e a base de dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como Contoso migra a aplicação de SmartHotel para uma aplicação web do Azure baseadas em contentores e migra a base de dados de aplicação para o servidor SQL do Azure. | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux para o serviço de aplicações do Azure e o servidor MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra a aplicação de Linux osTicket para o serviço de aplicações do Azure com o contentor de Docker do PHP 7.0. A base de código para a implementação é migrada para o GitHub. A base de dados de aplicação é migrada para MySQL do Azure. | Disponível
Artigo 11: Refatorizar uma implementação do TFS no VSTS | Migrar a aplicação de desenvolvimento TFS para o VSTS no Azure | Este artigo
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Mostra como Contoso migra e rearchitects a respetiva aplicação SmartHotel para o Azure. Eles rearquitetar a camada de web de aplicação como um contentor do Windows e a base de dados de aplicação numa base de dados SQL do Azure. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Mostra como Contoso reconstruir a sua aplicação de SmartHotel com uma gama de capacidades do Azure e serviços, incluindo serviços de aplicações, Kubernetes do Azure, as funções do Azure, serviços cognitivos e Cosmos DB. | Disponível


## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com o seu negócio para identificar os objetivos de futuros. Parceiros de negócios não estão muito preocupados com ferramentas de desenvolvimento e tecnologias, mas eles tem capturados nestes pontos:

- **Software**: independentemente da atividade principal, todas as empresas estão agora as empresas de software, incluindo Contoso. Liderança da empresa está interessada em como IT pode ajudar a levar a empresa com novas práticas de trabalho para os utilizadores e experiências para seus clientes.
- **Eficiência**: a Contoso precisa de simplificar o processo e remover procedimentos desnecessários para os desenvolvedores e usuários. Isto irá permiti-los satisfazer os requisitos dos clientes com mais eficiência. As necessidades empresariais IT para rápida, sem perder tempo ou dinheiro.
- **Agilidade**: Contoso TI precisa para responder às necessidades de negócio e reagir mais rapidamente do que o marketplace para ativar o sucesso numa economia global. IT não pode ser um impedimento para a empresa.

## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud Contoso tiver afixado para baixo de objetivos para a migração para o VSTS:

- Eles precisam de uma ferramenta para migrar os dados para a cloud. Devem ser necessários alguns processos manuais.
- Dados de item de trabalho e o histórico do último ano deve ser migrado.
- Eles não querem configurar novos nomes de utilizador e palavras-passe. Todas as atribuições de sistema atual devem ser mantidas.
- Pretende mover para fora do Team Foundation versão controle (TFVC) para o Git para controlo de origem.
- A transição para Git será uma "migração sugestão" que importa apenas a versão mais recente do código-fonte. Isso vai acontecer durante um período de indisponibilidade quando todo o trabalho vai ser suspensa como os turnos de base de código. Eles compreendem que apenas o histórico do ramo principal atual estarão disponível após a mudança.
- Eles esteja preocupados com a alteração e desejam testá-lo antes de efetuar uma mudança completa. Eles querem manter o acesso ao TFS, mesmo após a migração para o VSTS.
- Ter múltiplas coleções e pretende começar com um que tem apenas alguns projetos para compreender melhor o processo.
- Eles compreendem que coleções do TFS são uma relação um para um com as contas VSTS, para que eles têm várias URLs. No entanto, isso corresponde ao seu modelo atual de separação para bases de código e projetos.


## <a name="proposed-architecture"></a>Arquitetura proposta

- Contoso irá mover seus projetos do TFS para a cloud e já não é hospedar seus projetos ou origem controle no local.
- TFS será migrado para o VSTS.
- Atualmente a Contoso tem uma coleção de TFS com o nome **ContosoDev**, que será migrado para uma conta do VSTS denominada **contosodevmigration.visualstudio.com**.
- Os projetos, itens de trabalho, bugs e iterações do último ano serão migradas para o VSTS.
- Contoso aproveitará o Azure Active Directory, que configuraram quando eles [implementado a sua infraestrutura do Azure](contoso-migration-infrastructure.md) no início do seu planejamento de migração. 


![Arquitetura do cenário](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Processo de migração

Contoso concluirá o processo de migração da seguinte forma:

1. Há muita preparação envolvida. Como primeiro passo, a Contoso precisa de atualizar a sua implementação do TFS para um nível suportado. Eles estão atualmente em execução TFS 2017 Update 3, mas para utilizar a migração de base de dados que precisam para executar uma versão suportada do 2018 com as atualizações mais recentes.
2. Após a atualização, que irá executar a ferramenta de migração do TFS e validar a sua coleção.
3. Irá criar um conjunto de ficheiro de preparação e executar uma execução completa de migração para o teste.
4. Em seguida, executará outra migração, desta vez uma migração completa que inclui itens de trabalho, bugs, sprints e de código.
5. Após a migração, irá mover seu código no TFVC para Git.

![Processo de migração](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>Passos do cenário

Eis como o Azure irá concluir a migração:

> [!div class="checklist"]
> * **Passo 1: Criar uma conta de armazenamento do Azure**: esta conta de armazenamento será utilizada durante o processo de migração.
> * **Passo 2: TFS de atualização**: eles irá atualizar a implantação para o TFS 2018 atualização 2. 
> * **Passo 3: Verificar a coleção**: vai validar a sua coleção de TFS em preparação para a migração.
> * **Passo 4: Ficheiro de preparação de compilação**: irá criar os ficheiros de migração utilizando a ferramenta de migração do TFS. 


## <a name="step-1-create-a-storage-account"></a>Passo 1: Criar uma conta de armazenamento

1. No portal do Azure, a Contoso cria uma conta de armazenamento (**contosodevmigration**).
2. A conta são colocados em sua região secundária que utilizam para a ativação pós-falha - E.U.A. Central. Utilizam uma conta de standard para fins gerais com o armazenamento de redudant localmente.

    ![Conta de armazenamento](./media/contoso-migration-tfs-vsts/storage1.png) 


**Precisa de mais ajuda?**

- [Introdução ao armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Passo 2: TFS de atualização

Contoso atualiza o seu servidor do TFS para o TFS 2018 Update 2. Antes de este começar:

- Transferem [TFS 2018 atualização 2](https://visualstudio.microsoft.com/downloads/)
- Verificam a [requisitos de hardware](https://docs.microsoft.com/tfs/server/requirements)e leitura por meio da [notas de versão](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) e [atualizar armadilhas](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

Atualização da seguinte forma:

1. Para começar, eles fazer cópias de segurança do seu servidor do TFS (em execução numa VMware vM) e tirar um instantâneo de VMware.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. O instalador do TFS é iniciado, e escolherem a localização de instalação. O instalador tem acesso à internet.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. Após a instalação terminar, inicia o Assistente de configuração do servidor.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Após a verificação, o Assistente de atualização estiver concluída.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. Contoso verifica se a instalação do TFS, revendo os projetos, itens de trabalho e código.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Algumas atualizações do TFS tem de executar o Assistente para configurar funcionalidades após a conclusão da atualização. [Saiba mais](https://docs.microsoft.com/vsts/work/customize/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**Precisa de mais ajuda?**

Saiba mais sobre [atualizar o TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Passo 3: Verificar a coleção de TFS

Contoso é executado a ferramenta de migração do TFS no banco de dados de coleção ContosoDev para validá-la antes da migração.

1. Contoso transfere e unzips a [ferramenta de migração do TFS](https://www.microsoft.com/download/details.aspx?id=54274). É importante transferir a versão para a atualização do TFS que está a executar. A versão pode ser verificada na consola de administração.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. Se executarem a ferramenta para efetuar a validação, especificando o URL da coleção de projeto de equipe:

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. A ferramenta mostra um erro.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Eles localizado o registo de ficheiros estão localizados na **registos** pasta, imediatamente antes da localização de ferramenta. Um arquivo de log é gerado para cada validação principais. **TfsMigration.log** contém a informação principal.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Contoso encontra nesta entrada, relacionados com a identidade.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Contoso é executado **TfsMigration validar /help** na linha de comando e vê que o comando **/tenantDomainName** parece ser necessária para validar as identidades.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Execute novamente o comando de validação e inclua este valor, juntamente com o nome do Azure AD: **TfsMigrator validar /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Aparece um início de sessão do Azure AD no ecrã e introduza as credenciais de um utilizador de Administrador Global.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. A validação passa e ser confirmado pela ferramenta.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Passo 4: Criar os ficheiros de migração

Com a validação completa, Contoso pode usar a ferramenta de migração do TFS para criar os ficheiros de migração.

1. Eles são executados o passo de preparação na ferramenta.

    **TfsMigrator preparar /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Preparação](./media/contoso-migration-tfs-vsts/prep1.png)

    Preparar o seguinte procedimento:
    - Examina a coleção para encontrar uma lista de todos os utilizadores e preenche o registo de mapa de identificação (**IdentityMapLog.csv**])
    - Prepara a ligação ao Azure Active Directory para localizar uma correspondência para cada identidade.
    - Contoso já implementada do Azure AD e foram sincronizados com o AD Connect, para que preparar deve ser capaz de encontrar as identidades correspondentes e marcá-los como o Active Directory.

2. Aparece um início de sessão do Azure AD no ecrã e Contoso insere as credenciais de Administrador Global.

    ![Preparação](./media/contoso-migration-tfs-vsts/prep2.png)

3. Preparar estiver concluída, e a ferramenta de relatórios que os ficheiros de importação foi gerados com êxito.

    ![Preparação](./media/contoso-migration-tfs-vsts/prep3.png)

4. Contoso agora pode ver que o IdentityMapLog.csv e o ficheiro de import.json foram criados numa nova pasta.

    ![Preparação](./media/contoso-migration-tfs-vsts/prep4.png)

5. O ficheiro de import.json fornece importar as definições. Ele inclui informações como o nome de conta pretendida e informações de conta de armazenamento. A maioria dos campos é preenchida automaticamente. Alguns campos necessária intervenção do utilizador. Contoso abre o arquivo e adiciona o nome da conta VSTS a ser criado: **contosodevmigration**. Com este nome será o URL do VSTS **contosodevmigration.visualstudio.com**.

    ![Preparação](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > A conta tem de ser criada antes da migração, pode ser alterado após a migração é efetuada.

6. Eles analisam o ficheiro de mapa de registo de identidade que mostra as contas que serão apresentadas no VSTS durante a importação. 

    - Identidades do Active Directory referem-se às identidades que irão tornar-se os utilizadores no VSTS após a importação.
    - No VSTS, estas identidades serão licenciadas e apresentado como um utilizador na conta após a migração.
    - Estas identidades estão marcadas como **Active Directory** no **estado da importação esperado** coluna no ficheiro.

    ![Preparação](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-vsts"></a>Passo 5: Migrar para o VSTS

Com a preparação funcionando, Contoso pode agora concentrar-se na migração. Depois de executar a migração, irá mudar de utilizar o TFVC para Git para controle de versão.

Antes de este começar, Contoso agenda de tempo de inatividade com a equipe de desenvolvimento, para tirar a coleção de offline para migração. Estes são os passos para o processo de migração:

1. **Anular a exposição a coleção**: dados de identidade para a coleção residem na base de dados de configuração de servidor do TFS, enquanto a coleção está anexado e online. Quando uma coleção é desligada do servidor do TFS, ele faz uma cópia desses dados de identidade e empacota com a coleção para o transporte. Sem esses dados, não é possível executar a parte de identidade da importação. Recomenda-se que a coleção permanecem desligado até que a importação foi concluída, porque não existe nenhuma forma de importar as alterações que ocorreram durante a importação.
2. **Gerar uma cópia de segurança**: A próxima etapa do processo de migração é gerar uma cópia de segurança que pode ser importada para o VSTS. Camada de dados aplicação componente pacotes (DACPAC), é uma funcionalidade do SQL Server que permite que as alterações de base de dados ser empacotados num único arquivo e implantados com outras instâncias do SQL. Ele também pode ser restaurado diretamente para o VSTS e, portanto, é utilizado como o método de empacotamento para obtenção de dados de coleção para a cloud. Contoso usará a ferramenta de SqlPackage.exe para gerar o DACPAC. Essa ferramenta está incluída no SQL Server Data Tools.
3. **Carregar para o armazenamento**: após DACPAC é criado, eles carregá-lo ao armazenamento do Azure. Depois de serem carregado, eles recebem uma assinatura de acesso partilhado (SAS), para permitir o acesso de ferramenta de migração do TFS para o armazenamento.
4. **Preencha a importação**: Contoso, em seguida, pode preencher campos em falta no ficheiro de importação, incluindo a definição de DACPAC. Para começar, vai especificar que quer fazer uma **DryRun** importação, para verificar que tudo o que está a funcionar corretamente antes da migração completa.
5. **Fazer uma execução completa**: execução imports ajudar a testar a migração de coleção. Execuções de dry limitaram a vida e são eliminadas antes de uma execução de uma migração de produção. Sendo excluído automaticamente após um determinado período de tempo. Uma observação sobre o quando a execução será eliminada está incluída no e-mail de êxito recebido depois de concluída a importação. Tome nota e planeie em conformidade.
6. **Concluir a migração de produção**: com a migração de execução foi concluída, o Contoso faz a migração final ao atualizar o import.json e executar novamente a importação.



### <a name="detach-the-collection"></a>Anular a exposição a coleção

Antes de iniciar, Contoso utiliza uma cópia de segurança local do SQL Server e VMware instantâneo do servidor TFS, antes de desligar.

1.  Na consola de administração do TFS, selecionar a coleção que pretendem desligar (**ContosoDev**).

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate1.png)

2. Na **gerais**, eles selecionam **desanexar coleção**

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate2.png)

3. No Assistente de coleção a do projeto de equipe desanexar > **manutenção mensagem**, elas fornecem uma mensagem para os utilizadores que poderão tentar ligar a projetos na coleção.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate3.png)

4. No **progresso da Desanexação**, monitorize o progresso e clique em **próxima** quando o processo estar concluído.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate4.png)

5. Na **verificações de preparação**, quando verificações concluir, clique em **anulação de exposições**.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate5.png)

6. Clicarem **fechar** para concluir a cópia de segurança.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate6.png)

6. A coleção já não é referenciada na consola de administração do TFS.

    ![Migrar](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Gerar um DACPAC

Contoso cria uma cópia de segurança (DACPAC) para importação no VSTS.

- SqlPackage.exe no SQL Server Data Tools, é utilizado para criar o DACPAC. Existem várias versões do SqlPackage.exe instalado com o SQL Server Data Tools, localizados em pastas com nomes como 140, 130 e 120. É importante utilizar a versão correta para preparar o DACPAC.
- Importações de 2018 do TFS tem de utilizar SqlPackage.exe a partir da pasta 140 ou superior.  Para CONTOSOTFS, este ficheiro está localizado na pasta: **C:\Program Files (x86) \Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Contoso gera o DACPAC da seguinte forma:

1. Eles abra uma linha de comandos e navegue para a localização de SQLPackage.exe. Eles escreva este comando seguinte para gerar o DACPAC:

    **SqlPackage.exe /sourceconnectionstring: "origem de dados = SQLSERVERNAME\INSTANCENAME; Initial Catalog = Tfs_ContosoDev; Integrated Security = True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData = VERDADEIRO/de p: eu gnoreUserLoginMappings = true /p:IgnorePermissions = true /p:Storage = memória** 

    ![Cópia de segurança](./media/contoso-migration-tfs-vsts/backup1.png)

2. A seguinte mensagem aparece depois do comando é executado.

    ![Cópia de segurança](./media/contoso-migration-tfs-vsts/backup2.png)

3. Verificam as propriedades do DACPACfile

    ![Cópia de segurança](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Atualizar o ficheiro para o armazenamento

Depois do DACPAC é criado, o Contoso carrega-o para o armazenamento do Azure.

1. Eles [transferir e instalar](https://azure.microsoft.com/features/storage-explorer/) Explorador de armazenamento do Azure.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup5.png)

4. Eles ligar à sua subscrição e localize a conta de armazenamento que criaram para a migração (**contosodevmigration**). Eles criam um novo contentor de BLOBs **vstsmigration**.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup6.png)

5. Especifica o ficheiro DACPAC para carregamento como um blob de blocos.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. Depois de carregar o ficheiro, pode clicar em nome do ficheiro > **gerar SAS**. Expanda os contentores de BLOBs na conta de armazenamento, selecione o contentor com os ficheiros de importar e clique em **obter assinatura de acesso partilhado**.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup8.png)

8. Eles aceite as predefinições e clique em **criar**. Isso permite o acesso a durante 24 horas.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup9.png)

9. Eles copie o URL de assinatura de acesso partilhado, para que possa ser utilizado pela ferramenta de migração do TFS.

    ![Carregar](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> A migração deve ocorrer antes no tempo permitido irão expirar a janela ou permissões.
> Não gere uma chave de SAS do portal do Azure. Chaves geradas como essa são com âmbito de conta e não funcionarão com a importação.

### <a name="fill-in-the-import-settings"></a>Preencha as definições de importação

Anteriormente, Contoso parcialmente preenchida o ficheiro de especificação de importação (import.json). Agora, precisa de adicionar as restantes definições.

Abra o ficheiro de import.json e preencha os campos seguintes: • localização: localização da chave SAS que tenha sido criado acima.
• Dacpac: definir o nome para o ficheiro DACPAC carregou para a conta de armazenamento. Inclua a extensão ". dacpac".
• ImportType: definido como DryRun por agora.


![Importar as definições](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Efetuar uma migração de execução

Contoso começar com uma migração de execução, para se certificar de que está tudo a funcionar conforme esperado.

1. Abra uma linha de comandos e localizar para a localização de TfsMigration (C:\TFSMigrator).
2. Como primeiro passo de validar o ficheiro de importação. Eles querem para garantir que o ficheiro é formatado corretamente e que a chave SAS está a funcionar.

    **TfsMigrator importar /importFile:C:\TFSMigrator\import.json /validateonly**

3. A validação devolve um erro que a chave SAS tem mais tempo de expiração.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test1.png)

3. Eles usam o Explorador de armazenamento do Azure para criar uma nova chave SAS com sete dias de expiração.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test2.png)

3. Eles atualizar o ficheiro de import.json e execute novamente a validação. Desta vez ele for concluída com êxito.

    **TfsMigrator importar /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Execução completa](./media/contoso-migration-tfs-vsts/test3.png)
    
7. São iniciados a execução:

    **TfsMigrator importação /importFile:C:\TFSMigrator\import.json**

8. Uma mensagem é emitida para confirmar a migração. Tenha em atenção o período de tempo para o qual os dados em etapas serão mantidos após a execução.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test4.png)

9. O Azure AD de sessão aparece e deve ser a conclusão com o início de sessão do administrador da Contoso.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test5.png)

10. Uma mensagem mostra informações sobre a importação.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test6.png)

11. Após 15 minutos ou menos, o Contoso navega para o URL e verá as seguintes informações:

     ![Execução completa](./media/contoso-migration-tfs-vsts/test7.png)

12. Após a migração é concluída uma sinais de oportunidades potenciais de desenvolvimento do Contoso no VSTS para verificar se a execução funcionou adequadamente. Após a autenticação, o VSTS precisa de alguns detalhes para confirmar a conta.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test8.png)

13. No VSTS, o líder de desenvolvimento pode ver que os projetos foram migrados para o VSTS. Há um aviso a indicar que a conta será eliminada dentro de 15 dias.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test9.png)

14. O líder de desenvolvimento abre um dos projetos e abre **itens de trabalho** > **que me são atribuídos**. Isto mostra que os dados de item de trabalho tem sido migrados, juntamente com a sua identidade.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test10.png)

15. Ele também verifica se existem outros projetos e código, para confirmar que o código-fonte e o histórico foi migrada.

    ![Execução completa](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Executar a migração de produção

Com a execução completa, Contoso passar para a migração de produção. Eliminar a execução, Atualize as definições de importação e execute novamente a importação.

1. No portal do VSTS, eles elimina a conta de execução.
2. Eles atualizam o ficheiro de import.json para definir o **ImportType** ao **ProductionRun**.

    ![Produção](./media/contoso-migration-tfs-vsts/full1.png)

3. Eles iniciar a migração como eram válidas para a execução: **TfsMigrator importação /importFile:C:\TFSMigrator\import.json**.
4. Uma mensagem mostra para confirmar a migração e avisa que dados poderá ser mantidos numa localização segura como uma área de transição até sete dias.

    ![Produção](./media/contoso-migration-tfs-vsts/full2.png)

5. No Azure AD de sessão, a Contoso Especifica um administrador da Contoso início de sessão.

    ![Produção](./media/contoso-migration-tfs-vsts/full3.png)

6. Uma mensagem mostra informações sobre a importação.

    ![Produção](./media/contoso-migration-tfs-vsts/full4.png)

7. Após cerca de 15 minutos, Contoso navega para o URL e verá as seguintes informações:

    ![Produção](./media/contoso-migration-tfs-vsts/full5.png)

8. Após a conclusão da migração um logon de oportunidades potenciais de desenvolvimento do Contoso de mensagens em fila no VSTS para verificar se a migração funcionou adequadamente. Após o início de sessão, he podem vê que projetos tenham sido migrados.

    ![Produção](./media/contoso-migration-tfs-vsts/full6.png)

8. O líder de desenvolvimento abre um dos projetos e abre **itens de trabalho** > **que me são atribuídos**. Isto mostra que os dados de item de trabalho tem sido migrados, juntamente com a sua identidade.

    ![Produção](./media/contoso-migration-tfs-vsts/full7.png)

9. Ele verifica os outros dados de item de trabalho para confirmar.

    ![Produção](./media/contoso-migration-tfs-vsts/full8.png)

15. Ele também verifica se existem outros projetos e código, para confirmar que o código-fonte e o histórico foi migrada.

    ![Produção](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Mover o controlo de origem de TFVC para GIT

Com a migração é concluída, a Contoso quer mover-se de TFVC para Git para a gestão de código de origem. Terá de importar o código-fonte atualmente na respetiva conta do VSTS como repositórios de Git na mesma conta.

1. No portal do VSTS, abrem-se um dos repositórios de TFVC (**$/ PolicyConnect**) e revê-lo.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Clicarem a **origem** menu pendente > **importação**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. Na **tipo de origem** selecionarem **TFVC**e especifique o caminho para o repositório. Eles decidiu por não migrar o histórico.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > Devido às diferenças em como TFVC e Git armazenam informações de controle de versão, é recomendável para não migrar o histórico. Essa é a abordagem que a Microsoft fez quando ele migrados Windows e outros produtos do controlo de versões centralizado para Git.

4. Após a importação, a Contoso analisa o código.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. Eles Repita o processo para o repositório de segundo (**$/ SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Depois de rever a origem, o desenvolvimento leva concorda que a migração para o VSTS é feita. VSTS agora torna-se a origem para todo o desenvolvimento entre as equipes envolvidas na migração.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**Precisa de mais ajuda?**

[Saiba mais](https://docs.microsoft.com/vsts/git/import-from-tfvc?view=vsts) sobre a importação de TFVC.

##  <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração é concluída, a Contoso precisa de fazer o seguinte:

- Reveja os [pós-importação de](https://docs.microsoft.com/vsts/articles/migration-post-import?view=vsts) artigo para obter informações sobre as atividades de importação.
- Ou eliminar os repositórios TFVC ou colocá-los no modo só de leitura. O código bases não podem usado, mas pode ser referenciado para o respetivo histórico.

## <a name="next-steps"></a>Passos Seguintes

Contoso tem de fornecer formação VSTS e o Git para os membros da Equipe relevantes.



