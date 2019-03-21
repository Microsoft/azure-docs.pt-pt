---
title: Copiar ficheiros novos e alterados por LastModifiedDate com o Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar um modelo de solução para copiar ficheiros novos e alterados por LastModifiedDate com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111944"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiar ficheiros novos e alterados por LastModifiedDate com o Azure Data Factory

Este artigo descreve um modelo de solução que pode utilizar para copiar ficheiros de novos e alterados apenas por LastModifiedDate de um arquivo baseado em ficheiros para um arquivo de destino. 

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo seleciona primeiro os ficheiros novos e alterados apenas por seus atributos **LastModifiedDate**e, em seguida, copia os ficheiros selecionados do arquivo de origem de dados para o arquivo de dados de destino.

O modelo contém uma atividade:
- **Cópia** para copiar arquivos novos e alterados apenas por LastModifiedDate de um arquivo de ficheiros para um arquivo de destino.

O modelo define quatro parâmetros:
-  *FolderPath_Source* é o caminho da pasta onde pode ler os ficheiros de arquivo de origem. É necessário substituir o valor predefinido com seu próprio caminho de pasta.
-  *FolderPath_Destination* é o caminho da pasta onde pretende copiar ficheiros para o arquivo de destino. É necessário substituir o valor predefinido com seu próprio caminho de pasta.
-  *LastModified_From* é utilizado para selecionar os ficheiros cujo atributo LastModifiedDate é após ou igual a este valor de datetime.  Para selecionar os novos ficheiros apenas, que não foi copiado pela última vez, este valor de datetime pode ser a hora quando o pipeline foi acionado a última vez. Pode substituir o valor predefinido ' 2019-02-01T00:00:00Z "para seu LastModifiedDate esperado no fuso horário UTC. 
-  *LastModified_To* é utilizado para selecionar os ficheiros cujo atributo LastModifiedDate é antes deste valor de datetime. Para selecionar os novos ficheiros apenas, que não foi copiado pela última vez, este valor de datetime pode ser a hora presente.  Pode substituir o valor predefinido ' 2019-02-01T00:00:00Z "para seu LastModifiedDate esperado no fuso horário UTC. 

## <a name="how-to-use-this-solution-template"></a>Como utilizar este modelo de solução

1. Aceda ao modelo **copiar novos ficheiros apenas por LastModifiedDate**. Criar uma **New** ligação ao seu arquivo de armazenamento de origem. O arquivo de armazenamento de origem é onde pretende copiar os ficheiros.

    ![Criar uma nova ligação para a origem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Primeiro, selecione o armazenamento **tipo**. Depois que o armazenamento de entrada **nome da conta** e o **chave de conta**. Por fim, selecione **concluir**.

    ![Introduza uma cadeia de ligação](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Criar uma **New** ligação ao seu arquivo de destino. O arquivo de destino é onde pretende copiar ficheiros para. Também tem de introduzir as informações de ligação do arquivo de destino de dados semelhante como no passo 2.

    ![Criar uma nova ligação para o destino](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Verá o pipeline disponível no painel, conforme mostrado no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Selecione **depurar**, escreva o valor para o **parâmetros** e selecione **concluir**.  A figura a seguir, definimos os parâmetros como o seguinte.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     O exemplo é que indica os arquivos que foram modificados pela última vez no período de tempo entre *2019-02-01T00:00:00Z* e *2019-03-01T00:00:00Z* serão copiados a partir de uma pasta */source/*  para uma pasta */destination/*.  Pode substitui-los com os seus parâmetros.
    
     ![Executar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Reveja o resultado. Verá apenas os ficheiros modificado pela última vez configurada no período de tempo foi copiado para o arquivo de destino.

    ![O resultado da revisão](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Agora pode adicionar um acionador de windows em cascata para automatizar este pipeline, para que o pipeline pode sempre copiar arquivos novos e alterados apenas por LastModifiedDate periodicamente.  Selecione **adicionar acionador**e selecione **novo/editar**.

    ![O resultado da revisão](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Na **adicionar Acionadores** janela, selecione **+ novo**.

    ![O resultado da revisão](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Selecione **janela em cascata** para o tipo de Acionador, defina **cada minuto de 15 (s)** como a periodicidade (pode alterar para qualquer tempo de intervalo) e, em seguida, selecione **seguinte**.

    ![Criar acionador](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Escrever o valor para o **parâmetros da execução do acionador** como a seguir e selecione **concluir**.
    - **FolderPath_Source** = **/source/**.  Pode substituir com sua pasta no arquivo de dados de origem.
    - **FolderPath_Destination** = **/destination/**.  Pode substituir com sua pasta no arquivo de dados de destino.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  É uma variável do sistema de Acionador que determina o tempo quando o pipeline foi acionado a última vez.
    - **LastModified_To** = **@trigger().outputs.windowEndTime**.  É uma variável do sistema de Acionador que determina o tempo quando o pipeline é acionado neste momento.
    
    ![Parâmetros de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Selecione **Publicar Tudo**.
    
    ![Publicar Tudo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Crie novos ficheiros na sua pasta de origem do arquivo de origem de dados.  Agora estão a aguardar para o pipeline sejam acionadas automaticamente e os novos ficheiros serão copiados para o arquivo de destino.

14. Selecione **monitorização** separador no painel de navegação esquerdo e aguarde cerca de 15 minutos, se a recorrência do acionador foi definida como a cada 15 minutos. 

    ![Selecione monitorização](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Reveja o resultado. Verá o pipeline será acionado automaticamente a cada 15 minutos e apenas os ficheiros novos ou alterados de arquivo de origem serão copiados para o arquivo de destino em cada execução de pipeline.

    ![O resultado da revisão](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Data Factory](introduction.md)