---
title: Melhorar o desempenho comprimindo ficheiros no Azure CDN Microsoft Docs
description: Saiba como melhorar a velocidade de transferência de ficheiros e aumentar o desempenho da carga de página comprimindo os seus ficheiros em Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: ceed62d466627d6a23554229bd6f4b96c674c7e9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148752"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Improve performance by compressing files in Azure CDN (Comprimir ficheiros na CDN do Azure para melhorar o desempenho)
A compressão de ficheiros é um método simples e eficaz para melhorar a velocidade de transferência de ficheiros e aumentar o desempenho da carga de página, reduzindo o tamanho de um ficheiro antes de ser enviado do servidor. A compressão de ficheiros pode reduzir os custos de largura de banda e proporcionar uma experiência mais responsiva para os seus utilizadores.

Existem duas formas de permitir a compressão do ficheiro:

- Ativar a compressão no servidor de origem. Neste caso, a Azure CDN passa ao longo dos ficheiros comprimidos e entrega-os aos clientes que os solicitam.
- Ativar a compressão diretamente nos servidores CDN POP *(compressão na mosca).* Neste caso, o CDN comprime os ficheiros e serve-os aos utilizadores finais, mesmo que não tenham sido comprimidos pelo servidor de origem.

> [!IMPORTANT]
> As alterações de configuração do Azure CDN podem demorar algum tempo a propagar-se através da rede: 
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 
> 
> Se estiver a configurar a compressão pela primeira vez para o seu ponto final CDN, considere esperar 1-2 horas antes de resolver problemas para garantir que as definições de compressão se propagaram para os POPs.

