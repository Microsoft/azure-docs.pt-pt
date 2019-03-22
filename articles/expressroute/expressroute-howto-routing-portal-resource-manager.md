---
title: 'Configure o peering para um circuito - ExpressRoute: Azure | Microsoft Docs'
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 401827f3fa5eb6d364d0f133d8cd672cf8968031
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103184"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar um peering de um circuito do ExpressRoute

Este artigo ajuda-o a criar e gerir a configuração de encaminhamento de um circuito do ExpressRoute no modelo de implementação do Resource Manager com o portal do Azure. Também pode verificar o estado, update ou delete e desaprovisionar peerings para um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com o seu circuito, selecione um artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo - peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [acerca dos circuitos e peerings](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute tem de ser num Estado aprovisionado e ativado para que possa ser capaz de executar os cmdlets nas próximas seções.
* Se planeia utilizar um hash de chave/MD5 partilhado, certifique-se de que para utilizá-lo em ambos os lados do túnel e limitar o número de carateres para um máximo de 25.

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

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de prosseguir. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering da Microsoft para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes.

   ![listar o peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: Tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional –** cliente ASN: Se estiver a anunciar prefixos que não estão registados para o peering número, pode especificar o número as no qual estão registados.
   * Nome do registo de encaminhamento: Pode especificar o RIR / IRR em relação aos quais o número e os prefixos são registados.
   * **Opcional –** um hash MD5 se optar por utilizar um.
3. Pode selecionar o peering que pretende configurar, conforme mostrado no exemplo a seguir. Selecione a linha de peering da Microsoft.

   ![Selecione a linha de peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configure o peering da Microsoft. A imagem seguinte mostra um exemplo de configuração:

   ![configurar o peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Guarde a configuração depois de especificar todos os parâmetros.

   Se o seu circuito chegar a uma "validação necessária" Estado (como mostrado na imagem), tem de abrir um pedido de suporte para demonstrar prova de titularidade dos prefixos à nossa equipa de suporte.

   ![Guardar a configuração do peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

   Pode abrir um pedido de suporte diretamente a partir do portal, conforme mostrado no exemplo a seguir:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Depois da configuração ter sido aceite com êxito, verá algo semelhante à seguinte imagem:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Para ver os detalhes do peering Microsoft

Pode ver as propriedades do peering público do Azure ao selecionar o peering.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Pode selecionar a linha para peering e modificar as propriedades do peering.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>A eliminar peering da Microsoft

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado na imagem seguinte:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering privado do Azure para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes.

   ![list](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

   * Uma sub-rede /30 para a ligação primária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode usar uma privada como número para este peering, exceto para o número do 65515 ao 65520, inclusivamente.
   * **Opcional –** um hash MD5 se optar por utilizar um.
3. Selecione a linha de peering privado do Azure, conforme mostrado no exemplo a seguir:

   ![privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configure o peering privado. A imagem seguinte mostra um exemplo de configuração:

   ![Configure o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Guarde a configuração depois de especificar todos os parâmetros. Depois da configuração ter sido aceite com êxito, verá algo semelhante ao seguinte exemplo:

   ![guardar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Para ver os detalhes de peering privados do Azure

Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

![ver o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Para atualizar a configuração de peering privado do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering.

![Atualizar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado na imagem seguinte:

> [!WARNING]
> Tem de se certificar de que todas as ligações de alcance Global do ExpressRoute e de redes virtuais são removidas antes de executar este exemplo. 
> 
> 

![eliminar um peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Peering público do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de prosseguir. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering público do Azure para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes.

   ![listar o peering público](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering público do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router. 
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * **Opcional –** um hash MD5 se optar por utilizar um.
3. Selecione a linha de peering pública do Azure, conforme mostrado na imagem seguinte:

   ![Selecione a linha de peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configure o peering público. A imagem seguinte mostra um exemplo de configuração:

   ![Configure o peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Guarde a configuração depois de especificar todos os parâmetros. Depois da configuração ter sido aceite com êxito, verá algo semelhante ao seguinte exemplo:

   ![Guardar a configuração do peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Para ver os detalhes de peering públicos do Azure

Pode ver as propriedades do peering público do Azure ao selecionar o peering.

![Ver propriedades do peering públicas](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Para atualizar a configuração do peering público do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering.

![Selecione a linha de peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Para eliminar o peering público do Azure

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado no exemplo a seguir:

![eliminar o peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>Passos Seguintes

Passo seguinte, [ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
