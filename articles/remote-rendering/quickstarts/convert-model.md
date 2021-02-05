---
title: Converter um modelo
description: Quickstart que mostra os passos de conversão para um modelo personalizado.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: c9b5d525954e7f0742cd13fe4d64a73df64ea854
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594472"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Início Rápido: Converter um modelo para composição

Em [Quickstart: Rendere um modelo com Unidade,](render-model.md)aprendeu a usar o projeto de amostra de Unidade para tornar um modelo incorporado. Este guia mostra como converter os seus próprios modelos.

Vai aprender a:

> [!div class="checklist"]
>
> * Configurar uma conta de armazenamento de blob Azure para entrada e saída
> * Faça upload e converta um modelo 3D para uso com renderização remota Azure
> * Incluir o modelo 3D convertido numa aplicação para renderização

## <a name="prerequisites"></a>Pré-requisitos

* [Quickstart Completo: Rendere um modelo com Unidade](render-model.md)
* Para a conversão utilizando o script PowerShell: Instale a Azure PowerShell [(documentação)](/powershell/azure/)
  * Abrir um PowerShell com direitos de administração
  * Corra: `Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Descrição Geral

O renderizador do servidor não pode funcionar diretamente com formatos de modelo de origem, tais como FBX ou GLTF. Em vez disso, requer que o modelo esteja num formato binário proprietário.
O serviço de conversão consome modelos a partir do armazenamento de blob Azure e escreve modelos convertidos de volta para um recipiente de armazenamento de bolhas Azure fornecido.

É necessário:

* Uma subscrição do Azure
* Uma conta 'StorageV2' na sua subscrição
* Um recipiente de armazenamento de bolhas para o seu modelo de entrada
* Um recipiente de armazenamento de bolhas para os seus dados de saída
* Um modelo para converter, ver [modelos de amostra](../samples/sample-model.md)
  * Consulte a lista de formatos de [origem suportados](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Para utilizar o script de conversão da amostra, certifique-se de que prepara uma pasta de entrada que contém o modelo e todas as dependências externas (como texturas externas ou geometria)

## <a name="azure-setup"></a>Configuração azul

Se ainda não tiver uma conta, clique [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/) na opção conta gratuita e siga as instruções.

Assim que tiver uma conta Azure, vá a [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home) .

### <a name="storage-account-creation"></a>Criação de conta de armazenamento

Para criar armazenamento de bolhas, primeiro precisa de uma conta de armazenamento.
Para criar um, clique no botão "Criar um recurso":

![Azure - adicionar recurso](media/azure-add-a-resource.png)

A partir do novo ecrã, escolha **Armazenamento** no lado esquerdo e, em **seguida, conta de armazenamento - blob, arquivo, tabela, fila** a partir da coluna seguinte:

![Azure - adicione armazenamento](media/azure-add-storage.png)

Clicar neste botão irá trazer o seguinte ecrã com propriedades de armazenamento para preencher:

![Configuração Azure](media/azure-setup1.png)

Preencha o formulário da seguinte forma:

* Crie um novo Grupo de Recursos a partir do link abaixo da caixa de entrega e nomeie este **ARR_Tutorial**
* Para o nome da **conta De armazenamento,** insira um nome único aqui. **Este nome deve ser globalmente único,** caso contrário haverá uma solicitação que o informe de que o nome já está tomado. No âmbito deste arranque rápido, **chamamos-lhe arrtutorialstorage.** Assim, precisa substituí-lo pelo seu nome por qualquer ocorrência neste arranque rápido.
* Selecione um **local** próximo de si. Utilize idealmente o mesmo local utilizado para configurar a renderização no outro quickstart.
* **Conjunto** de desempenho para 'Standard'
* **Tipo de conta** definido para 'StorageV2 (finalidade geral v2)'
* **Replicação** definida para "Armazenamento geo-redundante de acesso à leitura (RA-GRS)"
* **Nível de acesso** definido para 'Hot'

Nenhuma das propriedades em outros separadores tem de ser alterada, para que possa proceder com **"Review + create"** e, em seguida, seguir os passos para completar a configuração.

O site informa-o agora sobre o progresso da sua implementação e informa que "a sua implementação está completa" eventualmente. Clique no botão **"Ir ao recurso"** para os próximos passos:

![Criação de armazenamento Azure completa](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Criação de armazenamento de bolhas

Em seguida, precisamos de dois recipientes blob, um para entrada e um para a saída.

A partir do botão **"Ir para** o recurso" acima, você chega a uma página com um painel à esquerda que contém um menu de lista. Nessa lista na categoria **"Serviço Blob",** clique no botão **"Contentores":**

![Azure - adicione recipientes](./media/azure-add-containers.png)

Prima o botão **"+ recipiente"** para criar o recipiente de armazenamento de bolhas de **entrada.**
Utilize as seguintes definições ao criá-la:
  
* Nome = arrinput
* Nível de acesso público = Privado

Depois de criado o recipiente, clique **novamente em + Recipiente** e repita com estas definições para o recipiente **de saída:**

* Nome = arroutput
* Nível de acesso público = Privado

Deve agora ter dois recipientes de armazenamento de bolhas:

![Configuração de armazenamento de bolhas](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Executar a conversão

Existem três formas distintas de desencadear uma conversão de modelo:

### <a name="1-conversion-via-the-arrt-tool"></a>1. Conversão através da ferramenta ARRT

Existe uma [ferramenta baseada em UI chamada ARRT](./../samples/azure-remote-rendering-asset-tool.md) para iniciar conversões e interagir com o resultado renderizado.
![ARRT](./../samples/media/azure-remote-rendering-asset-tool.png "Screenshot arrt")

### <a name="2-conversion-via-a-powershell-script"></a>2. Conversão através de um script PowerShell

Para facilitar a chamada do serviço de conversão de ativos, fornecemos um script de utilidade. Está localizado na pasta *Scripts* e chama-se **Conversion.ps1**.

Em particular, este guião

1. envia todos os ficheiros num dado diretório do disco local para o recipiente de armazenamento de entrada
1. chama [a conversão do ativo REST API,](../how-tos/conversion/conversion-rest-api.md)que irá recuperar os dados do recipiente de armazenamento de entrada e iniciar uma conversão, que devolverá um ID de conversão
1. poll the conversion status API with the retrieved conversion ID until the conversion process terminates with success or fail
1. recupera uma ligação com o ativo convertido no armazenamento de saída

O script lê a sua configuração a partir do ficheiro *Scripts\arrconfig.jsem*. Abra o ficheiro JSON num editor de texto.

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

A configuração dentro do grupo **accountSettings** (ID de conta e chave) deve ser preenchida análoga às credenciais no [Render um modelo com arranque rápido da Unidade](render-model.md).

Dentro do grupo **de activosConversionSettings,** certifique-se de alterar **o grupo de recursos**, **blobInputContainerName** e **blobOutputContainerName,** como acima visto.
Note que o valor para **a arrtutorialstorage** precisa de ser substituído pelo nome único que escolheu durante a criação da conta de armazenamento.

Altere **o LocalAssetDirectoryPath** para apontar para o diretório do seu disco, que contém o modelo que pretende converter. Tenha cuidado para escapar adequadamente às costas (" \\ ") no caminho utilizando duplos backslashes (" \\ \\ ").

Todos os dados do caminho dado no **LocalAssetDirectoryPath** serão enviados para o **blobInputContainerName** blob recipiente blob sob um subpato dado pelo **inputFolderPath**. Assim, na configuração de exemplo acima do conteúdo do diretório "D: \\ \\ tmp robot" será enviado para o recipiente blob "arrinput" da conta de armazenamento "arrtutorialstorage" no caminho "robotConversion". Os ficheiros já existentes serão substituídos.

Altere **o inputAssetPath** para o caminho do modelo a converter - o caminho é relativo ao LocalAssetDirectoryPath. Use "/" em vez de \\ " como separador do caminho. Assim, para um ficheiro "robot.fbx", que está localizado diretamente em "D: \\ \\ tmp robot", use "robot.fbx".

Uma vez convertido o modelo, este será devolvido ao recipiente de armazenamento dado pelo **blobOutputContainerName**. Um subpata pode ser especificado fornecendo a **saída opcionalFolderPath**. No exemplo acima, o "robot.arrAsset" resultante será copiado para o recipiente de bolhas de saída em "convertido/robô".

A definição de **config saídaAssetFileName** determina o nome do ativo convertido - o parâmetro é opcional e o nome de ficheiro de saída será deduzido do nome do ficheiro de entrada de outra forma.

Abra um PowerShell, certifique-se de que instalou o *Azure PowerShell,* conforme mencionado nos [pré-requisitos](#prerequisites). Em seguida, inicie sessão na sua subscrição com o seguinte comando e siga as instruções no ecrã:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Caso a sua organização tenha mais do que uma subscrição, poderá necessitar de especificar os argumentos do SubscriptionId e do Inquilino. Encontre detalhes na [documentação Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Mude para o `azure-remote-rendering\Scripts` diretório e execute o script de conversão:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Devia ver algo assim: ![Conversion.ps1](./media/successful-conversion.png)

### <a name="3-conversion-via-api-calls"></a>3. Conversão através de chamadas API

Tanto o C# como a API C++ fornecem um ponto de entrada para interagir com o serviço:
* [C# RemoteRenderingClient.StartAssetConversionAsync()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.startassetconversionasync)
* [C++ RemoteRenderingClient::StartAssetConversionAsync()](/cpp/api/remote-rendering/remoterenderingclient#startassetconversionasync)


## <a name="insert-new-model-into-quickstart-sample-app"></a>Insira novo modelo na Quickstart Sample App

O script de conversão gera um URI *de Assinatura de Acesso Partilhado (SAS)* para o modelo convertido. Pode agora copiar este URI como o Nome do **Modelo** na aplicação de amostra de arranque rápido (ver [Quickstart: Render a model with Unitity](render-model.md)).

![Substituir modelo na Unidade](./media/replace-model-in-unity.png)

 A amostra deve agora carregar e tornar o seu modelo personalizado!

## <a name="optional-re-creating-a-sas-uri"></a>Opcional: Recriar um SAS URI

O SAS URI criado pelo script de conversão só será válido por 24 horas. No entanto, depois de expirado, não precisa de voltar a converter o seu modelo. Em vez disso, pode criar um novo SAS no portal, conforme descrito nos próximos passos:

1. Aceda ao [Portal do Azure](https://www.portal.azure.com)
1. Clique no recurso da sua **conta de Armazenamento:** ![ Screenshot que realça o recurso de conta de Armazenamento selecionado.](./media/portal-storage-accounts.png)
1. No ecrã seguinte, clique no **explorador de armazenamento** no painel esquerdo e encontre o seu modelo de saída *(.arrAsset* file) no recipiente de armazenamento de bolhas de *arroutput.* Clique com o botão direito no ficheiro e **selecione Obter Assinatura** de Acesso Partilhado a partir do menu de contexto: Acesso à ![ assinatura](./media/portal-storage-explorer.png)
1. Um novo ecrã abre onde pode selecionar uma data de validade. Prima **Criar**, e copiar o URI que é mostrado no diálogo seguinte. Este novo URI substitui o URI temporário que o script criou.

## <a name="next-steps"></a>Passos seguintes

Agora que conhece o básico, olhe para os nossos tutoriais para obter mais conhecimento aprofundado.

Se quiser conhecer os detalhes da conversão do modelo, confira [a API de conversão do modelo](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Tutorial: Visualização remota de modelos renderizados](../tutorials/unity/view-remote-models/view-remote-models.md)