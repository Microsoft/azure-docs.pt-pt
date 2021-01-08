---
title: Rode credenciais de login em trabalhos Azure Stream Analytics
description: Este artigo descreve como atualizar as credenciais de entradas e sumidouros de saída em trabalhos Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: fd6c072f9783e8ff5d4d5e465b513c2e530bfd63
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015237"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rode as credenciais de login para entradas e saídas de um Trabalho de Análise de Fluxo

Sempre que regenerar credenciais para uma entrada ou saída de um trabalho stream Analytics, deve atualizar o trabalho com novas credenciais. Tens de parar o trabalho antes de atualizar as credenciais, não podes substituir as credenciais enquanto o trabalho está a decorrer. Para reduzir o desfasamento entre parar e reiniciar o trabalho, o Stream Analytics apoia o reinício de um trabalho a partir da sua última produção. Este tópico descreve o processo de rotação das credenciais de login e de reiniciar o trabalho com novas credenciais.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Regenerar novas credenciais e atualizar o seu trabalho com as novas credenciais 

Nesta secção, vamos acompanhá-lo através de credenciais regeneradoras para Blob Storage, Event Hubs, SQL Database e Table Storage. 

### <a name="blob-storagetable-storage"></a>Armazenamento de bolhas/armazenamento de mesa
1. Inscreva-se no portal Azure > navegue na conta de armazenamento que utilizou como entrada/saída para o trabalho stream Analytics.    
2. A partir da secção de definições, abra **as teclas de acesso**. Entre as duas teclas predefinidos (key1, key2), escolha a que não é usada pelo seu trabalho e regenera-a:  
   ![Chaves regeneradas para conta de armazenamento](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Copie a chave recém-gerada.    
4. A partir do portal Azure, navegue no seu trabalho stream Analytics > selecione **Stop** e espere que o trabalho pare.    
5. Localize a entrada/saída de armazenamento blob/mesa para a qual deseja atualizar as credenciais.    
6. Encontre o campo **Chave de Conta de Armazenamento** e cole a sua chave recém-gerada > clique em **Guardar**.    
7. Um teste de ligação iniciar-se-á automaticamente quando guardar as suas alterações, pode vê-lo a partir do separador notificações. Existem duas notificações: uma corresponde à poupança da atualização e outra corresponde ao teste da ligação:  
   ![Notificações após a edição da chave](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Comece a iniciar o seu trabalho a partir da última secção [de tempo parado.](#start-your-job-from-the-last-stopped-time)

### <a name="event-hubs"></a>Hubs de Eventos

1. Inscreva-se no portal Azure > navegue no Centro de Eventos que utilizou como entrada/saída para o trabalho stream Analytics.    
2. A partir da secção de definições, abra **as políticas de acesso partilhado** e selecione a política de acesso necessária. Entre a **Chave Primária** e a **Chave Secundária,** escolha a que não é usada pelo seu trabalho e regenera-a:  
   ![Chaves regeneração para centros de eventos](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Copie a chave recém-gerada.    
4. A partir do portal Azure, navegue no seu trabalho stream Analytics > selecione **Stop** e espere que o trabalho pare.    
5. Localize a entrada/saída dos centros de eventos para as quais pretende atualizar as credenciais.    
6. Encontre o campo **'Chave política do centro de eventos'** e cole a sua chave recém-gerada > clique em **Guardar**.    
7. Um teste de ligação iniciar-se-á automaticamente quando guardar as alterações, certifique-se de que passou com sucesso.    
8. Comece a iniciar o seu trabalho a partir da última secção [de tempo parado.](#start-your-job-from-the-last-stopped-time)

### <a name="sql-database"></a>Base de Dados SQL

Tem de se ligar à Base de Dados SQL para atualizar as credenciais de login de um utilizador existente. Pode atualizar credenciais utilizando o portal Azure ou uma ferramenta do lado do cliente, como o SQL Server Management Studio. Esta secção demonstra o processo de atualização de credenciais utilizando o portal Azure.

1. Inscreva-se no portal Azure > navegue na base de dados SQL que utilizou como saída para o trabalho de Stream Analytics.    
2. A partir do Explorador de **Dados,** faça login/conecte-se à sua base de dados > selecione o tipo de autorização como autenticação do **servidor SQL** > digite nos seus dados **de Login** e **palavra-passe** > Select **Ok**.  
   ![Credenciais regeneração para base de dados SQL](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. No separador de consulta, altere a palavra-passe de um dos seus utilizadores executando a seguinte consulta (certifique-se de que substitui `<user_name>` pelo seu nome de utilizador e pela sua nova `<new_password>` senha):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Tome nota da nova senha.    
5. A partir do portal Azure, navegue no seu trabalho stream Analytics > selecione **Stop** e espere que o trabalho pare.    
6. Localize a saída SQL Database para a qual pretende rodar as credenciais. Atualize a palavra-passe e guarde alterações.    
7. Um teste de ligação iniciar-se-á automaticamente quando guardar as alterações, certifique-se de que passou com sucesso.    
8. Comece a iniciar o seu trabalho a partir da última secção [de tempo parado.](#start-your-job-from-the-last-stopped-time)

### <a name="power-bi"></a>Power BI
1. Inscreva-se no portal Azure > navegue no seu trabalho stream Analytics > selecione **Stop** e espere que o trabalho pare.    
2. Localize a saída Power BI para a qual pretende renovar credenciais > clique na **autorização Renovar** (deve ver uma mensagem de sucesso) > **Guarde** as alterações.    
3. Um teste de ligação iniciar-se-á automaticamente quando guardar as alterações, certifique-se de que passou com sucesso.    
4. Comece a iniciar o seu trabalho a partir da última secção [de tempo parado.](#start-your-job-from-the-last-stopped-time)

## <a name="start-your-job-from-the-last-stopped-time"></a>Comece o seu trabalho desde a última paragem.

1. Navegue para o painel de **visão geral** do trabalho > selecione **Comece** a iniciar o trabalho.    
2. Selecione **Quando a última vez parou** > clique em **Iniciar**. Note que a opção "Quando a última paragem" só aparece se já executou o trabalho e teve alguma saída gerada. O trabalho é reiniciado com base no tempo do último valor de saída.
   ![Inicie o trabalho stream analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Próximos passos
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)