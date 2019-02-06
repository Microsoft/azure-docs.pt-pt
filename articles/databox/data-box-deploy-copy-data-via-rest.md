---
title: Copiar dados para o armazenamento de BLOBs de caixa de dados do Azure através de REST APIs | Documentos da Microsoft
description: Saiba como copiar dados para o armazenamento de BLOBs de caixa de dados do Azure através de REST APIs
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 412bf66902f156c6c8720a720973f97d1d7b1d8f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751621"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Tutorial: Copiar dados para o armazenamento de BLOBs de caixa de dados do Azure através de REST APIs  

Este tutorial descreve os procedimentos para ligar ao armazenamento de BLOBs de caixa de dados do Azure através de REST APIs através de *http* ou *https*. Assim que estiver ligado, os passos necessários para copiar os dados para o armazenamento de BLOBs de caixa de dados e preparar o Data Box para envio, também estão descritas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao armazenamento de BLOBs de caixa de dados via *http* ou *https*
> * Copiar dados para o Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Que recebeu o Data Box e é o estado da encomenda no portal **entregues**.
3. Reviu os [requisitos de sistema para o armazenamento de BLOBs de caixa de dados](data-box-system-requirements-rest.md) e esteja familiarizado com as versões suportadas de APIs, SDKs e ferramentas.
4. Tem acesso a um computador anfitrião que tenha os dados que pretende que o copie para o Data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, pode ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.
5. [Baixe o AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) no seu computador anfitrião. Irá utilizar o AzCopy para copiar dados para o armazenamento de BLOBs de caixa de dados do Azure do seu computador anfitrião.


## <a name="connect-to-data-box-blob-storage"></a>Ligar ao armazenamento de BLOBs de caixa de dados

Pode ligar ao armazenamento de BLOBs de caixa de dados ao longo *http* ou *https*. Em geral, *https* é a maneira segura e recomendada para ligar ao armazenamento de BLOBs de caixa de dados. *Http* é utilizado ao estabelecer ligação através da fidedigno redes. Dependendo se está a ligar ao armazenamento de BLOBs de caixa de dados ao longo *http* ou *https*, os passos podem ser diferentes.

## <a name="connect-via-http"></a>Ligar através de http

Ligação para as APIs REST de armazenamento de BLOBs de caixa de dados ao longo *http* requer os seguintes passos:

- Adicione o IP do dispositivo e ponto final de serviço para o anfitrião remoto de BLOBs
- Configurar o software de terceiros e verificar a ligação

Cada um destes passos está descrito nas seções a seguir.

#### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Adicionar endereço IP do dispositivo e ponto final de serviço para o anfitrião remoto de BLOBs

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

#### <a name="configure-partner-software-and-verify-connection"></a>Configurar o software de parceiro e verificar a ligação

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Ligar através de https

Ligação para APIs de REST de armazenamento de Blobs do Azure através de https requer os seguintes passos:

- Transfira o certificado a partir do portal do Azure
- Preparar o computador anfitrião para a gestão remota
- Adicione o IP do dispositivo e ponto final de serviço para o anfitrião remoto de BLOBs
- Configurar o software de terceiros e verificar a ligação

Cada um destes passos está descrito nas seções a seguir.

### <a name="download-certificate"></a>Transferir certificado

Utilize o portal do Azure para transferir o certificado.

