---
title: 'Configurar o emparelhamento para um circuito – ExpressRoute: Azure | Microsoft Docs'
description: Este artigo documenta as etapas para criar e provisionar o emparelhamento privado e o Microsoft ExpressRoute. Este artigo também demonstra como verificar o status, atualizar ou excluir emparelhamentos de um circuito.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 30b330d60d75896406cbdf2d1eb41537960c5be5
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965290"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar o emparelhamento para um circuito de ExpressRoute

Este artigo ajuda você a criar e gerenciar a configuração de roteamento para um circuito de ExpressRoute Azure Resource Manager (ARM), usando o portal do Azure. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos para um circuito do ExpressRoute. Se você quiser usar um método diferente para trabalhar com o circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo-emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – emparelhamento público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo-emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Você pode configurar o emparelhamento privado e Microsoft do Azure para um circuito do ExpressRoute (o emparelhamento público do Azure é preterido para novos circuitos). Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre os domínios de roteamento e emparelhamentos, consulte [sobre circuitos e emparelhamentos](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. Para configurar o emparelhamento, o circuito do ExpressRoute deve estar em um estado provisionado e habilitado. 
* Se você planeja usar um hash de chave compartilhada/MD5, certifique-se de usá-lo em ambos os lados do túnel e limitar o número de caracteres alfanuméricos a um máximo de 25. Não há suporte para caracteres especiais. 

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IPVPN, como o MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você. 

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Verifique com seu provedor de serviços antes de configurar emparelhamentos de BGP.
> 
> 

## <a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute que foram configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados por meio do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos do ExpressRoute que são configurados em ou após 1º de agosto de 2017 não terão prefixos anunciados até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Configure o circuito ExpressRoute. Verifique o **status do provedor** para garantir que o circuito seja totalmente provisionado pelo provedor de conectividade antes de continuar.

   Se seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode solicitar que seu provedor de conectividade habilite o emparelhamento da Microsoft para você. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se o provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, prossiga com estas etapas.

   **Status do provedor de circuito: não provisionado**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Status do provedor de circuito: provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao roteador, uma vez que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao roteador, uma vez que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para links primários e secundários, você deve usar a mesma ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se você planeja enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional-** ASN do cliente: se você estiver anunciando prefixos que não estão registrados para o número as de emparelhamento, você pode especificar o número as para o qual eles são registrados.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * **Opcional-** Um hash MD5 se você optar por usar um.
3. Você pode selecionar o emparelhamento que deseja configurar, conforme mostrado no exemplo a seguir. Selecione a linha de peering da Microsoft.

   [![Selecionar a linha de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selecionar a linha de emparelhamento da Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure o peering da Microsoft. **Salve** a configuração depois de especificar todos os parâmetros. A imagem a seguir mostra uma configuração de exemplo:

   ![Configurar o emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft verifica se os ' prefixos públicos publicados ' e ' peer ASN ' (ou ' cliente ASN ') especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver obtendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá a mensagem "validação necessária". 
>
> Se você vir a mensagem "validação necessária", colete os documentos que mostram os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registro de roteamento e envie esses documentos para validação manual por abrindo um tíquete de suporte, conforme mostrado abaixo. 
>

   Se o circuito chegar a um estado de "validação necessária", você deverá abrir um tíquete de suporte para mostrar a prova de propriedade dos prefixos para nossa equipe de suporte. Você pode abrir um tíquete de suporte diretamente do portal, conforme mostrado no exemplo a seguir:

   ![Validação necessária-tíquete de suporte](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Depois que a configuração for aceita com êxito, você verá algo semelhante à imagem a seguir:

   ![Status de emparelhamento: configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Status de emparelhamento: configurado")]

### <a name="getmsft"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode exibir as propriedades do emparelhamento da Microsoft selecionando a linha para o emparelhamento.

[![Exibir Propriedades de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Ver propriedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Para atualizar a configuração de emparelhamento da Microsoft

Você pode selecionar a linha para o emparelhamento que deseja modificar e, em seguida, modificar as propriedades de emparelhamento e salvar suas modificações.

![Selecionar linha de emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Para excluir o emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento clicando no ícone excluir, conforme mostrado na imagem a seguir:

![Excluir emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar. 

   Se seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode pedir que seu provedor de conectividade habilite o emparelhamento privado do Azure para você. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, prossiga com as próximas etapas.

   **Status do provedor de circuito: não provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Status do provedor de circuito: provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

   * Uma sub-rede /30 para a ligação primária. A sub-rede não deve fazer parte de nenhum espaço de endereço reservado para redes virtuais. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao roteador, uma vez que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não deve fazer parte de nenhum espaço de endereço reservado para redes virtuais. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao roteador, uma vez que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para links primários e secundários, você deve usar a mesma ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Você pode usar um número AS privado para esse emparelhamento, exceto para o número de 65515 a 65520, inclusive.
   * Você deve anunciar as rotas do seu roteador de borda local para o Azure por meio do BGP ao configurar o emparelhamento privado.
   * **Opcional-** Um hash MD5 se você optar por usar um.
3. Selecione a linha de emparelhamento privado do Azure, conforme mostrado no exemplo a seguir:

   [![Selecionar a linha de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selecionar a linha de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure o peering privado. **Salve** a configuração depois de especificar todos os parâmetros.

   ![configurar o emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Depois que a configuração for aceita com êxito, você verá algo semelhante ao exemplo a seguir:

   ![emparelhamento privado salvo](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Para exibir detalhes de emparelhamento privado do Azure

Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

[![Exibir Propriedades de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Exibir Propriedades de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Para atualizar a configuração de emparelhamento privado do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering. Após a atualização, salve as alterações.

![atualizar emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Para excluir o emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento selecionando o ícone excluir, conforme mostrado na imagem a seguir:

> [!WARNING]
> Você deve garantir que todas as redes virtuais e conexões de Alcance Global ExpressRoute sejam removidas antes de executar este exemplo. 
> 
> 

![excluir emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Emparelhamento público do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito do ExpressRoute.

> [!Note]
> O emparelhamento público do Azure foi preterido para novos circuitos. Para obter mais informações, consulte [emparelhamento de ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Para exibir detalhes de emparelhamento público do Azure

Exiba as propriedades do emparelhamento público do Azure selecionando o emparelhamento.

### <a name="updatepublic"></a>Para atualizar a configuração de emparelhamento público do Azure

Selecione a linha para emparelhamento e, em seguida, modifique as propriedades de emparelhamento.

### <a name="deletepublic"></a>Para excluir o emparelhamento público do Azure

Remova a configuração de emparelhamento selecionando o ícone Excluir.

## <a name="next-steps"></a>Passos seguintes

Próxima etapa, [vincular uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
