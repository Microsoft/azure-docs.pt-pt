---
title: Assinatura de acesso partilhado URI para imagens VM - Azure Marketplace
description: Gere uma assinatura de acesso partilhado (SAS) URI para os seus discos rígidos virtuais (VHD) no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/14/2020
ms.openlocfilehash: f3589fb9ae176e04f727f516cca7c18c87dad9e0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317506"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenha a assinatura de acesso partilhado URI para a sua imagem VM

Este artigo descreve como gerar um identificador de recursos uniforme (SAS) de acesso partilhado (URI) para cada disco rígido virtual (VHD).

Durante o processo de publicação, você deve fornecer um URI para cada VHD que está associado aos seus planos (anteriormente chamado SKUs). A Microsoft precisa de acesso a estes VHDs durante o processo de certificação. Você vai inserir este URI no **separador Planos** no Partner Center.

Ao gerar SAS URIs para os seus VHDs, siga estes requisitos:

* Apenas VHDs não geridos são suportados.
* Apenas `List` e `Read` permissões são necessárias. Não forneça acesso a Escrever ou Eliminar.
* A duração do acesso (data de validade) deve ser mínima de três semanas a partir da criação do SAS URI.
* Para proteger contra alterações de tempo UTC, desajei a data de início para um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2019, selecione 10/5/2019.

## <a name="generate-the-sas-address"></a>Gerar o endereço SAS

Existem duas ferramentas comuns usadas para criar um endereço SAS (URL):

* **Microsoft Storage Explorer** – Ferramenta gráfica disponível para Windows, macOS e Linux.
* **Microsoft Azure CLI** – Recomendado para sistemas operativos não Windows e ambientes de integração automatizado ou contínuo.

### <a name="use-microsoft-storage-explorer"></a>Use Microsoft Storage Explorer

1. Descarregue e instale [o Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o explorador e, no menu esquerdo, selecione **Adicionar Conta**. Aparece a caixa de diálogo **de armazenamento Connect to Azure.**
3. **Selecione Adicionar uma Conta Azure** e, em seguida, iniciar **sação .** Preencha os passos necessários para iniciar súm na sua conta Azure.
4. No painel esquerda-**Explorer,** vá às suas **Contas de Armazenamento** e expanda este nó.
5. Clique com o botão direito no VHD e, em seguida, **selecione Obter Obter Assinatura de Acesso a Partilha**.
6. Aparece a caixa de diálogo **assinatura de acesso** partilhado. Complete os seguintes campos:

    * **Hora de** início – Data de início da permissão para acesso vHD. Forneça uma data que seja um dia antes da data atual.
    * **Prazo de validade** – Data de validade da autorização para acesso vHD. Providenciar uma data que esteja pelo menos três semanas depois da data atual.
    * **Permissões** – Selecione as permissões de Leitura e Lista.
    * **Nível de recipiente** – Verifique a caixa de verificação **URI de assinatura de assinatura de acesso partilhado ao nível do recipiente Gerar.**

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ilustra a caixa de diálogo assinatura de acesso partilhado":::

7. Para criar o SAS URI associado para este VHD, selecione **Create**. A caixa de diálogo atualiza e mostra detalhes sobre esta operação.
8. Copie o **URI** e guarde-o num ficheiro de texto num local seguro.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Ilustra a caixa de detalhes da assinatura de acesso partilhado":::

    Este SAS URI gerado destina-se ao acesso ao nível do contentor. Para torná-lo específico, edite o ficheiro de texto para adicionar o nome VHD (próximo passo).

9. Insira o seu nome VHD após a corda vhds no SAS URI (inclua um corte para a frente). O SAS URI final deve ser assim:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Por exemplo, se o nome do VDH `TestRGVM2.vhd` for, então o SAS URI resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Repita estes passos para cada VHD nos planos que publicará.

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

1. Faça o download e instale [o Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). As versões estão disponíveis para Windows, macOS e vários distros do Linux.
2. Crie um ficheiro PowerShell (extensão de ficheiro.ps1), copie no seguinte código e guarde-o localmente.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Edite o ficheiro para utilizar os seguintes valores de parâmetro. Fornecer datas no formato de datata UTC, tais como `2020-04-01T00:00:00Z` .

    * `<account-name>`– O nome da sua conta de armazenamento Azure
    * `<account-key>`– A chave da sua conta de armazenamento Azure
    * `<vhd-name>`– O seu nome VHD
    * `<start-date>`– Data de início da permissão para acesso vHD. Forneça uma data um dia antes da data atual.
    * `<expiry-date>`– Data de validade da permissão para acesso vHD. Forneça uma data pelo menos três semanas após a data atual.

    Este exemplo mostra valores de parâmetros adequados (no momento desta escrita):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Guarde as alterações.
5. Utilizando um dos seguintes métodos, execute este script com privilégios administrativos para criar uma cadeia de **conexão SAS** para acesso ao nível do contentor:

    * Executar o guião a partir da consola. No Windows, clique com o botão direito no script e selecione **Executar como administrador**.
    * Execute o script a partir de um editor de scripts PowerShell, como [o Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Este ecrã mostra a criação de uma cadeia de conexão SAS dentro deste editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ilustra a criação de uma cadeia de conexão SAS com o Windows PowerShell ISE":::

6. Copie a cadeia de ligação SAS e guarde-a num ficheiro de texto num local seguro. Edite este string para adicionar as informações de localização VHD para criar o SAS URI final.
7. No portal Azure, vá ao armazenamento de bolhas que inclui o VHD associado ao novo URI.
8. Copie o URL do ponto final do **serviço Blob,** como mostrado na imagem seguinte

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ilustra o ponto final do serviço Blob":::

9. Editar o ficheiro de texto com a cadeia de ligação SAS a partir do passo 6. Crie o SAS URI completo utilizando este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Por exemplo, se o nome do VHD `TestRGVM2.vhd` for, o SAS URI será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita estes passos para cada VHD nos planos que publicará.

## <a name="verify-the-sas-uri"></a>Verifique o SAS URI

Reveja cada um criado SAS URI utilizando a seguinte lista de verificação para verificar se:

* O URI é assim:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* O URI inclui o seu nome de ficheiro de imagem VHD, incluindo a extensão do nome de ficheiro ".vhd".
* `sp=rl`aparece perto do meio do seu URI. Esta cadeia mostra que `Read` e o acesso é `List` especificado.
* Quando `sr=c` aparece, isto significa que o acesso ao nível do contentor é especificado.
* Copie e cole o URI num browser para testar o blob (pode cancelar a operação antes que o download esteja concluído).

## <a name="next-step"></a>Passo seguinte

Se tiver dificuldades em criar um SAS URI, consulte [os problemas de URL da Sas](common-sas-uri-issues.md)Common . Caso contrário, guarde o SAS URI(s) para um local seguro para utilização posterior. Vai precisar dele para publicar a sua oferta de VM no Partner Center.

* [Criar uma oferta de máquina virtual Azure](azure-vm-create-offer.md)
