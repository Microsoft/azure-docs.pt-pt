---
title: 4 notas de versão de atualização do serviço de aplicações no Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as novidades na atualização de quatro para o serviço de aplicações no Azure Stack, os problemas conhecidos e onde pode transferir a atualização.
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
ms.date: 11/13/2018
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 5108d4f65208f12875ad592e2e9222f8e1fdb130
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991086"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>Serviço de aplicações no notas de versão de atualização 4 do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Estas notas de versão descrevem as melhorias e correções no serviço de aplicações do Azure no Azure Stack atualização 4 e os problemas conhecidos. Problemas conhecidos são divididos em problemas diretamente relacionados com a implementação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização de 1809 seu sistema integrado do Azure Stack ou implementar o development kit do Azure Stack mais recentes antes de implementar 1.4 de serviço de aplicações do Azure.
>
>

## <a name="build-reference"></a>Criar a referência

O serviço de aplicações no número de compilação do Azure Stack atualização 4 é **78.0.13698.5**

### <a name="prerequisites"></a>Pré-requisitos

Consulte a [documentação antes de começar a utilizar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implementação.

Antes de iniciar a atualização do serviço de aplicações do Azure no Azure Stack para 1.4:

- Certifique-se de que todas as funções estiverem prontos na administração do serviço de aplicações do Azure no Portal de administração do Azure Stack

- Cópia de segurança do serviço de aplicações e bases de dados mestra:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Criar cópias de segurança da partilha de ficheiros de conteúdo da aplicação de inquilino

- Distribua a extensão de Script personalizado versão 1.9 do Marketplace

### <a name="new-features-and-fixes"></a>Novas funcionalidades e correções

Serviço de aplicações do Azure no Azure Stack atualização 4 inclui as seguintes melhorias e correções:

- Resolução [2018 de CVE-8600](https://aka.ms/CVE20188600) Cross Site Scripting vulnerabilidade.

- Foi adicionado suporte para versão de API do serviço de aplicações de 2018-02-01

- Atualiza para **inquilino de serviço de aplicações, o administrador, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- Atualizações de serviço básico para melhorar a fiabilidade e ativar mais fácil diagnóstico dos problemas comuns de mensagens de erro.

- **Atualizações para as seguintes arquiteturas de aplicações e ferramentas**:
  - Foi adicionado NodeJS 10.6.0
  - Foi adicionado NPM 6.1.0
  - Adicionado o Zulu OpenJDK 8.31.0.2
  - Foi adicionado Tomcat 8.5.34 e 9.0.12
  - Versões do PHP adicionado:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Atualizar para versões de Python:
    - 2.7.15
    - 3.6.6
  - Atualizado Git para Windows para v 2.17.1.2
  - Kudu atualizada para 78.11022.3613
  
- **Atualizações ao sistema operacional subjacente de todas as funções**:
  - [10 de 2018 atualização cumulativa do Windows Server 2016 para sistemas baseados em x64 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Resolvido o problema de validação de modelo ao implementar o Wordpress; DNN; e itens de Galeria de Orchard CMS

- Resolvido o problema de configuração quando o Azure Stack gira o certificado de cliente do Azure Resource Manager

- Restaurar a funcionalidade nas configurações cruzado a partilha de recursos de origem no portal de inquilino do serviço de aplicações

- Apresentar a mensagem de erro na experiência do portal de administração do serviço de aplicações quando o plano de controlo do fornecedor de recursos não é possível ligar à instância do SQL Server configurada

- Certifique-se de que ponto final é especificado na cadeia de ligação de armazenamento personalizado quando especificado na nova aplicação de função

### <a name="post-deployment-steps"></a>Passos de pós-implementação

> [!IMPORTANT]  
> Se forneceu a RP de serviço de aplicações com uma sempre na instância do SQL tem [adicionar as bases de dados appservice_hosting e appservice_metering a um grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) e sincronizar as bases de dados para evitar qualquer perda de serviço do evento de uma ativação pós-falha de base de dados.

### <a name="post-update-steps-optional"></a>Etapas de pós-atualização (opcionais)

Para os clientes que pretendem migrar para o banco de dados independente do serviço de aplicações do Azure atual em implementações do Azure Stack, execute estes passos depois do serviço de aplicações do Azure no Azure Stack 1.4 atualização estar concluída:

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
