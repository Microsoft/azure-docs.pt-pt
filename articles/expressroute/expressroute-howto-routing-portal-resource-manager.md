---
title: 'Tutorial: Configure peering para o circuito ExpressRoute - Portal Azure'
description: Este tutorial mostra-lhe como criar e providenciar ExpressRoute privado e Microsoft espreitando usando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/26/2020
ms.author: duau
ms.openlocfilehash: eb55e4633ef64bee0577b1c1defba27dad24a3b7
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515964"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Tutorial: Criar e modificar o espreitamento para um circuito ExpressRoute utilizando o portal Azure

Este tutorial mostra-lhe como criar e gerir a configuração de encaminhamento para um circuito ExpressRoute do Gestor de Recursos Azure utilizando o portal Azure. Também pode verificar o estado, a atualização ou eliminação e desprovisionamento de um circuito ExpressRoute. Se pretender utilizar um método diferente para trabalhar com o seu circuito, selecione um artigo da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Olhando público](about-public-peering.md)
> * [Vídeo - Peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - Microsoft a espreitar](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Pode configurar o espreitamento privado e a Microsoft a espreitar por um circuito ExpressRoute (o espremiamento público Azure é precotado para novos circuitos). Os seus pares podem ser configurados em qualquer ordem que escolherem. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e observação, consulte [os domínios de encaminhamento ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre olhando o público, consulte [o público ExpressRoute.](about-public-peering.md)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Configurar, atualizar e eliminar a Microsoft a espreitar por um circuito
> - Configurar, atualizar e apagar Azure private espreitar por um circuito

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que reviu as seguintes páginas antes de iniciar a configuração:
    * [Pré-requisitos](expressroute-prerequisites.md) 
    * [Requisitos do encaminhamento](expressroute-routing.md)
    * [Fluxos de trabalho](expressroute-workflows.md)
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de continuar. Para configurar o(s) de espreitar, o circuito ExpressRoute deve estar num estado aprovisionado e habilitado. 
* Se pretender utilizar uma chave partilhada/hash MD5, certifique-se de que utiliza a chave em ambos os lados do túnel. O limite é um máximo de 25 caracteres alfanuméricos. Personagens especiais não são suportados. 

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos da Camada 3 (normalmente um IPVPN, como o MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si. 

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Consulte o seu fornecedor de serviços antes de configurar os seus pares BGP.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Peering da Microsoft

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de espreitar a Microsoft para um circuito ExpressRoute.

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para o microsoft espreitar.](how-to-routefilter-powershell.md)
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Configure o circuito ExpressRoute. Verifique o **estado do Fornecedor** para garantir que o circuito é totalmente a provisionado pelo fornecedor de conectividade antes de continuar.

   Se o seu fornecedor de conectividade oferecer serviços geridos da Camada 3, pode pedir ao seu fornecedor de conectividade para ativar o seu olhar pela Microsoft. Não precisa de seguir as instruções listadas nas próximas secções. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, continue com estes passos.

   **Circuito - Estatuto do fornecedor: Não previsto**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Screenshot que mostra a página de visão geral para o Circuito de Demonstração ExpressRoute com uma caixa vermelha realçando o estado do Fornecedor definido para Não provisionado":::

   **Circuito - Estatuto do fornecedor: Provisionado**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Screenshot que mostra a página de visão geral para o Circuito de Demonstração ExpressRoute com uma caixa vermelha destacando o estado do Provedor definido para Provisionado":::

2. Configure o peering da Microsoft para o circuito. Certifique-se de que tem as seguintes informações antes de continuar.

   * Um par de sub-redes /30 propriedade de si e registado num RIR/IRR. Estes devem ser prefixos IPv4 públicos válidos. Uma sub-rede será usada para a ligação primária, enquanto a outra será usada para a ligação secundária. A partir de cada uma destas sub-redes, irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Ambos os links primários e secundários devem utilizar o mesmo ID VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: Você fornece uma lista de todos os prefixos que pretende anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgula. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional -** Cliente ASN: Se estiver a publicitar prefixos não registados no número AS, pode especificar o número AS em que estão registados.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * **Opcional -** Um haxixe MD5 se optar por usar um.
3. Pode selecionar o espreguite que deseja configurar, como mostra o exemplo a seguir. Selecione a linha de peering da Microsoft.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Selecione a linha de observação da Microsoft":::

4. Configure o peering da Microsoft. **Guarde** a configuração depois de especificar todos os parâmetros. A imagem a seguir mostra uma configuração de exemplo:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Configure a validação de peering da Microsoft necessária":::

> [!IMPORTANT]
> A Microsoft verifica se os prefixos públicos anunciados especificados e 'Peer ASN' (ou 'Cliente ASN') são-lhe atribuídos no Registo de Encaminhamento de Internet. Se estiver a receber os prefixos públicos de outra entidade e se a atribuição não for registada com o registo de encaminhamento, a validação automática não será completa e exigirá validação manual. Se a validação automática falhar, verá a mensagem "Validação necessária". 
>
> Se vir a mensagem 'Validação necessária', recolha os documentos(s) que mostram que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registo de encaminhamento e submeta esses documentos para validação manual através da abertura de um bilhete de apoio. 
>

   Se o seu circuito chegar a um estado de "Validação necessária", tem de abrir um bilhete de apoio para mostrar a comprovativo da propriedade dos prefixos à nossa equipa de apoio. Pode abrir um bilhete de apoio diretamente a partir do portal, como mostra o seguinte exemplo:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Validação Necessária - bilhete de apoio":::

5. Depois de a configuração ter sido aceite com sucesso, verá algo semelhante à seguinte imagem:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Estado de perspíura: Configurado":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Para ver os detalhes do peering da Microsoft

Pode ver as propriedades da Microsoft a espreitar selecionando a linha para o espreitamento.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Ver propriedades de observação da Microsoft":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Pode selecionar a linha para o espreitamento que pretende modificar, modificar as propriedades de espreitar e guardar as suas modificações.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Selecione a linha de espreitar":::

## <a name="azure-private-peering"></a><a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de observação privada Azure para um circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar. 

   Se o seu fornecedor de conectividade oferecer serviços geridos da Camada 3, pode pedir ao seu fornecedor de conectividade para ativar o seu olhar privado Azure. Não precisa de seguir as instruções listadas nas próximas secções. No entanto, se o seu fornecedor de conectividade não conseguir o encaminhamento para si, depois de criar o seu circuito, continue com os próximos passos.

   **Circuito - Estatuto do fornecedor: Não previsto**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Screenshot mostrando a página de visão geral para o Circuito de Demonstração ExpressRoute com uma caixa vermelha realçando o estado do Fornecedor que é definido para Não provisionado":::

   **Circuito - Estatuto do fornecedor: Provisionado**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Screenshot mostrando a página de visão geral para o Circuito de Demonstração ExpressRoute com uma caixa vermelha realçando o estado do Fornecedor que está definido para Provisionado":::

2. Configure o peering privado do Azure para o circuito. Certifique-se de que tem os seguintes itens antes de continuar com os próximos passos:

   * Um par de sub-redes /30 propriedade de si e registado num RIR/IRR. Uma sub-rede será usada para a ligação primária, enquanto a outra será usada para a ligação secundária. A partir de cada uma destas sub-redes, irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router. Tem três opções para este par de sub-redes:
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN. Ambos os links primários e secundários devem utilizar o mesmo ID VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número de AS privado para este espreitê-lo, exceto para o número de 65515 a 65520, inclusive.
   * Deve anunciar as rotas do seu router Edge para Azure via BGP quando configurar o seu persp de observação privado.
   * **Opcional -** Um haxixe MD5 se optar por usar um.
3. Selecione a linha de observação privada Azure, como mostra o seguinte exemplo:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Selecione a linha de observação privada":::

4. Configure o peering privado. **Guarde** a configuração depois de especificar todos os parâmetros.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Configurar o peering privado":::

5. Depois de a configuração ter sido aceite com sucesso, vê algo semelhante ao seguinte exemplo:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Poupou o espreitamento privado":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Para ver os detalhes do peering privado do Azure

Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Ver propriedades de observação privadas":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Para atualizar a configuração do peering privado do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering. Depois de atualizar, guarde as suas alterações.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Atualizar o espreitamento privado":::

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Para eliminar o peering da Microsoft

Pode remover a configuração de observação da Microsoft clicando no persitivo e selecionando **Eliminar** como mostrado na seguinte imagem:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Eliminar o peering da Microsoft":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de observação privada clicando no persitivo e selecionando **Eliminar** como mostrado na seguinte imagem:

> [!WARNING]
> Deve certificar-se de que todas as redes virtuais e as ligações ExpressRoute Global Reach são removidas antes de executar esta operação. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Apagar o espreitamento privado":::

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o Azure em privado, pode criar uma porta de entrada ExpressRoute para ligar uma rede virtual ao circuito. 

> [!div class="nextstepaction"]
> [Configurar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
