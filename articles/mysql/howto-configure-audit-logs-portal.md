---
title: Configurar e acessar logs de auditoria-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MySQL no portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350404"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configurar e acessar os logs de auditoria do banco de dados do Azure para MySQL no portal do Azure

Você pode configurar os [logs de auditoria do banco de dados do Azure para MySQL](concepts-audit-logs.md) e as configurações de diagnóstico do portal do Azure.

> [!IMPORTANT]
> A funcionalidade de log de auditoria está atualmente em visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer este guia de instruções, você precisa de:

- [Banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

Habilite e configure o log de auditoria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione a sua base de dados do Azure para o servidor MySQL.

1. Na seção **configurações** na barra lateral, selecione **parâmetros do servidor**.
    ![Parâmetros do servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualize o parâmetro **audit_log_enabled** para on.
    logs de auditoria do ![Enable @ no__t-1

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    eventos de log do ![Audit @ no__t-1

1. Adicione qualquer usuário do MySQL a ser excluído do registro em log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário do MySQL.
    log de ![Audit excluir usuários @ no__t-1

1. Depois de alterar os parâmetros, você pode clicar em **salvar**. Ou você pode **descartar** suas alterações.
    ![Save @ no__t-1

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**.

1. Clique em "+ adicionar configuração de diagnóstico" @no__t-configuração de diagnóstico 0Add @ no__t-1

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais coletores de dados enviar os logs de auditoria (conta de armazenamento, Hub de eventos e/ou espaço de trabalho Log Analytics).

1. Selecione "MySqlAuditLogs" como o tipo de log.
configuração de diagnóstico ![Configure @ no__t-1

1. Depois de configurar os coletores de dados para canalizar os logs de auditoria, você pode clicar em **salvar**.
configuração de diagnóstico ![Save @ no__t-1

1. Acesse os logs de auditoria explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MySQL.