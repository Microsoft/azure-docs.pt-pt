---
title: Obtenha assinatura de acesso partilhado URI para a sua imagem VM / Mercado Azure
description: Este artigo explica como obter a assinatura de acesso partilhado (SAS) URI para cada disco rígido virtual (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732645"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenha assinatura de acesso partilhado URI para a sua imagem VM

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas de Máquina Virtual Azure do Portal do Parceiro cloud para o Partner Center. Até que as suas ofertas sejam migradas, siga as instruções em Get shared access signature URI para a [sua imagem VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) para cloud partner Portal para gerir as suas ofertas.

Este artigo descreve como gerar um identificador uniforme de recurso de assinatura de acesso partilhado (SAS) para cada disco rígido virtual (VHD).

Durante o processo de publicação, deve fornecer um URI para cada VHD que esteja associado aos seus planos. Estes planos eram anteriormente referidos como SKUs, ou unidades de armazenamento de stocks. A Microsoft precisa de ter acesso a estes VHDs durante o processo de certificação. Você vai inserir este URI no separador **Planos** no Partner Center.

Ao gerar URIs SAS para os seus VHDs, siga estes requisitos:

* Apenas VHDs não geridos são suportados.
* `List` Apenas `Read` e são necessárias permissões. Não forneça acesso ao Write ou ao Delete.
* A duração do acesso (data de validade) deve ser mínima de três semanas a contar da criação do SAS URI.
* Para proteger contra alterações de tempo UTC, detete a data de início para um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2019, selecione 10/5/2019.

## <a name="generate-the-sas-address"></a>Gerar o endereço SAS

Existem duas ferramentas comuns usadas para criar um endereço SAS (URL):

* **Microsoft Storage Explorer** – Ferramenta gráfica disponível para Windows, macOS e Linux.
* **Microsoft Azure CLI** – Recomendado para sistemas operativos não Windows e ambientes de integração automatizada ou contínua.

### <a name="use-microsoft-storage-explorer"></a>Use Microsoft Storage Explorer

1. Descarregue e instale o [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o explorador e, no menu esquerdo, selecione **Adicionar Conta**. Aparece a caixa de diálogo **Connect to Azure Storage.**
3. Selecione **Adicionar uma Conta Azure** e, em seguida, **iniciar sessão**. Complete os passos necessários para assinar na sua conta Azure.
4. No painel esquerdo-**Explorer,** vá às suas **Contas de Armazenamento** e expanda este nó.
5. Clique no vHD e, em seguida, selecione **Obter Assinatura**de Acesso a Partilha .
6. Aparece a caixa de diálogo **Signature de Acesso Partilhado.** Complete os seguintes campos:

    * **Hora de início** – Data de início da permissão para acesso vHD. Forneça uma data que seja um dia antes da data atual.
    * **Tempo de validade** – Data de validade da permissão para acesso vHD. Forneça uma data que seja pelo menos três semanas além da data atual.
    * **Permissões** – Selecione as permissões de Leitura e Lista.
    * **Nível de contentores** – Verifique a caixa de verificação URI de **acesso partilhado ao nível do recipiente Generate.**

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ilustra a caixa de diálogo de assinatura de acesso partilhado":::

7. Para criar o SAS URI associado para este VHD, selecione **Criar**. A caixa de diálogo atualiza-se e mostra detalhes sobre esta operação.
8. Copie o **URI** e guarde-o para um ficheiro de texto num local seguro.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Ilustra a caixa de detalhes da Assinatura de Acesso Partilhado":::

    Este SAS URI gerado destina-se ao acesso ao nível do contentor. Para torná-lo específico, edite o ficheiro de texto para adicionar o nome VHD (próximo passo).

9. Insira o seu nome VHD após a cadeia vhds no SAS URI (inclua um corte para a frente). O SAS URI final deve ser assim:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Por exemplo, se o nome do `TestRGVM2.vhd`VDH for, então o SAS URI resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Repita estes passos para cada VHD nos planos que publicará.

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

1. Descarregue e [instale o Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). As versões estão disponíveis para Windows, macOS e vários distros do Linux.
2. Crie um ficheiro PowerShell (extensão de ficheiro.ps1), copie no seguinte código e, em seguida, guarde-o localmente.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Editar o ficheiro para utilizar os seguintes valores de parâmetro. Fornecer datas no formato data-data UTC, tais como `2020-04-01T00:00:00Z`.

    * `<account-name>`– O nome da sua conta de armazenamento Azure
    * `<account-key>`– A chave da conta de armazenamento Azure
    * `<vhd-name>`– O seu nome VHD
    * `<start-date>`– Data de início de autorização para acesso vHD. Forneça uma data um dia antes da data atual.
    * `<expiry-date>`– Data de validade da permissão para acesso vHD. Forneça uma data pelo menos três semanas após a data atual.

    Este exemplo mostra valores parâmetros adequados (no momento desta escrita):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Guarde as alterações.
5. Utilizando um dos seguintes métodos, execute este script com privilégios administrativos para criar uma cadeia de **ligação SAS** para acesso ao nível do contentor:

    * Executa o guião a partir da consola. No Windows, clique no script e selecione **Executar como administrador**.
    * Execute o script a partir de um editor de scripts PowerShell, como [o Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Este ecrã mostra a criação de uma cadeia de ligação SAS dentro deste editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ilustra a criação de uma linha de ligação SAS com o Windows PowerShell ISE":::

6. Copie a cadeia de ligação SAS e guarde-a para um ficheiro de texto num local seguro. Edite esta cadeia para adicionar as informações de localização VHD para criar o SAS URI final.
7. No portal Azure, vá ao armazenamento blob que inclui o VHD associado ao novo URI.
8. Copie o URL do ponto final do **serviço Blob,** como mostra a seguinte imagem

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ilustra o ponto final do serviço Blob":::

9. Editar o ficheiro de texto com a linha de ligação SAS a partir do passo 6. Crie o SAS URI completo utilizando este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Por exemplo, se o nome do `TestRGVM2.vhd`VHD for, o SAS URI será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita estes passos para cada VHD nas SKUs que pretende publicar.

## <a name="verify-the-sas-uri"></a>Verifique o SAS URI

Reveja cada um criado SAS URI utilizando a seguinte lista de verificação para verificar se:

* O URI é assim:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* O URI inclui o nome de ficheiro de imagem VHD, incluindo a extensão do nome de ficheiro ".vhd".
* `sp=rl`aparece perto do meio do seu URI. Esta corda `Read` mostra `List` isso e o acesso é especificado.
* Quando `sr=c` aparece, isto significa que o acesso ao nível do contentor é especificado.
* Copie e cole o URI num browser para testar o descarregamento da bolha (pode cancelar a operação antes de o download terminar).

## <a name="next-step"></a>Passo seguinte

Se tiver dificuldades em criar um SAS URI, consulte problemas de [URL SAS Comuns](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). Caso contrário, guarde os SAS URI(s) para um local seguro para posterior utilização. Vai precisar dele para publicar a sua oferta vm no Partner Center.

* [Criar uma oferta de Máquina Virtual do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
