---
title: Melhorar o desempenho ao comprimir ficheiros na CDN do Azure | Documentos da Microsoft
description: Saiba como melhorar a velocidade de transferência de ficheiro e aumentar o desempenho de carregamento da página, comprimir os ficheiros na CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: afe959e80b339db5112fa97fd79d0528390e3954
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096457"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Melhorar o desempenho ao comprimir ficheiros na CDN do Azure para
Compressão de ficheiros é um método simples e eficaz para melhorar a velocidade de transferência de ficheiro e aumentar o desempenho de carregamento da página, reduzindo o tamanho de um ficheiro antes do envio do servidor. Compressão de ficheiros pode reduzir os custos de largura de banda e proporcionar uma experiência de maior capacidade de resposta para os seus utilizadores.

Existem duas formas de ativar a compressão de ficheiros:

- Ative a compressão em seu servidor de origem. Neste caso, a CDN do Azure passa junto os arquivos compactados e fornece aos clientes que solicitam-los.
- Ativar a compressão diretamente nos servidores POP da CDN (*compressão num piscar de olhos*). Neste caso, a CDN compacta os arquivos e serve-los para os usuários finais, mesmo que eles não terem sido compactados pelo servidor de origem.

> [!IMPORTANT]
> As alterações de configuração do Azure CDN podem demorar algum tempo para propagar através da rede: 
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 
> 
> Se estiver a configurar a compressão da primeira vez para o ponto final da CDN, considere a aguardar 1-2 horas antes de resolver problemas para garantir que as definições de compressão terem sido propagados para os POPs.

