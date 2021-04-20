---
title: Como adicionar uma nova atualização | Microsoft Docs
description: How-To guia para adicionar uma nova atualização na Atualização do Dispositivo para IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ecbc76651f09a9b4f2bde01c733cace5037f5fd4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738833"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Adicione uma atualização à atualização do dispositivo para ioT hub
Saiba como adicionar uma nova atualização no Device Update para IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um Hub IoT com atualização do dispositivo para o IoT Hub ativado](create-device-update-account.md). 
* Um dispositivo IoT (ou simulador) previsto para a Atualização do Dispositivo dentro do Hub IoT.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) ou posteriormente (inclui instalações linux, macOS e Windows)
* Navegadores suportados:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Alguns dados submetidos a este serviço podem ser processados numa região fora da região em que este caso foi criado.

## <a name="obtain-an-update-for-your-devices"></a>Obtenha uma atualização para os seus dispositivos

Agora que a Atualização do Dispositivo está [configurada,](create-device-update-account.md)está pronto para atualizar os seus dispositivos. Em seguida, vai precisar dos ficheiros de atualização reais que irá implementar nesses dispositivos.

Se adquiriu dispositivos a um OEM ou um integrador de soluções, essa organização provavelmente fornecerá ficheiros de atualização para si, sem que necessite de criar as atualizações. Contacte o OEM ou o integrador de soluções para saber como disponibilizam as atualizações.

Se a sua organização já criar software para os dispositivos que utiliza, esse mesmo grupo será o único a criar as atualizações para esse software. Ao criar uma atualização a ser implementada usando a Atualização do Dispositivo para ioT Hub, comece com a [abordagem baseada em imagem ou em pacotes,](understand-device-update.md#support-for-a-wide-range-of-update-artifacts) dependendo do seu cenário. Nota: se quiser criar as suas próprias atualizações mas está apenas a começar, o GitHub é uma excelente opção para gerir o seu desenvolvimento. Pode armazenar e gerir o seu código fonte e fazer Integração Contínua (CI) e Implementação Contínua (CD) utilizando [ações do GitHub](https://docs.github.com/en/actions/guides/about-continuous-integration).

## <a name="create-a-device-update-import-manifest"></a>Criar um manifesto de importação de atualização de dispositivos

Se ainda não o fez, não se esqueça de se familiarizar com os [conceitos básicos de importação.](import-concepts.md)

1. Certifique-se de que os ficheiros de atualização estão localizados num diretório acessível a partir do PowerShell.

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


## <a name="review-the-generated-import-manifest"></a>Rever o manifesto de importação gerado

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

## <a name="import-an-update"></a>Importar uma atualização

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