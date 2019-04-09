---
title: 5 notas de versão de atualização do serviço de aplicações no Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as novidades na atualização cinco para o serviço de aplicações no Azure Stack, os problemas conhecidos e onde pode transferir a atualização.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 192ac256f013498e57ecf7939d29796af073b948
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260566"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>Serviço de aplicações no notas de versão de atualização 5 do Azure Stack

*Aplica-se a Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Estas notas de versão descrevem as melhorias e correções no serviço de aplicações do Azure em 5 de atualização de pilha do Azure e os problemas conhecidos. Problemas conhecidos são divididos em problemas diretamente relacionados com a implementação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização de 1901 seu sistema integrado do Azure Stack ou implementar o development kit do Azure Stack mais recentes antes de implementar 1.5 de serviço de aplicações do Azure.


## <a name="build-reference"></a>Criar a referência

O serviço de aplicações no número de compilação do Azure Stack Update 5 é **80.0.2.15**

### <a name="prerequisites"></a>Pré-requisitos

Consulte a [documentação antes de começar a utilizar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implementação.

Antes de iniciar a atualização do serviço de aplicações do Azure no Azure Stack para 1.5:

- Certifique-se de que todas as funções estiverem prontos na administração do serviço de aplicações do Azure no Portal de administração do Azure Stack

- Cópia de segurança do serviço de aplicações e bases de dados mestra:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Criar cópias de segurança da partilha de ficheiros de conteúdo da aplicação de inquilino

- Distribuição do **extensão de Script personalizado** versão **1.9.1** do Marketplace

### <a name="new-features-and-fixes"></a>Novas funcionalidades e correções

Serviço de aplicações do Azure no Azure Stack Update 5 inclui as seguintes melhorias e correções:

- Atualiza para **inquilino de serviço de aplicações, o administrador, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- As atualizações **runtime das funções do Azure** ao **v1.0.12205**.

- Atualiza para **ferramentas do Kudu** para resolver problemas com a definição de estilo e a funcionalidade para os clientes operacionais **desligado** Azure Stack. 

- Atualizações de serviço básico para melhorar a fiabilidade e ativar mais fácil diagnóstico dos problemas comuns de mensagens de erro.

- **Atualizações para as seguintes arquiteturas de aplicações e ferramentas**:
  - Foi adicionado o ASP.NET Core 2.1.6 e 2.2.0
  - Foi adicionado NodeJS 10.14.1
  - Foi adicionado NPM 6.4.1
  - Kudu atualizada para 79.20129.3767
  
- **Atualizações ao sistema operacional subjacente de todas as funções**:
  - [2019-02 atualização cumulativa do Windows Server 2016 para sistemas baseados em x64 (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Passos de pós-implementação

> [!IMPORTANT]  
> Se forneceu o fornecedor de recursos do serviço de aplicações com uma sempre na instância do SQL tem [adicionar as bases de dados appservice_hosting e appservice_metering a um grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) e sincronizar as bases de dados para evitar qualquer perda de serviço no caso de uma ativação pós-falha de base de dados.

### <a name="post-update-steps"></a>Passos de pós-atualização

Para os clientes que pretendem migrar para o banco de dados independente do serviço de aplicações do Azure atual em implementações do Azure Stack, execute estes passos depois do serviço de aplicações do Azure no Azure Stack 1.5 atualização estar concluída:

> [!IMPORTANT]
> O procedimento de migração demora, aproximadamente 5 a 10 minutos.  O procedimento envolve cancelar as sessões de início de sessão da base de dados existente.  Planear para o período de indisponibilidade migrar e validar o serviço de aplicações do Azure na migração de publicação do Azure Stack.  Se concluiu estes passos depois de atualizar para o serviço de aplicações do Azure no Azure Stack 1.3 estes passos não são necessários.
>
>

1. Adicionar [serviço de aplicações de bases de dados (appservice_hosting e appservice_metering) para um grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Ativar contido base de dados
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Converter uma base de dados para parcialmente independente, a conversão irá incorrer em períodos de indisponibilidade que tem de ser eliminadas todas as sessões ativas

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Validação

1. Verifique se o SQL Server tem contenção ativada

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Verificar o comportamento de contidos existente
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Os trabalhos são não é possível alcançar o servidor de ficheiros quando o serviço de aplicações é implementado numa rede virtual existente e o servidor de ficheiros só está disponível na rede privada, conforme descritas no serviço de aplicações do Azure, na documentação de implementação do Azure Stack.

Se optar por implementar numa rede virtual existente e um endereço IP interno para se ligar ao seu servidor de ficheiros, tem de adicionar uma regra de segurança de saída, permitindo que o tráfego entre a sub-rede de trabalho e o servidor de ficheiros SMB. Vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
 * Origem: Qualquer
 * Intervalo de portas de origem: *
 * Destino: Endereços IP
 * Intervalo de endereços IP de destino: Intervalo de IPs para o servidor de ficheiros
 * Intervalo de portas de destino: 445
 * Protocolo: TCP
 * Ação: Permitir
 * Prioridade: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para os administradores de nuvem operacional de serviço de aplicações do Azure no Azure Stack

Consulte a documentação no [notas de versão do Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do serviço de aplicações do Azure, consulte [serviço de aplicações do Azure no Descrição geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como preparar a implementação de serviço de aplicações no Azure Stack, veja [antes de começar com o serviço de aplicações no Azure Stack](azure-stack-app-service-before-you-get-started.md).
