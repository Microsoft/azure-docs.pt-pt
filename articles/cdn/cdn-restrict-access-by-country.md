---
title: Restringir o conteúdo do Azure CDN por país/região ! Microsoft Docs
description: Saiba como restringir o acesso por país/região ao seu conteúdo Azure CDN utilizando a função de geo-filtragem.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: ed82adcc1432bde27042d5775c454bfabcdb96ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358139"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Restringir o conteúdo do Azure CDN por país/região

## <a name="overview"></a>Descrição geral
Quando um utilizador solicita o seu conteúdo, por padrão, o conteúdo é servido independentemente da localização do utilizador que es faz o pedido. No entanto, em alguns casos, poderá querer restringir o acesso ao seu conteúdo por país/região. Com a *função de geo-filtragem,* pode criar regras sobre caminhos específicos no seu ponto final CDN para permitir ou bloquear conteúdos em países/regiões selecionados.

> [!IMPORTANT]
> **O Azure CDN Standard dos** perfis da Microsoft não suporta a geo-filtragem baseada em caminhos.
> 

## <a name="standard-profiles"></a>Perfis padrão
Os procedimentos nesta secção são para **Azure CDN Standard da Akamai** e **Azure CDN Standard apenas a partir de** perfis Verizon. 

Para **o Azure CDN Premium a partir de** perfis Verizon, deve utilizar o portal **Manage** para ativar a geo-filtragem. Para obter mais informações, consulte [o Azure CDN Premium dos perfis Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Defina o caminho do diretório
Para aceder à função de geo-filtragem, selecione o seu ponto final CDN dentro do portal e, em seguida, selecione **a filtragem de geo-filtragem** em DEFINIÇÕES no menu da esquerda. 

![Screenshot mostrando geo-filtragem selecionada do menu para um Ponto final.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

A partir da caixa **PATH,** especifique o caminho relativo para o local ao qual os utilizadores serão autorizados ou impedidos de aceder. 

Pode aplicar geo-filtragem para todos os seus ficheiros com uma barra dianteira (/) ou selecionar pastas específicas especificando caminhos de diretório (por exemplo, */imagens/*). Também pode aplicar geo-filtragem num único ficheiro (por exemplo */imagens/city.png*). São permitidas várias regras; depois de introduzir uma regra, uma fila em branco aparece para que você insira a regra seguinte.

Por exemplo, todos os seguintes filtros de percurso de diretório são válidos:   
*/*                                 
*/Fotos/*     
*/Fotos/Estrasburgo/*     
*/Fotos/Estrasburgo/city.png*

### <a name="define-the-type-of-action"></a>Definir o tipo de ação

Na lista **ACTION,** selecione **Permitir** ou **Bloquear:** 

- **Permitir:** Apenas os utilizadores dos países/regiões especificados têm acesso aos ativos solicitados a partir da via recursiva.

- **Bloco**: Aos utilizadores dos países/regiões especificados é negado o acesso aos ativos solicitados a partir da via recursiva. Se nenhuma outra opções de filtragem país/região tiver sido configurada para esse local, então todos os outros utilizadores terão acesso.

Por exemplo, uma regra de geo-filtragem para bloquear o caminho */Fotos/Estrasburgo/* filtra os seguintes ficheiros:     
*http: \/ / \<endpoint> .azureedge.net/Photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> .azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definir os países/regiões
Na lista **DE CÓDIGOS DE PAÍS,** selecione os países/regiões que pretende bloquear ou permitir o caminho. 

Depois de ter terminado a seleção dos países/regiões, **selecione Guardar** para ativar a nova regra de geo-filtragem. 

![A screenshot mostra códigos de país para usar para bloquear ou permitir países ou regiões.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Limpar os recursos
Para eliminar uma regra, selecione-a da lista na página **de geo-filtragem** e, em seguida, escolha **Eliminar**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium dos perfis Verizon
Para **O Azure CDN Premium dos** perfis Verizon, a interface do utilizador para criar uma regra de geo-filtragem é diferente:

1. A partir do menu superior do seu perfil Azure CDN, **selecione Gerir**.

2. A partir do portal Verizon, selecione **HTTP Large**e, em seguida, selecione **A Filtragem do País**.

    ![A screenshot mostra como seleciona a filtragem do país em Azure C D N.](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. **Selecione Adicionar filtro de país**.

    O **Passo Um:** a página aparece.

4. Introduza o caminho do diretório, selecione **Bloco** ou **Adicione**e, em seguida, selecione **Seguinte**.

    O **Passo Dois:** a página aparece. 

5. Selecione um ou mais países/regiões da lista e, em seguida, **selecione Terminar** para ativar a regra. 
    
    A nova regra aparece na tabela na página **de Filtragem do País.**

    ![A screenshot mostra onde a regra aparece na filtragem do país.](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Limpar os recursos
Na tabela de regras de filtragem país/região, selecione o ícone de eliminação ao lado de uma regra para eliminá-lo ou o ícone de edição para modificá-lo.

## <a name="considerations"></a>Considerações
* As alterações à sua configuração de geo-filtragem não têm efeito imediato:
   * Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
   * Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
   * Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 
 
* Esta funcionalidade não suporta caracteres wildcard (por exemplo, *).

* A configuração de geo-filtragem associada ao caminho relativo é aplicada novamente a esse caminho.

* Apenas uma regra pode ser aplicada ao mesmo caminho relativo. Ou seja, não é possível criar vários filtros de país/região que apontam para o mesmo caminho relativo. No entanto, como os filtros país/região são recursivos, uma pasta pode ter vários filtros de país/região. Por outras palavras, uma sub-classificação de uma pasta previamente configurada pode ser atribuída a um filtro país/região diferente.

* A função de geo-filtragem utiliza códigos de país para definir os países/regiões a partir dos quais um pedido é permitido ou bloqueado para um diretório seguro. Embora os perfis da Akamai e da Verizon apoiem a maioria dos mesmos códigos de país, existem algumas diferenças. Para obter mais informações, consulte [os códigos de países da Azure CDN](/previous-versions/azure/mt761717(v=azure.100)). 

