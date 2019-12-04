---
title: Acessar logs de consulta lentos-portal do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs lentos no banco de dados do Azure para MySQL no portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 52d219a94676e0f1ce4ceac31b0b50c9d36665ba
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770582"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configurar e acessar logs de consulta lentos no portal do Azure

Você pode configurar, listar e baixar os [logs de consulta lenta do banco de dados do Azure para MySQL](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas neste artigo exigem que você tenha o [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurar registro em log
Configure o acesso ao log de consultas lentas do MySQL. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o servidor do banco de dados do Azure para MySQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. 
   ![captura de tela de opções de logs do servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **clique aqui para habilitar logs e configurar parâmetros de log**.

5. Altere os parâmetros que você precisa ajustar. Todas as alterações feitas nesta sessão são realçadas em roxo. 

   Depois de alterar os parâmetros, selecione **salvar**. Ou você pode descartar suas alterações.

   ![Captura de tela de opções de parâmetros de servidor](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na página **parâmetros do servidor** , você pode retornar à lista de logs fechando a página.

## <a name="view-list-and-download-logs"></a>Exibir lista e baixar logs
Após o início do log, você pode exibir uma lista de logs de consultas lentas disponíveis e baixar arquivos de log individuais.

1. Abra o portal do Azure.

2. Selecione o servidor do banco de dados do Azure para MySQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. A página mostra uma lista de seus arquivos de log.

   ![Captura de tela da página de logs do servidor, com a lista de logs realçada](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A Convenção de nomenclatura do log é **MySQL-lento-< o nome do servidor >-yyyymmddhh. log**. A data e a hora usadas no nome de arquivo são a hora em que o log foi emitido. Os arquivos de log são girados a cada 24 horas ou 7,5 GB, o que vier primeiro. 

4. Se necessário, use a caixa de pesquisa para restringir rapidamente um log específico, com base na data e hora. A pesquisa está no nome do log.

5. Para baixar arquivos de log individuais, selecione o ícone de seta para baixo ao lado de cada arquivo de log na linha da tabela.

   ![Captura de tela da página de logs do servidor, com o ícone de seta para baixo realçado](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico** > **Adicionar configurações de diagnóstico**.

   ![Captura de tela das opções de configurações de diagnóstico](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais coletores de dados enviarão os logs de consulta lentos (conta de armazenamento, Hub de eventos ou espaço de trabalho Log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de log.
![captura de tela das opções de configuração de configurações de diagnóstico](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Depois de configurar os coletores de dados para canalizar os logs de consulta lentos, selecione **salvar**.
![captura de tela das opções de configuração de configurações de diagnóstico, com salvar realçado](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Acesse os logs de consulta lento explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Passos seguintes
- Consulte [acessar logs de consulta lentos na CLI](howto-configure-server-logs-in-cli.md) para saber como baixar logs de consulta lentos programaticamente.
- Saiba mais sobre [logs de consulta lentos](concepts-server-logs.md) no banco de dados do Azure para MySQL.
- Para obter mais informações sobre as definições de parâmetro e o log do MySQL, consulte a documentação do MySQL em [logs](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).