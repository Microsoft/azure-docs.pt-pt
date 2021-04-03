---
title: 'Tutorial: Utilize o serviço de cópia de dados para copiar para o seu dispositivo'
titleSuffix: Azure Data Box
description: Neste tutorial, aprende a copiar dados para o seu dispositivo Azure Data Box através do serviço de cópia de dados
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: e664055893bbdef0f7090811b8a160a1b8a4a1fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124053"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Tutorial: Utilize o serviço de cópia de dados para copiar dados na Caixa de Dados do Azure (pré-visualização)

Este tutorial descreve como ingerir dados utilizando o serviço de cópia de dados sem um hospedeiro intermédio. O serviço de cópia de dados funciona localmente na Microsoft Azure Data Box, conecta-se ao seu dispositivo de armazenamento (NAS) ligado à rede via SMB e copia dados para a Data Box.

Utilize o serviço de cópia de dados:

- Em ambientes NAS onde hospedeiros intermédios podem não estar disponíveis.
- Com pequenos ficheiros que demoram semanas para ingestão e upload de dados. O serviço de cópia de dados melhora significativamente o tempo de ingestão e upload para pequenos ficheiros.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Copiar dados para o Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Completou este tutorial: [Configurar a Caixa de Dados Azure.](data-box-deploy-set-up.md)
2. Recebeu o seu dispositivo Data Box e o estado da encomenda no portal é **entregue**.
3. Tem as credenciais do dispositivo NAS de origem a que irá ligar para a cópia de dados.
4. Está ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha pelo menos uma ligação Ethernet (GbE) de 10 Gigabit. Se uma ligação de 10 GbE não estiver disponível, pode utilizar uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Depois de estar ligado ao dispositivo NAS, o próximo passo é copiar os seus dados. Antes de começar a cópia de dados, reveja as seguintes considerações:

* Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos nos limites de armazenamento e caixa de dados do artigo [Azure](data-box-limits.md).

* Se os dados enviados pela Data Box forem enviados simultaneamente por outras aplicações fora da Data Box, podem resultar falhas no upload-job e corrupção de dados.

* Se os dados estiverem a ser modificados à medida que o serviço de cópia de dados estiver a lê-lo, poderá ver falhas ou corrupção de dados.

> [!IMPORTANT]
> Certifique-se de que mantém uma cópia dos dados de origem até poder confirmar que o Data Box transferiu os seus dados para o Armazenamento do Azure.

Para copiar dados utilizando o serviço de cópia de dados, é necessário criar um trabalho:

