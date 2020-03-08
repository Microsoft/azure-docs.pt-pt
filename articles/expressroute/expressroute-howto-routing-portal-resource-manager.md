---
title: 'Azure ExpressRoute: Configure peering'
description: Este artigo documenta os passos para criar e fornecer o expressRoute privado e o microsoft peering. Este artigo também demonstra como verificar o estado, atualizar ou apagar os pares de um circuito.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361725"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar um peering de um circuito do ExpressRoute

Este artigo ajuda-o a criar e gerir a configuração de encaminhamento para um circuito Descarregamento de Recursos Azure (ARM) ExpressRoute, utilizando o portal Azure. Também pode verificar o estado, update ou delete e desaprovisionar peerings para um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com o seu circuito, selecione um artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [O público espreita](about-public-peering.md)
> * [Vídeo - Peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Pode configurar o peering privado e a Microsoft a espreitar por um circuito ExpressRoute (o público azure é depreciado para novos circuitos). Os pares podem ser configurados em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e observações, consulte os domínios de [encaminhamento expressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o público, consulte o [público expressRoute.](about-public-peering.md)

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. Para configurar o epeering(s), o circuito ExpressRoute deve estar num estado aprovisionado e habilitado. 
* Se pretender utilizar uma chave partilhada/haxixe MD5, certifique-se de que o utiliza em ambos os lados do túnel e limite o número de caracteres alfanuméricos a um máximo de 25. Personagens especiais não são suportados. 

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si. 

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Contacte o seu fornecedor de serviços antes de configurar os peerings BGP.
> 
> 

## <a name="msft"></a>Peering da Microsoft

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de peering da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito. Para mais informações, consulte [Configure um filtro de rota para o peering da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Configure o circuito ExpressRoute. Verifique o **estado do Fornecedor** para garantir que o circuito está totalmente aprovisionado pelo fornecedor de conectividade antes de continuar.

   Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering da Microsoft para. Nesse caso, não terá de seguir as instruções listadas nas próximas secções. No entanto, se o seu fornecedor de conectividade não conseguir encaminhamento para si, depois de criar o seu circuito, proceda a estes passos.

   **Circuito - Estatuto do fornecedor: Não provisionado**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuito - Estatuto do fornecedor: Provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional -** Cliente ASN: Se estiver a publicitar prefixos que não estejam registados no número AS de peering, pode especificar o número AS a que estão registados.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * **Opcional -** Um haxixe MD5 se optar por usar um.
3. Pode selecionar o peering que pretende configurar, conforme mostrado no exemplo a seguir. Selecione a linha de peering da Microsoft.

   [![Selecione a linha de peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selecione a linha de peering da Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure o peering da Microsoft. **Guarde** a configuração depois de ter especificado todos os parâmetros. A imagem que se segue mostra uma configuração de exemplo:

   ![configurar o peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft verifica se os "prefixos públicos anunciados" e "Peer ASN" (ou 'Customer ASN') são-lhe atribuídos no Registo de Encaminhamento de Internet. Se estiver a receber os prefixos públicos de outra entidade e se a atribuição não for registada com o registo de encaminhamento, a validação automática não estará concluída e exigirá validação manual. Se a validação automática falhar, verá a mensagem 'Validação necessária'. 
>
> Se vir a mensagem 'Validação necessária', recolha os documentos que mostrem que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como proprietária dos prefixos no registo de encaminhamento e submeta estes documentos para validação manual por abertura de um bilhete de apoio como mostrado abaixo. 
>

   Se o seu circuito chegar a um estado de 'Validação necessária', você deve abrir um bilhete de apoio para mostrar a prova de propriedade dos prefixos para a nossa equipe de suporte. Pode abrir um pedido de suporte diretamente a partir do portal, conforme mostrado no exemplo a seguir:

   ![Validação Necessária - bilhete de apoio](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Depois de a configuração ter sido aceite com sucesso, verá algo semelhante à seguinte imagem:

   ![Estado de observação: Configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Estado de observação: Configurado")]

### <a name="getmsft"></a>Para ver detalhes de peering da Microsoft

Pode ver as propriedades da Microsoft a espreitar selecionando a linha para o peering.

[![Ver propriedades de peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Ver propriedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Para atualizar a configuração de peering da Microsoft

Pode selecionar a linha para o olhar que pretende modificar, depois modificar as propriedades de observação e guardar as suas modificações.

![Selecione linha de observação](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Para eliminar o peering da Microsoft

Pode remover a configuração de observação clicando no ícone de exclusão, como mostra a seguinte imagem:

![Eliminar o peering](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Peering privado azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar. 

   Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering privado do Azure para. Nesse caso, não terá de seguir as instruções listadas nas próximas secções. No entanto, se o seu fornecedor de conectividade não conseguir encaminhamento para si, depois de criar o seu circuito, proceda aos próximos passos.

   **Circuito - Estatuto do fornecedor: Não provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuito - Estatuto do fornecedor: Provisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

   * Uma sub-rede /30 para a ligação primária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais. Esta sub-rede vai atribuir o primeiro endereço IP pode ser utilizado para o router como a Microsoft utiliza o segundo IP pode ser utilizado para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Para obter ligações primária e secundária tem de utilizar o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Você pode usar um número as privado para este epeering, exceto o número de 65515 a 65520, inclusive.
   * Você deve anunciar as rotas do seu router Edge no local para Azure via BGP quando configurar o peering privado.
   * **Opcional -** Um haxixe MD5 se optar por usar um.
3. Selecione a linha de observação privada Azure, como mostra o seguinte exemplo:

   [![Selecione a linha de observação privada](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selecione a linha de observação privada")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure o peering privado. **Guarde** a configuração depois de ter especificado todos os parâmetros.

   ![Configure o peering privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Depois da configuração ter sido aceite com êxito, verá algo semelhante ao seguinte exemplo:

   ![salvos espreitando privado](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Para ver detalhes de espreitar privados de Azure

Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

[![Ver propriedades de pares privados](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Ver propriedades de pares privados")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Para atualizar a configuração de peering privado azure

Pode selecionar a linha para peering e modificar as propriedades do peering. Depois de atualizar, guarde as suas alterações.

![Atualizar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Para apagar o peering privado Azure

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado na imagem seguinte:

> [!WARNING]
> Tem de se certificar de que todas as ligações de alcance Global do ExpressRoute e de redes virtuais são removidas antes de executar este exemplo. 
> 
> 

![eliminar um peering privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Passos seguintes

Próximo passo, [Ligue um VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
