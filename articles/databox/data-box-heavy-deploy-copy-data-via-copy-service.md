---
title: 'Tutorial: Copiar dados para Caixa de Dados Azure Heavy através do serviço de cópia de dados'
description: Neste tutorial, aprende a copiar dados para o seu dispositivo Azure Data Box Heavy através do serviço de cópia de dados
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 67547db53d2b9ce05838335ffcb5d789b77ecbbe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77560225"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Tutorial: Utilize o serviço de cópia de dados para copiar dados em Caixa de Dados Do Azure Heavy (pré-visualização)

Este tutorial descreve como ingerir dados utilizando o serviço de cópia de dados sem um hospedeiro intermédio. O serviço de cópia de dados funciona localmente na Caixa de Dados Do Azure, conecta-se ao seu dispositivo de armazenamento ligado à rede (NAS) via SMB e copia dados para Data Box Heavy.

Utilize o serviço de cópia de dados:

- Em ambientes NAS onde os anfitriões intermédios podem não estar disponíveis.
- Com pequenos ficheiros que demoram semanas a ingestão e upload de dados. O serviço de cópia de dados melhora significativamente a ingestão e o tempo de upload para pequenos ficheiros.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Copiar dados para o Data Box Heavy

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Completou este tutorial: Instale a Caixa de [Dados Azure Heavy](data-box-heavy-deploy-set-up.md).
2. Recebeu o Data Box Heavy e o estado da encomenda no portal é **Entregue**.
3. Tem as credenciais do dispositivo NAS de origem a que se ligará para cópia de dados.
4. Estáligado a uma rede de alta velocidade. Para velocidades de cópia mais rápidas, podem ser utilizadas em paralelo duas ligações de 40 GbE (uma por nó). Se não tiver uma ligação de 40 GbE disponível, recomendamos que tenha pelo menos duas ligações de 10 GbE (uma por nó). 

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para o Data Box Heavy

Depois de estar ligado ao dispositivo NAS, o próximo passo é copiar os seus dados. Antes de começar a cópia de dados, reveja as seguintes considerações:

- Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos no artigo limites de [armazenamento azure e caixa](data-box-heavy-limits.md)de dados Pesados .
- Se os dados enviados pela Data Box Heavy forem simultaneamente carregados por outras aplicações fora da Data Box Heavy, podem resultar falhas no trabalho de upload e corrupção de dados.
- Se os dados estiverem a ser modificados à medida que o serviço de cópia de dados estiver a lê-lo, poderá ver falhas ou corrupção de dados.

Para copiar dados utilizando o serviço de cópia de dados, precisa de criar um trabalho:

