---
title: 'Tutorial: Use APIs de REPOUSO para copiar para armazenamento blob'
titleSuffix: Azure Data Box
description: Saiba como copiar dados para o seu armazenamento de Caixas de Dados Azure através de APIs REST
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: alkohli
ms.openlocfilehash: b7d58bb13644c992894510f26a4848ea80c9df00
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471844"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Tutorial: Copiar dados para armazenamento de blob de caixa de dados Azure através de APIs REST  

Este tutorial descreve procedimentos para ligar ao armazenamento de Blob de Caixa de Dados Azure através de APIs REST em *http* ou *https*. Uma vez ligados, são também descritos os passos necessários para copiar os dados para o armazenamento do Data Box Blob e preparar a Caixa de Dados para enviar.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligue-se ao armazenamento de Caixas de Dados Blob via *http* ou *https*
> * Copiar dados para o Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Completou o Tutorial: Instale a Caixa de [Dados Azure.](data-box-deploy-set-up.md)
2. Recebeu o Data Box e o estado da encomenda no portal é **Entregue**.
3. Reviu os requisitos do sistema para armazenamento de Caixas de [Dados Blob](data-box-system-requirements-rest.md) e está familiarizado com versões suportadas de APIs, SDKs e ferramentas.
4. Tem acesso a um computador anfitrião que tem os dados que pretende copiar para data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, pode ser utilizada uma ligação de dados de 1-GbE, mas as velocidades de cópia serão impactadas.
5. [Baixe o AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) no seu computador anfitrião. Utilizará o AzCopy para copiar dados para o armazenamento do Blob da Caixa de Dados Do Seu computador anfitrião.


## <a name="connect-via-http-or-https"></a>Conecte-se via http ou https

Pode ligar-se ao armazenamento de Caixas de Dados Blob em *http* ou *https*.

- *Https* é a forma segura e recomendada de se ligar ao armazenamento de Caixa de Dados Blob.
- *Http* é usado quando se conecta em redes fidedignas.

Os passos a ligar são diferentes quando se conecta ao armazenamento de Caixa de Dados Blob em *http* ou *https*.

## <a name="connect-via-http"></a>Conecte-se via http

A ligação ao armazenamento de caixas de dados REST APIs sobre *http* requer os seguintes passos:

- Adicione o ponto final do serviço IP e blob ao hospedeiro remoto
- Configure software de terceiros e verifique a ligação

Cada um destes passos é descrito nas seguintes secções.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicione endereço IP do dispositivo e ponto final do serviço blob

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="configure-partner-software-and-verify-connection"></a>Configure o software do parceiro e verifique a ligação

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Conecte-se via https

A ligação ao armazenamento Bluee Blob REST APIs sobre https requer os seguintes passos:

- Descarregue o certificado do portal Azure
- Importar o certificado no cliente ou anfitrião remoto
- Adicione o ponto final do serviço IP e blob ao cliente ou ao anfitrião remoto
- Configure software de terceiros e verifique a ligação

Cada um destes passos é descrito nas seguintes secções.

### <a name="download-certificate"></a>Certificado de descarregamento

Utilize o portal Azure para descarregar certificado.

