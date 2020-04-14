---
title: Melhorar o desempenho comprimindo ficheiros em Azure CDN / Microsoft Docs
description: Aprenda a melhorar a velocidade de transferência de ficheiros e aumente o desempenho da carga de página comprimindo os seus ficheiros em Azure CDN.
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
ms.topic: article
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 7124dd40d4510674014afe012a8f40dcb5bb6153
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253769"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Improve performance by compressing files in Azure CDN (Comprimir ficheiros na CDN do Azure para melhorar o desempenho)
A compressão de ficheiros é um método simples e eficaz para melhorar a velocidade de transferência de ficheiros e aumentar o desempenho da carga de página, reduzindo o tamanho de um ficheiro antes de ser enviado do servidor. A compressão de ficheiros pode reduzir os custos de largura de banda e proporcionar uma experiência mais responsiva para os seus utilizadores.

Existem duas formas de permitir a compressão de ficheiros:

- Ative a compressão no servidor de origem. Neste caso, a Azure CDN transmite ao longo dos ficheiros comprimidos e entrega-os aos clientes que os solicitam.
- Ativar a compressão diretamente nos servidores CDN POP *(compressão no voo*). Neste caso, o CDN comprime os ficheiros e serve-os aos utilizadores finais, mesmo que não tenham sido comprimidos pelo servidor de origem.

> [!IMPORTANT]
> As alterações de configuração do CDN Azure podem demorar algum tempo a propagar-se através da rede: 
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 
> 
> Se estiver a configurar a compressão pela primeira vez para o seu ponto final do CDN, considere esperar 1-2 horas antes de resolver problemas para garantir que as definições de compressão se tenham propagado aos POPs.

