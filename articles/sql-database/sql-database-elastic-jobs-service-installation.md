---
title: Instalar tarefas elásticas da base de dados | Documentos da Microsoft
description: Instruções sobre a instalação da funcionalidade de tarefa elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 32df3e7ac6dc22e247bd4aecea4f39bf6d3a8017
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316901"
---
# <a name="installing-elastic-database-jobs-overview"></a>Instalação de descrição geral das tarefas de bases de dados elásticas

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



[**Tarefas de base de dados elásticas** ](sql-database-elastic-jobs-overview.md) pode ser instalado através do PowerShell ou através do portal do Azure. Pode obter acesso para criar e gerir tarefas com a API de PowerShell apenas se instalar o pacote do PowerShell. Além disso, as APIs do PowerShell fornecem significativamente mais funcionalidade do que o portal no momento.

Se já tiver instalado **tarefas de bases de dados elásticas** através do Portal existente de um **conjunto elástico**, a pré-visualização mais recente do PowerShell inclui scripts para atualizar sua instalação existente. É altamente recomendável atualizar sua instalação para o versão mais recente **tarefas de bases de dados elásticas** componentes para tirar o máximo partido da nova funcionalidade exposta por meio das APIs do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Para uma avaliação gratuita, consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Instalar a versão mais recente com o [instalador de plataforma Web](https://go.microsoft.com/fwlink/p/?linkid=320376). Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* [O utilitário de linha de comandos do NuGet](https://nuget.org/nuget.exe) é utilizado para instalar o pacote de tarefas de bases de dados elásticas. Para obter mais informações, consulte https://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Transferir e importar o pacote de PowerShell de tarefas de bases de dados elásticas
1. Inicie a janela de comando do Microsoft Azure PowerShell e navegue para o diretório onde transferiu o utilitário de linha de comandos do NuGet (nuget.exe).
2. Transferir e importar **tarefas de bases de dados elásticas** pacote para o diretório atual com o seguinte comando:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    O **tarefas de bases de dados elásticas** os arquivos são colocados no diretório local numa pasta chamada **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** em que *x.x.xxxx.x* reflete o número de versão. Os cmdlets do PowerShell (incluindo DLLs de cliente necessários) estão localizados no **tools\ElasticDatabaseJobs** subdiretório e os scripts do PowerShell para instalar, atualizar e desinstalar também se encontram no **ferramentas** subdiretório.
3. Navegue para o subdiretório de ferramentas na pasta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x ao escrever ferramentas de cd, por exemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Execute o script de.\InstallElasticDatabaseJobsCmdlets.ps1 para copiar o diretório de ElasticDatabaseJobs para $home\Documents\WindowsPowerShell\Modules. Isso também importará automaticamente o módulo para utilização, por exemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Instalar os componentes de tarefas de bases de dados elásticas com o PowerShell
1. Inicie uma janela de comando do Microsoft Azure PowerShell e navegue para o subdiretório de \tools sob a pasta de Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: Tipo cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Executar o script do PowerShell de.\InstallElasticDatabaseJobs.ps1 e fornecer valores para suas variáveis pedidas. Este script irá criar componentes descritos na [tarefas de bases de dados elásticas, componentes e preços](sql-database-elastic-jobs-overview.md#components-and-pricing) juntamente com a configuração do serviço de Cloud do Azure para usar adequadamente os componentes dependentes.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Quando executa este comando uma janela é aberta solicitando um **nome de utilizador** e **palavra-passe**. Não se trata de suas credenciais do Azure, introduza o nome de utilizador e palavra-passe que serão as credenciais de administrador que pretende criar para o novo servidor.

Os parâmetros fornecidos nesta invocação de exemplo podem ser modificados para as definições desejadas. O seguinte fornece mais informações sobre o comportamento de cada parâmetro:

<table style="width:100%">
  <tr>
    <th>Parâmetro</th>
    <th>Descrição</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Fornece o nome do grupo de recursos do Azure criado para conter os componentes do Azure recentemente criados. Este parâmetro, por predefinição "__ElasticDatabaseJob". Não é recomendado alterar este valor.</td>
    </tr>

<tr>
    <td>ResourceGroupLocation</td>
    <td>Disponibiliza a localização do Azure a ser utilizado para os componentes do Azure recentemente criados. Este parâmetro, por predefinição para a localização E.U.A. Central.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Fornece o número de trabalhadores de serviço para instalar. Este parâmetro é predefinido para 1. Um número mais alto de trabalhos pode ser utilizado para aumentar horizontalmente o serviço e para proporcionar elevada disponibilidade. Recomenda-se para utilizar "2" para Implantações que exigem elevada disponibilidade do serviço.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Fornece o tamanho da VM para utilização no serviço Cloud. Este parâmetro, por predefinição A0. Valores de parâmetros de.... /.. / A3 são aceites que fazer com que a função de trabalho utilizar um tamanho de extra pequena/pequeno/médio/grande, respectivamente. Para obter mais informações sobre tamanhos de função de trabalho, consulte <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">tarefas de bases de dados elásticas, componentes e preços</a>.</td>
</tr>

<tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Fornece o tamanho de computação para uma edição Standard. Este parâmetro, por predefinição S0. Valores de parâmetro de.... /.. /.. /.. / S9/S12 são aceites que fazer com que a base de dados do SQL do Azure utilizar o tamanho de computação respectivos. Para obter mais informações sobre tamanhos de computação de base de dados SQL, consulte <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">tarefas de bases de dados elásticas, componentes e preços</a>.</td>
</tr>

<tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Fornece o nome de utilizador de administrador para o servidor de base de dados do Azure SQL criado recentemente. Quando não especificado, será aberta uma janela de credenciais do PowerShell para solicitar as credenciais.</td>
</tr>

<tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Fornece a palavra-passe de administrador para o servidor de base de dados do Azure SQL criado recentemente. Quando não fornecido, será aberta uma janela de credenciais do PowerShell para solicitar as credenciais.</td>
</tr>
</table>

Para sistemas que se destinam a ter um grande número de tarefas em execução em paralelo num grande número de bases de dados, é recomendado para especificar os parâmetros, tais como: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Atualizar uma instalação de componentes do tarefas elásticas da base de dados existente com o PowerShell
**Tarefas de base de dados elásticas** pode ser atualizada dentro de uma instalação existente para dimensionamento e elevada disponibilidade. Este processo permite que as atualizações futuras do código de serviço sem ter de remover e recriar a base de dados do controle. Este processo também pode ser utilizado a mesma versão para modificar o tamanho da VM de serviço ou a contagem de trabalho do servidor.

Para atualizar o tamanho da VM de uma instalação, execute o seguinte script com parâmetros atualizados para os valores da sua preferência.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parâmetro</th>
  <th>Descrição</th>
</tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Identifica o nome do grupo de recursos do Azure utilizado quando os componentes de tarefa elástica da base de dados foram instalados inicialmente. Este parâmetro, por predefinição "__ElasticDatabaseJob". Uma vez que não é recomendado alterar este valor, não deve ter de especificar este parâmetro.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Fornece o número de trabalhadores de serviço para instalar.  Este parâmetro é predefinido para 1.  Um número mais alto de trabalhos pode ser utilizado para aumentar horizontalmente o serviço e para proporcionar elevada disponibilidade.  Recomenda-se para utilizar "2" para Implantações que exigem elevada disponibilidade do serviço.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Fornece o tamanho da VM para utilização no serviço Cloud. Este parâmetro, por predefinição A0. Valores de parâmetros de.... /.. / A3 são aceites que fazer com que a função de trabalho utilizar um tamanho de extra pequena/pequeno/médio/grande, respectivamente. Para obter mais informações sobre tamanhos de função de trabalho, consulte <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">tarefas de bases de dados elásticas, componentes e preços</a>.</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Instalar os componentes de tarefas de bases de dados elásticas com o Portal
Assim que tiver [criou um conjunto elástico](sql-database-elastic-pool-manage-portal.md), pode instalar **as tarefas de bases de dados elásticas** componentes para habilitar a execução das tarefas administrativas em relação a cada base de dados do conjunto elástico. Ao contrário de quando utilizar o **tarefas de bases de dados elásticas** as APIs do PowerShell, a interface do portal está atualmente restrito à execução apenas em relação a um conjunto existente.

**Tempo estimado para concluir:** dez minutos.

1. Da vista do dashboard do conjunto elástico através da [portal do Azure](https://portal.azure.com/#) , clique em **criar tarefa**.
2. Se estiver a criar uma tarefa pela primeira vez, tem de instalar **tarefas de bases de dados elásticas** ao clicar em **termos de pré-visualização**.
3. Aceite os termos clicando na caixa de verificação.
4. Na vista de "Instalar os serviços", clique em **CREDENCIAIS da tarefa**.
   
    ![Instalar os serviços][1]
5. Escreva um nome de utilizador e palavra-passe para um administrador da base de dados. Como parte da instalação, é criado um novo servidor de base de dados do Azure SQL. Dentro deste novo servidor, uma nova base de dados, conhecido como a base de dados de controle, é criado e utilizado para conter os metadados para tarefas de bases de dados elásticas. O nome de utilizador e palavra-passe criada aqui são utilizados para efeitos de iniciar sessão na base de dados do controle. Uma credencial separada é utilizada para execução de scripts contra as bases de dados dentro do conjunto.
   
    ![Criar nome de utilizador e palavra-passe][2]
6. Clique no botão OK. Os componentes são criados para si dentro de alguns minutos numa nova [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Novo grupo de recursos está afixado ao quadro do início, conforme mostrado abaixo. Tarefas de bases de dados elásticas, assim que forem criados (serviço em nuvem, base de dados SQL, Service Bus e armazenamento) são criadas no grupo.
   
    ![grupo de recursos no quadro de início][3]
7. Se está tentando criar ou gerir uma tarefa, enquanto as tarefas de base de dados elástica está a instalar, ao fornecer **credenciais** , verá a seguinte mensagem.
   
    ![Implementação em curso][4]

Se a desinstalação for necessária, elimine o grupo de recursos. Ver [como desinstalar os componentes de trabalho de bases de dados elásticas](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Passos Seguintes
Certifique-se de uma credencial com os direitos adequados para a execução do script é criada em cada base de dados no grupo, para obter mais informações, consulte [proteger a sua base de dados do SQL](sql-database-manage-logins.md).
Ver [criando e gerenciando um tarefas de bases de dados elásticas](sql-database-elastic-jobs-create-and-manage.md) para começar a utilizar.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
