---
title: 'Tutorial: Copiar para blob armazenamento via REST APIs'
titleSuffix: Azure Data Box
description: Neste tutorial, aprenda a ligar-se ao armazenamento de Blob da Caixa de Dados Azure utilizando APIs REST em http ou https e, em seguida, copie dados da Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: alkohli
ms.openlocfilehash: cb0a90db0595c655191006969071bc5b9cceaa75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337598"
---
# <a name="tutorial-use-rest-apis-to-copy-data-to-azure-data-box-blob-storage"></a>Tutorial: Use APIs de REPOUSO para copiar dados para o armazenamento de blob da caixa de dados Azure  

Este tutorial descreve procedimentos para ligar ao armazenamento da Caixa de Dados Azure através de APIs REST em *http* ou *https*. Uma vez ligados, são também descritos os passos necessários para copiar os dados para o armazenamento da Caixa de Dados e preparar a Caixa de Dados para o envio.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conecte-se ao armazenamento de Bloco de Caixa de Dados via *http* ou *https*
> * Copiar dados para o Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Recebeu o Data Box e o estado da encomenda no portal é **Entregue**.
3. Reviu os [requisitos](data-box-system-requirements-rest.md) do sistema para o armazenamento de Data Box Blob e está familiarizado com versões suportadas de APIs, SDKs e ferramentas.
4. Tem acesso a um computador anfitrião que tem os dados que pretende copiar para a Data Box. O computador anfitrião tem de
    * Executar um [sistema operativo suportado](data-box-system-requirements.md).
    * Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, pode ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão impactadas.
5. [Faça o download do AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) no computador anfitrião. Utilizará o AzCopy para copiar dados para o armazenamento da Azure Data Box Blob a partir do seu computador anfitrião.

## <a name="connect-via-http-or-https"></a>Ligar via http ou https

Pode ligar-se ao armazenamento da Data Box Blob *em http* ou *https*.

* *Https* é a forma segura e recomendada de se ligar ao armazenamento da Bolha da Caixa de Dados.
* *Http* é usado ao ligar redes fidedignas.

Os passos a ligar são diferentes quando se conecta ao armazenamento de Bloco de Caixa de Dados *em http* ou *https*.

## <a name="connect-via-http"></a>Conecte-se via http

A ligação ao armazenamento de blob de caixa de dados REST APIs sobre *http* requer os seguintes passos:

* Adicione o dispositivo IP e o ponto final de serviço blob ao anfitrião remoto
* Configure software de terceiros e verifique a ligação

Cada um destes passos é descrito nas seguintes secções.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicionar endereço IP do dispositivo e ponto final de serviço blob

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="verify-connection-and-configure-partner-software"></a>Verifique a ligação e configurar o software do parceiro

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Conecte-se via https

A ligação ao armazenamento Azure Blob REST APIs sobre https requer os seguintes passos:

* Faça o download do certificado do portal Azure
* Importar o certificado no cliente ou anfitrião remoto
* Adicione o dispositivo IP e blob ponto final de serviço ao cliente ou anfitrião remoto
* Configure software de terceiros e verifique a ligação

Cada um destes passos é descrito nas seguintes secções.

### <a name="download-certificate"></a>Certificado de descarregamento

Utilize o portal Azure para descarregar o certificado.

1. Inicie sessão no portal do Azure.
2. Aceda à sua encomenda de Caixa de Dados e navegue para os **detalhes do Dispositivo General >**.
3. Sob **as credenciais do Dispositivo,** aceda ao acesso da **API** ao dispositivo. Clique em **Transferir**. Esta ação descarrega um ficheiro de certificado **\<your order name> .cer.** **Guarde** este ficheiro. Irá instalar este certificado no cliente ou no computador anfitrião que utilizará para ligar ao dispositivo.

    ![Certificado de descarregamento no portal Azure](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)

### <a name="import-certificate"></a>Importar o certificado

