---
title: Gerir um pedido de suporte do Azure
description: Descreve como visualizar pedidos de suporte, enviar mensagens, alterar o nível de gravidade do pedido, partilhar informações de diagnóstico com suporte Azure, reabrir um pedido de suporte fechado e carregar ficheiros.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: a9dd703dc0a3f5e8f85b1022fa2a71ff9a8c295d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745575"
---
# <a name="manage-an-azure-support-request"></a>Gerir um pedido de suporte do Azure

Depois de [criar um pedido de apoio Azure,](how-to-create-azure-support-request.md)pode geri-lo no portal [Azure](https://portal.azure.com), que está coberto neste artigo. Também pode criar e gerir pedidos programáticamente, utilizando o [bilhete de apoio Azure REST API.](/rest/api/support)

## <a name="view-support-requests"></a>Ver pedidos de suporte

Ver os detalhes e o estado dos pedidos de apoio indo para **a Ajuda + apoiar** todos os  >   **pedidos de apoio.**

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Todos os pedidos de apoio":::

Nesta página, pode pesquisar, filtrar e classificar pedidos de suporte. Selecione um pedido de apoio para visualizar detalhes, incluindo a sua gravidade e quaisquer mensagens associadas ao pedido.

## <a name="send-a-message"></a>Enviar uma mensagem

1. Na página **'Todos os pedidos de apoio',** selecione o pedido de apoio.

1. Na página **Pedido de Apoio,** selecione **Nova mensagem**.

1. Introduza a sua mensagem e **selecione Enviar por isso.**

## <a name="change-the-severity-level"></a>Alterar o nível de gravidade

> [!NOTE]
> O nível máximo de gravidade depende do seu [plano de suporte](https://azure.microsoft.com/support/plans).
>

1. Na página **'Todos os pedidos de apoio',** selecione o pedido de apoio.

1. Na página **Pedido de Apoio,** selecione **Alterar**.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Alterar pedido de apoio gravidade":::

1. O portal Azure mostra um de dois ecrãs, dependendo se o seu pedido já está atribuído a um engenheiro de suporte:

    - Se o seu pedido não foi atribuído, vê um ecrã como o seguinte. Selecione um novo nível de gravidade e, em seguida, selecione **Alterar**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Selecione um novo nível de gravidade":::

    - Se o seu pedido tiver sido atribuído, verá um ecrã como o seguinte. Selecione **OK,** em seguida, crie uma [nova mensagem](#send-a-message) para solicitar uma alteração no nível de gravidade.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Não pode selecionar um novo nível de gravidade":::

## <a name="share-diagnostic-information-with-azure-support"></a>Partilhe informações de diagnóstico com suporte da Azure

Quando cria um pedido de suporte, por predefinição é selecionada a opção **de informação de diagnóstico Partilhar.** Isto permite ao suporte da Azure recolher [informações](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) de diagnóstico dos seus recursos Azure:

* Não pode limpar esta opção depois de um pedido ser criado.

* Se tiver esclarecido a opção ao criar um pedido, pode selecioná-lo após a criação do pedido.

    1. Na página **'Todos os pedidos de apoio',** selecione o pedido de apoio.
    
    1. Na página **Pedido de Apoio,** selecione **a permissão grant**, em seguida, selecione **Sim** e **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Conceder permissões para informações de diagnóstico":::

## <a name="upload-files"></a>Carregar ficheiros

Pode utilizar a opção de upload de ficheiros para fazer upload de ficheiros de diagnóstico ou quaisquer outros ficheiros que considere relevantes para um pedido de suporte.

1. Na página **'Todos os pedidos de apoio',** selecione o pedido de apoio.

1. Na página **Pedido de Apoio,** navegue para encontrar o seu ficheiro e, em seguida, selecione **Upload**. Repita o processo se tiver vários ficheiros.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Carregar o ficheiro":::

### <a name="file-upload-guidelines"></a>Diretrizes de upload de ficheiros

Siga estas diretrizes quando utilizar a opção de upload de ficheiros:

* Para proteger a sua privacidade, não inclua nenhuma informação pessoal no seu upload.
* O nome do ficheiro não deve ter mais de 110 caracteres.
* Não pode enviar mais do que um ficheiro.
* Os ficheiros não podem ser maiores que 4 MB.
* Todos os ficheiros devem ter uma extensão de nome de ficheiro, como *.docx* ou *.xlsx*. A tabela seguinte mostra as extensões de nome de ficheiro que são permitidas para upload.

| 0-9, A-C    | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .hwl        | .odx  | .rar     | .tdb       | .xlam   |
| .a          | .db   | .ics        | .oft  | .rdl     | .tdf       | .xlr    |
| .abc        | .DMP  | .ini        | .velho  | .rdlc    | .text      | .xls    |
| .adm        | .do_  | .java       | .um  | .re_     | .thmx      | .xlsb   |
| .aspx       | .doc  | .jpg        | .osd  | .reg     | .tif       | .xlsm   |
| .ATF        | .docm | .LDF        | .OUT  | .remover  | .trc       | .xlsx   |
| .b          | .docx | .papel timbrado | .p1   | .ren     | .TTD       | .xlt    |
| .ba_        | .dotm | .lnk        | .pcap | .rename  | .tx_       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rft     | .txt       | .xml    |
| .bat        | .dtsx | .log        | .pdf  | .rpt     | .uccapilog | .xmla   |
| .blg        | .eds  | .lpk        | .piz  | .rte     | .uccplog   | .xps    |
| .CA_        | .emf  | .manifesto   | .pmls | .rtf     | .udcx      | .xsd    |
| .CAB        | .eml  | .mestre     | .png  | .run     | .vb_       | .xsn    |
| .cap        | .emz  | .mdmp       | .potx | .saz     | .vbs_      | .xxx    |
| .catx       | .err  | .mof        | .ppt  | .sql     | .vcf       | .z_     |
| .CFG        | .etl  | .mp3        | .pptm | .sqlplan | .vsd       | .z01    |
| .comprimido | .evt  | .mpg        | .pptx | .stp     | .wdb       | .z02    |
| .Config     | .evtx | .ms_        | .prn  | .svclog  | .wks       | .zi     |
| .cpk        | .EX   | .msg        | .psf  |   -       | .wma       | .zi_    |
| .cpp        | .ex_  | .msi        | .pst  |  -        | .wmv       | .zip    |
| .cs         | .ex0  | .mso        | .pub  | -         | .wmz       | .zip_   |
| .CSV        | .FRD  | .msu        | -      |-          | .wps       | .zipp   |
| .cvr        | .gif  | .nfo        | -      |-          | .wpt       | .zipped |
| -            | .guid | -            | -      | -         | .wsdl      | .zippy  |
| -            | .gz   | -            | -      | -         | .wsp       | .zipx   |
| -            | -      | -            | -      | -         | .wtl       | .zit    |
| -            | -      | -            | -      | -         |     -       | .zix    |
| -            | -      | -            | -      | -         |  -          | .zzz    |

## <a name="reopen-a-closed-request"></a>Reabra um pedido fechado

Se precisar de reabrir um pedido de apoio fechado, crie uma [nova mensagem](#send-a-message), que reabre automaticamente o pedido.

## <a name="next-steps"></a>Passos seguintes

[Como criar um pedido de suporte do Azure](how-to-create-azure-support-request.md)

[API REST de pedidos de suporte do Azure](/rest/api/support)
