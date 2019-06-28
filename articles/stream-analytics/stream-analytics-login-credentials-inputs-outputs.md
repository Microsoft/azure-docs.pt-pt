---
title: Alternar credenciais de início de sessão nas tarefas do Azure Stream Analytics
description: Este artigo descreve como atualizar as credenciais de entradas e Coletores de saída no Azure Stream Analytics tarefas.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d5ff0d33780362752b07361de5320707b402a3a2
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329883"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Alternar credenciais de início de sessão para entradas e saídas de uma tarefa do Stream Analytics

Sempre que voltar a gerar as credenciais de uma entrada ou saída de uma tarefa do Stream Analytics, deve atualizar a tarefa com novas credenciais. Tem de parar a tarefa antes de atualizar as credenciais, não pode substituir as credenciais, enquanto a tarefa está em execução. Para reduzir o atraso entre interromper e reiniciar a tarefa, o Stream Analytics oferece suporte a retomar um trabalho de sua última saída. Este tópico descreve o processo de efetuar a rotação das credenciais de início de sessão e reiniciar a tarefa com novas credenciais.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Voltar a gerar novas credenciais e atualize o seu trabalho com as novas credenciais 

Nesta secção, iremos irá guiá-lo através de credenciais de regeneração para armazenamento de BLOBs, os Hubs de eventos, base de dados SQL e armazenamento de tabelas. 

### <a name="blob-storagetable-storage"></a>Armazenamento de tabela do armazenamento de BLOBs
1. Inicie sessão no portal do Azure > procurar a conta de armazenamento que é utilizada como entrada/saída para a tarefa do Stream Analytics.    
2. A partir da secção definições, abra **chaves de acesso**. Entre as chaves de duas predefinidas (chave1, chave2), escolher aquele que não é utilizado pelo seu trabalho e gerá-lo novamente:  
   ![Regenerar chaves de conta de armazenamento](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Copie a chave recentemente gerada.    
4. No portal do Azure, procurar a tarefa de Stream Analytics > selecione **parar** e espere até que a tarefa parar.    
5. Localize o Blob/tabela entrada/saída de armazenamento para o qual pretende atualizar as credenciais.    
6. Encontrar o **chave de conta de armazenamento** campo e cole a sua chave gerada recentemente > clique em **guardar**.    
7. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, pode vê-lo a partir do separador notificações. Existem dois notificações-corresponde ao guardar a atualização e outros corresponde ao teste de ligação:  
   ![Notificações depois de editar a chave](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Avance para [iniciar a tarefa da última hora de paragem](#start-your-job-from-the-last-stopped-time) secção.

### <a name="event-hubs"></a>Hubs de Eventos

1. Inicie sessão no portal do Azure > procurar o Hub de eventos que utilizou como entrada/saída para a tarefa do Stream Analytics.    
2. A partir da secção definições, abra **políticas de acesso partilhado** e selecione a política de acesso necessária. Entre as **chave primária** e **chave secundária**, escolher aquele que não é utilizado pelo seu trabalho e gerá-lo novamente:  
   ![Voltar a gerar chaves dos Hubs de eventos](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Copie a chave recentemente gerada.    
4. No portal do Azure, procurar a tarefa de Stream Analytics > selecione **parar** e espere até que a tarefa parar.    
5. Localize o evento de hubs de entradam/saídam para o qual pretende atualizar as credenciais.    
6. Encontrar o **chave de política do Hub de eventos** campo e cole a sua chave gerada recentemente > clique em **guardar**.    
7. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, certifique-se de que tem passados com êxito.    
8. Avance para [iniciar a tarefa da última hora de paragem](#start-your-job-from-the-last-stopped-time) secção.

### <a name="sql-database"></a>SQL Database

Tem de se ligar à base de dados SQL para atualizar as credenciais de início de sessão de um utilizador existente. Pode atualizar as credenciais com o portal do Azure ou uma ferramenta de lado do cliente como o SQL Server Management Studio. Esta secção demonstra o processo de atualização de credenciais com o portal do Azure.

1. Inicie sessão no portal do Azure > procurar a base de dados do SQL que utilizou como saída para a tarefa do Stream Analytics.    
2. Da **Explorador de dados**, início de sessão/ligar a sua base de dados > selecione o tipo de autorização como **autenticação do SQL server** > escreva seu **início de sessão** e  **Palavra-passe** detalhes > selecione **Ok**.  
   ![Voltar a gerar as credenciais para a base de dados SQL](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. No separador de consulta, alterar a palavra-passe para um dos seus utilizadores ao executar a consulta seguinte (não se esqueça de substituir `<user_name>` com o seu nome de utilizador e `<new_password>` com sua nova palavra-passe):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Aponte a palavra-passe nova.    
5. No portal do Azure, procurar a tarefa de Stream Analytics > selecione **parar** e espere até que a tarefa parar.    
6. Localize a saída de base de dados SQL para o qual pretende rodar credenciais. Atualize a palavra-passe e guardar as alterações.    
7. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, certifique-se de que tem passados com êxito.    
8. Avance para [iniciar a tarefa da última hora de paragem](#start-your-job-from-the-last-stopped-time) secção.

### <a name="power-bi"></a>Power BI
1. Inicie sessão no portal do Azure > procurar a tarefa de Stream Analytics > selecione **parar** e espere até que a tarefa parar.    
2. Localize a saída do Power BI para o qual pretende renovar credenciais > Clique a **renovar a autorização** (deverá ver uma mensagem de êxito) > **guardar** as alterações.    
3. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, certifique-se de que passou com êxito.    
4. Avance para [iniciar a tarefa da última hora de paragem](#start-your-job-from-the-last-stopped-time) secção.

## <a name="start-your-job-from-the-last-stopped-time"></a>Iniciar a tarefa da última hora de paragem

1. Navegue para a tarefa **descrição geral** painel > selecione **iniciar** para iniciar a tarefa.    
2. Selecione **quando foi a última paragem** > clique em **iniciar**. Tenha em atenção que a opção "quando foi a última paragem" só é apresentada se anteriormente foi executada a tarefa e tinha algumas saídas gerado. A tarefa for reiniciada com base na hora da última saída do valor.
   ![Iniciar a tarefa de Stream Analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
