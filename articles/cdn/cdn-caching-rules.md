---
title: Controle O cDN de controlo comportamento de cache com regras de cache / Microsoft Docs
description: Pode utilizar regras de cache CDN para definir ou modificar o comportamento de expiração da cache padrão tanto a nível global como com condições, tais como um caminho de URL e extensões de ficheiros.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: 874ec75fb9173b6cee50bf8880510464fa13e9d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254245"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Controlar o comportamento da colocação em cache da CDN do Azure com as regras de colocação em cache

> [!NOTE] 
> As regras de cache estão disponíveis apenas para **o Azure CDN Standard da Verizon** e **Azure CDN Standard a partir dos** perfis da Akamai. Para o **Azure CDN a partir dos** perfis da Microsoft, tem de utilizar o motor [standard](cdn-standard-rules-engine-reference.md) rules For **Azure CDN Premium a partir dos** perfis Verizon, tem de utilizar o motor de regras [Verizon Premium](cdn-rules-engine.md) no portal **Manage** para funcionalidades semelhantes.
 
A Rede de Entrega de Conteúdos Azure (CDN) oferece duas formas de controlar a forma como os seus ficheiros estão em cache: 

- Regras de cache: Este artigo descreve como pode usar regras de cache da rede de entrega de conteúdos (CDN) para definir ou modificar o comportamento de expiração de cache padrão tanto a nível global como com condições personalizadas, como uma extensão de url e extensão de ficheiros. A CDN do Azure dispõe de dois tipos de regras de colocação em cache:

   - Regras globais de colocação em cache: pode definir uma regra global de colocação em cache para cada ponto final no seu perfil, o que afeta todos os pedidos para o ponto final. A regra global de colocação em cache substitui quaisquer cabeçalhos de diretivas de cache HTTP, se for definido algum.

   - Regras personalizadas de colocação em cache: pode configurar uma ou mais regras personalizadas de colocação em cache para cada ponto final no seu perfil. As regras personalizadas de colocação em cache correspondem a extensões de ficheiro e a caminhos específicos, são processadas por ordem e substituem a regra global de colocação em cache, se definida. 

- Caching de corda de consulta: Você pode ajustar como o CDN Azure trata o caching para pedidos com cordas de consulta. Para obter informações, consulte [Control Azure CDN cacheching comportamento com cordas de consulta](cdn-query-string.md). Se o ficheiro não for cacheable, a definição de cacheching de cordas de consulta não tem efeito, com base em regras de cache e comportamentos padrão da CDN.

Para obter informações sobre comportamento de cache padrão e cabeçalhos de diretiva de cache, consulte [como funciona o cache](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Aceder às regras de cache do Azure CDN

1. Abra o portal Azure, selecione um perfil CDN e, em seguida, selecione um ponto final.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Definições de comportamento de cache
Para regras globais e personalizadas de cache, pode especificar as seguintes definições de **comportamento de Caching:**

- **Cache de bypass**: Não cache e ignore cabeçalhos de diretiva de cache fornecidos pela origem.

- **Sobreposição**: Ignorar a duração do cache fornecido pela origem; utilize a duração da cache fornecida. Isto não sobrepor-se-á ao controlo de cache: sem cache.

- **Definir se faltar**: Honre cabeçalhos de diretiva de cache fornecidos pela origem, se existirem; caso contrário, utilize a duração da cache fornecida.

![Regras globais de colocação em cache](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Regras personalizadas de colocação em cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Duração da expiração do cache
Para regras globais e personalizadas de cache, pode especificar a duração da expiração da cache em dias, horas, minutos e segundos:

- Para as definições de comportamento de **sobreposição** e **definição se faltar** em instalações de **comportamento de cache,** as durações de cache válidas variam entre 0 segundos e 366 dias. Por um valor de 0 segundos, o CDN caches o conteúdo, mas deve revalidar cada pedido com o servidor de origem.

- Para a regulação da **cache bypass,** a duração da cache é automaticamente definida para 0 segundos e não pode ser alterada.

## <a name="custom-caching-rules-match-conditions"></a>Regras personalizadas de cache correspondem às condições

Para regras de cache personalizadas, estão disponíveis duas condições de jogo:
 
- **Caminho**: Esta condição corresponde ao caminho do URL, excluindo o\*nome de domínio, e suporta o símbolo wildcard (). Por exemplo, _/myfile.html,_ _/my/folder/*_, e _/my/images/*.jpg_. O comprimento máximo é de 260 caracteres.

- **Extensão**: Esta condição corresponde à extensão do ficheiro solicitado. Pode fornecer uma lista de extensões de ficheiros separadas por vírina para combinar. Por exemplo, _.jpg_, _.mp3_, ou _.png_. O número máximo de extensões é de 50 e o número máximo de caracteres por extensão é de 16. 

## <a name="global-and-custom-rule-processing-order"></a>Ordem de processamento de regras global e personalizada
As regras globais e personalizadas de cache são processadas na seguinte ordem:

- As regras globais de cacheching têm precedência sobre o comportamento padrão de cache cdn (definições de cabeçalho http cache-directive). 

- As regras de cache personalizadas têm precedência sobre as regras globais de cache, onde se aplicam. As regras de cache personalizadas são processadas de cima para baixo. Ou seja, se um pedido corresponder às duas condições, as regras na parte inferior da lista têm precedência sobre as regras no topo da lista. Por isso, deve colocar regras mais específicas mais baixas na lista.

**Exemplo:**
- Regra global do cache: 
   - Comportamento de caching: **Sobreposição**
   - Duração da expiração do cache: 1 dia

- Regra de cache personalizada #1:
   - Condição do jogo: **Caminho**
   - Valor do jogo: _/casa/*_
   - Comportamento de caching: **Sobreposição**
   - Duração da expiração do cache: 2 dias

- Regra de cache personalizada #2:
   - Condição do jogo: **Extensão**
   - Valor da correspondência: _.html_
   - Comportamento de cache: **Definir se faltar**
   - Duração da expiração do cache: 3 dias

Quando estas regras são definidas, um pedido de **Set if missing** _ &lt;nome&gt;_ de anfitrião de ponto final .azureedge.net/home/index.html aciona a regra de cache personalizada #2, que está definida para: Definir se faltar e 3 dias. Portanto, se o ficheiro *index.html* tiver `Cache-Control` ou `Expires` cabeçalhos HTTP, eles são honrados; caso contrário, se estes cabeçalhos não estiverem definidos, o ficheiro é colocado em cache durante 3 dias.

> [!NOTE] 
> Os ficheiros que são cacheantes de uma alteração de regra mantêm a definição de duração da cache de origem. Para repor as durações da cache, deve [expurgar o ficheiro](cdn-purge-endpoint.md). 
>
> As alterações de configuração do CDN Azure podem demorar algum tempo a propagar-se através da rede: 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para **o Azure CDN Standard a partir dos** perfis de Verizon, a propagação geralmente completa em 10 minutos.  
>

## <a name="see-also"></a>Consulte também

- [Como funciona a colocação em cache](cdn-how-caching-works.md)
- [Tutorial: Definir regras de colocação em cache da CDN do Azure](cdn-caching-rules-tutorial.md)
