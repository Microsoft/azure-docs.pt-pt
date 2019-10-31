---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182267"
---
Neste procedimento, você irá:

1. [Prepare-se para executar o executável do mantenedor](#to-prepare-to-run-the-maintainer) .
2. [Prepare o banco de dados de conteúdo e a lixeira para exclusão imediata de BLOBs órfãos](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Execute o mantenedor. exe](#to-run-the-maintainer).
4. [Reverter o banco de dados de conteúdo e as configurações da lixeira](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Para se preparar para executar o mantenedor
1. No servidor Web front-end, abra o Shell de gerenciamento do SharePoint 2013 como administrador.
2. Navegue até a pasta *unidade de inicialização*: \Program Files\Microsoft SQL Remote BLOB Storage 10.50 \ mantenedor\.
3. Renomeie **Microsoft. Data. SqlRemoteBlobs. mantenedorer. exe. config** para **Web. config**.
4. Use `aspnet_regiis -pdf connectionStrings` para descriptografar o arquivo Web. config.
5. No arquivo Web. config descriptografado, no nó `connectionStrings`, adicione a cadeia de conexão para a instância do SQL Server e o nome do banco de dados de conteúdo. Veja o seguinte exemplo.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Use `aspnet_regiis –pef connectionStrings` para criptografar novamente o arquivo Web. config. 
7. Renomeie Web. config para Microsoft. Data. SqlRemoteBlobs. Mantenedorer. exe. config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Para preparar o banco de dados de conteúdo e a lixeira para excluir imediatamente os BLOBs órfãos
1. Na SQL Server, no SQL Management Studio, execute as seguintes consultas de atualização para o banco de dados de conteúdo de destino: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. No servidor Web front-end, em **Administração Central**, edite as **configurações gerais do aplicativo Web** para o banco de dados de conteúdo desejado para desabilitar temporariamente a lixeira. Essa ação também esvaziará a lixeira para quaisquer conjuntos de sites relacionados. Para fazer isso, clique em **Administração Central** -> **gerenciamento de aplicativos** -> **aplicativos Web (gerenciar aplicativos web)**  -> **SharePoint-80** -> **configurações gerais do aplicativo**. Defina o **status da lixeira** como **desativado**.
   
    ![Configurações gerais do aplicativo Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Para executar o mantenedor
* No servidor Web front-end, no Shell de gerenciamento do SharePoint 2013, execute o mantenedor da seguinte maneira:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Somente a operação `GarbageCollection` tem suporte para o StorSimple no momento. Observe também que os parâmetros emitidos para Microsoft. Data. SqlRemoteBlobs. mantenedor. exe diferenciam maiúsculas de minúsculas. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Para reverter o banco de dados de conteúdo e as configurações da lixeira
1. Na SQL Server, no SQL Management Studio, execute as seguintes consultas de atualização para o banco de dados de conteúdo de destino:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. No servidor Web front-end, na **Administração Central**, edite as **configurações gerais do aplicativo Web** para o banco de dados de conteúdo desejado para reabilitar a lixeira. Para fazer isso, clique em **Administração Central** -> **gerenciamento de aplicativos** -> **aplicativos Web (gerenciar aplicativos web)**  -> **SharePoint-80** -> **configurações gerais do aplicativo**. Defina o status da lixeira como **ativado**.

