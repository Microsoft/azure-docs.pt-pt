---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73182267"
---
Neste procedimento, irá:

1. [Prepare-se para executar o Keeper executável](#to-prepare-to-run-the-maintainer) .
2. [Prepare a base de dados de conteúdo e recicle o recipiente para a eliminação imediata de BLOBs órfãos](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Run Maintainer.exe](#to-run-the-maintainer).
4. [Reverter a base de dados de conteúdo e as definições](#to-revert-the-content-database-and-recycle-bin-settings)do Reciclo .

#### <a name="to-prepare-to-run-the-maintainer"></a>Para se preparar para executar o Keeper
1. No servidor frontal web, abra o SharePoint 2013 Management Shell como administrador.
2. Navegue para a *unidade de arranque*da pasta :\Program Files\Microsoft SQL Remote Blob Storage 10.50\Keeper\.
3. Mude o nome **Microsoft.Data.Data.SqlRemoteBlobs.Maintainer.exe.config** para **web.config**.
4. Utilize `aspnet_regiis -pdf connectionStrings` para desencriptar o ficheiro web.config.
5. No ficheiro web.config desencriptado, sob o `connectionStrings` nó, adicione a cadeia de ligação para a sua instância de servidor SQL e o nome da base de dados de conteúdo. Veja o seguinte exemplo.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Utilize `aspnet_regiis –pef connectionStrings` para encriptar novamente o ficheiro web.config. 
7. Mude o nome web.config para Microsoft.Data.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Para preparar a base de dados de conteúdos e reciclar o caixote do lixo para eliminar imediatamente blobs órfãos
1. No Servidor SQL, no SQL Management Studio, executa as seguintes consultas de atualização para a base de dados de conteúdo-alvo: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. No servidor frontal web, sob **a Administração Central,** edite as **Definições Gerais** da Aplicação Web para a base de dados de conteúdo desejada para desativar temporariamente a caixa de reciclagem. Esta ação também esvaziará o Caixote do Ciclo para quaisquer coleções relacionadas com o site. Para tal, clique em Aplicações Web de Gestão de**Aplicações** -> da **Administração** -> Central **(Gerir aplicações web)** -> **SharePoint - 80** -> **Configurações gerais**de aplicação . Desloque o estado do recipiente de **reciclagem** para **desligar**.
   
    ![Definições gerais da aplicação web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Para executar o Keeper
* No servidor frontal web, no SharePoint 2013 Management Shell, execute o Keeper da seguinte forma:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Apenas `GarbageCollection` a operação é suportada para o StorSimple neste momento. Note também que os parâmetros emitidos para Microsoft.Data.Data.SqlRemoteBlobs.Maintainer.exe são sensíveis ao caso. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Para reverter a base de dados de conteúdo e as definições do Reciclo Bin
1. No Servidor SQL, no SQL Management Studio, executa as seguintes consultas de atualização para a base de dados de conteúdo-alvo:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. No servidor frontal web, na **Administração Central,** edite as **Definições Gerais** da Aplicação Web para a base de dados de conteúdo desejada para reativar o Reciclo Bin. Para tal, clique em Aplicações Web de Gestão de**Aplicações** -> da **Administração** -> Central **(Gerir aplicações web)** -> **SharePoint - 80** -> **Configurações gerais**de aplicação . Desloque o estado do recipiente de reciclagem para **on**. .

