---
title: Tutorial para copiar dados para Azure Data Box Heavy armazenamento de BLOBs por meio de APIs REST | Microsoft Docs
description: Saiba como copiar dados para o armazenamento de BLOBs Azure Data Box Heavy por meio de APIs REST
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: adc48acbadaef56958587dc79be377b76a21fdc0
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606302"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Tutorial: copiar dados para Azure Data Box armazenamento de BLOBs por meio de APIs REST  

Este tutorial descreve os procedimentos para se conectar ao armazenamento de BLOBs Azure Data Box por meio de APIs REST por *http* ou *https*. Uma vez conectado, as etapas necessárias para copiar os dados para Data Box armazenamento de blob são descritas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Conectar-se a Data Box armazenamento de blob via *http* ou *https*
> * Copiar dados para o Data Box Heavy

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Você concluiu o [tutorial: configurar Azure data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Recebeu o Data Box Heavy e o estado da encomenda no portal é **Entregue**.
3. Você examinou os [requisitos de sistema para data Box armazenamento de BLOBs](data-box-system-requirements-rest.md) e está familiarizado com versões com suporte de APIs, SDKs e ferramentas.
4. Você tem acesso a um computador host que tem os dados que deseja copiar para Data Box Heavy. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Para velocidades de cópia mais rápidas, podem ser utilizadas em paralelo duas ligações de 40 GbE (uma por nó). Se não tiver uma ligação de 40 GbE disponível, recomendamos que tenha pelo menos duas ligações de 10 GbE (uma por nó). 
5. [Baixe o AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) no computador host. Você usará o AzCopy para copiar dados para Azure Data Box armazenamento de BLOBs do seu computador host.


## <a name="connect-via-http-or-https"></a>Conectar via http ou https

Você pode se conectar a Data Box armazenamento de BLOBs via *http* ou *https*.

- *Https* é a maneira segura e recomendada de se conectar a data Box armazenamento de BLOBs.
- O *http* é usado ao conectar-se por redes confiáveis.

As etapas para conectar são diferentes quando você se conecta ao armazenamento de BLOBs Data Box por *http* ou *https*.

## <a name="connect-via-http"></a>Conectar via http

A conexão com Data Box APIs REST do armazenamento de BLOBs sobre *http* requer as seguintes etapas:

- Adicionar o ponto de extremidade do dispositivo IP e do serviço blob ao host remoto
- Configurar software de terceiros e verificar a conexão

Cada uma dessas etapas é descrita nas seções a seguir.

> [!IMPORTANT]
> Por Data Box Heavy, você precisará repetir todas as instruções de conexão para se conectar ao segundo nó.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicionar o endereço IP do dispositivo e o ponto de extremidade do serviço blob

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]



### <a name="configure-partner-software-and-verify-connection"></a>Configurar o software do parceiro e verificar a conexão

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Conectar via https

A conexão com as APIs REST do armazenamento de BLOBs do Azure via HTTPS requer as seguintes etapas:

- Baixar o certificado de portal do Azure
- Importar o certificado no cliente ou host remoto
- Adicionar o ponto de extremidade do dispositivo IP e do serviço blob ao cliente ou ao host remoto
- Configurar software de terceiros e verificar a conexão

Cada uma dessas etapas é descrita nas seções a seguir.

> [!IMPORTANT]
> Por Data Box Heavy, você precisará repetir todas as instruções de conexão para se conectar ao segundo nó.

### <a name="download-certificate"></a>Baixar certificado

Use o portal do Azure para baixar o certificado.

