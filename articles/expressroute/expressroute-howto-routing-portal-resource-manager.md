---
title: 'Azure ExpressRoute: configurar o emparelhamento'
description: Este artigo documenta as etapas para criar e provisionar o emparelhamento privado e o Microsoft ExpressRoute. Este artigo também demonstra como verificar o status, atualizar ou excluir emparelhamentos de um circuito.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436962"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar um peering de um circuito do ExpressRoute

Este artigo ajuda você a criar e gerenciar a configuração de roteamento para um circuito de ExpressRoute Azure Resource Manager (ARM), usando o portal do Azure. Também pode verificar o estado, update ou delete e desaprovisionar peerings para um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com o seu circuito, selecione um artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Emparelhamento público](about-public-peering.md)
> * [Vídeo - peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Você pode configurar emparelhamento privado e emparelhamento da Microsoft para um circuito do ExpressRoute (o emparelhamento público do Azure é preterido para novos circuitos). Os emparelhamentos podem ser configurados em qualquer ordem escolhida. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [domínios de encaminhamento do ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o emparelhamento público, consulte [emparelhamento público do ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. Para configurar o emparelhamento, o circuito do ExpressRoute deve estar em um estado provisionado e habilitado. 
* Se você planeja usar um hash de chave compartilhada/MD5, certifique-se de usá-lo em ambos os lados do túnel e limitar o número de caracteres alfanuméricos a um máximo de 25. Não há suporte para caracteres especiais. 

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si. 

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Contacte o seu fornecedor de serviços antes de configurar os peerings BGP.
> 
> 

## <a name="msft"></a>Peering da Microsoft

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de peering da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para peering da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Configure o circuito ExpressRoute. Verifique o **status do provedor** para garantir que o circuito seja totalmente provisionado pelo provedor de conectividade antes de continuar.

   Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering da Microsoft para. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se o provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, prossiga com estas etapas.

   **Status do provedor de circuito: não provisionado**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Status do provedor de circuito: provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional –** cliente ASN: Se estiver a anunciar prefixos que não estão registados para o peering número, pode especificar o número as no qual estão registados.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * **Opcional –** um hash MD5 se optar por utilizar um.
3. Pode selecionar o peering que pretende configurar, conforme mostrado no exemplo a seguir. Selecione a linha de peering da Microsoft.

   [![Selecionar a linha de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selecionar a linha de emparelhamento da Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure o peering da Microsoft. **Salve** a configuração depois de especificar todos os parâmetros. A imagem a seguir mostra uma configuração de exemplo:

   ![configurar o peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft verifica se os ' prefixos públicos publicados ' e ' peer ASN ' (ou ' cliente ASN ') especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver obtendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá a mensagem "validação necessária". 
>
> Se você vir a mensagem "validação necessária", colete os documentos que mostram os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registro de roteamento e envie esses documentos para validação manual por abrindo um tíquete de suporte, conforme mostrado abaixo. 
>

   Se o circuito chegar a um estado de "validação necessária", você deverá abrir um tíquete de suporte para mostrar a prova de propriedade dos prefixos para nossa equipe de suporte. Pode abrir um pedido de suporte diretamente a partir do portal, conforme mostrado no exemplo a seguir:

   ![Validação necessária-tíquete de suporte](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Depois que a configuração for aceita com êxito, você verá algo semelhante à imagem a seguir:

   ![Status de emparelhamento: configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Status de emparelhamento: configurado")]

### <a name="getmsft"></a>Para ver os detalhes do peering Microsoft

Você pode exibir as propriedades do emparelhamento da Microsoft selecionando a linha para o emparelhamento.

[![Exibir Propriedades de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Ver propriedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Você pode selecionar a linha para o emparelhamento que deseja modificar e, em seguida, modificar as propriedades de emparelhamento e salvar suas modificações.

![Selecionar linha de emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>A eliminar peering da Microsoft

Você pode remover a configuração de emparelhamento clicando no ícone excluir, conforme mostrado na imagem a seguir:

![Excluir emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar. 

   Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering privado do Azure para. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, prossiga com as próximas etapas.

   **Status do provedor de circuito: não provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Status do provedor de circuito: provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

   * Uma sub-rede /30 para a ligação primária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Você pode usar um número AS privado para esse emparelhamento, exceto para o número de 65515 a 65520, inclusive.
   * Você deve anunciar as rotas do seu roteador de borda local para o Azure por meio do BGP ao configurar o emparelhamento privado.
   * **Opcional –** um hash MD5 se optar por utilizar um.
3. Selecione a linha de emparelhamento privado do Azure, conforme mostrado no exemplo a seguir:

   [![Selecionar a linha de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selecionar a linha de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure o peering privado. **Salve** a configuração depois de especificar todos os parâmetros.

   ![Configure o peering privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Depois da configuração ter sido aceite com êxito, verá algo semelhante ao seguinte exemplo:

   ![emparelhamento privado salvo](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Para ver os detalhes de peering privados do Azure

Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

[![Exibir Propriedades de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Exibir Propriedades de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Para atualizar a configuração de peering privado do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering. Após a atualização, salve as alterações.

![Atualizar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado na imagem seguinte:

> [!WARNING]
> Tem de se certificar de que todas as ligações de alcance Global do ExpressRoute e de redes virtuais são removidas antes de executar este exemplo. 
> 
> 

![eliminar um peering privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Passos seguintes

Passo seguinte, [ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
