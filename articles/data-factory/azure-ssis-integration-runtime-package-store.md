---
title: Gerir pacotes com loja de pacotes de runtime de integração Azure-SSIS
description: Saiba como gerir pacotes com a loja de pacotes de runtime de integração Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 29d072c513d9a75055d4bb486f44b17b00b7f0a9
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638351"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Gerir pacotes com loja de pacotes de runtime de integração Azure-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Para levantar & transferir as cargas de trabalho dos Serviços de Integração de Servidores SQL (SSIS) no local para a nuvem, pode providenciar o tempo de execução de integração Azure-SSIS (IR) na Azure Data Factory (ADF). Para obter mais informações, consulte [Provision a Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md). Um Azure-SSIS IR suporta:

- Pacotes de execução implantados no catálogo SSIS (SSISDB) hospedados pelo servidor de base de dados Azure SQL/Gestão de Instâncias (Modelo de Implementação de Projetos)
- Executando pacotes implantados no sistema de ficheiros, ficheiros Azure ou base de dados do SQL Server (MSDB) hospedada por Azure SQL Managed Instance (Modelo de Implementação de Pacotes)

Quando utilizar o Modelo de Implementação de Pacotes, pode escolher se pretender a provisionar o seu Azure-SSIS IR com lojas de pacotes. Eles fornecem uma camada de gestão de pacotes em cima do sistema de ficheiros, Ficheiros Azure ou MSDB hospedados por Azure SQL Managed Instance. A loja de pacotes Azure-SSIS IR permite importar/eliminar/eliminar/executar pacotes e monitorizar/parar de funcionar pacotes através do SQL Server Management Studio (SSMS) semelhante à [antiga loja de pacotes SSIS](/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Ligue-se ao Azure-SSIS IR

Uma vez que o seu Azure-SSIS IR é a provisionado, pode ligar-se a ele para navegar nas suas lojas de pacotes em SSMS.

![Ligue-se ao Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Na janela **Object Explorer** do SSMS, selecione O tempo **de execução da integração Azure-SSIS** no menu de entrega do **Connect.** Em seguida, inscreva-se no Azure e selecione a subscrição relevante, ADF, e Azure-SSIS IR que adpôs com lojas de pacotes. O seu Azure-SSIS IR aparecerá com **pacotes de execução** e **nós de pacotes armazenados** por baixo. Expanda o nó **pacotes armazenados** para ver as suas lojas de pacotes por baixo. Expanda as suas lojas de pacotes para ver pastas e pacotes por baixo. Pode ser-lhe pedido que introduza as credenciais de acesso para as suas lojas de pacotes, se o SSMS não conseguir ligar-se automaticamente a elas. Por exemplo, se expandir uma loja de pacotes em cima do MSDB, pode ser-lhe pedido que se conecte primeiro à sua Instância Gerida Azure SQL.

![Ligue-se a Azure SQL Gestão De Instância](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Gerir pastas e pacotes

Depois de ligar ao seu Azure-SSIS IR em SSMS, pode clicar com o botão direito em quaisquer lojas de pacotes, pastas ou pacotes para aparecer um menu e selecionar **Nova Pasta,** **Pacote de Importação,** **Pacote de Exportação,** **Eliminar** ou **Refrescar** .

   ![Gerir pastas e pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selecione **Nova Pasta** para criar uma nova pasta para pacotes importados.

   *  Selecione **Pacote de Importação** para importar pacotes do Sistema de **Ficheiros,** **SQL Server** (MSDB) ou a antiga Loja de **Pacotes SSIS** na sua loja de pacotes.

      ![Pacote de Importação](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Dependendo da **localização** do Pacote a importar, selecione o tipo de autenticação **do servidor** / **Authentication type** relevante, introduza as credenciais de acesso se necessário, selecione a **trajetória pacote** , e introduza o novo nome **pacote** . Quando importam pacotes, o seu nível de proteção não pode ser alterado. Para alterá-lo, utilize ferramentas de dados do servidor SQL (SSDT) ou `dtutil` serviço de linha de comando.

      > [!NOTE]
      > Importar pacotes SSIS em lojas de pacotes Azure-SSIS IR só pode ser feito um a um e simplesmente copiá-los no sistema MSDB/ficheiros/Ficheiros Azure subjacentes, preservando a sua versão SQL Server/SSIS. 
      >
      > Uma vez que o Azure-SSIS IR se baseia atualmente no **SQL Server 2017,** a execução de pacotes de versão inferior no mesmo irá atualizá-los em pacotes SSIS 2017 em tempo de execução. A execução de pacotes de versão superior não é suportada.
      >
      > Além disso, uma vez que as lojas de pacotes SSIS antigas estão ligadas a uma versão específica do SQL Server e acessíveis apenas em SSMS para essa versão, os pacotes de versão inferior nas lojas de pacotes SSIS legacy precisam de ser exportados para o sistema de ficheiros primeiro utilizando a versão designada SSMS antes de poderem ser importados para lojas de pacotes Azure-SSIS IR utilizando versões SSMS 2019 ou posteriormente.
      >
      > Em alternativa, para importar vários pacotes SSIS em lojas de pacotes Azure-SSIS IR enquanto muda o seu nível de proteção, pode utilizar o utilitário da linha de comando [dtutil,](/sql/integration-services/dtutil-utility?view=sql-server-2017) ver [implantar vários pacotes com dtutil](#deploying-multiple-packages-with-dtutil).

   *  Selecione **pacote de exportação** para exportar pacotes da sua loja de pacotes para **o Sistema de Ficheiros,** **SqL Server** (MSDB) ou a antiga Loja de **Pacotes SSIS** .

      ![Pacote de Exportação](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Dependendo da **localização** do Pacote para exportar, selecione o tipo de autenticação **do servidor** / **Authentication type** relevante, introduza as credenciais de acesso se necessário e selecione o caminho **do Pacote** . Ao exportar pacotes, se estiverem encriptados, introduza as palavras-passe para desencriptar primeiro e depois pode alterar o seu nível de proteção, por exemplo, para evitar armazenar quaisquer dados sensíveis ou encriptar ou todos os dados com chave de utilizador ou palavra-passe.

      > [!NOTE]
      > A exportação de pacotes SSIS a partir de lojas de pacotes Azure-SSIS IR só pode ser feita um a um e fazê-lo sem alterar o seu nível de proteção irá simplesmente copiá-los preservando a sua versão SQL Server/SSIS, caso contrário irá atualizá-los em pacotes SSIS 2019 ou posterior versão.
      >
      > Uma vez que o Azure-SSIS IR se baseia atualmente no **SQL Server 2017,** a execução de pacotes de versão inferior no mesmo irá atualizá-los em pacotes SSIS 2017 em tempo de execução. A execução de pacotes de versão superior não é suportada.
      >
      > Em alternativa, para exportar vários pacotes SSIS a partir de lojas de pacotes Azure-SSIS IR enquanto muda o seu nível de proteção, pode utilizar o utilitário da linha de comando [dtutil,](/sql/integration-services/dtutil-utility?view=sql-server-2017) ver [implantar vários pacotes com dtutil](#deploying-multiple-packages-with-dtutil).

   *  **Selecione Eliminar** para eliminar as pastas/pacotes existentes na sua loja de pacotes.

   *  Selecione **Refresh** para mostrar as pastas/pacotes recém-adicionados na sua loja de pacotes.

## <a name="execute-packages"></a>Executar pacotes

Depois de ligar ao seu Azure-SSIS IR em SSMS, pode clicar com o botão direito em quaisquer pacotes armazenados para aparecer um menu e selecionar **o Run Package** .  Isto abrirá o diálogo **de utilitário de pacote execute,** onde pode configurar as suas execuções de pacotes em Azure-SSIS IR como executar atividades do Pacote SSIS em oleodutos ADF.

![Executar pacotes de utilidade 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Executar pacotes de utilidade 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

As páginas **Gerais** , **Configurações** , Opções de **Execução** e **Registo** de **Serviços de Utilidade de Pacotes executam** correspondem ao separador  **Definições** da atividade do Pacote SSIS Executar. Nestas páginas, pode introduzir a palavra-passe de encriptação do seu pacote e aceder a informações para o seu ficheiro de configuração do pacote. Também pode introduzir as credenciais e propriedades de execução do seu pacote, bem como as informações de acesso para a sua pasta de registo.  A página **'Valores definidos'** do diálogo **de utilidade de pacote execute** corresponde ao **separador Deposição** de Propriedade da atividade do Pacote Executo SSIS, onde pode introduzir as propriedades do pacote existentes para substituir. Para obter mais informações, consulte [os pacotes Run SSIS como executar as atividades do Pacote SSIS em oleodutos ADF.](./how-to-invoke-ssis-package-ssis-activity.md)

Quando selecionar o botão **Executar,** um novo oleoduto ADF com atividade do Pacote SSIS executar será automaticamente gerado e acionado. Se já existir um gasoduto ADF com as mesmas definições, será reexecutado e não será gerado um novo gasoduto. O oleoduto ADF e a atividade do Pacote SSIS executarão `Pipeline_SSMS_YourPackageName_HashString` `Activity_SSMS_YourPackageName` e, respectivamente.

![Executar botão de utilidade de pacote](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Executar atividade de pacote SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorize e pare de executar pacotes

Depois de ligar ao seu Azure-SSIS IR em SSMS, pode expandir o nó **'Pacotes Running'** para ver os seus pacotes atualmente em execução por baixo.  Clique com o botão direito em qualquer um deles para aparecer um menu e selecione **Stop** or **Refresh** .

   ![Monitorize e pare de executar pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selecione **Stop** para cancelar o pipeline ADF atualmente em execução que executa o pacote como executar a atividade do Pacote SSIS.

   *  Selecione **Refresh** para mostrar os pacotes recém-funcionais nas suas lojas de pacotes.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitor Azure-SSIS IR e editar lojas de pacotes

Depois de ligar ao seu Azure-SSIS IR em SSMS, pode clicar com o botão direito para aparecer um menu e selecionar **Ir ao portal Azure Data Factory** ou **Refrescar.**

   ![Ir para o portal ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selecione **Ir ao portal Azure Data Factory** para abrir a página de tempos de **integração** do centro de monitorização ADF, onde pode monitorizar o seu Azure-SSIS IR. No azulejo **pacote lojas,** pode ver o número de lojas de pacotes que estão anexadas ao seu Azure-SSIS IR.  A seleção desse número irá surgir uma janela onde pode editar serviços ligados à ADF que armazenam as informações de acesso para as suas lojas de pacotes.

      ![Editar lojas de pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selecione **Refresh** para mostrar as pastas/pacotes recém-adicionados nas suas lojas de pacotes e executar pacotes nas suas lojas de pacotes.

## <a name="deploying-multiple-packages-with-dtutil"></a>Implantação de vários pacotes com dtutil

Para levantar & transferir as cargas de trabalho SSIS no local para sSIS em ADF, mantendo o modelo de implementação de pacotes legado, precisa de implantar os seus pacotes a partir do sistema de ficheiros, MSDB hospedado pelo SQL Server, ou lojas de pacotes SSIS em Ficheiros Azure, MSDB hospedado por Azure SQL Managed Instance, ou lojas de pacotes Azure-SSIS IR. Ao mesmo tempo, também deverá alterar o seu nível de proteção da encriptação por chave do utilizador para não encriptado ou encriptação por palavra-passe se ainda não o tiver feito.

Pode utilizar o utilitário da linha de comando [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) que vem com a instalação SQL Server/SSIS para implantar vários pacotes em lotes. Está ligado a uma versão SSIS específica, por isso, se a utilizar para implantar pacotes de versão inferior sem mudar o seu nível de proteção, irá simplesmente copiá-los preservando a sua versão SSIS. Se o utilizar para os implementar e mudar o seu nível de proteção ao mesmo tempo, irá atualizá-los para a sua versão SSIS.

 Uma vez que o Azure-SSIS IR se baseia atualmente no **SQL Server 2017,** a execução de pacotes de versão inferior no mesmo irá atualizá-los em pacotes SSIS 2017 em tempo de execução. A execução de pacotes de versão superior não é suportada.

Consequentemente, para evitar atualizações de tempo de execução, a implementação de pacotes para funcionar no Azure-SSIS IR no Modelo de Implementação de Pacotes deverá utilizar o dtutil 2017 que vem com a instalação SQL Server/SSIS 2017. Pode descarregar e instalar a [Edição de Desenvolvimento 2017 do SQL Server/SSIS 2017](https://go.microsoft.com/fwlink/?linkid=853016) gratuita para este fim. Uma vez instalado, pode encontrar até 2017 nesta pasta: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Implantação de vários pacotes do sistema de ficheiros nas instalações em Ficheiros Azure com dtutil

 Para implantar vários pacotes do sistema de ficheiros para ficheiros Azure e mudar o seu nível de proteção ao mesmo tempo, pode executar os seguintes comandos numa decisão de comando. Por favor, substitua todas as cordas específicas do seu caso.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Para executar os comandos acima num ficheiro de lote, `%f` substitua-os por `%%f` .

Para implantar vários pacotes de lojas de pacotes SSIS em cima do sistema de ficheiros em Ficheiros Azure e mudar o seu nível de proteção ao mesmo tempo, pode utilizar os mesmos comandos, mas substituir `YourLocalDrive:\...\YourPackageFolder` por uma pasta local utilizada pelas antigas lojas de pacotes SSIS: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Por exemplo, se a sua loja de pacotes SSIS está ligada ao SQL Server 2016, vá a `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  Pode encontrar o valor de `YourSQLServerDefaultCompatibilityLevel` uma [lista de níveis de compatibilidade padrão do SQL Server](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#arguments).

Se configurar as lojas de pacotes Azure-SSIS IR em cima dos Ficheiros Azure, os seus pacotes implantados aparecerão neles quando ligar ao seu Azure-SSIS IR em SSMS 2019 ou versões posteriores.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Implantação de vários pacotes da MSDB nas instalações para a MSDB em Azure com dtutil

 Para implementar vários pacotes de MSDB hospedados pelo SQL Server ou lojas de pacotes SSIS em cima do MSDB em MSDB hospedados pela Azure SQL Managed Instance e mudar o seu nível de proteção ao mesmo tempo, pode ligar-se ao seu SqL Server em SSMS, clicar com o botão direito `Databases->System Databases->msdb` no nó no Explorador de **Objetos** de SSMS para abrir uma nova janela **queria** e executar o seguinte script T-SQL. Por favor, substitua todas as cordas específicas do seu caso:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Para utilizar o ponto final privado/público da sua Instância Gerida Azure SQL, `YourSQLManagedInstanceEndpoint` substitua-o, `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` respectivamente.

O script gerará linhas de comando dtutil para todos os pacotes em MSDB que pode utilizar multiselecção, copiar & pasta e executar num pedido de comando.

![Gerar linhas de comando dtutil](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Se configurar as lojas de pacotes Azure-SSIS IR em cima do MSDB, os seus pacotes implantados aparecerão neles quando ligar ao seu Azure-SSIS IR em SSMS 2019 ou versões posteriores.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Implantação de vários pacotes da MSDB nas instalações em Ficheiros Azure com dtutil

 Para implementar vários pacotes de MSDB hospedados pelo SQL Server ou por lojas de pacotes SSIS em cima do MSDB em Ficheiros Azure e mudar o seu nível de proteção ao mesmo tempo, pode ligar-se ao seu SQL Server no SSMS, clicar com o botão direito `Databases->System Databases->msdb` no nó no Explorador de **Objetos** de SSMS para abrir uma nova janela **query** e executar o seguinte script T-SQL. Por favor, substitua todas as cordas específicas do seu caso:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

O script gerará linhas de comando dtutil para todos os pacotes em MSDB que pode utilizar multiselecção, copiar & pasta e executar num pedido de comando.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Se configurar as lojas de pacotes Azure-SSIS IR em cima dos Ficheiros Azure, os seus pacotes implantados aparecerão neles quando ligar ao seu Azure-SSIS IR em SSMS 2019 ou versões posteriores.

## <a name="next-steps"></a>Passos seguintes

Pode reedição/edição dos oleodutos ADF gerados automaticamente com atividades do Pacote SSIS executado ou criar novos no portal ADF. Para obter mais informações, consulte [os pacotes Run SSIS como executar as atividades do Pacote SSIS em oleodutos ADF.](./how-to-invoke-ssis-package-ssis-activity.md)