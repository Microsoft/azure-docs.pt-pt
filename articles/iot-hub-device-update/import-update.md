---
title: Como importar uma nova atualização | Microsoft Docs
description: How-To guia para importar uma nova atualização para ioT Hub Device Update para IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 7f2353d9e87c35f01a9fd514df7cfb090a98bf27
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442949"
---
# <a name="import-new-update"></a>Importação Nova Atualização
Saiba como importar uma nova atualização para a Atualização de Dispositivos para IoT Hub. Se ainda não o fez, não se esqueça de se familiarizar com os [conceitos básicos de importação.](import-concepts.md)

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um Hub IoT com atualização do dispositivo para o IoT Hub ativado](create-device-update-account.md). Recomenda-se que utilize um nível S1 (Standard) ou superior para o seu Hub IoT. 
* Um dispositivo IoT (ou simulador) previsto para a Atualização do Dispositivo dentro do Hub IoT.
   * Se utilizar um dispositivo real, necessitará de um ficheiro de imagem de atualização para atualização de imagem ou [ficheiro APT Manifest](device-update-apt-manifest.md) para atualização de pacotes.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) ou mais tarde.
* Navegadores suportados:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Alguns dados submetidos a este serviço podem ser processados numa região fora da região em que este caso foi criado.

## <a name="create-device-update-import-manifest"></a>Criar manifesto de importação de atualização de dispositivos

1. Certifique-se de que o seu ficheiro de imagem de atualização ou ficheiro APT Manifesto está localizado num diretório acessível a partir de PowerShell.

2. Atualização do dispositivo de clone [para repositório IoT Hub](https://github.com/azure/iot-hub-device-update), ou descarregue-o como um ficheiro .zip para um local acessível a partir do PowerShell (assim que o ficheiro zip é descarregado, clique no botão direito para `Properties`  >  `General` o separador > verificar `Unblock` na secção para evitar `Security` avisos de segurança PowerShell).

3. Em PowerShell, navegue para o `tools/AduCmdlets` diretório e corra:

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
    | dispositivo Manfacturer | Fabricante do dispositivo a atualização é compatível com, por exemplo, Contoso
    | modelo de dispositivo | Modelo do dispositivo a atualização é compatível com, por exemplo, torradeira
    | atualizarProvider | Entidade que está a criar ou diretamente responsável pela atualização. Muitas vezes será um nome de empresa.
    | atualizarName | Identificador para uma classe de atualizações. A aula pode ser o que quiser. Muitas vezes será um dispositivo ou nome de modelo.
    | actualizaçãoVersão | Número de versão que distingue esta atualização de outras que têm o mesmo Fornecedor e Nome. Pode ou não coincidir com uma versão de um componente de software individual no dispositivo.
    | atualizaçãoType | <ul><li>Especificar `microsoft/swupdate:1` para atualização de imagem</li><li>Especificar `microsoft/apt:1` para atualização de pacotes</li></ul>
    | instaladoCriteria | <ul><li>Especificar o valor da SWVersion para `microsoft/swupdate:1` o tipo de atualização</li><li>Especifique o valor recomendado para `microsoft/apt:1` o tipo de atualização.
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
