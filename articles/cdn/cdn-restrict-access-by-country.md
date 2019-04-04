---
title: Restringir o conteúdo da CDN do Azure por país | Documentos da Microsoft
description: Saiba como restringir o acesso por país ao seu conteúdo da CDN do Azure ao utilizar a funcionalidade de filtragem geográfica.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: f6efec64b4e6659b822b76e0fd7f9cc71a164094
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917758"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Restringir o conteúdo da CDN do Azure por país

## <a name="overview"></a>Descrição geral
Quando um utilizador solicita o conteúdo, por predefinição, o conteúdo é fornecido, independentemente da localização do utilizador que efetua o pedido. No entanto, em alguns casos, talvez queira restringir o acesso aos seus conteúdos por país. Com o *filtragem geográfica* funcionalidade, pode criar regras em caminhos específicos no ponto final da CDN para permitir ou bloquear o conteúdo nos países selecionados.

> [!IMPORTANT]
> **CDN Standard do Azure da Microsoft** perfis não suportam baseado no caminho de filtragem geográfica.
> 

## <a name="standard-profiles"></a>Perfis padrão
Os procedimentos nesta secção destinam-se **CDN do Azure Standard da Akamai** e **CDN do Azure Standard da Verizon** perfis apenas. 

Para **CDN do Azure Premium da Verizon** perfis, tem de utilizar o **gerir** portal para ativar a filtragem geográfica. Para obter mais informações, consulte [do Azure CDN Premium da Verizon perfis](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definir o caminho do diretório
Para acessar a funcionalidade de filtragem geográfica, selecione o ponto final da CDN no portal, em seguida, selecione **filtragem geográfica** em definições no menu do lado esquerdo. 

![Filtragem geográfica standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Partir do **caminho** caixa, especifique o caminho relativo para a localização a que os utilizadores serão permitidos ou negados o acesso. 

Pode aplicar filtragem geográfica para todos os seus ficheiros com um reencaminhamento barra (/) ou selecionar pastas específicas ao especificar caminhos de diretório (por exemplo, */imagens/*). Também pode aplicar a filtragem geográfica a um único ficheiro (por exemplo */pictures/city.png*). São permitidas várias regras; Depois de introduzir uma regra, é apresentada uma linha em branco para que insira a seguinte regra.

Por exemplo, todos os seguintes filtros de caminho de diretório são válidos:   
*/*                                 
*/Photos/*     
*/Photos/Strasbourg /*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Definir o tipo de ação

Partir do **ação** lista, selecione **permitir** ou **bloco**: 

- **Permitir**: Apenas os utilizadores dos países especificados tenham permissão para aceder a recursos de pedido do caminho de recursiva.

- **Bloco**: Os utilizadores de países/regiões especificados for negados o acesso a recursos de pedido do caminho de recursiva. Se não existem outras opções de filtragem de país tiverem sido configuradas para esse local, em seguida, todos os outros utilizadores terão permissão de acesso.

Por exemplo, uma filtragem geográfica regra para bloquear o caminho */fotos/Strasbourg/* filtra os seguintes ficheiros:     
*http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Definir os países
Partir do **códigos de país** , selecione os países que deseja bloquear ou permitir para o caminho. 

Depois de terminar de selecionar os países, selecione **guardar** para ativar a nova regra de filtragem geográfica. 

![Regras com filtragem geográfica](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Para eliminar uma regra, selecione-o na lista no **filtragem geográfica** página, em seguida, escolha **eliminar**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>O Azure CDN Premium da Verizon perfis
Para **para o Azure CDN Premium da Verizon** perfis, a interface do usuário para criar uma regra de filtragem geográfica é diferente:

1. No menu superior no seu perfil da CDN do Azure, selecione **gerir**.

2. No portal da Verizon, selecione **HTTP grandes**, em seguida, selecione **filtragem de país**.

    ![Filtragem geográfica standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Selecione **Adicionar filtro de país**.

    O **etapa um:** é apresentada a página.

4. Introduza o caminho do diretório, selecione **bloco** ou **Add**, em seguida, selecione **seguinte**.

    O **etapa dois:** é apresentada a página. 

5. Selecione um ou mais países na lista, em seguida, selecione **concluir** para ativar a regra. 
    
    A nova regra é apresentada na tabela no **filtragem de país** página.

    ![Regras com filtragem geográfica](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Na tabela de regras filtragem de país, selecione o ícone Eliminar junto a uma regra para eliminá-lo ou no ícone de edição de modificá-lo.

## <a name="considerations"></a>Considerações
* Alterações à sua configuração de filtragem geográfica não entrar em vigor imediatamente:
   * Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
   * Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
   * Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 
 
* Esta funcionalidade não suporta carateres universais (por exemplo, *).

* A configuração de filtragem geográfica associada com o caminho relativo é aplicada recursivamente para esse caminho.

* Pode ser aplicada apenas uma regra para o mesmo caminho relativo. Ou seja, não é possível criar vários filtros de país que apontam para o mesmo caminho relativo. No entanto, como os filtros de país são recursiva, uma pasta pode ter vários filtros de país. Em outras palavras, pode ser atribuída uma subpasta da pasta configurada anteriormente um filtro de país diferente.

* A funcionalidade de filtragem geográfica utiliza códigos de país para definir os países do que um pedido é permitido ou bloqueado para um diretório protegido. Embora os perfis de Akamai e Verizon suportam a maioria dos mesmos códigos de país, existem algumas diferenças. Para obter mais informações, consulte [códigos de país do CDN do Azure](/previous-versions/azure/mt761717(v=azure.100)). 