## <a name="enabling-compression"></a>Habilitar a compressão
Os níveis de CDN padrão e premium fornecem a mesma funcionalidade de compressão, mas a interface do utilizador difere. Para obter mais informações sobre as diferenças entre os níveis standard e premium de CDN, consulte a [visão geral do CDN de Azure](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Perfis CDN padrão 
> [!NOTE]
> Esta secção aplica-se ao **Azure CDN Standard da Microsoft,** **Azure CDN Standard da Verizon**e **Azure CDN Standard dos** perfis da Akamai.
> 
> 

1. A partir da página de perfil da CDN, selecione o ponto final do CDN que pretende gerir.

    ![Pontos finais do perfil da CDN](./media/cdn-file-compression/cdn-endpoints.png)

    A página final do CDN abre.
2. Selecione **Compressão**.

    ![Seleção de compressão CDN](./media/cdn-file-compression/cdn-compress-select-std.png)

    A página de compressão abre.
3. Selecione **Ligar** para ligar a compressão.

    ![Opções de compressão de ficheiros CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Utilize os tipos MIME predefinidos ou modifique a lista adicionando ou removendo os tipos MIME.

   > [!TIP]
   > Embora seja possível, não é aconselhável aplicar compressão em formatos comprimidos. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

   > [!NOTE]
   > A modificação da lista padrão dos tipos MIME não é atualmente suportada no Azure CDN Standard da Microsoft.
   > 

5. Depois de efazer as suas alterações, selecione **Guardar**.

### <a name="premium-cdn-profiles"></a>Perfis CDN premium
> [!NOTE]
> Esta secção aplica-se apenas ao **Azure CDN Premium a partir dos** perfis da Verizon.
> 

1. A partir da página de perfil cdN, selecione **Gerir**.

    ![CDN Gerir selecione](./media/cdn-file-compression/cdn-manage-btn.png)

    Abre o portal de gestão da CDN.
2. Paire sobre a aba **HTTP Grande** e, em seguida, paire sobre as **Definições de Cache** flyout. Selecione **Compressão**.

    ![Seleção de compressão CDN](./media/cdn-file-compression/cdn-compress-select.png)

    As opções de compressão são apresentadas.

    ![Opções de compressão de ficheiros CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Ativar a compressão selecionando ativado por **compressão**. Introduza os tipos MIME que pretende comprimir como uma lista de comma delimitada (sem espaços) na caixa de Tipos de **Ficheiros.**

   > [!TIP]
   > Embora seja possível, não é aconselhável aplicar compressão em formatos comprimidos. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

4. Depois de efazer as suas alterações, selecione **'Atualizar**' .

## <a name="compression-rules"></a>Regras de compressão

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Padrão Azure CDN a partir dos perfis da Microsoft

Para **o Azure CDN Standard a partir dos** perfis da Microsoft, apenas os ficheiros elegíveis são comprimidos. Para ser elegível para compressão, um ficheiro deve:
- Seja do tipo MIME que tenha sido [configurado para compressão](#enabling-compression).
- Seja maior que 1 KB
- Seja menor que 8 MB

Estes perfis suportam as seguintes codificações de compressão:
- gzip (zip GNU)
- brotli 

Se o pedido suportar mais do que um tipo de compressão, a compressão de brotli tem precedência.

Quando um pedido de um ativo especifica a compressão gzip e o pedido resulta numa falha de cache, o Azure CDN executa a compressão gzip do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache.

### <a name="azure-cdn-from-verizon-profiles"></a>CDN Azure a partir de perfis verizon

Para **a Norma CDN Azure da Verizon** e **azure CDN Premium dos** perfis verizon, apenas os ficheiros elegíveis são comprimidos. Para ser elegível para compressão, um ficheiro deve:
- Ser maior que 128 bytes
- Seja menor que 3 MB

Estes perfis suportam as seguintes codificações de compressão:
- gzip (zip GNU)
- ESVAZIAR
- bzip2
- brotli 

Se o pedido suportar mais do que um tipo de compressão, esses tipos de compressão têm precedência sobre a compressão de brotli.

Quando um pedido de um ativo especifica a compressão brotli (cabeçalho HTTP) `Accept-Encoding: br`e o pedido resulta numa falha de cache, o Azure CDN realiza a compressão brotli do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Padrão Azure CDN dos perfis da Akamai

Para **o Azure CDN Standard a partir dos** perfis da Akamai, todos os ficheiros são elegíveis para compressão. No entanto, um ficheiro deve ser de um tipo MIME que tenha sido [configurado para compressão](#enabling-compression).

Estes perfis suportam apenas a codificação da compressão de zíper. Quando um ponto final de perfil solicita um ficheiro codificado com gzip, é sempre solicitado a partir da origem, independentemente do pedido do cliente. 

## <a name="compression-behavior-tables"></a>Tabelas de comportamento de compressão
As tabelas que se seguem descrevem o comportamento de compressão do CDN Azure para cada cenário:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>A compressão é desativada ou o ficheiro não é elegível para compressão
| Formato solicitado pelo cliente (via cabeçalho Accept-Encoding) | Formato de ficheiro cached | A resposta da CDN ao cliente | &nbsp; &nbsp; Notas&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos | |
| Comprimidos |Descomprimido |Descomprimido | |
| Comprimidos |Não cached |Comprimido ou não comprimido |A resposta de origem determina se a CDN realiza uma compressão. |
| Descomprimido |Comprimidos |Descomprimido | |
| Descomprimido |Descomprimido |Descomprimido | |
| Descomprimido |Não cached |Descomprimido | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>A compressão está ativada e o ficheiro é elegível para compressão
| Formato solicitado pelo cliente (via cabeçalho Accept-Encoding) | Formato de ficheiro cached | Resposta da CDN ao cliente | Notas |
| --- | --- | --- | --- |
| Comprimidos |Comprimidos |Comprimidos |Os transcódigos CDN entre formatos suportados. |
| Comprimidos |Descomprimido |Comprimidos |A CDN realiza uma compressão. |
| Comprimidos |Não cached |Comprimidos |O CDN realiza uma compressão se a origem devolver um ficheiro não comprimido. <br/>**O Azure CDN da Verizon** passa o ficheiro não comprimido no primeiro pedido e, em seguida, comprime e caches o ficheiro para pedidos subsequentes. <br/>Os ficheiros com o `Cache-Control: no-cache` cabeçalho nunca são comprimidos. |
| Descomprimido |Comprimidos |Descomprimido |A CDN realiza uma descompressão. |
| Descomprimido |Descomprimido |Descomprimido | |
| Descomprimido |Não cached |Descomprimido | |

## <a name="media-services-cdn-compression"></a>Compressão CDN dos Serviços de Mídia
Para pontos finais ativados para o streaming CDN dos Serviços de Media, a compressão é ativada por padrão para os seguintes tipos MIME: 
- aplicação/vnd.ms-sstr+xml 
- aplicação/dash+xml
- aplicação/vnd.apple.mpegurl
- aplicação/f4m+xml 

## <a name="see-also"></a>Consulte também
* [Troubleshooting CDN file compression](cdn-troubleshoot-compression.md) (Resolver problemas de compressão de ficheiros da CDN)    

