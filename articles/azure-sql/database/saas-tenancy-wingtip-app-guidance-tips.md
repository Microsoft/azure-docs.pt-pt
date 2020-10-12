---
title: Exemplo de app multi-inquilino - Wingtip SaaS
description: Fornece passos e orientações para a instalação e execução da aplicação multi-inquilino de amostra que utiliza a Base de Dados Azure SQL, o exemplo de Wingtip Tickets SaaS.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84051886"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Orientação geral para trabalhar com os bilhetes wingtip amostra aplicativos SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo contém orientações gerais para a execução das aplicações SaaS da Amostra de Bilhetes wingtip que utilizam a Base de Dados Azure SQL.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Descarregue e desbloqueie os scripts SaaS dos Bilhetes Wingtip

Os conteúdos executáveis (scripts, dlls) podem ser bloqueados pelo Windows quando os ficheiros zip são descarregados de uma fonte externa e extraídos. Ao extrair os scripts de um ficheiro zip, **siga os passos abaixo para desbloquear o ficheiro .zip antes de extrair**. Isto garante que os scripts podem ser executados.

1. Navegue pelos bilhetes de ponta da Asa SaaS GitHub para o padrão de arrendamento de base de dados que deseja explorar:
    - [WingtipTicketsSaa-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaA-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaa-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Clique **em Clone ou descarregue.**
3. Clique **em Baixar o fecho** e guarde o ficheiro.
4. Clique com o botão direito no ficheiro zip e selecione **Propriedades**. O nome do ficheiro zip corresponderá ao nome do repo. (ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_
5. No **separador Geral,** selecione **Desbloqueie**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Os scripts estão localizados no *.. \\ Pasta de Módulos de Aprendizagem.*


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Trabalhando com os scripts PowerShell dos bilhetes wingtip

Para tirar o máximo partido da amostra, é necessário mergulhar nos scripts fornecidos. Use breakpoints e passe pelos scripts à medida que executam e examine como os diferentes padrões SaaS são implementados. Para passar facilmente pelos scripts e módulos fornecidos para melhor compreensão, recomendamos a utilização do [PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualize o ficheiro de configuração para a sua implementação

Editar o ficheiro **UserConfig.psm1** com o grupo de recursos e o valor do utilizador que definiu durante a implementação:

1. Abra o *PowerShell ISE* e carregue... \\ Módulos de Aprendizagem \\ *UserConfig.psm1*
2. Atualizar O Nome e *Nome* *do Grupo de Recursos* com os valores específicos para a sua implantação (apenas nas linhas 10 e 11).
3. Guarde as alterações!

Definir estes valores aqui simplesmente impede que você tenha que atualizar estes valores específicos de implementação em cada script.

### <a name="execute-the-scripts-by-pressing-f5"></a>Execute os scripts pressionando F5

Vários scripts usam *$PSScriptRoot* para navegar em pastas, e *$PSScriptRoot* só é avaliado quando os scripts são executados pressionando **F5**.Realçar e executar uma seleção **(F8)** pode resultar em erros, por isso prima **F5** ao executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Analisar os scripts para examinar a implementação

A melhor maneira de entender os guiões é pisando-os para ver o que fazem. Confira os scripts **Demo** incluídos que apresentam um fluxo de trabalho de alto nível. Os scripts **Demo** mostram os passos necessários para realizar cada tarefa, por isso definir pontos de rutura e perfurar mais profundamente as chamadas individuais para ver detalhes de implementação para os diferentes padrões SaaS.

Dicas para explorar e pisar os scripts PowerShell:

- Open **Demo-** scripts no PowerShell ISE.
- Execute ou continue com **F5** (a utilização **de F8** não é aconselhada porque *$PSScriptRoot* não é avaliada ao executar seleções de um script).
- Para colocar os pontos de interrupção, clique ou selecione uma linha e prima **F9**.
- Para executar o próximo passo numa chamada de função ou script, utilize **F10**.
- Para avançar para uma chamada de função ou script, utilize **F11**.
- Para ir até ao fim do procedimento da chamada de função ou script atual, utilize **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de base de dados e executar consultas SQL com o SSMS

Utilize [o SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para ligar e navegar nos servidores de aplicações e bases de dados.

A implementação inicialmente tem inquilinos e servidores de catálogo para se conectar. O nome dos servidores depende do padrão de arrendamento da base de dados (ver abaixo para especificidades).

   - **Aplicação autónoma:** servidores para cada inquilino (ex. *contosoconcerthall- &lt; Servidor &gt; do utilizador)* e *catálogo-sa-Utilizador &lt; &gt; *
   - **Base de dados por inquilino:** *inquilinos1-dpt- &lt; Servidores de utilizador &gt; * e *catálogo-dpt- &lt; Servidores de utilizadores &gt; *
   - **Base de dados multi-inquilino:** *inquilinos1-mt- &lt; Servidores de utilizador &gt; * e *catálogo-mt-user &lt; &gt; *

Para garantir uma ligação de demonstração bem sucedida, todos os servidores têm uma regra de [firewall](firewall-configure.md) que permite a passagem de todos os IPs.


1. Abra *a SSMS* e ligue-se aos inquilinos. O nome do servidor depende do padrão de arrendamento da base de dados que selecionou (ver abaixo para especificações):
    - **Aplicação autónoma:** servidores de inquilinos individuais (ex. *contosoconcerthall- &lt; Utilizador &gt; .database.windows.net*)
    - **Base de dados por inquilino:** *inquilinos1-dpt- &lt; Utilizador &gt; .database.windows.net*
    - **Base de dados multi-inquilino:** *inquilinos1-mt- &lt; Utilizador &gt; .database.windows.net*
2. Clique **no**  >  **motor de base de dados de ligação...**

   ![servidor de catálogo](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Credenciais de demonstração são: Login = *desenvolvedor*, Password = *P \@ ssword1*

    A imagem abaixo demonstra o login para a *Base de Dados por* padrão de inquilino.
    ![conexão](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Repita os passos 2-3 e ligue-se ao servidor do catálogo (ver abaixo para nomes específicos do servidor com base no padrão de arrendamento da base de dados selecionado)
    - **Aplicação autónoma:** *catalog-sa- &lt; User &gt; .database.windows.net*
    - **Base de dados por inquilino:** *catalog-dpt- &lt; User &gt; .database.windows.net*
    - **Base de dados multi-inquilino:** *catalog-mt- &lt; User &gt; .database.windows.net*


Depois de uma ligação com sucesso, deverá ver todos os servidores. A sua lista de bases de dados pode ser diferente, dependendo dos inquilinos que adquirou.

A imagem abaixo demonstra o login para a *Base de Dados por* padrão de inquilino.

![explorador de objetos](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Passos seguintes
- [Implementar a aplicação de ingressos saas de ponta saas](../../sql-database/saas-standaloneapp-get-started-deploy.md)
- [Implementar a Base de Dados saaS de Ingressos de Ponta de Asa por pedido de Inquilino](../../sql-database/saas-dbpertenant-get-started-deploy.md)
- [Implementar a aplicação de base de dados de multi-inquilinos Wingtip SaaS](../../sql-database/saas-multitenantdb-get-started-deploy.md)