## <a name="enabling-compression"></a>Ativar a compressão
Os escalões CDN standard e premium fornecem a mesma funcionalidade de compactação, mas a interface do usuário é diferente. Para obter mais informações sobre as diferenças entre as camadas CDN standard e premium, consulte [descrição geral da CDN do Azure](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Perfis de CDN Standard 
> [!NOTE]
> Esta secção aplica-se ao **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Verizon**, e **CDN do Azure Standard da Akamai** perfis.
> 
> 

1. Na página de perfil de CDN, selecione o ponto final da CDN que pretende gerir.

    ![Pontos finais do perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)

    É aberta a página de ponto final CDN.
2. Selecione **compressão**.

    ![Seleção de compressão de CDN](./media/cdn-file-compression/cdn-compress-select-std.png)

    É aberta a página de compressão.
3. Selecione **no** para ativar a compressão.

    ![Opções de compactação de arquivo CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Utilize os tipos de MIME predefinido ou modificar a lista ao adicionar ou remover tipos de MIME.

   > [!TIP]
   > Embora seja possível, não é recomendado para aplicar a compressão para formatos de compressão. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

   > [!NOTE]
   > Modificar a lista predefinida de tipos de MIME não é suportado atualmente na CDN Standard do Microsoft Azure.
   > 

5. Depois de fazer as alterações, selecione **guardar**.

### <a name="premium-cdn-profiles"></a>Perfis de CDN Premium
> [!NOTE]
> Esta secção aplica-se apenas ao **CDN do Azure Premium da Verizon** perfis.
> 

1. Na página de perfil de CDN, selecione **gerir**.

    ![Selecione gerir CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    É aberto o portal de gestão da CDN.
2. Paire o rato sobre o **HTTP grandes** separador, em seguida, coloque o cursor sobre o **definições de Cache** submenu. Selecione **compressão**.

    ![Seleção de compressão de CDN](./media/cdn-file-compression/cdn-compress-select.png)

    São apresentadas as opções de compressão.

    ![Opções de compactação de arquivo CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Ativar a compressão, selecionando **compressão ativada**. Introduza os tipos de MIME que pretende comprimir como uma lista delimitada por vírgulas (sem espaços) na **tipos de ficheiro** caixa.

   > [!TIP]
   > Embora seja possível, não é recomendado para aplicar a compressão para formatos de compressão. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

4. Depois de fazer as alterações, selecione **atualização**.

## <a name="compression-rules"></a>Regras de compressão

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>CDN Standard do Azure de perfis da Microsoft

Para **CDN Standard do Microsoft Azure** perfis, apenas os ficheiros elegíveis são compactados. Para ser elegível para compressão, um ficheiro tem de:
- Ter um tipo MIME que tem sido [configurado para compressão](#enabling-compression).
- Apresentar mais do que 1 KB
- Ser menor do que 8 MB

Estes perfis suportam as seguintes codificações de compressão:
- gzip (GNU zip)
- brotli 

Se o pedido de suportar mais do que um tipo de compactação, compressão de brotli tem precedência.

Quando um pedido para um recurso Especifica compactação gzip e os resultados do pedido numa falha de acerto na cache, CDN do Azure realiza a compactação gzip do elemento de diretamente no servidor POP. Depois disso, o ficheiro comprimido é servido a partir da cache.

### <a name="azure-cdn-from-verizon-profiles"></a>CDN do Azure da Verizon perfis

Para **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon** perfis, apenas os ficheiros elegíveis são compactados. Para ser elegível para compressão, um ficheiro tem de:
- Ser maior do que 128 bytes
- Ser menor que 3 MB

Estes perfis suportam as seguintes codificações de compressão:
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

Se o pedido de suportar mais do que um tipo de compactação, esses tipos de compressão precedência sobre brotli compressão.

Quando um pedido para um recurso Especifica brotli compressão (cabeçalho HTTP é `Accept-Encoding: br`) e os resultados do pedido num erro de cache da CDN do Azure efetua brotli compressão do elemento de diretamente no servidor POP. Depois disso, o ficheiro comprimido é servido a partir da cache.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>CDN Standard do Azure da Akamai perfis

Para **CDN do Azure Standard da Akamai** perfis, todos os ficheiros são elegíveis para compressão. No entanto, tem de ser um ficheiro de um tipo MIME que tem sido [configurado para compressão](#enabling-compression).

Estes perfis suportam gzip compressão codificação apenas. Quando um ponto final do perfil solicita um ficheiro com codificação gzip, sempre que for solicitada na origem, independentemente do pedido do cliente. 

## <a name="compression-behavior-tables"></a>Tabelas de comportamento de compressão
As tabelas seguintes descrevem o comportamento de compressão da CDN do Azure para cada cenário:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Compressão está desativada ou não é elegível para compressão de ficheiros
| Formato de pedido de cliente (por meio do cabeçalho Accept-Encoding) | Formato de ficheiro armazenados em cache | A resposta CDN ao cliente | Notas&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos | |
| Comprimidos |Não comprimida |Não comprimida | |
| Comprimidos |Não colocar em cache |Comprimidos ou descomprimidos |A resposta de origem determina se a CDN faz uma compressão. |
| Não comprimida |Comprimidos |Não comprimida | |
| Não comprimida |Não comprimida |Não comprimida | |
| Não comprimida |Não colocar em cache |Não comprimida | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>A compressão está ativada e o ficheiro é elegível para compressão
| Formato de pedido de cliente (por meio do cabeçalho Accept-Encoding) | Formato de ficheiro armazenados em cache | Resposta CDN ao cliente | Notas |
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos |CDN transcodifica entre os formatos suportados. |
| Comprimidos |Não comprimida |Comprimidos |CDN faz uma compressão. |
| Comprimidos |Não colocar em cache |Comprimidos |CDN faz uma compressão se a origem devolve um arquivo descompactado. <br/>**A CDN do Azure da Verizon** transmite o arquivo descompactado na primeira solicitação e, em seguida, compacta e armazena em cache o ficheiro para pedidos subsequentes. <br/>Ficheiros com o `Cache-Control: no-cache` cabeçalho nunca são compactados. |
| Não comprimida |Comprimidos |Não comprimida |CDN faz um descompressão. |
| Não comprimida |Não comprimida |Não comprimida | |
| Não comprimida |Não colocar em cache |Não comprimida | |

## <a name="media-services-cdn-compression"></a>Compressão de CDN dos serviços de multimédia
Para pontos finais ativados para CDN de serviços de multimédia de transmissão em fluxo, a compressão está ativada por predefinição para os seguintes tipos MIME: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- aplicação/f4m + xml 

## <a name="see-also"></a>Consulte também
* [Troubleshooting CDN file compression](cdn-troubleshoot-compression.md) (Resolver problemas de compressão de ficheiros da CDN)    

