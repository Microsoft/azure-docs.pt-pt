---
title: Criar uma tarefa de importação para importar/exportar do Azure | Documentos da Microsoft
description: Saiba como criar uma importação para o serviço de importação/exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fa76f4fb5d4da5fd00bb9fa4ed862c6977a47e90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102184"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Criar uma tarefa de importação para o serviço importar/exportar do Azure

A criação de uma tarefa de importação para o serviço de importação/exportação do Microsoft Azure com a API REST envolve os seguintes passos:

- Preparar as unidades com a ferramenta de importação/exportação do Azure.

- Obter a localização para enviar a unidade.

- A criar a tarefa de importação.

- As unidades para a Microsoft através de um serviço de deteção de carrier suportadas de envio.

- A atualizar a tarefa de importação com os detalhes de envio.

  Ver [com o serviço de importação/exportação do Microsoft Azure para transferir dados para armazenamento de BLOBs](storage-import-export-service.md) para obter uma descrição geral do serviço importar/exportar e um tutorial que demonstra como utilizar o [portal do Azure](https://portal.azure.com/) para criar e gerir a importar e exportar tarefas.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Preparar as unidades com a ferramenta de importação/exportação do Azure

Os passos para preparar unidades para uma tarefa de importação são os mesmos, se criar a tarefa através do portal ou através da API REST.

Segue-se uma breve descrição geral da preparação da unidade. Consulte a [referência de ExportTool de importação de Azure](storage-import-export-tool-how-to-v1.md) para obter instruções completas. Pode baixar a ferramenta de importação/exportação do Azure [aqui](https://go.microsoft.com/fwlink/?LinkID=301900).

A preparação de sua unidade envolve:

- Identificar os dados a serem importados.

- Identificando os blobs de destino no armazenamento do Windows Azure.

- Usando a ferramenta de importação/exportação do Azure para copiar os dados para um ou mais unidades de disco rígido.

  A ferramenta de importação/exportação do Azure também irão gerar um arquivo de manifesto para as unidades conforme é preparado. Um arquivo de manifesto contém:

- Uma enumeração de todos os arquivos que se destina a carregar e os mapeamentos desses arquivos para blobs.

- Somas de verificação de segmentos de cada arquivo.

- Informações sobre os metadados e propriedades para associar cada blob.

- Uma listagem da ação a tomar se um blob que está a ser carregado tem o mesmo nome que um blob existente no contentor. Opções possíveis são: a) substituir o blob com o arquivo, b) manter o blob existente e ignorar carregamento do ficheiro, c) Acrescentar um sufixo ao nome, de modo que ele não entram em conflito com outros ficheiros.

## <a name="obtaining-your-shipping-location"></a>Obter a localização de envio

Antes de criar uma tarefa de importação, tem de obter um envio de localização de nome e endereço chamando o [lista de localizações](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) operação. `List Locations` irá devolver uma lista de localizações e os respetivos endereços de correio postal. Pode selecionar uma localização a lista devolvida e envie os discos rígidos a esse endereço. Também pode utilizar o `Get Location` operação para obter o endereço de envio para uma localização específica diretamente.

 Siga os passos abaixo para obter a localização de envio:

-   Identificar o nome da localização da conta de armazenamento. Este valor pode ser encontrado no **localização** campo na conta de armazenamento **Dashboard** no Azure portal ou consultado para usando a operação de API de gestão de serviço [obter conta de armazenamento Propriedades](/rest/api/storagerp/storageaccounts).

-   Obter a localização que esteja disponível para processar esta conta de armazenamento ao chamar o `Get Location` operação.

-   Se o `AlternateLocations` propriedade da localização contém a localização em si, em seguida, há problema em utilizar esta localização. Caso contrário, chamar o `Get Location` operação com uma das localizações alternativas. A localização original pode ser fechada temporariamente para manutenção.

## <a name="creating-the-import-job"></a>Criar a tarefa de importação
Para criar a tarefa de importação, chamar o [colocar tarefa](/rest/api/storageimportexport/jobs) operação. Terá de fornecer as seguintes informações:

-   Um nome para a tarefa.

-   O nome da conta de armazenamento.

-   O nome da localização envio, obtido a partir do passo anterior.

-   Um tipo de tarefa (importação).

-   O endereço do remetente em que as unidades devem ser enviadas depois da tarefa de importação for concluída.

-   A lista de unidades na tarefa. Para cada unidade, tem de incluir as seguintes informações que foi obtidas durante o passo de preparação de unidade:

    -   A Id de unidade

    -   A chave do BitLocker

    -   O caminho relativo do arquivo de manifesto no disco rígido

    -   O Base16 codificado hash de ficheiro de manifesto MD5

## <a name="shipping-your-drives"></a>Suas unidades de envio
Tem envie as suas unidades para o endereço que obteve no passo anterior e tem de fornecer o serviço importar/exportar com o número de controlo do pacote.

> [!NOTE]
>  Têm de ser enviados suas unidades através de um serviço de deteção de carrier suportados, o que irá fornecer um número de controlo para o seu pacote.

## <a name="updating-the-import-job-with-your-shipping-information"></a>A atualizar a tarefa de importação com as suas informações de envio
Depois de ter o seu número de controlo, chamar o [atualizar propriedades da tarefa](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) operação para atualizar o nome da operadora de envio, o número de controlo para a tarefa e o número de conta de operadora para envio de devolução. Opcionalmente, pode especificar o número de unidades e também a data de remessa.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar a API de REST do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
