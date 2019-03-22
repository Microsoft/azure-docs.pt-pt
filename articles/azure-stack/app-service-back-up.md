---
title: Cópia de segurança de serviço de aplicações no Azure Stack | Documentos da Microsoft
description: Orientação detalhada para cópia de segurança do serviço de aplicações do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340553"
---
# <a name="back-up-app-service-on-azure-stack"></a>Fazer cópias de segurança do serviço de aplicações no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Este documento fornece instruções sobre como fazer cópias de segurança do serviço de aplicações no Azure Stack.

> [!IMPORTANT]
> Serviço de aplicações no Azure Stack não cópia de segurança como parte da [cópia de segurança do Azure Stack infraestrutura](azure-stack-backup-infrastructure-backup.md). Como um operador de pilha do Azure, tem de tomar medidas para garantir que o serviço de aplicações podem ser recuperado com êxito se for necessário.

Serviço de aplicações do Azure no Azure Stack tem quatro componentes principais a considerar quando planear a recuperação de desastres:
1. A infraestrutura de fornecedor de recursos; funções de servidor, escalões de worker, etc. 
2. Os segredos de serviço de aplicações
3. O serviço de aplicações SQL Server que aloja e bases de dados de medição
4. A serviço de aplicações utilizador carga de trabalho de conteúdo armazenada na partilha de ficheiros de serviço de aplicações   

## <a name="back-up-app-service-secrets"></a>Criar cópias de segurança segredos de serviço de aplicações
Ao recuperar o serviço de aplicações de cópia de segurança, tem de fornecer as chaves de serviço de aplicações utilizadas pela implantação inicial. Estas informações devem ser salvos assim que o serviço de aplicações é implementado com êxito e armazenado numa localização segura. A configuração de infraestrutura do fornecedor de recursos será recriada da cópia de segurança durante a recuperação com o serviço de aplicações recovery cmdlets do PowerShell.

Utilize o portal de administração para criar cópias de segurança segredos de serviço de aplicação seguindo estes passos: 

1. Inicie sessão no portal de administração do Azure Stack como administrador de serviços.

2. Navegue até **serviço de aplicações** -> **segredos**. 

3. Selecione **transferir segredos**.

   ![Transferir segredos](./media/app-service-back-up/download-secrets.png)

4. Quando segredos estão prontos para baixar, clique em **salvar** e armazenar os segredos de serviço de aplicações (**SystemSecrets.JSON**) ficheiro numa localização segura. 

   ![Guardar segredos](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Repita estes passos, sempre que são revezados dos segredos de serviço de aplicações.

## <a name="back-up-the-app-service-databases"></a>Criar cópias de segurança de bases de dados do serviço de aplicações
Para restaurar o serviço de aplicações, terá do **Appservice_hosting** e **Appservice_metering** cópias de segurança de base de dados. Recomendamos que utilize planos de manutenção do SQL Server ou servidor de cópia de segurança do Azure para garantir que esses bancos de dados são uma cópia de segurança e guardados em segurança em intervalos regulares. No entanto, qualquer método de garantir cópias de segurança regulares do SQL são criados podem ser utilizados.

Para criar manualmente cópias de segurança esses bancos de dados enquanto tiver sessão iniciada para o SQL Server, pode utilizar os seguintes comandos do PowerShell:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Se precisar de fazer cópias de segurança de bases de dados do AlwaysOn de SQL, siga [estas instruções](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Depois de todas as bases de dados tem sido submetidas com êxito a cópia de segurança, copie os ficheiros. bak numa localização segura, juntamente com as informações de segredos de serviço de aplicações.

## <a name="back-up-the-app-service-file-share"></a>Criar cópias de segurança da partilha de ficheiros do serviço de aplicações
Serviço de aplicações lojas inquilino informações da aplicação na partilha de ficheiros. Isso deve ser uma cópia de segurança em intervalos regulares, juntamente com as bases de dados do serviço de aplicações para que os dados tão pouco quanto possível serão perdidos se ser necessário um restauro. 

Para criar cópias de segurança da partilha de ficheiros do serviço de aplicações, conteúda, que pode utilizar o Azure Backup Server ou outro método para copiar regularmente a partilha de ficheiros para a localização de conteúdo que guardou todas as informações de recuperação anterior. 

Por exemplo, pode utilizar estes passos para utilizar o robocopy a partir de uma sessão de consola do Windows PowerShell (ISE do PowerShell não):

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Passos Seguintes
[Restaurar o serviço de aplicações no Azure Stack](app-service-recover.md)