1. Na web local UI do seu dispositivo Data Box Heavy, vá para **gerir** > **dados de Cópia**.
2. Na página de **dados copy,** selecione **Criar**.

    ![Selecione Criar na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Na caixa de trabalho **configure e iniciar** o diálogo, preencha os seguintes campos:
    
    |Campo                          |Valor    |
    |-------------------------------|---------|
    |**Nome da tarefa**                       |Um nome único menos de 230 caracteres para o trabalho. Estes personagens não são permitidos \< \>no \| \?nome \* \\do \: \/trabalho: , , , , , , , e\\\.         |
    |**Localização da fonte**                |Fornecer o caminho SMB para a `\\<ServerIPAddress>\<ShareName>` fonte `\\<ServerName>\<ShareName>`de dados no formato: ou .        |
    |**Nome de utilizador**                       |Nome de `\\<DomainName><UserName>` utilizador em formato de acesso à fonte de dados. Se um administrador local estiver ligado, precisarão de permissões de segurança explícitas. Clique na pasta à direita, selecione **Propriedades** e, em seguida, selecione **Security**. Isto deve adicionar o administrador local no separador **Segurança.**       |
    |**Palavra-passe**                       |Senha de acesso à fonte de dados.           |
    |**Conta de armazenamento de destino**    |Selecione a conta de armazenamento alvo para fazer o upload de dados para a partir da lista.         |
    |**Tipo de destino**       |Selecione o tipo de armazenamento de alvo da lista: **Block Blob,** **Page Blob**ou **Azure Files**.        |
    |**Recipiente de destino/partilha**    |Insira o nome do recipiente ou partilhe que pretende fazer upload de dados na sua conta de armazenamento de destino. O nome pode ser um nome de partilha ou um nome de contentor. Por exemplo, utilize `myshare` ou `mycontainer`. Também pode introduzir o nome `sharename\directory_name` `containername\virtual_directory_name`no formato ou .        |
    |**Copiar ficheiros correspondentes padrão**    | Pode introduzir o padrão de correspondência de nome de ficheiro nas seguintes duas formas:<ul><li>**Utilize expressões wildcard:** `*` Apenas `?` e são apoiados em expressões wildcard. Por exemplo, `*.vhd` a expressão corresponde a `.vhd` todos os ficheiros que têm a extensão. Da mesma `*.dl?` forma, corresponde a `.dl` todos os `.dl`ficheiros `.dll`com a extensão ou com que começam com , como . Da mesma `*foo` forma, corresponde a `foo`todos os ficheiros cujos nomes terminam com .<br>Pode entrar diretamente na expressão wildcard no campo. Por padrão, o valor que entra no campo é tratado como uma expressão wildcard.</li><li>**Utilize expressões regulares:** Expressões regulares baseadas em POSIX são suportadas. Por exemplo, a `.*\.vhd` expressão regular corresponderá `.vhd` a todos os ficheiros que tenham a extensão. Para expressões regulares, forneça o `<pattern>` diretamente como `regex(<pattern>)`. Para mais informações sobre expressões regulares, vá à linguagem de [expressão regular - uma referência rápida](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Otimização de ficheiros**              |Quando esta funcionalidade está ativada, os ficheiros com menos de 1 MB são embalados durante a ingestão. Esta embalagem acelera a cópia de dados para pequenos ficheiros. Também poupa uma quantidade significativa de tempo quando o número de ficheiros excede em muito o número de diretórios.        |
 
4. Selecione **Iniciar**. As inputs são validadas, e se a validação for bem sucedida, então o trabalho começa. Pode levar alguns minutos para o trabalho começar.

    ![Inicie um trabalho a partir da caixa de diálogo "Configure trabalho e início"](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. É criado um trabalho com as definições especificadas. Pode parar, retomar, cancelar ou reiniciar um trabalho. Selecione a caixa de verificação ao lado do nome de trabalho e, em seguida, selecione o botão apropriado.

    ![Gerir um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Pode parar um trabalho se estiver a afetar os recursos do dispositivo NAS durante as horas de ponta:

        ![Pausa de um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Pode retomar o trabalho mais tarde durante as horas fora do pico:

        ![Retomar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Pode cancelar um trabalho a qualquer momento:

        ![Cancelar um trabalho na página "Copiar dados"](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Quando cancela um trabalho, é necessária uma confirmação:

        ![Confirmar o cancelamento de emprego](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Se decidir cancelar um trabalho, os dados que já estão copiados não são apagados. Para eliminar quaisquer dados que tenha copiado para o seu dispositivo Data Box, reset o dispositivo.

        ![Redefinir um dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Se cancelar ou interromper um trabalho, ficheiros grandes só podem ser parcialmente copiados. Estes ficheiros parcialmente copiados são enviados no mesmo estado para o Azure. Quando cancelar ou fazer uma pausa no trabalho, certifique-se de que os seus ficheiros foram devidamente copiados. Para validar os ficheiros, veja as ações da SMB ou descarregue o ficheiro BOM.

    - Pode reiniciar um trabalho se tiver falhado devido a um erro transitório, como uma falha de rede. Mas não pode reiniciar um trabalho se tiver atingido um estado terminal, como **o Sucesso** ou O Completou **com erros**. Falhas de emprego podem ser causadas por questões de nomeação de ficheiros ou tamanho de ficheiro. Estes erros estão registados, mas o trabalho não pode ser reiniciado depois de concluído.

        ![Reinicie um trabalho falhado](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Se sentir uma falha e não conseguir reiniciar o trabalho, faça o download dos registos de erros e procure a falha nos ficheiros de registo. Depois de corrigir o problema, crie um novo emprego para copiar os ficheiros. Também pode [copiar os ficheiros em sMB](data-box-deploy-copy-data.md).
    
    - Nesta versão, não pode apagar um trabalho.
    
    - Pode criar empregos ilimitados, mas só pode gerir um máximo de 10 postos de trabalho em paralelo a qualquer momento.
    - Se a **otimização** do Ficheiro estiver pronta, os pequenos ficheiros são embalados na ingestão para melhorar o desempenho da cópia. Nestes casos, verá um ficheiro embalado (terá um GUID como nome de ficheiro). Não apague este ficheiro. Será desembalado durante o upload.

6. Enquanto o trabalho está em andamento, na página de **dados copy:**

    - Na coluna **Status,** pode ver o estado do trabalho de cópia. O estado pode ser:
        - **A executar**
        - **Falhou**
        - **Bem-sucedido**
        - **A Colocar em Pausa**
        - **Em pausa**
        - **A Cancelar**
        - **Cancelado**
        - **Concluído com erros**
    - Na coluna **Ficheiros,** pode ver o número e o tamanho total dos ficheiros a serem copiados.
    - Na coluna **Processada,** pode ver o número e o tamanho total dos ficheiros que são processados.
    - Na coluna de detalhes de **Trabalho,** selecione **Ver** para ver os detalhes do trabalho.
    - Se ocorrerem erros durante o processo de cópia, como mostrado na coluna **#Errors,** aceda à coluna **de registo error** e descarregue os registos de erro para resolução de problemas.

Espere que o trabalho de cópia termine. Como alguns erros são registados apenas na página **Connect e copy,** certifique-se de que o trabalho de cópia terminou sem erros antes de passar para o próximo passo.

![Sem erros na página "Ligar e copiar"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Para garantir a integridade dos dados, um controlo é calculado inline à medida que os dados são copiados. Depois de a cópia estar completa, selecione **'Ver painel'** para verificar o espaço utilizado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Repita as mesmas instruções para copiar dados para o segundo nó na Caixa de Dados Pesada.

Depois de terminar o trabalho de cópia, pode selecionar **Preparar para enviar**.

>[!NOTE]
> **Preparar-se para o navio** não pode funcionar enquanto os trabalhos de cópia estão em andamento.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo tutorial para aprender a enviar o seu dispositivo Data Box Heavy de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Envie o seu dispositivo De caixa de dados Azure Para a Microsoft](./data-box-heavy-deploy-picked-up.md)

