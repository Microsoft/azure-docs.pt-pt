---
title: Restringir o conteúdo do CDN Azure por país/região [ Microsoft Docs
description: Aprenda a restringir o acesso por país/região ao seu conteúdo Azure CDN utilizando a funcionalidade de geofiltração.
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
ms.topic: article
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: 7ae7224efdaa281106dfbe2118ab0092c8284c6e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260161"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Restringir o conteúdo do CDN Azure por país/região

## <a name="overview"></a>Descrição geral
Quando um utilizador solicita o seu conteúdo, por predefinição, o conteúdo é servido independentemente da localização do utilizador que efaz o pedido. No entanto, em alguns casos, poderá querer restringir o acesso ao seu conteúdo por país/região. Com a funcionalidade *de geofiltração,* pode criar regras sobre caminhos específicos no seu ponto final cdN para permitir ou bloquear conteúdos em países/regiões selecionados.

> [!IMPORTANT]
> **O Azure CDN Standard a partir de** perfis da Microsoft não suporta a geofiltração baseada em percursos.
> 

## <a name="standard-profiles"></a>Perfis padrão
Os procedimentos nesta secção são para **o Azure CDN Standard da Akamai** e **Azure CDN Standard apenas a partir dos** perfis de Verizon. 

Para **o Azure CDN Premium a partir dos** perfis verizon, deve utilizar o portal **Manage** para ativar a geofiltração. Para mais informações, consulte [o Azure CDN Premium a partir dos perfis da Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Defina o caminho do diretório
Para aceder à função de geofiltração, selecione o seu ponto final CDN dentro do portal e, em seguida, selecione **geofiltração** em DEFINIÇÕES no menu esquerdo. 

![Norma de geofiltração](./media/cdn-filtering/cdn-geo-filtering-standard.png)

A partir da caixa **PATH,** especifique o caminho relativo para o local a que os utilizadores serão autorizados ou negados acesso. 

Pode aplicar geofiltração para todos os seus ficheiros com uma barra avançada (/) ou selecionar pastas específicas especificando caminhos de diretório (por exemplo, */imagens/*). Também pode aplicar geofiltração num único ficheiro (por *exemplo/pictures/city.png).* São permitidas múltiplas regras; depois de entrar numa regra, aparece uma linha em branco para entrar na próxima regra.

Por exemplo, todos os seguintes filtros de percurso de diretório são válidos:   
*/*                                 
*/Fotos/*     
*/Fotos/Estrasburgo/*     
*/Fotos/Estrasburgo/city.png*

### <a name="define-the-type-of-action"></a>Definir o tipo de ação

Da lista **ACTION,** selecione **Permitir** ou **Bloquear:** 

- **Permitir**: Apenas os utilizadores dos países/regiões especificados têm acesso a ativos solicitados a partir da trajetória recursiva.

- **Bloco**: Os utilizadores dos países/regiões especificados são impedidos de aceder aos ativos solicitados a partir da trajetória recursiva. Se nenhuma outra opção de filtragem país/região tiver sido configurada para esse local, então todos os outros utilizadores terão acesso.

Por exemplo, uma regra de geofiltração para bloquear o caminho */Fotos/Estrasburgo/* filtra os seguintes ficheiros:     
*\//http:\<endpoint>.azureedge.net/Photos/Estrasburgo/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Estrasburgo/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definir os países/regiões
Na lista de **CÓDIGOS DO PAÍS,** selecione os países/regiões que pretende bloquear ou permitir o caminho. 

Depois de terminar de selecionar os países/regiões, selecione **Save** para ativar a nova regra de geofiltração. 

![Regras de geofiltração](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Para eliminar uma regra, selecione-a a partir da lista na página **de geofiltração** e, em seguida, escolha **Eliminar**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium dos perfis da Verizon
**Para o Azure CDN Premium a partir dos** perfis verizon, a interface de utilizador para criar uma regra de geofiltração é diferente:

1. A partir do menu superior do seu perfil Azure CDN, selecione **Gerir**.

2. A partir do portal Verizon, selecione **HTTP Large**, em seguida, selecione **Country Filtering**.

    ![Norma de geofiltração](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. **Selecione Adicionar filtro country**.

    A página **do Passo Um:** aparece.

4. Introduza o caminho do diretório, selecione **Bloquear** ou **Adicionar,** em seguida, selecione **Next**.

    A página **do Passo Dois:** aparece. 

5. Selecione um ou mais países/regiões da lista e, em seguida, selecione **Finish** para ativar a regra. 
    
    A nova regra aparece na tabela na página de Filtragem do **País.**

    ![Regras de geofiltração](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Na tabela de regras de filtragem país/região, selecione o ícone de exclusão ao lado de uma regra para eliminá-lo ou o ícone de edição para modificá-lo.

## <a name="considerations"></a>Considerações
* Alterações na sua configuração de geofiltração não fazem efeito imediatamente:
   * Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
   * Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
   * Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 
 
* Esta funcionalidade não suporta caracteres wildcard (por exemplo, *).

* A configuração geofiltrante associada ao caminho relativo é aplicada de forma recursiva a esse caminho.

* Apenas uma regra pode ser aplicada no mesmo caminho relativo. Ou seja, não se pode criar vários filtros de país/região que apontam para o mesmo caminho relativo. No entanto, como os filtros país/região são recursivos, uma pasta pode ter vários filtros de país/região. Por outras palavras, uma subpasta de uma pasta previamente configurada pode ser atribuída a um filtro país/região diferente.

* A função de geofiltração utiliza códigos de país para definir os países/regiões a partir dos quais um pedido é permitido ou bloqueado para um diretório seguro. Embora os perfis de Akamai e Verizon apoiem a maioria dos mesmos códigos de país, existem algumas diferenças. Para mais informações, consulte [os códigos de país da Azure CDN](/previous-versions/azure/mt761717(v=azure.100)). 

