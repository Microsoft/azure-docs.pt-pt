---
title: Rode as credenciais de login nos trabalhos da Azure Stream Analytics
description: Este artigo descreve como atualizar as credenciais de inputs e afunda-se em empregos de Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3ae639dd7c5a42fc6880240988f0fb2817b09f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425979"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rode as credenciais de login para inputs e saídas de um Stream Analytics Job

Sempre que regenera credenciais para uma entrada ou saída de um trabalho stream analytics, deve atualizar o trabalho com novas credenciais. Tens de parar o trabalho antes de atualizar as credenciais, não podes substituir as credenciais enquanto o trabalho está a decorrer. Para reduzir o desfasamento entre parar e reiniciar o trabalho, o Stream Analytics suporta retomar um trabalho a partir da sua última saída. Este tópico descreve o processo de rotação das credenciais de login e reinício do trabalho com novas credenciais.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Regenerar novas credenciais e atualizar o seu trabalho com as novas credenciais 

Nesta secção, iremos acompanhá-lo através de credenciais regenerativas para Armazenamento de Blob, Hubs de Eventos, Base de Dados SQL e Armazenamento de Mesa. 

### <a name="blob-storagetable-storage"></a>Armazenamento de bolhas/Armazenamento de mesa
1. Inscreva-se no portal Azure > navegue na conta de armazenamento que utilizou como entrada/saída para o trabalho do Stream Analytics.    
2. A partir da secção de definições, abra **as teclas de acesso**. Entre as duas teclas predefinidas (tecla1, tecla2), escolha a que não é usada pelo seu trabalho e regenera-a:  
   ![Chaves regeneradas para conta de armazenamento](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Copie a chave recém-gerada.    
4. A partir do portal Azure, navegue no seu trabalho de Stream Analytics > selecione **Stop** e espere que o trabalho pare.    
5. Localize a entrada/saída de armazenamento Blob/Table para a qual pretende atualizar as credenciais.    
6. Encontre o campo **chave da conta** de armazenamento e colhe a sua chave recém-gerada > clique em **Guardar**.    
7. Um teste de ligação começará automaticamente quando guardar as suas alterações, pode vê-lo a partir do separador de notificações. Existem duas notificações: uma corresponde a salvar a atualização e outra corresponde ao teste da ligação:  
   ![Notificações após a edição da chave](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Comece a trabalhar a partir da última secção de [tempo parada.](#start-your-job-from-the-last-stopped-time)

### <a name="event-hubs"></a>Hubs de Eventos

1. Inscreva-se no portal Azure > navegue no Centro de Eventos que utilizou como entrada/saída para o trabalho do Stream Analytics.    
2. A partir da secção de definições, abra as políticas de **acesso partilhado** e selecione a política de acesso necessária. Entre a **Chave Primária** e a **Chave Secundária,** escolha a que não é usada pelo seu trabalho e regenera-a:  
   ![Chaves regeneradas para Centros de Eventos](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Copie a chave recém-gerada.    
4. A partir do portal Azure, navegue no seu trabalho de Stream Analytics > selecione **Stop** e espere que o trabalho pare.    
5. Localize a entrada/saída dos hubs do Evento para o qual pretende atualizar credenciais.    
6. Encontre o campo chave da chave da política do Hub de **Eventos** e colá a sua chave recém-gerada > clique em **Guardar**.    
7. Um teste de ligação começará automaticamente quando guardar as suas alterações, certifique-se de que passou com sucesso.    
8. Comece a trabalhar a partir da última secção de [tempo parada.](#start-your-job-from-the-last-stopped-time)

### <a name="sql-database"></a>SQL Database

É necessário ligar-se à base de dados SQL para atualizar as credenciais de login de um utilizador existente. Pode atualizar credenciais utilizando o portal Azure ou uma ferramenta do lado do cliente, como o SQL Server Management Studio. Esta secção demonstra o processo de atualização de credenciais utilizando o portal Azure.

1. Inscreva-se no portal Azure > navegue na base de dados SQL que utilizou como saída para o trabalho do Stream Analytics.    
2. Do explorador de **dados,** inicie sessão/ligação à sua base de dados > selecione o tipo de autorização como a autenticação do **servidor SQL** > tipo nos detalhes de **login** e **palavra-passe** > Selecione **Ok**.  
   ![Credenciais regeneradas para base de dados SQL](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. No separador de consulta, altere a palavra-passe para um dos seus `<user_name>` utilizadores executando `<new_password>` a seguinte consulta (certifique-se de substituir pelo seu nome de utilizador e pela sua nova palavra-passe):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Tome nota da nova senha.    
5. A partir do portal Azure, navegue no seu trabalho de Stream Analytics > selecione **Stop** e espere que o trabalho pare.    
6. Localize a saída de base de dados SQL para a qual pretende rodar credenciais. Atualize a palavra-passe e guarde as alterações.    
7. Um teste de ligação começará automaticamente quando guardar as suas alterações, certifique-se de que passou com sucesso.    
8. Comece a trabalhar a partir da última secção de [tempo parada.](#start-your-job-from-the-last-stopped-time)

### <a name="power-bi"></a>Power BI
1. Inscreva-se no portal Azure > navegue no seu trabalho de Stream Analytics > selecione **Stop** e espere que o trabalho pare.    
2. Localize a saída power BI para a qual pretende renovar as credenciais > clique na **autorização Renovar** (deve ver uma mensagem de sucesso) > **Guarde** as alterações.    
3. Um teste de ligação começará automaticamente quando guardar as suas alterações, certifique-se de que passou com sucesso.    
4. Comece a trabalhar a partir da última secção de [tempo parada.](#start-your-job-from-the-last-stopped-time)

## <a name="start-your-job-from-the-last-stopped-time"></a>Comece o seu trabalho a partir do último tempo parado

1. Navegue para o painel de **visão geral** do trabalho > selecione **Começar** a iniciar o trabalho.    
2. Selecione **Quando parado pela última vez** > clicar Em **Iniciar**. Note que a opção "Quando foi parada" só aparece se já tiver feito o trabalho e tiver alguma saída gerada. O trabalho é reiniciado com base no tempo do último valor de produção.
   ![Inicie o trabalho de Stream Analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