1. Entre no portal do Azure.
2. Vá para sua ordem de Data Box e navegue até **> detalhes do dispositivo geral**.
3. Em **credenciais do dispositivo**, vá para **acesso à API** para o dispositivo. Clique em **Transferir**. Essa ação baixa um **\<seu nome de pedido > arquivo de certificado. cer** . **Salve** este arquivo. Você instalará esse certificado no cliente ou computador host que será usado para se conectar ao dispositivo.

    ![Baixar o certificado no portal do Azure](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Importar certificado 

Acessar Data Box armazenamento de BLOBs via HTTPS requer um certificado SSL para o dispositivo. A maneira como esse certificado é disponibilizado para o aplicativo cliente varia de aplicativo para aplicativo e entre sistemas operacionais e distribuições. Alguns aplicativos podem acessar o certificado depois de serem importados para o repositório de certificados do sistema, enquanto outros não fazem uso desse mecanismo.

Informações específicas para alguns aplicativos são mencionadas nesta seção. Para obter mais informações sobre outros aplicativos, consulte a documentação do aplicativo e o sistema operacional usado.

Siga estas etapas para importar o arquivo de `.cer` para o armazenamento raiz de um cliente Windows ou Linux. Em um sistema Windows, você pode usar o Windows PowerShell ou a interface do usuário do Windows Server para importar e instalar o certificado em seu sistema.

#### <a name="use-windows-powershell"></a>Usar o Windows PowerShell

1. Inicie uma sessão do Windows PowerShell como administrador.
2. Na linha de comandos, escreva:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Usar a interface do usuário do Windows Server

1.  Clique com o botão direito do mouse no arquivo `.cer` e selecione **Instalar certificado**. Essa ação inicia o assistente para importação de certificados.
2.  Em **local do repositório**, selecione **computador local**e clique em **Avançar**.

    ![Importar certificado usando o PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Selecione **Coloque todos os certificados no repositório a seguir**e clique em **procurar**. Navegue até o repositório raiz do seu host remoto e clique em **Avançar**.

    ![Importar certificado usando o PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Clique em **Concluir**. Uma mensagem que informa que a importação foi bem-sucedida é exibida.

    ![Importar certificado usando o PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Usar um sistema Linux

O método para importar um certificado varia de acordo com a distribuição.

> [!IMPORTANT]
> Por Data Box Heavy, você precisará repetir todas as instruções de conexão para se conectar ao segundo nó.

Vários, como Ubuntu e Debian, usam o comando `update-ca-certificates`.  

- Renomeie o arquivo de certificado codificado em base64 para ter uma extensão `.crt` e copie-o para o `/usr/local/share/ca-certificates directory`.
- Execute o comando `update-ca-certificates`.

As versões recentes do RHEL, Fedora e CentOS usam o comando `update-ca-trust`.

- Copie o arquivo de certificado para o diretório `/etc/pki/ca-trust/source/anchors`.
- Execute `update-ca-trust`.

Consulte a documentação específica da sua distribuição para obter detalhes.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicionar o endereço IP do dispositivo e o ponto de extremidade do serviço blob 

Siga as mesmas etapas para [Adicionar o endereço IP do dispositivo e o ponto de extremidade do serviço blob ao se conectar via *http*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Configurar o software do parceiro e verificar a conexão

Siga as etapas para [Configurar o software de parceiro que você usou ao se conectar via *http*](#configure-partner-software-and-verify-connection). A única diferença é que você deve deixar a *opção usar http* desmarcada.

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para o Data Box Heavy

Quando você estiver conectado ao armazenamento de BLOBs Data Box, a próxima etapa será copiar dados. Antes da cópia de dados, examine as seguintes considerações:

-  Ao copiar dados, verifique se o tamanho dos dados está de acordo com os limites de tamanho descritos nos [limites de armazenamento e data Box Heavy do Azure](data-box-limits.md).
- Se os dados, que estão sendo carregados pelo Data Box Heavy, forem carregados simultaneamente por outros aplicativos fora do Data Box Heavy, isso poderá resultar em falhas de trabalho de upload e corrupção de dados.

Neste tutorial, AzCopy é usado para copiar dados para Data Box armazenamento de BLOBs. Você também pode usar Gerenciador de Armazenamento do Azure (se preferir uma ferramenta baseada em GUI) ou um software de parceiro para copiar os dados.

O procedimento de cópia tem as seguintes etapas:

- Criar um contentor
- Carregar o conteúdo de uma pasta para Data Box armazenamento de BLOBs
- Carregar arquivos modificados para Data Box armazenamento de BLOBs


Cada uma dessas etapas é descrita detalhadamente nas seções a seguir.

> [!IMPORTANT]
> Por Data Box Heavy, você precisará repetir todas as instruções de cópia para copiar dados para o segundo nó.

### <a name="create-a-container"></a>Criar um contentor

A primeira etapa é criar um contêiner, porque os BLOBs são sempre carregados em um contêiner. Os contêineres organizam grupos de BLOBs, como você organiza arquivos em pastas em seu computador. Siga estas etapas para criar um contêiner de BLOB.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento na qual você deseja criar o contêiner de BLOB.
3. Clique com o botão direito do mouse em **contêineres de blob**e, no menu de contexto, selecione **criar contêiner de blob**.

   ![Menu de contexto criar contêineres de BLOB](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Uma caixa de texto é exibida abaixo da pasta **contêineres de blob** . Introduza o nome do contentor de blobs. Consulte [criar o contêiner e definir permissões](../storage/blobs/storage-quickstart-blobs-dotnet.md) para obter informações sobre regras e restrições sobre como nomear contêineres de BLOB.
5. Pressione **Enter** quando terminar de criar o contêiner de BLOB ou **ESC** para cancelar. Depois que o contêiner de blob for criado com êxito, ele será exibido na pasta **contêineres de blob** para a conta de armazenamento selecionada.

   ![Contêiner de blob criado](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Carregar o conteúdo de uma pasta para Data Box armazenamento de BLOBs

Use AzCopy para carregar todos os arquivos em uma pasta para o armazenamento de BLOBs no Windows ou Linux. Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Substitua `<key>` pela sua chave de conta. Para obter sua chave de conta, na portal do Azure, vá para sua conta de armazenamento. Acesse **configurações > chaves de acesso**, selecione uma chave e cole-a no comando AzCopy.

Se o contentor de destino especificado não existir, o AzCopy cria-o e carrega o ficheiro para o mesmo. Atualize o caminho de origem para o diretório de dados e substitua `data-box-storage-account-name` na URL de destino pelo nome da conta de armazenamento associada à sua Data Box.

Para carregar o conteúdo do diretório especificado para o armazenamento de Blobs recursivamente, especifique a opção `--recursive` (Linux) ou `/S` (Windows). Quando executar o AzCopy com uma destas opções, todas as subpastas e respetivos ficheiros são também carregados.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Carregar arquivos modificados para Data Box armazenamento de BLOBs

Use AzCopy para carregar arquivos com base na hora da última modificação. Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Para carregar apenas ficheiros novos ou atualizados, adicione o parâmetro `--exclude-older` (Linux) ou `/XO` (Windows) ao comando do AzCopy.

Se quiser copiar apenas recursos de origem que não existem no destino, especifique os parâmetros `--exclude-older` e `--exclude-newer` (Linux) ou `/XO` e `/XN` (Windows) no comando do AzCopy. O AzCopy carrega apenas os dados atualizados, com base no respetivo carimbo de data/hora.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO

Se houver erros durante a operação de conexão ou de cópia, consulte [solucionar problemas com o armazenamento de blobs data Box](data-box-troubleshoot-rest.md).

A próxima etapa é preparar seu dispositivo para envio.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Conectar-se a Data Box armazenamento de blob via *http* ou *https*
> * Copiar dados para o Data Box Heavy


Avance para o tutorial seguinte para saber como enviar o Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box Heavy para a Microsoft](./data-box-heavy-deploy-picked-up.md)
