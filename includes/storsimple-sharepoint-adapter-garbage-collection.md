---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9c734ff03b1cf277c7e0967d8b76b1941434f414
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050456"
---
Neste procedimento, irá:

1. [Prepare-se para executar o Executável Keeper](#to-prepare-to-run-the-maintainer) .
2. [Prepare a base de dados de conteúdos e o Caixote do Lixo para a eliminação imediata dos BLOBs órfãos](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Executar Maintainer.exe. ](#to-run-the-maintainer)
4. [Reverta a base de dados de conteúdos e as definições do Caixote do Lixo](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Para preparar para executar o Keeper
1. No servidor frontal da Web, abra o SharePoint 2013 Management Shell como administrador.
2. Navegar para a *unidade de arranque* da pasta :\Ficheiros programam ficheiros\Microsoft SQL Remote Blob Storage 10.50\Manutenção\.
3. Mude o nome **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** para **web.config.**
4. Use `aspnet_regiis -pdf connectionStrings` para desencriptar o ficheiro web.config.
5. No ficheiro de web.config desencriptado, sob o `connectionStrings` nó, adicione a cadeia de ligação para a sua instância do servidor SQL e o nome da base de dados de conteúdos. Veja o seguinte exemplo.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Utilize `aspnet_regiis –pef connectionStrings` para reen encriptar o ficheiro web.config. 
7. Mude o nome web.config para Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Para preparar a base de dados de conteúdos e o Caixote do Lixo para eliminar imediatamente blobs órfãos
1. No SQL Server, no SQL Management Studio, execute as seguintes consultas de atualização para a base de dados de conteúdo-alvo: 

    `use WSS_Content`

    `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

    `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. No servidor frontal da Web, sob **Administração Central,** edite as **Definições Gerais da Aplicação Web** para a base de dados de conteúdo desejada para desativar temporariamente o Caixote do Lixo. Esta ação também esvaziará o Caixote do Lixo para quaisquer coleções relacionadas com o site. Para isso, clique em  ->  Aplicações Web **de Gestão de Aplicações de Gestão de Aplicações da** Administração Central  ->  **(Gerir aplicações web)**  ->  **SharePoint - 80**  ->  **Configurações gerais de aplicação**. Deslole o **estado do recipiente de reciclagem** . 
   
    ![Configurações gerais da aplicação web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Para executar o Keeper
* No servidor frontal da web, na Shell de Gestão SharePoint 2013, execute o Keeper da seguinte forma:
  
    `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Apenas a `GarbageCollection` operação é suportada para storSimple neste momento. Note também que os parâmetros emitidos para Microsoft.Data.SqlRemoteBlobs.Maintainer.exe são sensíveis a casos. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Para reverter a base de dados de conteúdos e as definições de Reciclar Bin
1. No SQL Server, no SQL Management Studio, execute as seguintes consultas de atualização para a base de dados de conteúdo-alvo:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. No servidor frontal da Web, na **Administração Central,** edite as **Definições Gerais** da Aplicação Web para a base de dados de conteúdo desejada para reativar o Caixote do Lixo. Para isso, clique em  ->  Aplicações Web **de Gestão de Aplicações de Gestão de Aplicações da** Administração Central  ->  **(Gerir aplicações web)**  ->  **SharePoint - 80**  ->  **Configurações gerais de aplicação**. Deslois para o conjunto DO DEPÓSITO DE **Reciclagem**.

