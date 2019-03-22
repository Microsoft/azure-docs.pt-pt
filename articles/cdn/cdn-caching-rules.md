---
title: Controlar o comportamento de cache com regras de colocação em cache de CDN do Azure | Documentos da Microsoft
description: Pode utilizar as regras de cache de CDN para definir ou modificar o comportamento de expiração do cache de padrão global e com condições, como extensões de ficheiro e caminho de URL.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: 3a94b8252feb7c5c345d678579c477fce02d6e03
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259742"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Comportamento de cache com regras de colocação em cache de CDN do Azure de controlo

> [!NOTE] 
> Estão disponíveis apenas para regras de colocação em cache **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfis. Para **CDN do Azure Premium da Verizon** perfis, tem de utilizar o [motor de regras de CDN do Azure](cdn-rules-engine.md) no **gerir** portal para uma funcionalidade semelhante.
 
Azure Content Delivery Network (CDN) oferece duas formas de controlar a forma como os ficheiros são colocados em cache: 

- Regras de colocação em cache: Este artigo descreve como pode utilizar a rede de entrega de conteúdos (CDN), as regras de cache para definir ou modificar o comportamento de expiração do cache de padrão global e com condições personalizadas, como uma extensão de ficheiro e caminho do URL. A CDN do Azure dispõe de dois tipos de regras de colocação em cache:

   - Regras de colocação em cache globais: Pode definir uma regra de colocação em cache global para cada ponto de extremidade no seu perfil, o que afeta todos os pedidos para o ponto final. A regra global de colocação em cache substitui quaisquer cabeçalhos de diretivas de cache HTTP, se for definido algum.

   - Personalizar regras de colocação em cache: Pode definir uma ou mais regras colocação em cache personalizadas para cada ponto de extremidade no seu perfil. As regras personalizadas de colocação em cache correspondem a extensões de ficheiro e a caminhos específicos, são processadas por ordem e substituem a regra global de colocação em cache, se definida. 

- Colocação em cache de cadeia de caracteres de consulta: Pode ajustar a forma como a CDN do Azure trata de colocação em cache para pedidos com cadeias de consulta. Para obter informações, consulte [comportamento com cadeias de consulta de cache de CDN do Azure de controle](cdn-query-string.md). Se o ficheiro não está em cache, a definição de colocação em cache de cadeia de consulta não tem efeito, com base na cache de regras e comportamentos de padrão CDN.

Para obter informações sobre o padrão de comportamento de colocação em cache e cabeçalhos de diretiva de colocação em cache, consulte [funciona como o cache](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Acesso a regras de colocação em cache da CDN do Azure

1. Abra o portal do Azure, selecione um perfil da CDN, em seguida, selecione um ponto final.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Definições de comportamento de colocação em cache
Para regras de colocação em cache globais e personalizadas, pode especificar o seguinte procedimento **comportamento de colocação em cache** definições:

- **Ignorar cache**: Não colocar em cache e ignorar cabeçalhos de diretivas de cache fornecido de origem.

- **Substituir**: Ignorar a duração da cache fornecido de origem; em alternativa, utilize a duração da cache fornecido. Isso não substituirá a cache-control: não-cache.

- **Definir se em falta**: Respeite os cabeçalhos de diretivas de cache fornecido de origem, caso existam; caso contrário, utilize a duração da cache fornecido.

![Regras globais de colocação em cache](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Regras personalizadas de colocação em cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Duração de expiração da cache
Para regras de colocação em cache globais e personalizadas, pode especificar a duração de expiração do cache em dias, horas, minutos e segundos:

- Para o **substituir** e **definir se em falta** **comportamento de colocação em cache** configurações, o intervalo de durações de cache válido entre 0 segundos e 366 dias. Para um valor de 0 segundos, a CDN armazena em cache o conteúdo, mas tem revalide cada pedido com o servidor de origem.

- Para o **ignorar a cache** definição, a duração de cache é automaticamente definida como 0 segundos e não pode ser alterada.

## <a name="custom-caching-rules-match-conditions"></a>Condições de correspondência de regras de colocação em cache personalizada

Para regras de cache personalizada, condições de correspondência de dois estão disponíveis:
 
- **Caminho**: Esta condição corresponde ao caminho do URL, excluindo o nome de domínio e suporta o símbolo de caráter universal (\*). Por exemplo, _/myfile.html_, _//My/Folder/ *_, e _/my/images/*.jpg_. O comprimento máximo é de 260 carateres.

- **Extensão**: Esta condição corresponde a extensão de ficheiro do arquivo solicitado. Pode fornecer uma lista separada por vírgulas de extensões de ficheiro para corresponder. Por exemplo, _. jpg_, _. mp3_, ou _PNG_. O número máximo de extensões é 50 e o número máximo de carateres por extensão é 16. 

## <a name="global-and-custom-rule-processing-order"></a>Ordem de processamento da regra globais e personalizadas
As regras de colocação em cache globais e personalizadas são processadas pela seguinte ordem:

- Regras de colocação em cache globais têm precedência sobre o comportamento de colocação em cache de CDN do padrão (configurações de diretivas de cache de cabeçalho HTTP). 

- Regras de colocação em cache personalizadas têm precedência sobre regras de colocação em cache globais, onde são aplicadas. Regras de colocação em cache personalizadas são processadas na ordem de cima para baixo. Ou seja, se um pedido de corresponder a ambas as condições, regras na parte inferior da lista de precedência sobre as regras na parte superior da lista. Por conseguinte, deve colocar a regras mais específicas mais baixo na lista.

**Exemplo**:
- Regra de colocação em cache global: 
   - Comportamento de colocação em cache: **Override**
   - Duração de expiração da cache: 1 dia

- Colocação em cache personalizada da regra #1:
   - Condição de correspondência: **Caminho**
   - Corresponde ao valor:   _/home / *_
   - Comportamento de colocação em cache: **Override**
   - Duração de expiração da cache: 2 dias

- Colocação em cache personalizada da regra #2:
   - Condição de correspondência: **Extensão**
   - Corresponde ao valor: _. HTML_
   - Comportamento de colocação em cache: **Definir se em falta**
   - Duração de expiração da cache: 3 dias

Quando estas regras estiverem definidas, um pedido para  _&lt;nome de anfitrião do ponto final&gt;_ acionadores.azureedge.net/home/index.html #2, que está definido como regra de colocação em cache personalizada: **Definir se em falta** e 3 dias. Por conseguinte, se o *Index. HTML* ficheiro tem `Cache-Control` ou `Expires` cabeçalhos HTTP, eles são cumpridos; caso contrário, se esses cabeçalhos não forem definidos, o arquivo é armazenado em cache para 3 dias.

> [!NOTE] 
> Ficheiros que estão em cache antes de a regra ser alterada de manter sua definição de duração da cache de origem. Para repor as respetivas durações de cache, deve [remover o ficheiro](cdn-purge-endpoint.md). 
>
> As alterações de configuração do Azure CDN podem demorar algum tempo para propagar através da rede: 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para **CDN do Azure Standard da Verizon** perfis, propagação normalmente fica concluída em 10 minutos.  
>

## <a name="see-also"></a>Consulte também

- [Como funciona a colocação em cache](cdn-how-caching-works.md)
- [Tutorial: Definir as regras de cache de CDN do Azure](cdn-caching-rules-tutorial.md)
