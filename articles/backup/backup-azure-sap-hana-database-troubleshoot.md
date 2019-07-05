---
title: Resolver problemas de erros ao backup bancos de dados do SAP HANA com o Azure Backup | Documentos da Microsoft
description: Este guia explica como resolver erros comuns ao tentar a cópia de segurança bases de dados SAP HANA com o Azure Backup.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514182"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Resolver problemas de cópia de segurança do servidor do SAP HANA no Azure

Este artigo fornece informações de resolução de problemas para proteger bases de dados do SAP HANA em máquinas de virtuais do Azure. Antes de avançar para a resolução de problemas, vamos entender alguns pontos-chave sobre as permissões e definições.

## <a name="understanding-pre-requisites"></a>Pré-requisitos de compreensão

Como parte da [pré-requisitos](backup-azure-sap-hana-database.md#prerequisites), o script de pré-registo deve ser executado na máquina virtual em que o HANA é instalado para configurar as permissões corretas.

### <a name="setting-up-permissions"></a>Configuração de permissões

O que faz o script de pré-registo:

1. Cria AZUREWLBACKUPHANAUSER no sistema HANA e adiciona necessário funções e permissões, conforme indicado abaixo:
    - ADMINISTRADOR de banco de dados - criar novo bds durante o restauro
    - LEITURA de catálogo – para ler o catálogo de cópia de segurança
    - SAP_INTERNAL_HANA_SUPPORT – para aceder a algumas tabelas privadas
2. Adiciona a chave para Hdbuserstore Plug-in do HANA para fazer todas as operações (consulta de base de dados, configurando o backup, fazer cópia de segurança, fazendo o restauro)
   
   - Para confirmar a criação da chave, execute o comando HDBSQL dentro da máquina do HANA com credenciais SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    A saída do comando deve apresentar a chave {SID} {DBNAME} com o utilizador como 'AZUREWLBACKUPHANAUSER'.

> [!NOTE]
> Certifique-se de que tem um conjunto exclusivo de ficheiros SSFS no caminho "/ usr/sap/{SID}/home/.hdb/". Deve haver apenas uma pasta sob esse caminho.

### <a name="setting-up-backint-parameters"></a>Configurar parâmetros de BackInt

Depois de uma base de dados é escolhido para cópia de segurança, o serviço de cópia de segurança do Azure irá configurar backInt parâmetros ao nível da base de dados.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Certificar-se de que estes parâmetros não estão presentes no nível do anfitrião. Parâmetros de nível de anfitrião irão substituir estes parâmetros e podem causar um comportamento diferente que o esperado.

## <a name="understanding-common-user-errors"></a>Compreender erros de utilizador comuns

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Falha ao ligar ao system.check HANA que seu sistema estiver em execução.| O serviço de cópia de segurança do Azure não é possível ligar ao HANA, porque a BD do HANA está inativo. Ou HANA está em execução, mas não permitir que o serviço de cópia de segurança do Azure para ligar | Verifique se o serviço/DB de HANA está inativo. Se o serviço e DB HANA está a funcionar, verifique se todas as permissões estão configuradas como mencionado [aqui](#setting-up-permissions). Se a chave está em falta, execute novamente o script de pré-registo para criar uma nova chave. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Configuração de Backint inválido detetado. Pare a proteção e reconfigurar a base de dados.| Os parâmetros de backInt incorretamente são especificados para o Azure Backup. | Verifique os parâmetros são como mencionado [aqui](#setting-up-backint-parameters). Se existirem parâmetros backInt com base no anfitrião, em seguida, removê-los. Se parâmetros não estão presentes no anfitrião, mas foram modificados manualmente num nível de base de dados, em seguida, revertê-las para os valores adequados conforme mencionado acima. Ou "pare a proteção com manter dados" do portal do Azure e "retomar cópia de segurança" mais uma vez.|
