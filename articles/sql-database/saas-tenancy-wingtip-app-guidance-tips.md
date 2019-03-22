---
title: Orientações para o exemplo de aplicação multi-inquilino de base de dados SQL - Wingtip SaaS | Documentos da Microsoft
description: Fornece etapas e instruções para instalar e executar a aplicação multi-inquilino de exemplo que utiliza o SQL Database do Azure, o exemplo de Wingtip Tickets SaaS.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 758cb47760f4a15e262a4d682089ac7d9fee64e8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900510"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Orientações gerais para trabalhar com Wingtip Tickets aplicações SaaS de exemplo

Este artigo contém orientações gerais para executar aplicações de SaaS de exemplo Wingtip Tickets que utilizam a base de dados do Azure SQL. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Transferir e os scripts de Wingtip Tickets SaaS de desbloqueio

Conteúdo executável (scripts, dlls) pode estar bloqueado por Windows quando arquivos zip são transferidos a partir de uma origem externa e extraídos. Quando extrair os scripts de um arquivo zip, **siga os passos abaixo para desbloquear o ficheiro. zip antes de extrair**. Isto garante que os scripts podem ser executados.

1. Navegue para o repositório do GitHub de SaaS Wingtip Tickets para o padrão de inquilinos de base de dados que deseja explorar: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Clique em **clonar ou transferir**.
3. Clique em **transferir zip** e guarde o ficheiro.
4. O ficheiro zip com o botão direito e selecione **propriedades**. O nome do ficheiro zip corresponderá ao nome do repositório. (ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Sobre o **gerais** separador, selecione **desbloqueio**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Scripts estão localizados no *.... \\Módulos de aprendizagem* pasta.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Trabalhar com scripts do PowerShell de bilhetes Wingtip

Para tirar o máximo proveito do exemplo de que precisa para aprofundar os scripts fornecidos. Utilizar pontos de interrupção e siga os passos os scripts que executarem e examinar a forma como os diferentes padrões SaaS são implementados. Para seguir facilmente por meio de scripts e módulos fornecidos para a melhor compreensão, recomendamos que utilize o [ISE do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualizar o ficheiro de configuração para a sua implementação

Editar a **UserConfig.psm1** ficheiro com o valor de utilizador e grupo de recursos que definir durante a implementação:

1. Abra o *ISE do PowerShell* e carregar... \\Módulos de aprendizagem\\*UserConfig.psm1* 
2. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
3. Guarde as alterações!

Definir esses valores aqui simplesmente evita que tenha que atualizar estes valores específicos da implementação em todos os scripts.

### <a name="execute-the-scripts-by-pressing-f5"></a>Executar scripts ao premir F5

Vários scripts utilizam *$PSScriptRoot* para navegar de pastas, e *$PSScriptRoot* é avaliada apenas quando os scripts são executados, premindo **F5**.  Destacar e executar uma seleção (**F8**) pode resultar em erros, por isso, prima **F5** ao executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Analisar os scripts para examinar a implementação

É a melhor maneira de entender os scripts, execute-os para ver o que fazer. Confira o incluído **Demo -** scripts que apresentam uma fácil de seguir o fluxo de trabalho de alto nível. O **Demo -** scripts mostram os passos necessários para realizar cada tarefa, então, definir pontos de interrupção e fazer uma busca mais aprofundada para as chamadas individuais para ver detalhes de implementação para os diferentes padrões SaaS.

Dicas para explorar e passo a passo scripts do PowerShell:

- Open **Demo -** scripts no ISE do PowerShell.
- Execute ou continue com **F5** (utilizar **F8** não é aconselhável porque *$PSScriptRoot* não é avaliado quando se executam seleções de um script).
- Para colocar os pontos de interrupção, clique ou selecione uma linha e prima **F9**.
- Para executar o próximo passo numa chamada de função ou script, utilize **F10**.
- Para avançar para uma chamada de função ou script, utilize **F11**.
- Para ir até ao fim do procedimento da chamada de função ou script atual, utilize **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de base de dados e executar consultas SQL com o SSMS

Uso [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se ligar e procurar os servidores de aplicações e bases de dados.

A implementação tem inicialmente inquilinos e os servidores de base de dados SQL de catálogo para ligar a. A nomenclatura dos servidores depende o padrão de inquilinos de base de dados (veja abaixo para obter informações específicas). 

   - **Aplicação autónoma:** servidores para cada inquilino (ex. *contosoconcerthall -&lt;usuário&gt;*  server) e *catálogo-sa -&lt;utilizador&gt;*
   - **Base de dados por inquilino:** *tenants1-dpt -&lt;utilizador&gt;*  e *catálogo-dpt -&lt;utilizador&gt;*  servidores
   - **Base de dados do multi-inquilino:** *tenants1-mt -&lt;utilizador&gt;*  e *catálogo-mt -&lt;utilizador&gt;*  servidores

Para garantir uma ligação de demonstração com êxito, todos os servidores têm uma [regra de firewall](sql-database-firewall-configure.md) permitindo que todos os IPs por meio de.


1. Open *SSMS* e ligue-se para os inquilinos. O nome do servidor depende o padrão de inquilinos de base de dados que selecionou (veja abaixo para obter informações específicas):
    - **Aplicação autónoma:** servidores de inquilinos individuais (ex. *contosoconcerthall-&lt;User&gt;.database.windows.net*) 
    - **Base de dados por inquilino:** *tenants1-dpt -&lt;utilizador&gt;. database.windows.net*
    - **Base de dados do multi-inquilino:** *tenants1-mt -&lt;utilizador&gt;. database.windows.net* 
2. Clique em **Ligar** > **Motor de Base de Dados...**:

   ![servidor de catálogo](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. As credenciais de demonstração são: Início de sessão = *desenvolvedor*, palavra-passe = *P\@ssword1*

    A imagem abaixo demonstra o início de sessão para o *base de dados por inquilino* padrão. 
    ![ligação](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Repita os passos 2 a 3 e ligue-se ao servidor de catálogo (veja abaixo para nomes de servidor específico com base no padrão de inquilinos na base de dados selecionado)
    - **Aplicação autónoma:** *catálogo-sa -&lt;utilizador&gt;. database.windows.net*
    - **Base de dados por inquilino:** *catálogo-dpt -&lt;utilizador&gt;. database.windows.net*
    - **Base de dados do multi-inquilino:** *catálogo-mt -&lt;utilizador&gt;. database.windows.net*


Depois de ligar com êxito, verá todos os servidores. Sua lista de bases de dados pode ser diferente, consoante os inquilinos que aprovisionou.

A imagem abaixo demonstra o início de sessão para o *base de dados por inquilino* padrão.

![explorador de objetos](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Passos Seguintes
- [Implementar a aplicação de SaaS autónomo de bilhetes Wingtip](saas-standaloneapp-get-started-deploy.md)
- [Implementar a base de dados da SaaS de bilhetes Wingtip por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md)
- [Implementar a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)

