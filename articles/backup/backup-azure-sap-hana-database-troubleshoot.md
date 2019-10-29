---
title: Solucionar erros de backup de bancos de dados SAP HANA-backup do Azure
description: Descreve como solucionar erros comuns que podem ocorrer quando você usa o backup do Azure para fazer backup de bancos de dados do SAP HANA.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2019
ms.author: dacurwin
ms.openlocfilehash: 004d10b794c6eca2e078e437880f44d91ca30acb
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968458"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Solucionar problemas de backup de bancos de dados SAP HANA no Azure

Este artigo fornece informações de solução de problemas para fazer backup de bancos de dados SAP HANA em máquinas virtuais do Azure. A seção a seguir aborda dados conceituais importantes necessários para diagnosticar erros comuns no backup do SAP HANA.

## <a name="prerequisites"></a>Pré-requisitos

Como parte dos pré-requisitos, verifique se o script de [pré-](backup-azure-sap-hana-database.md#prerequisites)registro foi executado na máquina virtual em que o Hana está instalado.

### <a name="setting-up-permissions"></a>Configurando permissões

O que o script de auto-registro faz:

1. Cria AZUREWLBACKUPHANAUSER no sistema HANA e adiciona essas funções e permissões necessárias:
    - ADMIN. do banco de dados: para criar novos bancos durante a restauração.
    - LEITURA do catálogo: para ler o catálogo de backup.
    - SAP_INTERNAL_HANA_SUPPORT: para acessar algumas tabelas privadas.
2. Adiciona uma chave para Hdbuserstore para o plug-in do HANA para lidar com todas as operações (consultas de banco de dados, operações de restauração, configuração e execução de backup).

   Para confirmar a criação da chave, execute o comando HDBSQL no computador HANA com as credenciais do SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```

    A saída do comando deve exibir a chave {SID} {DBNAME}, com o usuário mostrado como AZUREWLBACKUPHANAUSER.

> [!NOTE]
> Verifique se você tem um conjunto exclusivo de arquivos SSFS em **/usr/SAP/{Sid}/Home/.HDB/** . Deve haver apenas uma pasta neste caminho.

### <a name="setting-up-backint-parameters"></a>Configurando parâmetros de BackInt

Depois que um banco de dados é escolhido para backup, o serviço de backup do Azure configura os parâmetros backInt no nível do banco de dados:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels: 1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout: 7200]

> [!NOTE]
> Verifique se esses parâmetros *não* estão presentes no nível do host. Os parâmetros de nível de host substituirão esses parâmetros e poderão causar um comportamento inesperado.

## <a name="restore-checks"></a>Restaurar verificações

### <a name="single-container-database-sdc-restore"></a>Restauração de banco de dados de contêiner único (SDC)

Tome cuidado com as entradas durante a restauração de um banco de dados de contêiner único (SDC) para HANA para outro computador SDC. O nome do banco de dados deve ser fornecido com letras minúsculas e com "SDC" anexado entre colchetes. A instância do HANA será exibida em maiúsculas.

Suponha que uma instância do HANA do SDC "H21" seja submetida a backup. A página itens de backup mostrará o nome do item de backup como **"H21 (SDC)"** . Se você tentar restaurar esse banco de dados para outro SDC de destino, digamos H11, as entradas a seguir precisarão ser fornecidas.

![SDC restaurar entradas](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenha em atenção os seguintes pontos:

- Por padrão, o nome do BD restaurado será populado com o nome do item de backup, ou seja, H21 (SDC)
- Selecionar o destino como H11 não alterará automaticamente o nome do banco de BD restaurado. **Ele deve ser editado para H11 (SDC)** . No caso de SDC, o nome do BD restaurado será a ID da instância de destino com letras minúsculas e ' SDC ' anexado entre colchetes.
- Como o SDC pode ter apenas um único banco de dados, você também precisa clicar na caixa de seleção para permitir a substituição dos existentes nos data com os dados do ponto de recuperação.
- O Linux diferencia maiúsculas de minúsculas e, portanto, não se esqueça de preservar o caso.

### <a name="multiple-container-database-mdc-restore"></a>Restauração de banco de dados de contêiner múltiplo (MDC)

Em vários bancos de dados de contêiner para HANA, a configuração padrão é SYSTEMDB + 1 ou mais bancos de dados de locatário. A restauração de uma instância de SAP HANA inteira significa restaurar bancos de SYSTEMDB e locatários. Uma restaura SYSTEMDB primeiro e, em seguida, prossegue para o banco de do locatário. Essencialmente, o banco de dados do sistema significa substituir as informações do sistema no destino selecionado. Isso também substitui as informações relacionadas ao BackInt na instância de destino. Portanto, depois que o banco de BD do sistema for restaurado para uma instância de destino, será necessário executar o script de pré-registro novamente. Somente as restaurações subsequentes do banco de bancos de locatário serão realizadas com sucesso.

## <a name="common-user-errors"></a>Erros comuns do usuário

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

dado| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Falha ao conectar ao sistema HANA. Verifique se o sistema está em execução.| O serviço de backup do Azure não pode se conectar ao HANA porque o banco de dados do HANA está inoperante. Ou o HANA está em execução, mas não permite que o serviço de backup do Azure se conecte. | Verifique se o serviço ou banco de dados HANA está inoperante. Se o banco de dados ou serviço do HANA estiver em execução, verifique se [todas as permissões estão definidas](#setting-up-permissions). Se a chave estiver ausente, execute novamente o script de auto-registro para criar uma nova chave. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Configuração de BACKINT inválida detectada. Interrompa a proteção e reconfigure o banco de dados.| Os parâmetros backInt estão especificados incorretamente para o backup do Azure. | Verifique se [os parâmetros estão definidos](#setting-up-backint-parameters). Se os parâmetros baseados em backInt estiverem presentes no HOST, remova-os. Se os parâmetros não estiverem presentes no nível do HOST, mas tiverem sido modificados manualmente em um nível de banco de dados, reverta-os para os valores apropriados, conforme descrito anteriormente. Ou execute o **Stop Protection e mantenha os dados de backup** do portal do Azure e selecione **retomar backup**.|
