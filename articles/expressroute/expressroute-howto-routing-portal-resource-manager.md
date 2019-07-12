---
title: 'Configure o peering para um circuito - ExpressRoute: Azure | Microsoft Docs'
description: Este artigo documenta os passos para criar e aprovisionar privado do ExpressRoute e o peering da Microsoft. Este artigo também demonstra como verificar o estado, atualizar ou eliminar peerings para um circuito.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 08d8103c4b35148a87d347e31b11c7c8c968598b
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622333"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar um peering de um circuito do ExpressRoute

Este artigo ajuda-o a criar e gerir a configuração de encaminhamento de um circuito do ExpressRoute do Azure Resource Manager (ARM), com o portal do Azure. Também pode verificar o estado, update ou delete e desaprovisionar peerings para um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com o seu circuito, selecione um artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo - peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Pode configurar privado do Azure e peering da Microsoft para um circuito do ExpressRoute (peering público do Azure foi preterido para os novos circuitos). Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [acerca dos circuitos e peerings](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. Para configurar o peering (s), o circuito do ExpressRoute tem de ser num Estado aprovisionado e ativado. 
* Se planeia utilizar um hash MD5/chave partilhado, certifique-se de que para utilizá-lo em ambos os lados do túnel e limitar o número de carateres de alfanuméricos para um máximo de 25. Não são suportados carateres especiais. 

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

1. Configure o circuito ExpressRoute. Verifique os **estado do fornecedor** para se certificar de que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de prosseguir.

   Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering da Microsoft para. Nesse caso, não terá de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, prossiga com estes passos.

   **Circuito - estado do fornecedor: Não aprovisionado**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Estado do fornecedor: Não aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuito - estado do fornecedor: aprovisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Estado do fornecedor = aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
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

   [![Selecione a linha de peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "selecione a linha de peering da Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure o peering da Microsoft. **Guardar** a configuração depois de especificar todos os parâmetros. A imagem seguinte mostra um exemplo de configuração:

   ![configurar o peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

   Se o seu circuito chegar a uma "validação necessária" de estado, tem de abrir um pedido de suporte para demonstrar prova de titularidade dos prefixos à nossa equipa de suporte. Pode abrir um pedido de suporte diretamente a partir do portal, conforme mostrado no exemplo a seguir:

   ![Validação necessária - pedido de suporte](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Depois da configuração ter sido aceite com êxito, verá algo semelhante à seguinte imagem:

   ![Estado do peering: Configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "estado do Peering: Configurado")]

### <a name="getmsft"></a>Para ver os detalhes do peering Microsoft

Pode ver as propriedades do peering selecionando a linha para o peering da Microsoft.

[![Ver propriedades peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "ver propriedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Pode selecionar a linha para o peering que pretende modificar, modificar as propriedades do peering e salvar suas modificações.

![Selecione a linha de peering](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>A eliminar peering da Microsoft

Pode remover a configuração do peering clicando no ícone Eliminar, conforme mostrado na imagem seguinte:

![Eliminar um peering](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar. 

   Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering privado do Azure para. Nesse caso, não terá de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, prossiga com os passos seguintes.

   **Circuito - estado do fornecedor: Não aprovisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Estado do fornecedor = não aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuito - estado do fornecedor: aprovisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Estado do fornecedor = aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

   * Uma sub-rede /30 para a ligação primária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode usar uma privada como número para este peering, exceto para o número do 65515 ao 65520, inclusivamente.
   * Tem anuncia as rotas do seu router de limite no local para o Azure através do BGP ao configurar o peering privado.
   * **Opcional –** um hash MD5 se optar por utilizar um.
3. Selecione a linha de peering privada do Azure, conforme mostrado no exemplo a seguir:

   [![Selecione a linha de peering privada](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "selecione a linha de peering privada")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure o peering privado. **Guardar** a configuração depois de especificar todos os parâmetros.

   ![Configure o peering privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Depois da configuração ter sido aceite com êxito, verá algo semelhante ao seguinte exemplo:

   ![guardar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Para ver os detalhes de peering privados do Azure

Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

[![Ver propriedades do peering privadas](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "ver propriedades do peering privadas")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Para atualizar a configuração de peering privado do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering. Depois de atualizar, guarde as alterações.

![Atualizar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado na imagem seguinte:

> [!WARNING]
> Tem de se certificar de que todas as ligações de alcance Global do ExpressRoute e de redes virtuais são removidas antes de executar este exemplo. 
> 
> 

![eliminar um peering privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Peering público do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito do ExpressRoute.

> [!Note]
> Peering público do Azure foi preterido para circuitos de novo. Para obter mais informações, consulte [peering de ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Para ver os detalhes de peering públicos do Azure

Ver as propriedades do peering público do Azure ao selecionar o peering.

### <a name="updatepublic"></a>Para atualizar a configuração do peering público do Azure

Selecione a linha para peering e modificar as propriedades do peering.

### <a name="deletepublic"></a>Para eliminar o peering público do Azure

Remova a configuração do peering selecionando o ícone Eliminar.

## <a name="next-steps"></a>Passos seguintes

Passo seguinte, [ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
