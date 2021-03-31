---
title: Gerir um pedido de suporte do Azure
description: Descreve como visualizar pedidos de suporte, enviar mensagens, alterar o nível de gravidade do pedido, partilhar informações de diagnóstico com suporte Azure, reabrir um pedido de suporte fechado e carregar ficheiros.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 4d0c03e0035f6b71a23891ac1691f5421c1bdb76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502523"
---
# <a name="manage-an-azure-support-request"></a>Gerir um pedido de suporte do Azure

Depois de [criar um pedido de apoio Azure,](how-to-create-azure-support-request.md)pode geri-lo no portal [Azure](https://portal.azure.com), que está coberto neste artigo. Também pode criar e gerir pedidos programáticamente, utilizando o [bilhete de apoio Azure REST API,](/rest/api/support)ou utilizando [o Azure CLI](/cli/azure/azure-cli-support-request).

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

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Ficheiro de upload":::

### <a name="file-upload-guidelines"></a>Diretrizes de upload de ficheiros

Siga estas diretrizes quando utilizar a opção de upload de ficheiros:

* Para proteger a sua privacidade, não inclua nenhuma informação pessoal no seu upload.
* O nome do ficheiro não deve ter mais de 110 caracteres.
* Não pode enviar mais do que um ficheiro.
* Os ficheiros não podem ser maiores que 4 MB.
* Todos os ficheiros devem ter uma extensão de nome de ficheiro, como *.docx* ou *.xlsx*. A tabela seguinte mostra as extensões de nome de ficheiro que são permitidas para upload.

| 0-9, A-C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .har        | .odx  | .rar     | .uccapilog | .xlam   |
| .a          | .db   | .hwl        | .oft  | .rdl     | .uccplog   | .xlr    |
| .abc        | . DMP  | .ics        | .velho  | .rdlc    | .udcx      | .xls    |
| .adm        | .do_  | .ini        | .um  | .re_     | .vb_       | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .remover  | .vbs_      | .xlsm   |
| . ATF        | .docm | .jpg        | . FORA  | .ren     | .vcf       | .xlsx   |
| .b          | .docx | . LDF        | .p1   | .rename  | .vsd       | .xlt    |
| .ba_        | .dotm | .papel timbrado | .pcap | .rft     | .wdb       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rpt     | .wks       | .xml    |
| .blg        | .dtsx | .log        | .pdf  | .rte     | .wma       | .xmla   |
| .CA_        | .eds  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| . TÁXI        | .emf  | .manifesto   | .pmls | .run     | .wmz       | .xsd    |
| .cap        | .eml  | .mestre     | .png  | .saz     | .wps       | .xsn    |
| .catx       | .emz  | .mdmp       | .potx | .sql     | .wpt       | .xxx    |
| . CFG        | .err  | .mof        | .ppt  | .sqlplan | .wsdl      | .z_     |
| .comprimido | .etl  | .mp3        | .pptm | .stp     | .wsp       | .z01    |
| . Config     | .evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | .prn  | .tdb     | -          | .zi     |
| .cpp        | . EX   | .msg        | .psf  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | .mso        | .pst  | .text    | -          | .zip    |
| . CSV        | .ex0  | .msu        | .pub  | .thmx    | -          | .zip_   |
| .cvr        | . FRD  | .nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | .trc     | -          | .zipped |
| -           | .guid | -           | -     | . TTD     | -          | .zippy  |
| -           | .gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="close-a-support-request"></a>Fechar um pedido de apoio

Se precisar de fechar um pedido de apoio, [envie uma mensagem](#send-a-message) a pedir que o pedido seja encerrado.

## <a name="reopen-a-closed-request"></a>Reabra um pedido fechado

Se precisar de reabrir um pedido de apoio fechado, crie uma [nova mensagem](#send-a-message), que reabre automaticamente o pedido.

## <a name="cancel-a-support-plan"></a>Cancelar um plano de suporte

Se precisar de cancelar um plano de apoio, consulte [cancelar um plano de apoio](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Passos seguintes

[Como criar um pedido de suporte do Azure](how-to-create-azure-support-request.md)

[API REST de pedidos de suporte do Azure](/rest/api/support)
