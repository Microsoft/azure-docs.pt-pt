---
title: Recuperar o serviço de aplicações no Azure Stack | Documentos da Microsoft
description: Documentação de orientação detalhada para a recuperação de desastres do serviço de aplicações do Azure Stack
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
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340246"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Recuperação do serviço de aplicações no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Este documento fornece instruções sobre as ações a tomar para recuperação de desastres do serviço de aplicações.

Para recuperar o serviço de aplicações no Azure Stack de cópia de segurança devem ser executadas as seguintes ações:
1.  Restaurar as bases de dados do serviço de aplicações
2.  Restaurar o conteúdo de partilha do servidor de ficheiro
3.  Restaurar as funções do serviço de aplicações e serviços

Se o armazenamento do Azure Stack foi utilizado para armazenamento de aplicações de função, em seguida, terá também de ter passos para restaurar a aplicações de funções.

## <a name="restore-the-app-service-databases"></a>Restaurar as bases de dados do serviço de aplicações
As bases de dados de aplicação serviço SQL Server devem ser restaurados numa instância de SQL Server pronta de produção. 

Após [preparar a instância do SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) para alojar as bases de dados do serviço de aplicações, utilize estes passos para restaurar as bases de dados a partir de cópia de segurança:

1. Inicie sessão no SQL Server que alojará as bases de dados do serviço de aplicações recuperadas com permissões de administrador.
2. Utilize os seguintes comandos para restaurar as bases de dados do serviço de aplicações a partir de um prompt de comando executando com permissões de administrador:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Certifique-se de que ambas as bases de dados do serviço de aplicações foi restauradas com êxito e sair do SQL Server Management Studio.

> [!NOTE]
> Para recuperar a partir de uma falha de instância de cluster de ativação pós-falha, [estas instruções](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Restaurar o conteúdo de partilha de ficheiros do serviço de aplicações
Após [preparar o servidor de ficheiros](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) para alojar a partilha de ficheiros do serviço de aplicações, tem de restaurar o conteúdo de partilha de ficheiros do inquilino a partir de cópia de segurança. Pode utilizar qualquer método que tem disponíveis para copiar os ficheiros para a localização de partilha de ficheiros do serviço de aplicações criada recentemente. Em execução neste exemplo, no servidor de ficheiros irá utilizar PowerShell e o robocopy para ligar a uma partilha remota e copie os ficheiros para a partilha:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Para além de copiar o conteúdo de partilha de ficheiros, também tem de repor as permissões na partilha de ficheiros em si. Para tal, abra uma linha de comandos administrativa no computador do servidor de ficheiros e execute o **ReACL.cmd** ficheiro. O **ReACL.cmd** ficheiro está localizado nos ficheiros de instalação do serviço de aplicações no **BCDR** diretório.

## <a name="restore-app-service-roles-and-services"></a>Restaurar as funções do serviço de aplicações e serviços
Depois de restaurar as bases de dados do serviço de aplicações e o conteúdo de partilha de ficheiros, em seguida tem de utilizar o PowerShell para restaurar as funções do serviço de aplicações e serviços. Estes passos irão restaurar os segredos de serviço de aplicações e configurações de serviço.  

1. Inicie sessão no controlador de serviço de aplicações **CN0-VM** VM como **roleadmin** usando a palavra-passe que indicou durante a instalação do serviço de aplicações. 
    > [!TIP]
    > Terá de modificar o grupo de segurança de rede da VM para permitir ligações de RDP. 
2. Copiar o **SystemSecrets.JSON** ficheiro localmente para o controlador de VM. Terá de fornecer o caminho para este ficheiro como o `$pathToExportedSecretFile` parâmetro no próximo passo. 
3. Utilize os seguintes comandos numa janela de consola elevada do PowerShell para restaurar as funções do serviço de aplicações e serviços:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> É altamente recomendado para fechar esta sessão do PowerShell quando o comando for concluído.

## <a name="restore-function-apps"></a>Restaure as aplicações de função 
Serviço de aplicações para o Azure Stack não suporta o restauro de aplicações de utilizador do inquilino ou dados que não sejam o conteúdo de partilha de ficheiros. Por conseguinte, estes tem de ser uma cópia de segurança e recuperar fora do serviço de aplicações de cópia de segurança e restaurar as operações. Se o armazenamento do Azure Stack foi utilizado para armazenamento de aplicações de funções, os seguintes passos devem de ser realizados para recuperar de perda de dados:

1. Crie uma nova conta de armazenamento a ser utilizado pela aplicação de função. Este armazenamento pode ser armazenamento do Azure Stack, o armazenamento do Azure ou qualquer armazenamento compatível.
2. Obter a cadeia de ligação para o armazenamento.
3. Abra o portal de função, navegue para a aplicação de funções.
4. Navegue para o **funcionalidades de plataforma** separador e clique em **configurações de aplicativo**.
5. Alteração **AzureWebJobsDashboard** e **AzureWebJobsStorage** para a nova cadeia de ligação e clique em **guardar**.
6. Mude para o **descrição geral**.
7. Reinicie a aplicação. Poderá demorar várias tentativas para limpar todos os erros.

## <a name="next-steps"></a>Passos Seguintes
[Descrição geral do Serviço de Aplicações no Azure Stack](azure-stack-app-service-overview.md)