Aceder ao armazenamento de Caixa de Dados Blob sobre HTTPS requer um certificado TLS/SSL para o dispositivo. A forma como este certificado é disponibilizado à aplicação do cliente varia de aplicação para aplicação e através de sistemas operativos e distribuições. Algumas aplicações podem aceder ao certificado depois de ser importado para a loja de certificados do sistema, enquanto outras aplicações não utilizam esse mecanismo.

Nesta secção são mencionadas informações específicas para algumas aplicações. Para mais informações sobre outras aplicações, consulte a documentação para a aplicação e o sistema operativo utilizado.

Siga estes passos para importar o `.cer` ficheiro para a loja raiz de um cliente Windows ou Linux. Num sistema Windows, pode utilizar o Windows PowerShell ou o UI do Windows Server para importar e instalar o certificado no seu sistema.

#### <a name="use-windows-powershell"></a>Utilizar o Windows PowerShell

1. Inicie uma sessão Do Windows PowerShell como administrador.
2. Na linha de comandos, escreva:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Use o Servidor do Windows UI

1. Clique com o botão direito no `.cer` ficheiro e selecione **o certificado de instalação.** Esta ação inicia o Certificado De Importação De Feiticeiro.
2. Para **localizar a Loja**, selecione Máquina **Local** e, em seguida, clique em **Seguinte**.

    ![Assistente de Importação de Certificado, Servidor do Windows](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3. Selecione **Coloque todos os certificados na seguinte loja** e, em seguida, clique em **procurar**. Navegue até à loja de raiz do seu anfitrião remoto e, em seguida, clique em **Seguinte**.

    ![Assistente de Importação de Certificados, Loja de Certificados](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4. Clique em **Concluir**. Uma mensagem que diz que a importação foi bem sucedida aparece.

    ![Certificado Assistente de Importação, fim da importação](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Utilize um sistema Linux

O método de importação de um certificado varia por distribuição.

Vários, como Ubuntu e Debian, usam o `update-ca-certificates` comando.  

* Mude o nome do ficheiro de certificado codificado base64 para ter uma `.crt` extensão e copiá-lo no `/usr/local/share/ca-certificates directory` .
* Execute o comando `update-ca-certificates`.

Versões recentes de RHEL, Fedora e CentOS usam o `update-ca-trust` comando.

* Copie o ficheiro do certificado para o `/etc/pki/ca-trust/source/anchors` diretório.
* Execute `update-ca-trust`.

Consulte a documentação específica para a sua distribuição para mais detalhes.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicionar endereço IP do dispositivo e ponto final de serviço blob 

Siga os mesmos passos para [adicionar o endereço IP do dispositivo e o ponto final do serviço blob ao ligar *em http*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Configurar o software do parceiro e verificar a ligação

Siga os passos para configurar o [software do parceiro que utilizou durante a ligação em *http*](#verify-connection-and-configure-partner-software). A única diferença é que deve deixar a *opção Utilizar http* sem ser verificada.

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Uma vez ligado ao armazenamento da Caixa de Dados Blob, o próximo passo é copiar dados. Antes da cópia de dados, reveja as seguintes considerações:

* Ao copiar os dados, certifique-se de que o respetivo tamanho está em conformidade com os limites descritos em [Limites de armazenamento do Azure e do Data Box](data-box-limits.md).
* Se os dados, que estão a ser carregados pela Data Box, forem simultaneamente carregados por outras aplicações fora da Data Box, isso poderá resultar em falhas de trabalho no upload e corrupção de dados.

> [!IMPORTANT]
> Certifique-se de que mantém uma cópia dos dados de origem até poder confirmar que o Data Box transferiu os seus dados para o Armazenamento do Azure.

Neste tutorial, o AzCopy é usado para copiar dados para o armazenamento de Data Box Blob. Também pode utilizar o Azure Storage Explorer (se preferir uma ferramenta baseada em GUI) ou um software parceiro para copiar os dados.

O procedimento de cópia tem os seguintes passos:

* Criar um contentor
* Faça upload de conteúdo de uma pasta para o armazenamento de Data Box Blob
* Faça upload de ficheiros modificados para o armazenamento de Bloco de Caixa de Dados

Cada um destes passos é descrito em detalhe nas seguintes secções.

### <a name="create-a-container"></a>Criar um contentor

O primeiro passo é criar um recipiente, porque as bolhas são sempre carregadas num recipiente. Os contentores organizam grupos de bolhas como você organizam ficheiros em pastas no seu computador. Siga estes passos para criar um recipiente de bolhas.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento na qual deseja criar o recipiente blob.
3. Clique à direita em **Recipientes Blob**, e a partir do menu de contexto, selecione **Create Blob Container**.

   ![Menu de contexto de recipientes blob, criar recipiente blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Uma caixa de texto aparece por baixo da pasta **Blob Containers.** Introduza o nome do contentor de blobs. Consulte o [recipiente Criar e definir permissões](../storage/blobs/storage-quickstart-blobs-dotnet.md) para obter informações sobre regras e restrições sobre o nome de recipientes de bolhas.
5. Prima **Introduza** quando for feito para criar o recipiente blob ou **Esc** para cancelar. Uma vez criado o recipiente blob com sucesso, é apresentado sob a pasta **Blob Containers** para a conta de armazenamento selecionada.

   ![Recipiente blob criado](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Faça upload de conteúdo de uma pasta para o armazenamento de Data Box Blob

Utilize o AzCopy para fazer o upload de todos os ficheiros numa pasta para o armazenamento blob no Windows ou Linux. Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

#### <a name="linux"></a>Linux

```azcopy
azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S
```

`<key>`Substitua-a pela chave da conta. Para obter a chave da sua conta, no portal Azure, vá à sua conta de armazenamento. Vá às **definições > teclas de acesso,** selecione uma chave e cole-a no comando AzCopy.

Se o contentor de destino especificado não existir, o AzCopy cria-o e carrega o ficheiro para o mesmo. Atualize o caminho de origem para o seu diretório de dados e substitua `data-box-storage-account-name` no URL de destino pelo nome da conta de armazenamento associada à sua Caixa de Dados.

Para carregar o conteúdo do diretório especificado para o armazenamento de Blobs recursivamente, especifique a opção `--recursive` (Linux) ou `/S` (Windows). Quando executar o AzCopy com uma destas opções, todas as subpastas e respetivos ficheiros são também carregados.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Faça upload de ficheiros modificados para o armazenamento de Bloco de Caixa de Dados

Utilize o AzCopy para carregar ficheiros com base no seu último tempo modificado. Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Para carregar apenas ficheiros novos ou atualizados, adicione o parâmetro `--exclude-older` (Linux) ou `/XO` (Windows) ao comando do AzCopy.

Se quiser copiar apenas recursos de origem que não existem no destino, especifique os parâmetros `--exclude-older` e `--exclude-newer` (Linux) ou `/XO` e `/XN` (Windows) no comando do AzCopy. O AzCopy carrega apenas os dados atualizados, com base no respetivo carimbo de data/hora.

#### <a name="linux"></a>Linux

```azcopy
azcopy \
--source /mnt/myfolder \
--destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
--dest-key <key> \
--recursive \
--exclude-older
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO
```

Se houver erros durante a operação de ligação ou cópia, consulte [problemas de resolução de problemas com o armazenamento de Bloco de Caixa de Dados](data-box-troubleshoot-rest.md).

O próximo passo é preparar o seu dispositivo para o navio.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conecte-se ao armazenamento de Bloco de Caixa de Dados via *http* ou *https*
> * Copiar dados para o Data Box

Avance para o tutorial seguinte para saber como enviar o Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)
