---
title: Exemplo de aplicativo multi-inquilino - Wingtip SaaS
description: Fornece passos e orientações para a instalação e execução da aplicação de multi-inquilinos da amostra que utiliza a Base de Dados Azure SQL, o exemplo wingtip Tickets SaaS.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 4c3a141525f650d6e254f79ce9e3396f2c35a95f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051886"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Orientação geral para trabalhar com bilhetes wingtip amostra apps SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo contém orientações gerais para executar as aplicações SaaS da amostra de bilhetes Wingtip que utilizam a Base de Dados Azure SQL.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Descarregue e desbloqueie os scripts SaaS bilhetes wingtip

Os conteúdos executáveis (scripts, dlls) podem ser bloqueados pelo Windows quando os ficheiros zip são descarregados de uma fonte externa e extraídos. Ao extrair os scripts de um ficheiro zip, **siga os passos abaixo para desbloquear o ficheiro .zip antes**de extrair . Isto garante que os scripts são autorizados a ser executados.

1. Navegue no Wingtip Tickets SaaS GitHub repo para o padrão de arrendamento de base de dados que deseja explorar:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Clique em **Clone ou faça o download.**
3. Clique em **Baixar zip** e guardar o ficheiro.
4. Clique no ficheiro zip e selecione **Propriedades**. O nome do ficheiro zip corresponderá ao nome do repo. (ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. No separador **Geral,** selecione **Desbloquear**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Os scripts estão localizados na *.. \\ Pasta de Módulos de Aprendizagem.*


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Trabalhando com os scripts PowerShell de bilhetes wingtip

Para tirar o máximo partido da amostra, é necessário mergulhar nos scripts fornecidos. Use breakpoints e passe pelos scripts enquanto executam e examinam como os diferentes padrões SaaS são implementados. Para passar facilmente pelos scripts e módulos fornecidos para a melhor compreensão, recomendamos a utilização do [PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualize o ficheiro de configuração para a sua implementação

Editar o ficheiro **UserConfig.psm1** com o grupo de recursos e o valor do utilizador que definiu durante a implementação:

1. Abra o *PowerShell ISE* e carregue... \\ Módulos de Aprendizagem \\ *UserConfig.psm1*
2. Atualizar *O Nome* e *Nome* do Grupo de Recursos com os valores específicos para a sua implementação (apenas nas linhas 10 e 11).
3. Guarde as alterações!

Definir estes valores aqui simplesmente impede que você tenha que atualizar estes valores específicos de implementação em cada script.

### <a name="execute-the-scripts-by-pressing-f5"></a>Execute os scripts pressionando F5

Vários scripts usam *$PSScriptRoot* para navegar em pastas, e *$PSScriptRoot* só é avaliado quando os scripts são executados pressionando **F5**.Realçar e executar uma seleção **(F8)** pode resultar em erros, por isso prima **F5** ao executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Analisar os scripts para examinar a implementação

A melhor maneira de entender os guiões é pisando neles para ver o que fazem. Confira os scripts de **Demo** incluídos que apresentam um fluxo de trabalho de alto nível fácil de seguir. Os scripts **Demo** mostram os passos necessários para realizar cada tarefa, de modo que detetete te pontos de rutura e perfure mais profundamente as chamadas individuais para ver detalhes de implementação para os diferentes padrões SaaS.

Dicas para explorar e pisar através dos scripts PowerShell:

- Open **Demo-** scripts no PowerShell ISE.
- Execute ou continue com **F5** (usar **F8** não é aconselhado porque *$PSScriptRoot* não é avaliado ao executar seleções de um script).
- Para colocar os pontos de interrupção, clique ou selecione uma linha e prima **F9**.
- Para executar o próximo passo numa chamada de função ou script, utilize **F10**.
- Para avançar para uma chamada de função ou script, utilize **F11**.
- Para ir até ao fim do procedimento da chamada de função ou script atual, utilize **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de base de dados e executar consultas SQL com o SSMS

Utilize o Estúdio de Gestão de [Servidores SQL (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para ligar e navegar nos servidores de aplicações e bases de dados.

A implementação inicialmente tem inquilinos e servidores de catálogo para ligar. O nome dos servidores depende do padrão de arrendamento da base de dados (ver abaixo para obter detalhes).

   - **Aplicação autónoma:** servidores para cada inquilino (ex. *Contosoconcerthall... &lt; Servidor &gt; de utilizador)* e *utilizador de &lt; &gt; catálogo-sa*
   - **Base de dados por inquilino:** *inquilinos1-dpt- &lt; Utilizador &gt; * e servidores de * &lt; utilizador &gt; de catálogo*
   - **Base de dados multi-inquilinos:** *inquilinos1-mt- &lt; &gt; User* e * &lt; &gt; servidores* de utilizador de catálogo

Para garantir uma ligação de demonstração bem sucedida, todos os servidores têm uma regra de [firewall](firewall-configure.md) que permite a todos os IPs passarem.


1. Abra *o SSMS* e ligue-se aos inquilinos. O nome do servidor depende do padrão de arrendamento da base de dados que selecionou (ver abaixo para obter detalhes):
    - **Aplicação autónoma:** servidores de inquilinos individuais (ex. *Contosoconcerthall... &lt; Utilizador &gt; .database.windows.net*)
    - **Base de dados por inquilino:** *inquilinos1-dpt- &lt; Utilizador &gt; .database.windows.net*
    - **Base de dados multi-inquilinos:** *inquilinos1-mt- &lt; Utilizador &gt; .database.windows.net*
2. Clique **em ligar**motor de base de  >  **dados...**

   ![servidor de catálogo](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. As credenciais de demonstração são: Login = *desenvolvedor*, Password = *P \@ ssword1*

    A imagem abaixo demonstra o login para a Base de Dados por padrão *de inquilino.*
    ![conexão](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Repita os passos 2-3 e ligue-se ao servidor de catálogo (ver abaixo os nomes específicos do servidor com base no padrão de arrendamento de base de dados selecionado)
    - **Aplicação autónoma:** *catálogo-sa- &lt; Utilizador &gt; .database.windows.net*
    - **Base de dados por inquilino:** *catalog-dpt- &lt; Utilizador &gt; .database.windows.net*
    - **Base de dados multi-inquilinos:** *catálogo-mt- &lt; Utilizador &gt; .database.windows.net*


Depois de ligar com sucesso, deve ver todos os servidores. A sua lista de bases de dados pode ser diferente, dependendo dos inquilinos que você disponibilizou.

A imagem abaixo demonstra o login para a Base de Dados por padrão *de inquilino.*

![explorador de objetos](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Próximos passos
- [Implementar a aplicação autónoma de bilhetes SaaS](../../sql-database/saas-standaloneapp-get-started-deploy.md)
- [Implementar a base de dados saaS de bilhetes wingtip por pedido de inquilino](../../sql-database/saas-dbpertenant-get-started-deploy.md)
- [Implementar a aplicação de base de dados de bilhetes SaaS Multi-inquilinos](../../sql-database/saas-multitenantdb-get-started-deploy.md)

