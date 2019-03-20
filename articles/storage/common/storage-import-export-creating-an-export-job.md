---
title: Criar uma tarefa de importação/exportação de Azure de exportação | Documentos da Microsoft
description: Saiba como criar uma tarefa de exportação para o serviço de importação/exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: e0513bc18f1cf14beb4c1becfc1835235a5ddc96
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092230"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Criação de uma tarefa de exportação para o serviço importar/exportar do Azure
A criação de uma tarefa de exportação para o serviço de importação/exportação do Microsoft Azure com a API REST envolve os seguintes passos:

- Selecionar os blobs para exportar.

- Obtenção de uma localização de envio.

- A criar a tarefa de exportação.

- Envio suas unidades vazias para a Microsoft através de um serviço de deteção de carrier suportados.

- A atualizar a tarefa de exportação com as informações do pacote.

- Receber as unidades de volta da Microsoft.

  Ver [com o serviço importar/exportar do Windows do Azure para transferir dados para armazenamento de BLOBs](storage-import-export-service.md) para obter uma descrição geral do serviço importar/exportar e um tutorial que demonstra como utilizar o [portal do Azure](https://portal.azure.com/) para criar e Gerir a importar e exportar tarefas.

## <a name="selecting-blobs-to-export"></a>Selecionar blobs a exportar
 Para criar uma tarefa de exportação, terá de fornecer uma lista de blobs que pretende exportar a partir da sua conta de armazenamento. Existem algumas formas de selecionar os blobs para ser exportado:

- Pode utilizar um caminho relativo do blob para selecionar um único blob e todos os respetivos instantâneos.

- Pode utilizar um caminho relativo do blob para selecionar um único blob excluindo os respetivos instantâneos.

- Pode utilizar um caminho relativo do blob e de uma hora do instantâneo para selecionar um único instantâneo.

- Pode utilizar um prefixo de BLOBs para selecionar todos os blobs e instantâneos com o prefixo especificado.

- Pode exportar todos os blobs e instantâneos na conta de armazenamento.

  Para obter mais informações sobre como especificar blobs a exportar, consulte a [colocar tarefa](/rest/api/storageimportexport/jobs) operação.

## <a name="obtaining-your-shipping-location"></a>Obter a localização de envio
Antes de criar uma tarefa de exportação, tem de obter um envio de localização de nome e endereço chamando o [obter localização](https://portal.azure.com) ou [localizações de lista](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) operação. `List Locations` irá devolver uma lista de localizações e os respetivos endereços de correio postal. Pode selecionar uma localização a lista devolvida e envie os discos rígidos a esse endereço. Também pode utilizar o `Get Location` operação para obter o endereço de envio para uma localização específica diretamente.

Siga os passos abaixo para obter a localização de envio:

-   Identificar o nome da localização da conta de armazenamento. Este valor pode ser encontrado no **localização** campo na conta de armazenamento **Dashboard** no Azure portal ou consultado para usando a operação de API de gestão de serviço [obter conta de armazenamento Propriedades](/rest/api/storagerp/storageaccounts).

-   Obter a localização que estão disponíveis para processar esta conta de armazenamento ao chamar o `Get Location` operação.

-   Se o `AlternateLocations` propriedade da localização contém a localização em si, em seguida, há problema em utilizar esta localização. Caso contrário, chamar o `Get Location` operação com uma das localizações alternativas. A localização original pode ser fechada temporariamente para manutenção.

## <a name="creating-the-export-job"></a>Criar a tarefa de exportação
 Para criar a tarefa de exportação, chamar o [colocar tarefa](/rest/api/storageimportexport/jobs) operação. Terá de fornecer as seguintes informações:

-   Um nome para a tarefa.

-   O nome da conta de armazenamento.

-   O nome da localização envio, obtido no passo anterior.

-   Um tipo de tarefa (exportação).

-   O endereço do remetente em que as unidades devem ser enviadas depois da tarefa de exportação foi concluída.

-   A lista de blobs (ou os prefixos blob) para ser exportada.

## <a name="shipping-your-drives"></a>Suas unidades de envio
 Em seguida, utilize a ferramenta de importação/exportação do Azure para determinar o número de unidades que tiver de enviar, com base em blobs que selecionou seja exportada e o tamanho da unidade. Consulte a [referência da ferramenta importar/exportar do Azure](storage-import-export-tool-how-to-v1.md) para obter detalhes.

 As unidades num único pacote do pacote e enviá-los para o endereço de obteve no passo anterior. Tenha em atenção o número de controlo do seu pacote para a próxima etapa.

> [!NOTE]
>  Têm de ser enviados suas unidades através de um serviço de deteção de carrier suportados, o que irá fornecer um número de controlo para o seu pacote.

## <a name="updating-the-export-job-with-your-package-information"></a>A atualizar a tarefa de exportação com as suas informações de pacote
 Depois de ter o seu número de controlo, chamar o [propriedades da tarefa de atualização](/rest/api/storageimportexport/jobs) operação para atualizar o nome da operadora e o número para a tarefa de controle. Opcionalmente, pode especificar o número de unidades, o endereço do remetente e também a data de remessa.

## <a name="receiving-the-package"></a>O pacote a receber
 Após o processamento de sua tarefa de exportação, suas unidades serão retornadas para com os seus dados criptografados. Pode obter a chave do BitLocker para as unidades chamando o [Get Job de](/rest/api/storageimportexport/jobs) operação. Em seguida, pode desbloquear a unidade a utilizar a chave. O ficheiro de manifesto de unidade em cada unidade contém a lista de ficheiros na unidade, bem como o endereço original do blob para cada ficheiro.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar a API de REST do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