## <a name="enabling-compression"></a>Compressão ativadora
Os níveis padrão e premium de CDN fornecem a mesma funcionalidade de compressão, mas a interface do utilizador difere. Para obter mais informações sobre as diferenças entre os níveis padrão e premium cdN, consulte [a Visão Geral do CDN do Azure](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Perfis padrão de CDN 
> [!NOTE]
> Esta secção aplica-se ao **Azure CDN Standard da Microsoft**, **Azure CDN Standard da Verizon**, e **Azure CDN Standard a partir de** perfis Akamai.
> 
> 

1. Na página de perfil do CDN, selecione o ponto final do CDN que pretende gerir.

    ![Pontos finais de perfil cdn](./media/cdn-file-compression/cdn-endpoints.png)

    A página de ponta cdn abre.
2. **Selecione Compressão**.

    ![A screenshot mostra um Ponto final com compressão selecionada no menu do portal.](./media/cdn-file-compression/cdn-compress-select-std.png)

    A página de compressão abre.
3. Selecione **ligar** para ligar a compressão.

    ![A imagem mostra a compressão.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Utilize os tipos de MIME predefinidos ou modifique a lista adicionando ou removendo os tipos de MIME.

   > [!TIP]
   > Embora seja possível, não é aconselhável aplicar compressão em formatos comprimidos. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

   > [!NOTE]
   > A modificação da lista padrão dos tipos de MIME não é atualmente suportada no Azure CDN Standard da Microsoft.
   > 

5. Depois de escoar as alterações, **selecione Guardar**.

### <a name="premium-cdn-profiles"></a>Perfis premium do CDN
> [!NOTE]
> Esta secção aplica-se apenas ao **Azure CDN Premium dos** perfis Verizon.
> 

1. Na página de perfil do CDN, **selecione Gerir**.

    ![CDN Gerir selecionado](./media/cdn-file-compression/cdn-manage-btn.png)

    O portal de gestão cdn abre.
2. Passe por cima do separador **HTTP Large** e, em seguida, paire sobre o voo **das Definições cache.** **Selecione Compressão**.

    ![Seleção de compressão CDN](./media/cdn-file-compression/cdn-compress-select.png)

    As opções de compressão são apresentadas.

    ![Opções de compressão de ficheiros CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Ativar a compressão selecionando **a compressão Ativada**. Introduza os tipos MIME que pretende comprimir como uma lista delimitada em vírgula (sem espaços) na caixa **'Tipos** de Ficheiros'.

   > [!TIP]
   > Embora seja possível, não é aconselhável aplicar compressão em formatos comprimidos. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

4. Depois de escoar as alterações, selecione **Update**.

## <a name="compression-rules"></a>Regras de compressão

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard dos perfis da Microsoft

Para **o Azure CDN Standard a partir dos** perfis da Microsoft, apenas os ficheiros elegíveis são comprimidos. Para ser elegível para compressão, um ficheiro deve:
- Seja do tipo MIME que tenha sido [configurado para compressão](#enabling-compression).
- Seja maior que 1 KB
- Seja menor que 8 MB

Estes perfis suportam as seguintes codificações de compressão:
- gzip (zip GNU)
- brotli 

Se o pedido suportar mais de um tipo de compressão, a compressão brotli tem precedência.

Quando um pedido de um ativo especifica a compressão gzip e o pedido resulta numa falha de cache, a Azure CDN executa a compressão gzip do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache.

Se a origem utilizar a Codificação de Transferências Chunked (CTE) para enviar dados comprimidos para o CDN POP, então os tamanhos de resposta superiores a 8MB não são suportados. 

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN dos perfis verizon

Para **o Azure CDN Standard da Verizon** e **Azure CDN Premium dos** perfis Verizon, apenas os ficheiros elegíveis são comprimidos. Para ser elegível para compressão, um ficheiro deve:
- Seja maior que 128 bytes
- Seja menor que 3 MB

Estes perfis suportam as seguintes codificações de compressão:
- gzip (zip GNU)
- ESVAZIAR
- bzip2
- brotli 

Se o pedido suportar mais de um tipo de compressão, esses tipos de compressão têm precedência sobre a compressão brotli.

Quando um pedido de um ativo especifica a compressão brotli (cabeçalho HTTP é `Accept-Encoding: br` ) e o pedido resulta numa falha de cache, a Azure CDN executa a compressão brotli do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard dos perfis da Akamai

Para **o Azure CDN Standard dos** perfis da Akamai, todos os ficheiros são elegíveis para compressão. No entanto, um ficheiro deve ser de um tipo MIME que tenha sido [configurado para compressão](#enabling-compression).

Estes perfis suportam apenas a codificação da compressão gzip. Quando um ponto final de perfil solicita um ficheiro codificado por gzip, é sempre solicitado a partir da origem, independentemente do pedido do cliente. 

## <a name="compression-behavior-tables"></a>Tabelas de comportamento de compressão
As seguintes tabelas descrevem o comportamento de compressão Azure CDN para cada cenário:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>A compressão é desativada ou o ficheiro não é elegível para a compressão
| Formato solicitado pelo cliente (via Accept-Encoding cabeçalho) | Formato de ficheiro em cache | A resposta da CDN ao cliente | &nbsp; &nbsp; &nbsp; &nbsp; Notas &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos | |
| Comprimidos |Descomprimido |Descomprimido | |
| Comprimidos |Não em cache |Comprimido ou Descomprimido |A resposta de origem determina se a CDN executa uma compressão. |
| Descomprimido |Comprimidos |Descomprimido | |
| Descomprimido |Descomprimido |Descomprimido | |
| Descomprimido |Não em cache |Descomprimido | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>A compressão está ativada e o ficheiro é elegível para compressão
| Formato solicitado pelo cliente (via Accept-Encoding cabeçalho) | Formato de ficheiro em cache | Resposta da CDN ao cliente | Notas |
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos |Os transcos da CDN entre formatos suportados. |
| Comprimidos |Descomprimido |Comprimidos |A CDN executa uma compressão. |
| Comprimidos |Não em cache |Comprimidos |A CDN executa uma compressão se a origem devolver um ficheiro não comprimido. <br/>**O Azure CDN da Verizon** passa o ficheiro não comprimido no primeiro pedido e, em seguida, comprime e caches o ficheiro para pedidos subsequentes. <br/>Os ficheiros com o `Cache-Control: no-cache` cabeçalho nunca são comprimidos. |
| Descomprimido |Comprimidos |Descomprimido |A CDN executa uma descompressão. |
| Descomprimido |Descomprimido |Descomprimido | |
| Descomprimido |Não em cache |Descomprimido | |

## <a name="media-services-cdn-compression"></a>Compressão CDN dos Serviços de Mídia
Para os pontos finais ativados para o streaming CDN dos Serviços de Mídia, a compressão é ativada por padrão para os seguintes tipos de MIME: 
- aplicação/vnd.ms-sstr+xml 
- aplicação/traço+xml
- url de aplicação/vnd.apple.mpeg
- aplicação/f4m+xml 

## <a name="see-also"></a>Consulte também
* [Troubleshooting CDN file compression](cdn-troubleshoot-compression.md) (Resolver problemas de compressão de ficheiros da CDN)    

