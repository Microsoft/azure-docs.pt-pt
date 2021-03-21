---
title: Copie ficheiros novos e alterados por LastModifiedDate
description: Saiba como utilizar um modelo de solução para copiar ficheiros novos e alterados pela LastModifiedDate com a Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 94d09dc96d017dba3b16cc5fe113272a9393741d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362085"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copie ficheiros novos e alterados por LastModifiedDate com Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que pode usar para copiar ficheiros novos e alterados apenas pela LastModifiedDate de uma loja baseada em ficheiros para uma loja de destino. 

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo seleciona primeiro os novos ficheiros e alterados apenas pelos seus atributos **LastModifiedDate**, e depois copia os ficheiros selecionados da loja de origem de dados para a loja de destino de dados.

O modelo contém uma atividade:
- **Copie** para copiar ficheiros novos e alterados apenas pela LastModifiedDate de uma loja de ficheiros para uma loja de destino.

O modelo define seis parâmetros:
-  *FolderPath_Source* é o caminho da pasta onde pode ler os ficheiros da loja de origem. Tem de substituir o valor predefinido pelo seu próprio caminho de pasta.
-  *Directory_Source* é o caminho do sub-esfoldador onde se pode ler os ficheiros da loja de origem. Tem de substituir o valor predefinido pelo seu próprio caminho de sub-dobra.
-  *FolderPath_Destination* é o caminho da pasta onde pretende copiar ficheiros para a loja de destino. Tem de substituir o valor predefinido pelo seu próprio caminho de pasta.
-  *Directory_Destination* é o caminho do sub-esfolder onde pretende copiar ficheiros para a loja de destino. Tem de substituir o valor predefinido pelo seu próprio caminho de sub-dobra.
-  *LastModified_From* é utilizado para selecionar os ficheiros cujo atributo LastModifiedDate é após ou igual a este valor de data.  Para selecionar apenas os novos ficheiros, que não foram copiados da última vez, este valor da data pode ser o momento em que o pipeline foi acionado da última vez. Pode substituir o valor predefinido '2019-02-01T00:00:00Z' pelo esperado ÚltimoModifiedDate no tempo de UTC. 
-  *LastModified_To* é utilizado para selecionar os ficheiros cujo atributo LastModifiedDate é antes deste valor de data. Para selecionar apenas os novos ficheiros, que não foram copiados da última vez, este valor da data pode ser o momento presente.  Pode substituir o valor predefinido '2019-02-01T00:00:00Z' pelo esperado ÚltimoModifiedDate no tempo de UTC. 

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá ao modelo **Copiar novos ficheiros apenas por LastModifiedDate**. Crie uma **nova** ligação ao seu armazém de origem. A loja de armazenamento de origem é de onde pretende copiar ficheiros.

    ![Criar uma nova ligação à fonte](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Crie uma **nova** ligação à sua loja de destino. A loja de destino é para onde pretende copiar ficheiros. 

    ![Criar uma nova ligação ao destino](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Selecione **Utilize este modelo.**

    ![Utilizar este modelo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Verá o gasoduto disponível no painel, como mostra o seguinte exemplo:

    ![Mostrar o oleoduto](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Selecione **Debug,** escreva o valor para os **Parâmetros** e selecione **Acabamento**.  Na imagem abaixo, definimos os parâmetros como seguintes.
   - **FolderPath_Source** = erússão
   - **Directory_Source** = sub-dobra
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = sub-dobra
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    O exemplo indica que os ficheiros, que foram modificados pela última vez dentro do período **(2019-02-01T00:00:00Z** a **2019-03-01T00:00:00Z**) serão copiados da **origem/sub-dobra do** caminho de origem para o **destino destino destino.0folder/subfolder**.  Pode substituí-los por seus próprios parâmetros.

    ![Executar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Reveja o resultado. Verá apenas os ficheiros modificados pela última vez dentro do tempo configurado foram copiados para a loja de destino.

    ![Reveja o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Agora pode adicionar um gatilho de janelas para automatizar este oleoduto, para que o gasoduto possa sempre copiar ficheiros novos e alterados apenas por LastModifiedDate periodicamente.  **Selecione Adicionar o gatilho** e selecione **Novo/Editar**.

    ![Screenshot que realça a opção de menu Novo/Editar que aparece quando seleciona Adicionar gatilho.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Na janela **Add Triggers,** selecione **+ Novo**.

9. Selecione **A janela de paragem** para o tipo de gatilho, desaccione **cada 15 minutos** como recorrência (pode alterar para qualquer intervalo). Selecione **Sim** para caixa ativada e, em seguida, selecione **OK**.

    ![Criar acionador](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Desaccione o valor dos **parâmetros de execução** do gatilho como seguintes e selecione **Terminar**.
    - **FolderPath_Source**  =  **2018**.  Pode substituir-se pela sua pasta na loja de dados de origem.
    - **Directory_Source**  =  **sub-dobrador**.  Pode substituir-se pela sua sub-dobradeira na loja de dados de origem.
    - **FolderPath_Destination**  =  **destinationfolder**.  Pode substituir pela sua pasta na loja de dados de destino.
    - **Directory_Destination**  =  **sub-dobrador**.  Pode substituir pela sua sub-dobradeira na loja de dados de destino.
    - **LastModified_From**  =   **\@ gatilho().outputs.windowStartTime**.  É uma variável do sistema a partir do gatilho que determina a hora em que o gasoduto foi acionado da última vez.
    - **LastModified_To**  =  **\@ gatilho().outputs.windowEndTime**.  É uma variável do sistema a partir do gatilho que determina o momento em que o gasoduto é acionado desta vez.
    
    ![Parâmetros de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Selecione **Publicar Tudo**.
    
    ![Publicar Tudo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Crie novos ficheiros na sua pasta de origem da data source store.  Está agora à espera que o gasoduto seja acionado automaticamente e apenas os novos ficheiros serão copiados para a loja de destino.

13. Selecione o separador **Monitor** no painel de navegação esquerdo e aguarde cerca de 15 minutos se a recorrência do gatilho tiver sido definida a cada 15 minutos. 

14. Reveja o resultado. Verá que o seu oleoduto será acionado automaticamente a cada 15 minutos, e apenas os ficheiros novos ou alterados da loja de origem serão copiados para a loja de destino em cada corrida de gasoduto.

    ![Screenshot que mostra os resultados que regressam quando o gasoduto é acionado.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