1. Assine no portal Azure.
2. Vá à sua encomenda da Caixa de Dados e navegue para **detalhes gerais > dispositivos**.
3. Sob **as credenciais do Dispositivo,** aceda ao acesso da **API** ao dispositivo. Clique em **Transferir**. Esta ação descarrega um\<ficheiro de certificado **sinuoso>.cer.** **Guarde** este ficheiro. Irá instalar este certificado no cliente ou no computador anfitrião que utilizará para se ligar ao dispositivo.

    ![Certificado de descarregamento no portal Azure](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Importar certificado 

Aceder ao armazenamento de Caixa de Dados Blob em HTTPS requer um certificado SSL para o dispositivo. A forma como este certificado é disponibilizado à aplicação do cliente varia de aplicação para aplicação e através de sistemas operativos e distribuições. Alguns pedidos podem aceder ao certificado depois de importados para o certificado do sistema, enquanto outros pedidos não utilizam esse mecanismo.

Nesta secção são mencionadas informações específicas sobre algumas aplicações. Para obter mais informações sobre outras aplicações, consulte a documentação para a aplicação e o sistema operativo utilizado.

Siga estes passos para importar o ficheiro `.cer` para o armazém raiz de um cliente Windows ou Linux. Num sistema Windows, pode utilizar o Windows PowerShell ou o Windows Server UI para importar e instalar o certificado no seu sistema.

#### <a name="use-windows-powershell"></a>Utilizar o Windows PowerShell

1. Inicie uma sessão do Windows PowerShell como administrador.
2. Na linha de comandos, escreva:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Utilizar o Windows Server UI

1.  Clique no ficheiro `.cer` e selecione **Instalar o certificado**. Esta ação inicia o Certificado De Import Wizard.
2.  Para **armazenar a localização,** selecione **Local Machine**, e, em seguida, clique em **Seguinte**.

    ![Certificado de importação usando powerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Selecione **Coloque todos os certificados na seguinte loja**e, em seguida, clique em **Navegar**. Navegue até à loja de raízes do seu anfitrião remoto e, em seguida, clique em **Next**.

    ![Certificado de importação usando powerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Clique em **Concluir**. Uma mensagem que lhe diz que a importação foi bem sucedida aparece.

    ![Certificado de importação usando powerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Use um sistema Linux

O método de importação de um certificado varia por distribuição.

Vários, como Ubuntu e Debian, usam o comando `update-ca-certificates`.  

- Mude o nome do ficheiro de certificado codificado Base64 para ter uma extensão `.crt` e copiá-lo para o `/usr/local/share/ca-certificates directory`.
- Executar o comando `update-ca-certificates`.

Versões recentes de RHEL, Fedora e CentOS usam o comando `update-ca-trust`.

- Copie o ficheiro de certificado no diretório `/etc/pki/ca-trust/source/anchors`.
- Execute `update-ca-trust`.

Consulte a documentação específica da sua distribuição para obter mais detalhes.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicione endereço IP do dispositivo e ponto final do serviço blob 

Siga os mesmos passos para adicionar endereço IP do dispositivo e ponto final do [serviço de blob ao ligar-se sobre *http*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Configure o software do parceiro e verifique a ligação

Siga os passos para configurar o [software parceiro que utilizou durante a ligação em *http*](#configure-partner-software-and-verify-connection). A única diferença é que deve deixar a *opção Use http* sem ser verificada.

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Uma vez ligado ao armazenamento da Caixa de Dados Blob, o próximo passo é copiar dados. Antes da cópia dos dados, reveja as seguintes considerações:

-  Ao copiar os dados, certifique-se de que o respetivo tamanho está em conformidade com os limites descritos em [Limites de armazenamento do Azure e do Data Box](data-box-limits.md).
- Se os dados, que estão a ser carregados pela Data Box, forem simultaneamente carregados por outras aplicações fora da Data Box, isso poderá resultar em falhas de emprego e corrupção de dados.

Neste tutorial, o AzCopy é utilizado para copiar dados para armazenamento de Caixas de Dados Blob. Também pode utilizar o Azure Storage Explorer (se preferir uma ferramenta baseada em GUI) ou um software parceiro para copiar os dados.

O procedimento de cópia tem os seguintes passos:

- Criar um contentor
- Upload conteúdo de uma pasta para armazenamento de Caixa de Dados Blob
- Upload de ficheiros modificados para armazenamento de Caixa de Dados Blob

Cada um destes passos é descrito em detalhe nas seguintes secções.

### <a name="create-a-container"></a>Criar um contentor

O primeiro passo é criar um recipiente, porque as bolhas são sempre colocadas num recipiente. Os contentores organizam grupos de bolhas como você organiza ficheiros em pastas no seu computador. Siga estes passos para criar um recipiente de bolhas.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento dentro da qual pretende criar o recipiente de bolhas.
3. Clique direito em **Recipientes Blob,** e a partir do menu de contexto, **selecione Create Blob Container**.

   ![Criar menu de contexto de recipientes blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Uma caixa de texto aparece abaixo da pasta **Blob Containers.** Introduza o nome do contentor de blobs. Consulte o [Create the Container e detete permissões](../storage/blobs/storage-quickstart-blobs-dotnet.md) para obter informações sobre regras e restrições sobre a designação de recipientes blob.
5. Pressione **Introduza** quando estiver feito para criar o recipiente de bolha, ou **Esc** para cancelar. Uma vez criado com sucesso o recipiente blob, este é apresentado sob a pasta **Blob Containers** para a conta de armazenamento selecionada.

   ![Recipiente blob criado](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Upload conteúdo de uma pasta para armazenamento de Caixa de Dados Blob

Utilize o AzCopy para fazer o upload de todos os ficheiros numa pasta para o armazenamento blob no Windows ou no Linux. Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Substitua `<key>` com a chave da conta. Para obter a chave da sua conta, no portal Azure, vá à sua conta de armazenamento. Vá a **Definições > Teclas**de acesso, selecione uma tecla e cole-a no comando AzCopy.

Se o contentor de destino especificado não existir, o AzCopy cria-o e carrega o ficheiro para o mesmo. Atualize o caminho de origem para o seu diretório de dados e substitua `data-box-storage-account-name` no URL de destino com o nome da conta de armazenamento associada à sua Caixa de Dados.

Para carregar o conteúdo do diretório especificado para o armazenamento de Blobs recursivamente, especifique a opção `--recursive` (Linux) ou `/S` (Windows). Quando executar o AzCopy com uma destas opções, todas as subpastas e respetivos ficheiros são também carregados.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Upload de ficheiros modificados para armazenamento de Caixa de Dados Blob

Utilize o AzCopy para fazer upload de ficheiros com base no seu último tempo modificado. Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Para carregar apenas ficheiros novos ou atualizados, adicione o parâmetro `--exclude-older` (Linux) ou `/XO` (Windows) ao comando do AzCopy.

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

Se houver erros durante a operação de ligação ou cópia, consulte [problemas de Resolução de Problemas com o armazenamento de Caixa](data-box-troubleshoot-rest.md)de Dados Blob .

O próximo passo é preparar o seu dispositivo para o navio.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligue-se ao armazenamento de Caixas de Dados Blob via *http* ou *https*
> * Copiar dados para o Data Box


Avance para o tutorial seguinte para saber como enviar o Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)
