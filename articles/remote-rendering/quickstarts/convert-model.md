---
title: Converter um modelo
description: Quickstart que mostra os passos de conversão para um modelo personalizado.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 7ba8d201c29b5e3835fec52d8c479a388ca07f71
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312999"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Quickstart: Converter um modelo para renderização

Em [Quickstart: Render um modelo com Unidade,](render-model.md)aprendeu a usar o projeto de amostra de Unidade para renderum modelo incorporado. Este guia mostra como converter os seus próprios modelos.

Vai aprender a:

> [!div class="checklist"]
>
> * Criar uma conta de armazenamento de blob Azure para entrada e saída
> * Carregue e converta um modelo 3D para utilização com renderização remota Azure
> * Incluir o modelo 3D convertido numa aplicação para renderização

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Render um modelo com Unidade](render-model.md)
* Instalar a Azure PowerShell [(documentação)](https://docs.microsoft.com/powershell/azure/)
  * Abra um PowerShell com direitos de administração
  * Executar:`Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Descrição geral

O renderizador no servidor não pode funcionar diretamente com formatos de modelo de origem, como FBX ou GLTF. Em vez disso, requer que o modelo esteja num formato binário proprietário.
O serviço de conversão consome modelos do armazenamento de blob Azure e escreve modelos convertidos de volta para um recipiente de armazenamento de blob Azure fornecido.

É necessário:

* Uma subscrição do Azure.
* Uma conta 'StorageV2' na sua subscrição
* Um recipiente de armazenamento de bolhas para o seu modelo de entrada
* Um recipiente de armazenamento de bolhas para os seus dados de saída
* Um modelo para converter, ver [modelos de amostra](../samples/sample-model.md)
  * Consulte a lista de [formatos de origem suportados](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Para utilizar o script de conversão da amostra, certifique-se de que prepara uma pasta de entrada que contenha o modelo e todas as dependências externas (como texturas externas ou geometria)

## <a name="azure-setup"></a>Configuração azure

Se ainda não tiver uma conta, vá a [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/), clique na opção de conta gratuita e siga as instruções.

Assim que tiver uma conta [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home)Azure, vá a .

### <a name="storage-account-creation"></a>Criação de conta de armazenamento

Para criar armazenamento de bolhas, primeiro precisa de uma conta de armazenamento.
Para criar um, clique no botão "Criar um recurso":

![Azure - adicionar recurso](media/azure-add-a-resource.png)

A partir do novo ecrã, escolha **Armazenamento** no lado esquerdo e, em seguida, **conta de armazenamento - blob, arquivo, tabela, fila** a partir da próxima coluna:

![Azure - adicione armazenamento](media/azure-add-storage.png)

Clicar neste botão irá trazer o seguinte ecrã com propriedades de armazenamento para preencher:

![Configuração Azure](media/azure-setup1.png)

Preencha o formulário da seguinte forma:

* Crie um novo Grupo de Recursos a partir do link abaixo da caixa de lançamento e nomeie este **ARR_Tutorial**
* Para o nome da **conta De armazenamento,** insira um nome único aqui. **Este nome deve ser globalmente único,** caso contrário haverá uma solicitação que o informe de que o nome está pronto. No âmbito deste arranque rápido, damos-lhe o nome de **arrtutorialstorage.** Assim, tem de substituí-lo pelo seu nome por qualquer ocorrência neste arranque rápido.
* Selecione um **local** próximo. Idealmente, utilize o mesmo local usado para configurar a renderização no outro arranque rápido.
* **Desempenho** definido para 'Standard'
* **Tipo de conta** definido para 'StorageV2 (finalidade geral v2)'
* **Replicação** definida para "Armazenamento geo-redundante de acesso de leitura (RA-GRS)"
* **Nível de acesso** definido para 'Hot'

Nenhuma das propriedades de outros separadores tem de ser alterada, para que possa prosseguir com **"Review + create"** e, em seguida, seguir os passos para completar a configuração.

O site informa-o agora sobre o progresso da sua implementação e informa "A sua implementação está completa" eventualmente. Clique no botão **"Ir para o recurso"** para os próximos passos:

![Criação de Armazenamento Azure concluída](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Criação de armazenamento de blob

Em seguida, precisamos de dois recipientes de bolha, um para entrada e outro para a saída.

A partir do botão **"Ir para o recurso"** acima, você chega a uma página com um painel à esquerda que contém um menu de lista. Nessa lista na categoria **"Serviço Blob",** clique no botão **"Contentores":**

![Azure - adicione contentores](./media/azure-add-containers.png)

Pressione o botão **"+ Recipiente"** para criar o recipiente de armazenamento de bolhas de **entrada.**
Utilize as seguintes definições ao criá-la:
  
* Nome = arrinput
* Nível de acesso público = Privado

Depois de criado o recipiente, clique novamente em **recipiente +** e repita com estas definições para o recipiente de **saída:**

* Nome = arroutput
* Nível de acesso público = Privado

Agora deve ter dois recipientes de armazenamento de bolhas:

![Configuração de armazenamento blob](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Executar a conversão

Para facilitar a chamada do serviço de conversão de ativos, fornecemos um script de utilidade. Está localizado na pasta *Scripts* e chama-se **Conversão.ps1**.

Em particular, este guião

1. uploads todos os ficheiros em um dado diretório de disco local para o recipiente de armazenamento de entrada
1. chama [a conversão de ativos REST API](../how-tos/conversion/conversion-rest-api.md) que irá recuperar os dados do recipiente de armazenamento de entrada e iniciar uma conversão que devolverá um id de conversão
1. sondagem do estado de conversão API com o id de conversão recuperado até que o processo de conversão termine com sucesso ou falha
1. recupera uma ligação com o ativo convertido no armazenamento de saída

O script lê a sua configuração a partir do ficheiro *Scripts\arrconfig.json*. Abra o ficheiro JSON num editor de texto.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

A configuração dentro do grupo **de definições** de conta (ID de conta e chave) deve ser preenchida análoga às credenciais no [Render um modelo com unidade quickstart](render-model.md).

Dentro do grupo **AssetConversionSettings,** certifique-se de alterar **recursosGroup,** **blobInputContainerName**e **blobOutputContainerName** como visto acima.
Note que o valor **arrtutorialstorage** precisa ser substituído pelo nome único que escolheu durante a criação da conta de armazenamento.

Altere **localAssetDirectoryPath** para apontar para o diretório no seu disco que contém o modelo que pretende converter. Tenha cuidado para escapar corretamente\\às pestanas (" ") no\\\\caminho usando pestanas duplas (" ").

Todos os dados do caminho dado no **localAssetDirectoryPath** serão enviados para o recipiente **blobInputContainerName** blob sob um subpata dado pela **entradaFolderPath**. Assim, na configuração do exemplo acima\\do\\conteúdo do diretório "D: robô tmp" será enviado para o recipiente blob "arrinput" da conta de armazenamento "arrtutorialstorage" sob o caminho "robotConversion". Os ficheiros já existentes serão substituídos.

Alterar o **inputAssetPath** para o caminho do modelo a converter - o caminho é relativo ao AssetDirectoryPath local. Utilize "/" em\\vez de " como separador do caminho. Assim, para um ficheiro "robot.fbx" que está\\localizado\\diretamente em "D: robô tmp" use "robot.fbx".

Uma vez convertido o modelo, será reescrito para o recipiente de armazenamento dado por **blobOutputContainerName**. Um subpata pode ser especificado fornecendo o **outputoptionalFolderPath**. No exemplo acima do "robot.arrAsset" resultante será copiado para o recipiente de saída blob em "convertido/robô".

A saída de definição de **configAssetFileName** determina o nome do ativo convertido - o parâmetro é opcional e o nome de ficheiro de saída será deduzido do nome do ficheiro de entrada de outra forma. 

Abra um PowerShell, certifique-se de que instalou o *Azure PowerShell* conforme mencionado nos [pré-requisitos](#prerequisites). Em seguida, inicie sessão na sua subscrição com o seguinte comando e siga as instruções no ecrã:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Caso a sua organização tenha mais do que uma subscrição, poderá ser necessário especificar os argumentos Do Id e do Inquilino. Encontre detalhes na [documentação Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

Mude para `azure-remote-rendering\Scripts` o diretório e execute o script de conversão:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Devia ver algo assim: ![Conversão.ps1](./media/successful-conversion.png)

O script de conversão gera uma *Assinatura de Acesso Partilhado (SAS)* URI para o modelo convertido. Agora pode copiar este URI como **nome** modelo na aplicação de amostra quickstart (ver [Quickstart: Render um modelo com Unidade).](render-model.md)

![Substituir modelo em Unidade](./media/replace-model-in-unity.png)

 A amostra deve agora carregar e render o seu modelo personalizado!

## <a name="optional-re-creating-a-sas-uri"></a>Opcional: Recriar um SAS URI

O SAS URI criado pelo script de conversão só será válido por 24 horas. No entanto, depois de expirado, não precisa de voltar a converter o seu modelo. Em vez disso, pode criar um novo SAS no portal, conforme descrito nos próximos passos:

1. Aceda ao [Portal do Azure](https://www.portal.azure.com)
1. Clique no seu recurso ![de conta de **Armazenamento:** Acesso de Assinatura](./media/portal-storage-accounts.png)
1. No ecrã seguinte, clique no explorador de **armazenamento** no painel esquerdo e encontre o seu modelo de saída (ficheiro *.arrAsset)* no recipiente de armazenamento de blocos de *arroutput.* Clique à direita no ficheiro e selecione Obter ![Assinatura de Acesso **Partilhado** a partir do menu de contexto: Acesso de assinatura](./media/portal-storage-explorer.png)
1. Abre-se um novo ecrã onde pode selecionar uma data de validade. Pressione **Criar**e copiar o URI que é mostrado no diálogo seguinte. Este novo URI substitui o URI temporário que o guião criou.

## <a name="next-steps"></a>Passos seguintes

Agora que conhece o básico, veja os nossos tutoriais para ganhar mais conhecimento aprofundado.

Se quiser conhecer os detalhes da conversão do modelo, confira [a conversão do modelo REST API](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Tutorial: Criação de um projeto de unidade do zero](../tutorials/unity/project-setup.md)