1. Na UI web local do seu dispositivo Data Box, vá para **gerir**  >  **dados de Cópia .**
2. Na página **de dados** do Copy, selecione **Criar**.

    ![Selecione Criar na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. No **trabalho de configuração e iniciar a** caixa de diálogo, preencha os seguintes campos:
    
    |Campo                          |Valor    |
    |-------------------------------|---------|
    |**Nome da tarefa**                       |Um nome único com menos de 230 caracteres para o trabalho. Estes caracteres não são permitidos no nome do trabalho: \<, \> , , , , , , \| \? \* \\ \: \/ e \\\.         |
    |**Localização da origem**                |Fornecer o caminho SMB para a fonte de dados no formato: `\\<ServerIPAddress>\<ShareName>` ou `\\<ServerName>\<ShareName>` .        |
    |**Nome de Utilizador**                       |Nome de utilizador em `\\<DomainName><UserName>` formato para aceder à fonte de dados. Se um administrador local estiver a ligar-se, precisarão de permissões explícitas de segurança. Clique com o botão direito na pasta, selecione **Propriedades** e, em seguida, selecione **Security**. Isto deve adicionar o administrador local no separador **Segurança.**       |
    |**Palavra-passe**                       |Senha para aceder à fonte de dados.           |
    |**Conta de armazenamento de destino**    |Selecione a conta de armazenamento alvo para carregar os dados para a lista.         |
    |**Tipo de destino**       |Selecione o tipo de armazenamento alvo da lista: **Block Blob**, **Page Blob** ou **Azure Files**.        |
    |**Contentor/partilha de destino**    |Insira o nome do recipiente ou partilhe para o mesmo que pretende enviar dados na sua conta de armazenamento de destino. O nome pode ser um nome de partilha ou um nome de recipiente. Por exemplo, utilize `myshare` ou `mycontainer`. Também pode introduzir o nome no formato `sharename\directory_name` ou `containername\virtual_directory_name` .        |
    |**Padrão de correspondência de ficheiros de cópia**    | Pode introduzir o padrão de correspondência do nome do ficheiro de duas maneiras:<ul><li>**Utilize expressões wildcard:** Apenas `*` e `?` são apoiados em expressões wildcard. Por exemplo, a expressão `*.vhd` corresponde a todos os ficheiros que têm a `.vhd` extensão. Da mesma forma, `*.dl?` corresponde a todos os ficheiros com a extensão ou com , tal como `.dl` `.dl` `.dll` . Da mesma forma, `*foo` corresponde a todos os ficheiros cujos nomes terminam com `foo` .<br>Pode introduzir diretamente a expressão wildcard no campo. Por predefinição, o valor que introduz no campo é tratado como uma expressão wildcard.</li><li>**Utilize expressões regulares:** As expressões regulares baseadas em POSIX são suportadas. Por exemplo, a expressão regular `.*\.vhd` corresponderá a todos os ficheiros que tenham a `.vhd` extensão. Para expressões regulares, forneça `<pattern>` o seguinte diretamente como `regex(<pattern>)` . Para obter mais informações sobre expressões regulares, aceda à [linguagem de expressão regular - uma referência rápida](/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Otimização de ficheiros**              |Quando esta funcionalidade está ativada, os ficheiros inferiores a 1 MB são embalados durante a ingestão. Esta embalagem acelera a cópia de dados para ficheiros pequenos. Também poupa uma quantidade significativa de tempo quando o número de ficheiros excede em muito o número de diretórios.        |
 
4. Selecione **Iniciar**. As entradas são validadas, e se a validação for bem sucedida, então o trabalho começa. Pode levar alguns minutos para o trabalho começar.

    ![Inicie um trabalho a partir da caixa de diálogo "Configure e inicie"](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. É criado um trabalho com as definições especificadas. Pode parar, retomar, cancelar ou reiniciar um trabalho. Selecione a caixa de verificação ao lado do nome do trabalho e, em seguida, selecione o botão apropriado.

    ![Gerir um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Pode parar um emprego se estiver a afetar os recursos do dispositivo NAS durante as horas de ponta:

        ![Faça uma pausa na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Pode retomar o trabalho mais tarde durante as horas de ponta:

        ![Retomar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Pode cancelar um emprego a qualquer momento:

        ![Cancelar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Quando cancela um trabalho, é necessária uma confirmação:

        ![Confirmar cancelamento de emprego](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Se decidir cancelar um emprego, os dados já copiados não são apagados. Para eliminar quaisquer dados que tenha copiado para o seu dispositivo Data Box, reinicie o dispositivo.

        ![Reiniciar um dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Se cancelar ou interromper um trabalho, ficheiros grandes só podem ser parcialmente copiados. Estes ficheiros parcialmente copiados são enviados no mesmo estado para Azure. Quando cancelar ou interromper um trabalho, certifique-se de que os seus ficheiros foram devidamente copiados. Para validar os ficheiros, veja as ações do SMB ou descarregue o ficheiro BOM.

    - Pode reiniciar um trabalho se falhar devido a um erro transitório, como uma falha de rede. Mas não é possível reiniciar um trabalho se tiver atingido um estado terminal, como **"Bem sucedido"** ou **concluído com erros**. Falhas de emprego podem ser causadas por problemas de nomeação de ficheiros ou de tamanho de ficheiro. Estes erros estão registados, mas o trabalho não pode ser reiniciado depois de concluído.

        ![Reiniciar um trabalho falhado](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Se sentir uma falha e não conseguir reiniciar o trabalho, descarregue os registos de erros e procure a falha nos ficheiros de registo. Depois de corrigir o problema, crie um novo trabalho para copiar os ficheiros. Também pode [copiar os ficheiros em SMB](data-box-deploy-copy-data.md).
    
    - Neste comunicado, não se pode apagar um emprego.
    
    - Pode criar empregos ilimitados, mas só pode executar um máximo de 10 postos de trabalho em paralelo a qualquer momento.
    - Se **a otimização do Ficheiro** estiver acesa, os ficheiros pequenos são embalados na ingestão para melhorar o desempenho da cópia. Nestes casos, verá um ficheiro embalado (terá um GUID como nome de ficheiro). Não apague este ficheiro. Será desembalado durante o upload.

6. Enquanto o trabalho está em andamento, na página **de dados** do Copy:

    - Na coluna **Status,** pode visualizar o estado do trabalho de cópia. O estado pode ser:
        - **Em Execução**
        - **Com falhas**
        - **Com êxito**
        - **A Colocar em Pausa**
        - **Em pausa**
        - **A Cancelar**
        - **Cancelado**
        - **Concluído com erros**
    - Na coluna **Ficheiros,** é possível ver o número e o tamanho total dos ficheiros que estão a ser copiados.
    - Na coluna **Processada,** é possível ver o número e o tamanho total dos ficheiros que são processados.
    - Na coluna **'Detalhes do Trabalho',** selecione **Ver** para ver os detalhes do trabalho.
    - Se ocorrerem erros durante o processo de cópia, como mostrado na coluna **#Errors,** vá à coluna **de registo de erros** e descarregue os registos de erros para a resolução de problemas.

Espera que o trabalho de cópia termine. Como alguns erros são registados apenas na página **'Ligar' e copiar,** certifique-se de que o trabalho de cópia terminou sem erros antes de passar ao passo seguinte.

![Sem erros na página "Ligar e copiar"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Para garantir a integridade dos dados, uma função de verificação é calculada em linha à medida que os dados são copiados. Depois de concluída a cópia, selecione **Ver painel de instrumentos** para verificar o espaço utilizado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Depois de terminar o trabalho de cópia, pode selecionar **Prepare-se para o envio**.

>[!NOTE]
> **Preparem-se para o navio** enquanto os trabalhos de cópia estão em andamento.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo tutorial para aprender a enviar o seu dispositivo Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Envie o seu dispositivo Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)