1. Inicie sessão no portal do Azure.
2. Vá para a sua encomenda do Data Box e navegue para **geral > detalhes do dispositivo**.
3. Sob **credenciais do dispositivo**, aceda à **acesso à API** ao dispositivo. Clique em **Transferir**. Esta ação transfere um  **<your order name>. cer** ficheiro de certificado. **Guardar** este ficheiro. Irá instalar este certificado no computador cliente ou anfitrião que irá utilizar para ligar ao dispositivo.

    ![Transferir o certificado no portal do Azure](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="prepare-the-host-for-remote-management"></a>Preparar o anfitrião para a gestão remota

Siga estes passos para preparar o cliente do Windows para uma ligação remota que utiliza um *https* sessão:

- Importe o ficheiro. cer para o arquivo de raiz do cliente ou anfitrião remoto.
- Adicionar endereço IP do dispositivo e o ponto final de serviço para o ficheiro hosts no cliente Windows de Blobs.

Cada um dos procedimentos anteriores, está descrito abaixo.

#### <a name="import-the-certificate-on-the-remote-host"></a>Importar o certificado no anfitrião remoto

Pode utilizar o Windows PowerShell ou a interface do Usuário do Windows Server para importar e instalar o certificado no seu sistema de host.

**Utilizar o PowerShell**

1. Inicie uma sessão do Windows PowerShell como administrador.
2. Na linha de comandos, escreva:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

**Usando o Windows Server da interface do Usuário**

1.  O ficheiro. cer com o botão direito e selecione **instalar certificado**. Esta ação inicia o Assistente para importar certificados.
2.  Para **Store localização**, selecione **máquina Local**e, em seguida, clique em **seguinte**.

    ![Importar certificado com o PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Selecione **colocar todos os certificados no seguinte arquivo**e, em seguida, clique em **procurar**. Navegue para o arquivo de raiz do seu anfitrião remoto e, em seguida, clique em **seguinte**.

    ![Importar certificado com o PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Clique em **Concluir**. É apresentada uma mensagem a indicar que a importação foi concluída com êxito.

    ![Importar certificado com o PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

### <a name="to-add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Para adicionar o endereço IP do dispositivo e ponto final de serviço para o anfitrião remoto de BLOBs

Os passos a seguir são idênticos aos utilizados ao ligar ao longo *http*.

### <a name="configure-partner-software-to-establish-connection"></a>Configurar o software de parceiro para estabelecer ligação

Os passos a seguir são idênticos aos utilizados ao ligar ao longo *http*. A única diferença é que deve deixar a *utilizar a opção de http* desmarcada.

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Assim que estiver ligado ao armazenamento de BLOBs de caixa de dados, a próxima etapa é copiar dados. Antes da cópia de dados, reveja as seguintes considerações:

-  Ao copiar os dados, certifique-se de que o respetivo tamanho está em conformidade com os limites descritos em [Limites de armazenamento do Azure e do Data Box](data-box-limits.md).
- Se os dados, o que está a ser carregados por Data Box, são carregados em simultâneo por outras aplicações fora do Data Box, isso pode resultar em danos de falhas e os dados de tarefa de carregamento.

Neste tutorial, o AzCopy é utilizado para copiar dados para armazenamento de BLOBs de caixa de dados. Também pode utilizar o Explorador de armazenamento do Azure (se preferir uma ferramenta baseada na GUI) ou um software de parceiro a copiar os dados.
O procedimento de cópia tem os seguintes passos:

- Criar um contentor
- Carregue o conteúdo de uma pasta no armazenamento de BLOBs de caixa de dados
- Carregar ficheiros modificados para o armazenamento de BLOBs de caixa de dados

Cada uma dessas etapas é descrita detalhadamente nas seções a seguir.

### <a name="create-a-container"></a>Criar um contentor

A primeira etapa é criar um contentor, porque os blobs são sempre carregados para um contentor. Contentores organizam grupos de blobs, como organiza os ficheiros em pastas no seu computador. Siga estes passos para criar um contentor de Blobs.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento no qual pretende criar o contentor de Blobs.
3. Com o botão direito **contentores de BLOBs**e no menu de contexto, selecione **criar contentor de BLOBs**.

   ![Criar o menu de contexto de contentores de BLOBs](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. É apresentada uma caixa de texto abaixo a **contentores de BLOBs** pasta. Introduza o nome do contentor de blobs. Consulte a [criar o contentor e definir permissões](../storage/blobs/storage-quickstart-blobs-dotnet.md) para obter informações sobre regras e restrições à nomenclatura dos contentores de Blobs.
5. Prima **Enter** quando terminar de criar o contentor de BLOBs, ou **Esc** para cancelar. Depois de criar o contentor de Blobs com êxito, é apresentado no **contentores de BLOBs** pasta para a conta de armazenamento selecionada.

   ![Contentor de BLOBs criado](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Carregue o conteúdo de uma pasta no armazenamento de BLOBs de caixa de dados

Utilize o AzCopy para carregar todos os ficheiros numa pasta no armazenamento de BLOBs no Windows ou Linux. Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Substitua `<key>` pela chave da conta. Para obter a chave da conta, no portal do Azure, aceda à sua conta de armazenamento. Aceda a **definições > chaves de acesso**, selecione uma chave e cole-a no comando do AzCopy.

Se o contentor de destino especificado não existir, o AzCopy cria-o e carrega o ficheiro para o mesmo. Atualize o caminho de origem para o seu diretório de dados e substitua `data-box-storage-account-name` no destino URL com o nome da conta do storage associadas com o Data Box.

Para carregar o conteúdo do diretório especificado para o armazenamento de Blobs recursivamente, especifique a opção `--recursive` (Linux) ou `/S` (Windows). Quando executar o AzCopy com uma destas opções, todas as subpastas e respetivos ficheiros são também carregados.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Carregar ficheiros modificados para o armazenamento de BLOBs de caixa de dados

Utilize o AzCopy para carregar ficheiros com base na respetiva hora da última modificação. Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Para carregar apenas ficheiros novos ou atualizados, adicione o parâmetro `--exclude-older` (Linux) ou `/XO` (Windows) ao comando do AzCopy.

Se quiser copiar apenas recursos de origem que não existem no destino, especifique os parâmetros `--exclude-older` e `--exclude-newer` (Linux) ou `/XO` e `/XN` (Windows) no comando do AzCopy. O AzCopy carrega apenas os dados atualizados, com base no respetivo carimbo de data/hora.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO


Passo seguinte consiste em preparar o seu dispositivo para enviar.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao armazenamento de BLOBs de caixa de dados via *http* ou *https*
> * Copiar dados para o Data Box


Avance para o próximo tutorial para saber como enviar o Data Box para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)
