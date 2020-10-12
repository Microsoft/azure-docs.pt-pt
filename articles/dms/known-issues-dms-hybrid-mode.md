---
title: Questões conhecidas/limitações de migração com a utilização do modo Híbrido
description: Conheça as questões conhecidas/limitações de migração utilizando o Azure Database Migration Service em modo híbrido.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291848"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Questões conhecidas/limitações de migração com a utilização do modo híbrido

Os problemas e limitações conhecidos associados à utilização do Azure Database Migration Service em modo híbrido são descritos nas seguintes secções.

## <a name="installer-fails-to-authenticate"></a>Instalador falha em autenticar

Depois de carregar o certificado para o seu AdApp, há um atraso de até alguns minutos antes de poder autenticar com o Azure. O instalador tentará voltar a tentar com algum atraso, mas é possível que o atraso de propagação seja maior do que a repetição, e verá uma mensagem **FailedToGetAccessTokenException.** Se o certificado foi carregado para o AdApp correto e o AppId correto foi fornecido em dmsSettings.js, tente executar novamente o comando de instalação.

## <a name="service-offline-after-successful-installation"></a>Serviço "offline" após instalação bem sucedida

Se o serviço aparecer como offline após o processo de instalação ter terminado com sucesso, tente utilizar os seguintes passos.

1. No portal Azure, no seu caso de Azure Database Migration Service, navegue para o separador de definições **Híbridas** e verifique se o trabalhador está registado verificando a grelha de trabalhadores registados.

    O estado deste trabalhador deve estar **online,** mas pode mostrar como **Offline** se houver algum problema.

2. No computador do trabalhador, verifique o estado do serviço executando o seguinte comando PowerShell:

    ```
    Get-Service Scenario*
    ```

    Este comando dá-lhe o estado do serviço Windows que executa o trabalhador. Só deve haver um único resultado. Se o trabalhador for parado, pode tentar reiniciá-lo utilizando o seguinte comando PowerShell:

    ```
    Start-Service Scenario*
    ```

    Também pode consultar o serviço no UI do Windows Services.

3. Se o serviço windows ciclos entre Running e Stop, então o trabalhador encontrou problemas de arranque. Consulte os registos híbridos do Serviço de Migração do Serviço de Migração da Base de Dados Azure para determinar o problema.

    - Os registos de processos de instalação são armazenados na pasta "logs" dentro da pasta a partir da qual o instalador foi executado.
    - Os registos híbridos do Serviço de Migração Azure Database estão armazenados na pasta **WorkerLogs,** na pasta em que o trabalhador está instalado. A localização predefinida para os ficheiros de registo do trabalhador híbrido é **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Usando o seu próprio certificado assinado

O certificado gerado pela ação GenerateCert é um certificado auto-assinado, que pode não ser aceitável com base nas suas políticas de segurança interna. Em vez de usar este certificado, pode fornecer o seu próprio certificado e fornecer a impressão digital em dmsSettings.js. Este certificado terá de ser carregado para o seu AdApp e instalado no computador no qual está a instalar o trabalhador híbrido Azure Database Migration Service. Em seguida, instale este certificado com a chave privada na loja de certificados Local Machine.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Gerir o serviço de trabalhadores como uma conta de baixo privilégio

Por padrão, o serviço híbrido Azure Database Migration Service funciona como conta do Sistema Local. Pode alterar a conta utilizada para este serviço desde que a conta que utiliza tenha permissões de rede. Para alterar a conta 'executar como' do serviço, utilize o seguinte processo.

1. Parar o serviço, seja através do Windows Services ou utilizando o comando Stop-Service em PowerShell.

2. Atualize o serviço para utilizar uma conta de início de são diferente.

3. Em certificados de computador local, dê permissões de chaves privadas para a nova conta para a Chave de **Aplicação Híbrida DMS** e certificados **DMS Scenario Engine Key Pair.**

    a. Abrir certmgr para ver as seguintes teclas:

    - Chave de aplicativo híbrido DMS
    - Chave de configuração do trabalhador híbrido DMS
    - Par chave do motor de cenário DMS

    b. Clique com o botão direito na entrada da **Chave da Aplicação Híbrida DMS,** aponte para **todas as tarefas**e, em seguida, selecione **Gerir Chaves Privadas**.

    c. No separador **Segurança,** selecione **Add**e, em seguida, insira o nome da conta.

    d. Use os mesmos passos para conceder permissão de chave privada para a nova conta para o certificado **DMS Scenario Engine Key Pair.**

## <a name="unregistering-the-worker-manually"></a>Desregundo manualmente o trabalhador

Se já não tiver acesso ao computador do trabalhador, pode desregistar o trabalhador e reutilizar a sua instância do Serviço de Migração da Base de Dados Azure, realizando os seguintes passos:

1. No portal Azure, chegue à sua instância do Serviço de Migração da Base de Dados Azure e, em seguida, navegue para a página de definições **híbridas.**

   A entrada do seu trabalhador aparece na lista, com o estado a aparecer como **Offline**.

2. Para a extrema direita da listagem de entrada do trabalhador, selecione as elipses e, em seguida, selecione **Unregister**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Abordar questões para cenários específicos de migração

As secções abaixo descrevem questões específicas do cenário relacionadas com a utilização do modo híbrido Azure Database Migration Service para realizar uma migração online.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Migrações on-line para Azure SQL Managed Instance

**Elevada utilização da CPU**

**Problema**: Para migrações on-line para SQL Managed Instance, o computador que executa o trabalhador híbrido irá encontrar uma alta utilização da CPU se houver demasiadas cópias de segurança ou se as cópias de segurança forem demasiado grandes.

**Mitigação**: Para mitigar este problema, utilize cópias de segurança comprimidos, divida a migração de modo a utilizar várias partilhas ou escalone o computador que executa o trabalhador híbrido.
