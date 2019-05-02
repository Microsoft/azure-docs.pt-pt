---
title: Tutorial para copiar dados para o dispositivo do Azure Data Box através do serviço de cópia de dados | Documentos da Microsoft
description: Neste tutorial, irá aprender a copiar dados para o seu dispositivo do Azure Data Box através do serviço de cópia de dados
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 412b554710c552bdfdf8bf94a59e822beed55b32
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925306"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Tutorial: Utilizar o serviço de cópia de dados para copiar dados para o Azure Data Box (pré-visualização)

Este tutorial descreve como para ingestão de dados com o serviço de cópia de dados sem um host de intermediário. O serviço de cópia de dados é executado localmente no Microsoft Azure Data Box, liga para o seu dispositivo de armazenamento ligados à rede (NAS) através de SMB e copia os dados para o Data Box.

Utilize o serviço de cópia de dados:

- Em ambientes de em que anfitriões intermediários podem não estar disponíveis.
- Com arquivos pequenos que levam semanas para ingestão e carregamento de dados. O serviço de cópia de dados melhora significativamente o tempo de ingestão e carregamento de ficheiros pequenos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Copiar dados para o Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu este tutorial: [Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Que recebeu o seu dispositivo do Data Box e é o estado da encomenda no portal **entregues**.
3. Tem as credenciais do dispositivo de origem NAS que se conectará para cópia de dados.
4. Está ligado a uma rede de alta velocidade. É altamente recomendável que tenha pelo menos uma ligação de Ethernet (GbE) de 10 Gigabit. Se uma ligação de 10 GbE não estiver disponível, pode utilizar uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Quando estiver ligado ao dispositivo NAS, a próxima etapa é copiar os dados. Antes de iniciar a cópia de dados, reveja as seguintes considerações:

- Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos no artigo [armazenamento do Azure e limites do Data Box](data-box-limits.md).
- Se os dados carregados por Data Box são carregados em simultâneo por outros aplicativos padrões de dados, poderão resultar falhas de trabalho de carregamento e danos em dados.
- Se os dados está a ser modificados como o serviço de cópia de dados é lê-lo, poderá ver falhas ou corrupção de dados.

Para copiar dados com o serviço de cópia de dados, terá de criar uma tarefa:

1. Na IU da web local do seu dispositivo do Data Box, aceda a **Manage** > **copiar dados**.
2. Sobre o **copiar dados** página, selecione **criar**.

    ![Selecione criar na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Na **tarefa de configurar e iniciar** caixa de diálogo, preencha os campos seguintes:
    
    |Campo                          |Value    |
    |-------------------------------|---------|
    |**Nome da tarefa**                       |Um único nome de menos de 230 carateres para a tarefa. Estes carateres não são permitidos no nome da tarefa: \<, \>, \|, \?, \*, \\, \:, \/, e \\\.         |
    |**Localização de origem**                |Forneça o caminho SMB para a origem de dados no formato: `\\<ServerIPAddress>\<ShareName>` ou `\\<ServerName>\<ShareName>`.        |
    |**Nome de Utilizador**                       |Nome de utilizador no `\\<DomainName><UserName>` formato para aceder à origem de dados.        |
    |**Palavra-passe**                       |Palavra-passe para aceder à origem de dados.           |
    |**Conta de armazenamento de destino**    |Selecione a conta de armazenamento de destino para carregar dados a partir da lista.         |
    |**Tipo de destino**       |Selecione o tipo de armazenamento de destino na lista: **Blob de blocos**, **BLOBs de páginas**, ou **ficheiros do Azure**.        |
    |**Contentor/partilha de destino**    |Introduza o nome do contentor ou Compartilhe o que deseja carregar dados na sua conta de armazenamento de destino. O nome pode ser um nome de partilha ou um nome de contentor. Por exemplo, usar `myshare` ou `mycontainer`. Também pode introduzir o nome no formato `sharename\directory_name` ou `containername\virtual_directory_name`.        |
    |**Copiar ficheiros correspondentes padrão**    | Pode introduzir o padrão de correspondência do nome de ficheiro das seguintes duas formas:<ul><li>**Utilize expressões com carateres universais:** Apenas `*` e `?` são suportados em expressões de caráter universal. Por exemplo, a expressão `*.vhd` corresponde a todos os ficheiros que tenham o `.vhd` extensão. Da mesma forma, `*.dl?` corresponde a todos os ficheiros com qualquer um da extensão `.dl` ou que inicie com `.dl`, como `.dll`. Da mesma forma, `*foo` corresponde a todos os arquivos cujos nomes terminam com `foo`.<br>Pode introduzir diretamente a expressão com carateres universais no campo. Por predefinição, o valor introduzido no campo é tratado como uma expressão com carateres universais.</li><li>**Use expressões regulares:** Baseadas em POSIX expressões regulares são suportadas. Por exemplo, a expressão regular `.*\.vhd` irá corresponder a todos os ficheiros que tenham o `.vhd` extensão. Para expressões regulares, forneça o `<pattern>` diretamente como `regex(<pattern>)`. Para obter mais informações sobre expressões regulares, aceda a [linguagem de expressão Regular - uma referência rápida](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Otimização de ficheiros**              |Quando esta funcionalidade está ativada, os arquivos menores do que 1 MB são incluídos durante a ingestão. Esta remessa acelera a cópia de dados para ficheiros pequenos. Também economiza uma quantidade significativa de tempo quando o número de ficheiros excede o número de diretórios muito.        |
 
4. Selecione **iniciar**. As entradas são validadas e, se a validação for bem-sucedida, em seguida, a tarefa é iniciada. Poderá demorar alguns minutos para que a tarefa iniciar.

    ![Iniciar uma tarefa na caixa de diálogo "tarefa de configurar e iniciar"](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. É criada uma tarefa com as definições especificadas. Pode colocar em pausa, retomar, cancelar ou reiniciar uma tarefa. Selecione a caixa de verificação junto ao nome da tarefa e, em seguida, selecione o botão adequado.

    ![Gerir uma tarefa na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Pode interromper uma tarefa se está a afetar recursos do dispositivo NAS durante o horário de pico:

        ![Colocar em pausa uma tarefa na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Pode retomar a tarefa mais tarde durante horas de ponta:

        ![Retomar uma tarefa na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Pode cancelar uma tarefa a qualquer momento:

        ![Cancelar uma tarefa na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Quando cancelar uma tarefa, é necessária uma mensagem de confirmação:

        ![Confirmar cancelamento da tarefa](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Se optar por cancelar uma tarefa, os dados copiados já não são eliminados. Para eliminar todos os dados que copiou para o seu dispositivo do Data Box, repor o dispositivo.

        ![Repor um dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Se cancela ou interromper um trabalho, arquivos grandes podem ser copiados apenas parcialmente. Estes ficheiros parcialmente copiados são carregados no mesmo Estado para o Azure. Ao cancelar ou interromper um trabalho, certifique-se de que os ficheiros foram copiados corretamente. Para validar os ficheiros, observar as partilhas SMB ou transfira o ficheiro BOM.

    - Pode reiniciar uma tarefa que ela falhe devido a um erro transitório, por exemplo, uma falha de rede. Mas não é possível reiniciar uma tarefa se o ter atingido um Estado terminal, tal como **bem-sucedido** ou **concluída com erros**. Falhas de tarefas poderão ser causadas por problemas de nomes de arquivos ou tamanho de ficheiro. Estes erros são registados, mas não é possível reiniciar a tarefa após ser concluído.

        ![Reiniciar uma tarefa falhada](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Se ocorrer uma falha e não é possível reiniciar a tarefa, transfira os registos de erros e procurar a falha nos ficheiros de registo. Depois de ter corrigido o problema, crie uma nova tarefa para copiar os ficheiros. Também pode [copie os ficheiros através de SMB](data-box-deploy-copy-data.md).
    
    - Nesta versão, não é possível eliminar uma tarefa.
    
    - Pode criar tarefas ilimitadas, mas pode executar apenas um máximo de 10 tarefas em paralelo num dado momento.
    - Se **otimização de ficheiros** estiver ativada, arquivos pequenos são incluídos na ingestão para melhorar o desempenho de cópia. Nestes casos, verá um arquivo compactado (terá um GUID como o seu nome de ficheiro). Não elimine este ficheiro. Será descompactada durante o carregamento.

6. Enquanto a tarefa está em curso, a ser o **copiar dados** página:

    - Na **estado** coluna, pode ver o estado da tarefa de cópia. O estado pode ser:
        - **Em execução**
        - **Falhou**
        - **Foi efetuada com êxito**
        - **Colocar em pausa**
        - **Em pausa**
        - **A cancelar**
        - **Foi cancelada**
        - **Concluído com erros**
    - Na **ficheiros** coluna, pode ver o número e o tamanho total dos ficheiros que está a ser copiados.
    - Na **processados** coluna, pode ver o número e o tamanho total dos ficheiros que são processados.
    - Na **detalhes da tarefa** coluna, selecione **vista** para ver os detalhes da tarefa.
    - Se ocorrerem erros durante o processo de cópia, como mostra a **# erros** coluna, aceda ao **registo de erros** coluna e a transferência para resolução de problemas nos registos de erros.

Aguarde que a tarefa de cópia concluir. Uma vez que alguns erros são registados apenas nos **Connect e a cópia** página, certifique-se de que a tarefa de cópia foi concluída sem erros antes de ir para o passo seguinte.

![Não existem erros na página "Ligar e copiar"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Para garantir a integridade dos dados, uma soma de verificação é calculada inline como os dados são copiados. Depois da cópia estiver concluída, selecione **ver o dashboard** para verificar o espaço utilizado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Depois de concluída a tarefa de cópia, pode selecionar **preparação para envio**.

>[!NOTE]
> **Preparação para envio** não é possível executar enquanto as tarefas de cópia estão em curso.

## <a name="next-steps"></a>Passos Seguintes

Avance para o próximo tutorial para saber como envie o seu dispositivo do Data Box para a Microsoft.

> [!div class="nextstepaction"]
> [Envie o seu dispositivo do Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

