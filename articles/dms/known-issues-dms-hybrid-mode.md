---
title: Questões conhecidas/limitações de migração com a utilização do modo Híbrido
description: Conheça questões/limitações de migração conhecidas com a utilização do Serviço de Migração de Bases de Dados Azure em modo híbrido.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649607"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Questões conhecidas/limitações de migração com utilização do modo híbrido

As questões e limitações conhecidas associadas à utilização do Serviço de Migração de Bases de Dados Azure em modo híbrido são descritas nas seguintes secções.

## <a name="installer-fails-to-authenticate"></a>Instalador não autentica

Depois de enviar o certificado para o seu AdApp, há um atraso de até alguns minutos antes de poder autenticar com o Azure. O instalador tentará voltar a tentar com algum atraso, mas é possível que o atraso de propagação seja mais longo do que o retry, e verá uma mensagem **FailedToAccessAccessTokenException.** Se o certificado foi enviado para o AdApp correto e o AppId correto foi fornecido em dmsSettings.json, tente executar novamente o comando de instalação.

## <a name="service-offline-after-successful-installation"></a>Serviço "offline" após instalação bem sucedida

Se o serviço mostrar como offline após o processo de instalação terminar com sucesso, tente utilizar os seguintes passos.

1. No portal Azure, no seu caso do Serviço de Migração de Bases de Dados Azure, navegue para o separador de definições **Híbridae** e verifique se o trabalhador está registado verificando a grelha dos trabalhadores registados.

    O estado deste trabalhador deve estar **online,** mas pode mostrar como **Offline** se houver algum problema.

2. No computador do trabalhador, verifique o estado do serviço executando o seguinte comando PowerShell:

    ```
    Get-Service Scenario*
    ```

    Este comando confere-lhe o estado do serviço Windows que executa o trabalhador. Só deve haver um único resultado. Se o trabalhador for parado, pode tentar reiniciá-lo utilizando o seguinte comando PowerShell:

    ```
    Start-Service Scenario*
    ```

    Também pode consultar o serviço no Windows Services UI.

3. Se o serviço Windows se ciclou entre Correr e Parar, então o trabalhador encontrou problemas no arranque. Verifique os registos híbridos do Serviço de Migração de Bases de Dados Azure para determinar o problema.

    - Os registos do processo de instalação são armazenados na pasta "logs" dentro da pasta a partir da qual o instalador executável foi executado.
    - Os registos híbridos do Serviço de Migração azure Database são armazenados na pasta **WorkerLogs,** na pasta em que o trabalhador está instalado. A localização padrão dos ficheiros de registo dos trabalhadores híbridos é **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Usando o seu próprio certificado assinado

O certificado gerado pela ação GenerateCert é um certificado auto-assinado, que pode não ser aceitável com base nas suas políticas de segurança interna. Em vez de utilizar este certificado, pode fornecer o seu próprio certificado e fornecer a impressão digital em dmsSettings.json. Este certificado terá de ser enviado para o seu AdApp e instalado no computador no qual está a instalar o trabalhador híbrido do Serviço de Migração de Bases de Dados Azure. Em seguida, instale este certificado com a chave privada na loja de certificados Local Machine.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Gerir o serviço de trabalhadores como uma conta de baixo privilégio

Por padrão, o serviço híbrido de trabalho híbrido do Serviço de Migração de Bases de Dados Azure funciona como conta do Sistema Local. Pode alterar a conta utilizada para este serviço desde que a conta que utiliza tenha permissões de rede. Para alterar a conta de 'executar como' do serviço, utilize o seguinte processo.

1. Pare o serviço, através dos Serviços Windows ou utilizando o comando stop-service no PowerShell.

2. Atualize o serviço para utilizar uma conta de logon diferente.

3. Em certmgr para certificados de computador local, dê permissões de chave privadas para a nova conta para os certificados **DMS Hybrid App Key** e **DMS Scenario Engine Pair.**

    a. Abra certmgr para ver as seguintes teclas:

    - Chave de aplicativo sotada híbrida DMS
    - Chave de configuração de trabalhador híbrido DMS
    - Par de chaves de motor de cenário DMS

    b. Clique à direita na entrada da chave da chave da **aplicação híbrida DMS,** aponte para **todas as tarefas**e, em seguida, selecione **Gerir chaves privadas**.

    c. No separador **'Segurança',** selecione **Adicionar**, e, em seguida, introduza o nome da conta.

    d. Utilize os mesmos passos para conceder autorização de chave privada para a nova conta para o certificado **DMS Scenario Engine Pair.**

## <a name="unregistering-the-worker-manually"></a>Desregistar manualmente o trabalhador

Se já não tiver acesso ao computador operário, pode desregistar o trabalhador e reutilizar a sua instância de Serviço de Migração azure, executando os seguintes passos:

1. No portal Azure, chegou à sua instância do Serviço de Migração de Bases de Dados Azure e, em seguida, navegue para a página de definições **Híbrida.**

   A sua entrada no trabalhador aparece na lista, com o estado a mostrar-se **offline**.

2. À extrema-direita da lista de entrada do trabalhador, selecione as elipses e, em seguida, selecione **Desregistre**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Abordar questões para cenários específicos de migração

As secções abaixo descrevem questões específicas do cenário relacionadas com a utilização do modo híbrido do Serviço de Migração de Bases de Dados Azure para realizar uma migração online.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Migrações online para a Base de Dados Azure SQL gerida

**Elevada utilização da CPU**

**Problema**: Para as migrações on-line para a Base de Dados SQL gerida sql, o computador que executa o trabalhador híbrido encontrará uma alta utilização de CPU se houver demasiadas cópias de segurança ou se as cópias de segurança forem demasiado grandes.

**Mitigação**: Para mitigar este problema, utilize backups comprimidos, divida a migração de modo a utilizar várias ações ou dimensione o computador que executa o trabalhador híbrido.
