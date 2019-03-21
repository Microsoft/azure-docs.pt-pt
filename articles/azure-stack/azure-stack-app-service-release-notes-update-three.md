---
title: 3 notas de versão de atualização do serviço de aplicações no Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as novidades na atualização três para o serviço de aplicações no Azure Stack, os problemas conhecidos e onde pode transferir a atualização.
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
ms.date: 08/20/2018
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: 765701188de6375a5dfe0c9a6c04afe1ee9f6740
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886444"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Serviço de aplicações no notas de versão de atualização 3 do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Estas notas de versão descrevem as melhorias e correções no serviço de aplicações do Azure no Azure Stack Update 3 e os problemas conhecidos. Problemas conhecidos são divididos em problemas diretamente relacionados com a implementação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização de 1807 seu sistema integrado do Azure Stack ou implementar o development kit do Azure Stack mais recentes antes de implementar 1.3 de serviço de aplicações do Azure.
>
>

## <a name="build-reference"></a>Criar a referência

O serviço de aplicações no número de compilação do Azure Stack Update 3 é **74.0.13698.31**

### <a name="prerequisites"></a>Pré-requisitos

Consulte a [documentação antes de começar a utilizar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implementação.

Antes de iniciar a atualização do serviço de aplicações do Azure no Azure Stack para 1.3, certifique-se de todas as funções estiverem prontos na administração do serviço de aplicações do Azure no Portal de administração do Azure Stack

![Estado da função de serviço de aplicações](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Novas funcionalidades e correções

Serviço de aplicações do Azure no Azure Stack Update 3 inclui as seguintes melhorias e correções:

- Suporte para utilização do SQL Server Always On para bases de dados do fornecedor de recursos do serviço de aplicações do Azure.

- Foi adicionado o novo parâmetro de ambiente para o script de programa auxiliar de AADIdentityApp criar para o ajudar a direcionamento diferentes regiões do AAD.

- Atualiza para **inquilino de serviço de aplicações, o administrador, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- Atualizações de serviço básico para melhorar a fiabilidade e ativar mais fácil diagnóstico dos problemas comuns de mensagens de erro.

- **Atualizações para as seguintes arquiteturas de aplicações e ferramentas**:
  - Foi adicionado ASP.NET Core 2.1.2
  - Foi adicionado NodeJS 10.0.0
  - Adicionado o Zulu OpenJDK 8.30.0.1
  - Foi adicionado Tomcat 8.5.31 e 9.0.8
  - Versões do PHP adicionado:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Foi adicionado Wincache 2.0.0.8
  - Atualizado Git para Windows para v 2.17.1.2
  - Kudu atualizada para 74.10611.3437
  
- **Atualizações ao sistema operacional subjacente de todas as funções**:
  - [Atualização de pilha de manutenção do Windows Server 2016 para sistemas baseados em x64 (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [07 de 2018 atualização cumulativa do Windows Server 2016 para sistemas baseados em x64 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Publicar os passos de atualização (opcional)

Para os clientes que pretendem migrar para o banco de dados independente do serviço de aplicações do Azure atual em implementações do Azure Stack, execute estes passos depois do serviço de aplicações do Azure no Azure Stack 1.3 atualização estar concluída:

> [!IMPORTANT]
> Este procedimento demora, aproximadamente 5 a 10 minutos.  Este procedimento envolve cancelar as sessões de início de sessão da base de dados existente.  Planear para o período de indisponibilidade migrar e validar o serviço de aplicações do Azure na migração de publicação do Azure Stack
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

1. Conversão de uma base de dados parcialmente independente.  Este passo irá incorrer em períodos de indisponibilidade que tem de ser eliminadas todas as sessões ativas

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

- Os trabalhos são não é possível alcançar o servidor de ficheiros quando o serviço de aplicações é implementado numa rede virtual existente e o servidor de ficheiros só está disponível na rede privada.  Isso é também chamado no serviço de aplicações do Azure, na documentação de implementação do Azure Stack.

Se optar por implementar numa rede virtual existente e um endereço IP interno para se ligar ao seu servidor de ficheiros, tem de adicionar uma regra de segurança de saída, permitindo que o tráfego entre a sub-rede de trabalho e o servidor de ficheiros SMB. Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
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

Consulte a documentação nas notas de versão do Azure Stack 1807.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do serviço de aplicações do Azure, consulte [serviço de aplicações do Azure no Descrição geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como preparar a implementação de serviço de aplicações no Azure Stack, veja [antes de começar com o serviço de aplicações no Azure Stack](azure-stack-app-service-before-you-get-started.md).
