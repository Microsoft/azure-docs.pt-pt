---
title: Exemplo de aplicativo multilocatário – Wingtip SaaS
description: Fornece etapas e orientações para instalar e executar o aplicativo de multilocatário de exemplo que usa o banco de dados SQL do Azure, o exemplo de SaaS do Wingtip tickets.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132292"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Diretrizes gerais para trabalhar com aplicativos SaaS de exemplo do Wingtip tickets

Este artigo contém diretrizes gerais para executar os aplicativos SaaS de exemplo Wingtip tickets que usam o banco de dados SQL do Azure.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Baixar e desbloquear os scripts SaaS do Wingtip tickets

Conteúdos executáveis (scripts, DLLs) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, **siga as etapas abaixo para desbloquear o arquivo. zip antes de extrair**. Isso garante que os scripts tenham permissão para serem executados.

1. Navegue até o repositório GitHub do Wingtip tickets SaaS para o padrão de aluguel de banco de dados que você deseja explorar:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Clique em **clonar ou baixar**.
3. Clique em **baixar zip** e salve o arquivo.
4. Clique com o botão direito do mouse no arquivo zip e selecione **Propriedades**. O nome do arquivo zip corresponderá ao nome do repositório. Estendi. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na guia **geral** , selecione **desbloquear**.
6. Clique em **OK**.
7. Extraia os arquivos.

Os scripts estão localizados na pasta *..\\módulos de aprendizado* .


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Trabalhando com os scripts do PowerShell do Wingtip tickets

Para obter o máximo do exemplo, você precisa se aprofundar nos scripts fornecidos. Use pontos de interrupção e percorra os scripts à medida que eles são executados e examine como os diferentes padrões de SaaS são implementados. Para percorrer facilmente os scripts e módulos fornecidos para obter a melhor compreensão, recomendamos o uso do [ISE do PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualizar o arquivo de configuração para sua implantação

Edite o arquivo **userconfig. psm1** com o grupo de recursos e o valor do usuário que você definiu durante a implantação:

1. Abra o *ISE do PowerShell* e carregue...\\módulos de aprendizado\\*userconfig. psm1*
2. Atualize *ResourceGroupName* e *nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
3. Salve as alterações!

Definir esses valores aqui simplesmente evita que você precise atualizar esses valores específicos da implantação em cada script.

### <a name="execute-the-scripts-by-pressing-f5"></a>Execute os scripts pressionando F5

Vários scripts usam *$PSScriptRoot* para navegar em pastas e *$PSScriptRoot* só é avaliada quando os scripts são executados pressionando **F5**.  Realçar e executar uma seleção (**F8**) pode resultar em erros; portanto, pressione **F5** ao executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Analisar os scripts para examinar a implementação

A melhor maneira de entender os scripts é percorrendo-os para ver o que eles fazem. Confira os scripts de **demonstração** incluídos que apresentam um fluxo de trabalho de alto nível fácil de seguir. Os scripts de **demonstração** mostram as etapas necessárias para realizar cada tarefa, portanto, defina pontos de interrupção e aprofunde-se nas chamadas individuais para ver os detalhes de implementação para os diferentes padrões de SaaS.

Dicas para explorar e percorrer os scripts do PowerShell:

- Abrir scripts de **demonstração** no ISE do PowerShell.
- Execute ou continue com **F5** (não é recomendável usar **F8** porque *$PSScriptRoot* não é avaliada ao executar seleções de um script).
- Para colocar os pontos de interrupção, clique ou selecione uma linha e prima **F9**.
- Para executar o próximo passo numa chamada de função ou script, utilize **F10**.
- Para avançar para uma chamada de função ou script, utilize **F11**.
- Para ir até ao fim do procedimento da chamada de função ou script atual, utilize **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de base de dados e executar consultas SQL com o SSMS

Use o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se conectar e procurar os servidores de aplicativos e bancos de dados.

A implantação inicialmente tem locatários e servidores de banco de dados SQL de catálogo para se conectar. A nomenclatura dos servidores depende do padrão de aluguel do banco de dados (veja abaixo os detalhes específicos).

   - **Aplicativo autônomo:** servidores para cada locatário (por exemplo, *contosoconcerthall-&lt;servidor de&gt;de usuário* ) e o *usuário Catalog-sa-&lt;&gt;*
   - **Banco de dados por locatário:** *tenants1-dpt-&lt;&gt;* de usuário e *Catálogo-DPT-&lt;usuário&gt;* servidores
   - **Banco de dados multilocatário:** *tenants1-mt-&lt;&gt;* do usuário e *Catalog-MT-&lt;usuários&gt;* servidores

Para garantir uma conexão de demonstração bem-sucedida, todos os servidores têm uma [regra de firewall](sql-database-firewall-configure.md) que permite todos os IPs por meio do.


1. Abra o *SSMS* e conecte-se aos locatários. O nome do servidor depende do padrão de aluguel de banco de dados que você selecionou (veja abaixo as especificações):
    - **Aplicativo autônomo:** servidores de locatários individuais (por exemplo, *contosoconcerthall-&lt;User&gt;.database.windows.net*)
    - **Banco de dados por locatário:** *tenants1-DPT-&lt;usuário&gt;. Database.Windows.net*
    - **Banco de dados multilocatário:** *tenants1-MT-&lt;usuário&gt;. Database.Windows.net*
2. Clique em **Ligar** > **Motor de Base de Dados...** :

   ![servidor de catálogo](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. As credenciais de demonstração são: login = *Developer*, password = *P\@ssword1*

    A imagem abaixo demonstra o logon do padrão de *banco de dados por locatário* .
    ![ligação](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Repita as etapas 2-3 e conecte-se ao servidor de catálogo (veja abaixo os nomes de servidor específicos com base no padrão de aluguel de banco de dados selecionado)
    - **Aplicativo autônomo:** *Catalog-SA-&lt;usuário&gt;. Database.Windows.net*
    - **Banco de dados por locatário:** *Catalog-DPT-&lt;usuário&gt;. Database.Windows.net*
    - **Banco de dados multilocatário:** *Catalog-MT-&lt;usuário&gt;. Database.Windows.net*


Depois de se conectar com êxito, você deverá ver todos os servidores. Sua lista de bancos de dados pode ser diferente, dependendo dos locatários que você provisionou.

A imagem abaixo demonstra o logon para o padrão de *banco de dados por locatário* .

![explorador de objetos](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Passos seguintes
- [Implantar o aplicativo autônomo SaaS Wingtip tickets](saas-standaloneapp-get-started-deploy.md)
- [Implantar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md)
- [Implantar o aplicativo de banco de dados multilocatário SaaS Wingtip tickets](saas-multitenantdb-get-started-deploy.md)

