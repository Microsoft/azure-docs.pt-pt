---
title: Copiar novos e alterados ficheiros por LastModifiedDate
description: Saiba como utilizar um modelo de solução para copiar novos ficheiros alterados pelo LastModifiedDate com a Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 363af1012b327a55703cc6e35a02dc1f27869bd0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629070"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiar novos e alterados ficheiros por LastModifiedDate com fábrica de dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que pode usar para copiar novos ficheiros e alterados apenas pelo LastModifieDDate de uma loja baseada em ficheiros para uma loja de destino. 

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo seleciona primeiro os novos ficheiros e alterou ficheiros apenas pelos seus atributos **LastModifieDDate**, e depois copia os ficheiros selecionados da loja de dados para a loja de destino de dados.

O modelo contém uma atividade:
- **Copie** para copiar novos ficheiros e altere ficheiros apenas pelo LastModifieDDate de uma loja de ficheiros para uma loja de destino.

O modelo define seis parâmetros:
-  *FolderPath_Source* é o caminho da pasta onde pode ler os ficheiros da loja de origem. Tem de substituir o valor predefinido pelo seu próprio caminho de pasta.
-  *Directory_Source* é o caminho da subpasta onde pode ler os ficheiros da loja de origem. Tem de substituir o valor predefinido pelo seu próprio caminho de subpasta.
-  *FolderPath_Destination* é o caminho da pasta onde pretende copiar ficheiros para a loja de destino. Tem de substituir o valor predefinido pelo seu próprio caminho de pasta.
-  *Directory_Destination* é o caminho da subpasta onde pretende copiar ficheiros para a loja de destino. Tem de substituir o valor predefinido pelo seu próprio caminho de subpasta.
-  *LastModified_From* é utilizado para selecionar os ficheiros cujo atributo LastModifiedDate é posterior ou igual a este valor de data.  Para selecionar apenas os novos ficheiros, que não foram copiados da última vez, este valor de data pode ser o momento em que o gasoduto foi acionado da última vez. Pode substituir o valor predefinido '2019-02-01T00:00:00Z' ao esperado LastModifiedDate no fuso horário UTC. 
-  *LastModified_To* é utilizado para selecionar os ficheiros cujo atributo LastModifiedDate é antes deste valor de data. Para selecionar apenas os novos ficheiros, que não foram copiados da última vez, este valor de data pode ser o momento atual.  Pode substituir o valor predefinido '2019-02-01T00:00:00Z' ao esperado LastModifiedDate no fuso horário UTC. 

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o modelo **Copiar novos ficheiros apenas por LastModifiedDate**. Crie uma **nova** ligação com o seu armazém de origem. O armazém de origem é onde pretende copiar ficheiros.

    ![Criar uma nova ligação com a fonte](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Crie uma **nova** ligação à sua loja de destino. A loja de destino é para onde pretende copiar ficheiros. 

    ![Criar uma nova ligação ao destino](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Verá o gasoduto disponível no painel, como mostra o seguinte exemplo:

    ![Mostre o oleoduto](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Selecione **Debug,** escreva o valor para os **Parâmetros** e selecione **Terminar**.  Na imagem abaixo, definimos os parâmetros como seguindo.
   - **FolderPath_Source** = pasta de origem
   - **Directory_Source** = subpasta
   - **FolderPath_Destination** = pasta de destino
   - **Directory_Destination** = subpasta
   - **LastModified_From** = 2019-02-01T00:00:00
   - **LastModified_To** = 2019-03-01T00:00:00
    
    O exemplo indica que os ficheiros, que foram modificados pela última vez dentro do tempo **(2019-02-01T00:00:00Z** a **2019-03-01T00:00:00:00Z**) serão copiados da **pasta/subpasta** do caminho-de-destino para a **pasta/subpasta**do caminho de destino.  Pode substituí-los com os seus próprios parâmetros.

    ![Executar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Reveja o resultado. Verá que apenas os ficheiros modificados pela última vez dentro do tempo configurado foram copiados para a loja de destino.

    ![Reveja o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Agora pode adicionar um gatilho de janelas caindo para automatizar este pipeline, de modo que o pipeline pode sempre copiar novos e alterados ficheiros apenas pelo LastModifiedDate periodicamente.  **Selecione Adicionar gatilho**, e selecione **New/Edit**.

    ![Reveja o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Na janela **Adicionar gatilhos,** selecione **+ Novo**.

9. Selecione **Janela tumbling** para o tipo de gatilho, definir **A cada 15 minutos como** a recorrência (pode mudar para qualquer intervalo). Selecione **Sim** para a caixa Ativada e, em seguida, selecione **OK**.

    ![Criar acionador](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Defina o valor para os parâmetros de **execução** do gatilho como seguintes e selecione **Terminar**.
    - **FolderPath_Source** = **pasta de origem.**  Pode substituir a pasta na loja de dados de origem.
    - **Directory_Source** = **subpasta.**  Pode substituir a sua subpasta na loja de dados de origem.
    - **FolderPath_Destination** = **pasta de destino.**  Pode substituir a pasta na loja de dados de destino.
    - **Directory_Destination** = **subpasta.**  Pode substituir a sua subpasta na loja de dados de destino.
    - **LastModified_From** =  **LastModified_From\@gatilho().outputs.windowStartTime**.  É uma variável do sistema a partir do gatilho que determina o tempo em que o gasoduto foi acionado da última vez.
    - **LastModified_To** = **LastModified_To\@gatilho().outputs.windowEndTime**.  É uma variável do sistema a partir do gatilho que determina o tempo em que o gasoduto é acionado desta vez.
    
    ![Parâmetros de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Selecione **Publicar Tudo**.
    
    ![Publicar Tudo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Crie novos ficheiros na sua pasta fonte de armazenamento de fontes de dados.  Está agora à espera que o gasoduto seja acionado automaticamente e apenas os novos ficheiros serão copiados para a loja de destino.

13. Selecione o separador **Monitor** no painel de navegação esquerdo e aguarde cerca de 15 minutos se a recorrência do gatilho tiver sido definida a cada 15 minutos. 

14. Reveja o resultado. Verá que o seu pipeline será acionado automaticamente a cada 15 minutos, e apenas os novos ou alterados ficheiros da loja source serão copiados para a loja de destino em cada pipeline.

    ![Reveja o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
