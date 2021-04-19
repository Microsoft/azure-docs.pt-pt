---
title: Como importar uma nova atualização | Microsoft Docs
description: How-To guia para importar uma nova atualização para ioT Hub Device Update para IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 196a449f25d97fb1c1b7b8d79ee8889e0d31a5ae
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717752"
---
# <a name="import-new-update"></a>Importação Nova Atualização
Saiba como importar uma nova atualização para a Atualização de Dispositivos para IoT Hub. Se ainda não o fez, não se esqueça de se familiarizar com os [conceitos básicos de importação.](import-concepts.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um ficheiro de atualização existente que pretende implementar nos dispositivos. Pode ser um ficheiro de imagem para atualização baseada em imagem ou um [ficheiro APT Manifesto](device-update-apt-manifest.md) para atualização baseada em pacotes. [Como escolho?](understand-device-update.md#support-for-a-wide-range-of-update-artifacts)
* [Acesso a um Hub IoT com atualização do dispositivo para o IoT Hub ativado](create-device-update-account.md). 
* Um dispositivo IoT (ou simulador) previsto para a Atualização do Dispositivo dentro do Hub IoT.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) ou posteriormente (inclui instalações linux, macOS e Windows)
* Navegadores suportados:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Alguns dados submetidos a este serviço podem ser processados numa região fora da região em que este caso foi criado.

## <a name="create-device-update-import-manifest"></a>Criar manifesto de importação de atualização de dispositivos

1. Se ainda não o fez, obtenha um ficheiro de imagem ou um ficheiro APT Manifesto que pretenda implantar nos dispositivos. Isto pode ser do fabricante dos seus dispositivos ou de um integrador de sistema com o que trabalha, ou mesmo de um grupo dentro da sua organização. Certifique-se de que o ficheiro de imagem de atualização ou o ficheiro APT Manifesto estão localizados num diretório acessível a partir do PowerShell.

2. Crie um ficheiro de texto chamado **AduUpdate.psm1** no diretório onde está localizado o seu ficheiro de imagem de atualização ou ficheiro APT Manifesto. Em seguida, abra o [cmdlet AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell, copie o conteúdo do seu ficheiro de texto e, em seguida, guarde o ficheiro de texto.

3. Em PowerShell, navegue para o diretório onde criou o seu cmdlet PowerShell a partir do passo 2. Utilize a opção Copiar abaixo e, em seguida, cole-a em PowerShell para executar os comandos:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Executar os seguintes comandos substituindo os valores dos parâmetros da amostra para gerar um manifesto de importação, um ficheiro JSON que descreve a atualização:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Para referência rápida, aqui estão alguns valores de exemplo para os parâmetros acima. Você também pode ver o esquema completo [de importação manifesto](import-schema.md) para mais detalhes.

    | Parâmetro | Descrição |
    | --------- | ----------- |
    | dispositivo Manfacturer | Fabricante do dispositivo a atualização é compatível com, por exemplo, Contoso. Deve coincidir com [a propriedade](./device-update-plug-and-play.md#device-properties)do dispositivo _do fabricante._
    | modelo de dispositivo | Modelo do dispositivo a atualização é compatível, por exemplo, com torradeira. Deve corresponder à propriedade do [dispositivo](./device-update-plug-and-play.md#device-properties) _modelo._
    | atualizarProvider | Entidade que está a criar ou diretamente responsável pela atualização. Muitas vezes será um nome de empresa.
    | atualizarName | Identificador para uma classe de atualizações. A aula pode ser o que quiser. Muitas vezes será um dispositivo ou nome de modelo.
    | actualizaçãoVersão | Número de versão que distingue esta atualização de outras que têm o mesmo Fornecedor e Nome. Não tem uma versão de um componente de software individual no dispositivo (mas pode, se quiser).
    | atualizaçãoType | <ul><li>Especificar `microsoft/swupdate:1` para atualização de imagem</li><li>Especificar `microsoft/apt:1` para atualização de pacotes</li></ul>
    | instaladoCriteria | <ul><li>Especificar o valor da SWVersion para `microsoft/swupdate:1` o tipo de atualização</li><li>Especificar **a versão do nome,** onde o _nome_ é o nome do Manifesto APT e a _versão_ é a versão do Manifesto APT. Por exemplo, contoso-iot-edge-1.0.0.0.
    | actualizarFilePath(s) | Caminho para o(s) ficheiros de atualização no seu computador


## <a name="review-generated-import-manifest"></a>Manifesto de importação gerado por revisão

Exemplo:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Atualização de importação

> [!NOTE]
> As instruções abaixo mostram como importar uma atualização através do portal Azure UI. Também pode utilizar a Atualização do [Dispositivo para APIs IoT Hub](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) para importar uma atualização. 

1. Inicie sessão no [portal Azure](https://portal.azure.com) e navegue para o seu IoT Hub com aTualização do dispositivo.

2. No lado esquerdo da página, selecione "Atualizações do Dispositivo" em "Gestão Automática de Dispositivos".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Atualizações de Importação" lightbox="media/import-update/import-updates-3.png":::

3. Verá vários separadores na parte superior do ecrã. Selecione o separador Atualizações.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Updates" lightbox="media/import-update/updates-tab.png":::

4. Selecione "+ Import New Update" abaixo do cabeçalho "Pronto a Implementar".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importação Nova Atualização" lightbox="media/import-update/import-new-update-2.png":::

5. Selecione o ícone de pasta ou caixa de texto em "Selecione um Ficheiro Manifesto de Importação". Verá um diálogo de selecionador de ficheiros. Selecione o Manifesto de Importação criado anteriormente utilizando o cmdlet PowerShell. Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecione um ou mais ficheiros de atualização". Verá um diálogo de selecionador de ficheiros. Selecione o seu(s) ficheiro de atualização.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Selecione ficheiros de atualização" lightbox="media/import-update/select-update-files.png":::

6. Selecione o ícone de pasta ou caixa de texto em "Selecione um recipiente de armazenamento". Em seguida, selecione a conta de armazenamento apropriada. O recipiente de armazenamento é utilizado para encenar os ficheiros de atualização temporariamente.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Conta de Armazenamento" lightbox="media/import-update/storage-account.png":::

7. Se já criou um contentor, pode reutilizá-lo. (Caso contrário, selecione "+ Container" para criar um novo recipiente de armazenamento para atualizações.).  Selecione o recipiente que pretende utilizar e clique em "Selecione".

   :::image type="content" source="media/import-update/container.png" alt-text="Selecione Recipiente" lightbox="media/import-update/container.png":::

8. Selecione "Submeter" para iniciar o processo de importação.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publicar atualização" lightbox="media/import-update/publish-update.png":::

9. O processo de importação começa e o ecrã muda para a secção "História das Importações". Selecione "Refresh" para ver o progresso até que o processo de importação esteja concluído (dependendo do tamanho da atualização, este pode completar em poucos minutos, mas pode demorar mais tempo).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Atualizar sequenciação de importação" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando a coluna Status indicar que a importação foi bem sucedida, selecione o cabeçalho "Pronto a Implantar". Devia ver a sua atualização importada na lista agora.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Estatuto do Trabalho" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Passos Seguintes

[Criar Grupos](create-update-group.md)

[Conheça os conceitos de importação](import-concepts.md)