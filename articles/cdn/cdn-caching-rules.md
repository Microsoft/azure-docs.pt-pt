---
title: Controlar o comportamento de cache da CDN do Azure com regras de cache | Microsoft Docs
description: Você pode usar as regras de cache da CDN para definir ou modificar o comportamento de expiração de cache padrão globalmente e com condições, como um caminho de URL e extensões de arquivo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: ddd7dc7e1245c2a77e866a454bf6bfa3c1f16f88
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278142"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Controlar o comportamento de cache da CDN do Azure com regras de cache

> [!NOTE] 
> As regras de cache estão disponíveis apenas para os perfis da **CDN standard do Azure da Verizon** e **da CDN standard do Azure da Akamai** . Para os perfis **da CDN do Azure da Microsoft** , você deve usar o [mecanismo de regras padrão](cdn-standard-rules-engine-reference.md) para perfis **da CDN Premium do Azure da Verizon** . você deve usar o [mecanismo de regras do Verizon Premium](cdn-rules-engine.md) no portal de **Gerenciamento** para funcionalidade semelhante.
 
A CDN (rede de distribuição de conteúdo) do Azure oferece duas maneiras de controlar como os arquivos são armazenados em cache: 

- Regras de cache: Este artigo descreve como você pode usar as regras de cache da CDN (rede de distribuição de conteúdo) para definir ou modificar o comportamento de expiração de cache padrão globalmente e com condições personalizadas, como um caminho de URL e uma extensão de arquivo. A CDN do Azure dispõe de dois tipos de regras de colocação em cache:

   - Regras globais de colocação em cache: pode definir uma regra global de colocação em cache para cada ponto final no seu perfil, o que afeta todos os pedidos para o ponto final. A regra global de colocação em cache substitui quaisquer cabeçalhos de diretivas de cache HTTP, se for definido algum.

   - Regras personalizadas de colocação em cache: pode configurar uma ou mais regras personalizadas de colocação em cache para cada ponto final no seu perfil. As regras personalizadas de colocação em cache correspondem a extensões de ficheiro e a caminhos específicos, são processadas por ordem e substituem a regra global de colocação em cache, se definida. 

- Cache de cadeia de caracteres de consulta: você pode ajustar como a CDN do Azure trata o cache para solicitações com cadeias de caracteres de consulta. Para obter informações, consulte [controlar o comportamento de cache da CDN do Azure com cadeias de consulta](cdn-query-string.md). Se o arquivo não for armazenável em cache, a configuração de cache da cadeia de caracteres de consulta não terá efeito, com base nas regras de cache e nos comportamentos padrão da CDN.

Para obter informações sobre o comportamento de cache padrão e cabeçalhos de diretiva de cache, consulte [como funciona o Caching](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Acessando regras de cache da CDN do Azure

1. Abra o portal do Azure, selecione um perfil CDN e, em seguida, selecione um ponto de extremidade.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Configurações de comportamento de cache
Para regras de cache globais e personalizadas, você pode especificar as seguintes configurações de **comportamento de cache** :

- **Ignorar cache**: não armazenar em cache e ignorar os cabeçalhos de diretiva de cache fornecidos pela origem.

- **Substituir**: ignorar a duração do cache fornecida pela origem; em vez disso, use a duração de cache fornecida. Isso não substituirá o Cache-Control: no-cache.

- **Definir se ausente**: honra os cabeçalhos de diretiva de cache fornecidos pela origem, se existirem; caso contrário, use a duração de cache fornecida.

![Regras globais de colocação em cache](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Regras personalizadas de colocação em cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Duração da expiração do cache
Para regras de cache globais e personalizadas, você pode especificar a duração da expiração do cache em dias, horas, minutos e segundos:

- Para a **substituição** e **defina se** as configurações de **comportamento de cache** ausentes, as durações de cache válidas variam entre 0 e 366 dias. Para um valor de 0 segundos, a CDN armazena em cache o conteúdo, mas deve revalidar cada solicitação com o servidor de origem.

- Para a configuração **ignorar cache** , a duração do cache é definida automaticamente como 0 segundo e não pode ser alterada.

## <a name="custom-caching-rules-match-conditions"></a>Regras de cache personalizadas correspondem às condições

Para regras de cache personalizadas, duas condições de correspondência estão disponíveis:
 
- **Caminho**: essa condição corresponde ao caminho da URL, excluindo o nome de domínio e dá suporte ao símbolo curinga (\*). Por exemplo, _/MyFile.html_, _/minha/pasta/*_ e _/My/images/*. jpg_. O comprimento máximo é de 260 caracteres.

- **Extensão**: essa condição corresponde à extensão de arquivo do arquivo solicitado. Você pode fornecer uma lista de extensões de arquivo separadas por vírgulas para corresponder. Por exemplo, _. jpg_, _. mp3_ou _. png_. O número máximo de extensões é 50 e o número máximo de caracteres por extensão é 16. 

## <a name="global-and-custom-rule-processing-order"></a>Ordem de processamento de regra global e personalizada
As regras de cache globais e personalizadas são processadas na seguinte ordem:

- As regras de cache global têm precedência sobre o comportamento padrão de cache da CDN (configurações de cabeçalho de diretiva de cache HTTP). 

- As regras de cache personalizadas têm precedência sobre as regras de cache globais, onde se aplicam. As regras de cache personalizadas são processadas na ordem de cima para baixo. Ou seja, se uma solicitação corresponder a ambas as condições, as regras na parte inferior da lista têm precedência sobre as regras na parte superior da lista. Portanto, você deve posicionar regras mais específicas abaixo na lista.

**Exemplo**:
- Regra de cache global: 
   - Comportamento de Caching: **substituição**
   - Duração da expiração do cache: 1 dia

- #1 de regra de cache Personalizada:
   - Condição de correspondência: **caminho**
   - Valor de correspondência: _/Home/*_
   - Comportamento de Caching: **substituição**
   - Duração da expiração do cache: 2 dias

- #2 de regra de cache Personalizada:
   - Condição de correspondência: **extensão**
   - Valor de correspondência: _. html_
   - Comportamento de Caching: **definir se ausente**
   - Duração da expiração do cache: 3 dias

Quando essas regras são definidas, uma solicitação de _&lt;nome do host do ponto de extremidade&gt;_ . azureedge.net/home/index.html dispara a regra de cache Personalizada #2, que é definida como: **definir se ausente** e 3 dias. Portanto, se o arquivo *index. html* tiver `Cache-Control` ou `Expires` cabeçalhos HTTP, eles serão aceitos; caso contrário, se esses cabeçalhos não estiverem definidos, o arquivo será armazenado em cache por 3 dias.

> [!NOTE] 
> Os arquivos armazenados em cache antes da alteração da regra mantêm sua configuração de duração do cache de origem. Para redefinir suas durações de cache, você deve [limpar o arquivo](cdn-purge-endpoint.md). 
>
> As alterações de configuração da CDN do Azure podem levar algum tempo para serem propagadas pela rede: 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para perfis **da CDN standard do Azure da Verizon** , a propagação geralmente é concluída em 10 minutos.  
>

## <a name="see-also"></a>Consulte também

- [Como funciona a colocação em cache](cdn-how-caching-works.md)
- [Tutorial: Definir regras de colocação em cache da CDN do Azure](cdn-caching-rules-tutorial.md)
