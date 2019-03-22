---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ee5faedd4f59aa791424a1f178f0462922f21d28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094691"
---
Cada endpoint tem um *Porta pública* e uma *porta privada*:

* A porta pública é utilizada pelo balanceador de carga do Azure para escutar tráfego de entrada para a máquina virtual a partir da internet.
* A porta privada é utilizada pela máquina virtual para escutar tráfego de entrada, normalmente destinado a uma aplicação ou serviço em execução na máquina virtual.

Valores predefinidos para o protocolo IP e portas TCP ou UDP para rede bem conhecido protocolos são fornecidos quando criar pontos finais no portal do Azure. Para os pontos finais personalizados, especifique o protocolo correto de IP (TCP ou UDP) e as portas públicas e privadas. Para distribuir o tráfego de entrada aleatoriamente em várias máquinas virtuais, crie um conjunto com balanceamento de carga, que consiste de vários pontos de extremidade.

Depois de criar um ponto de extremidade, pode utilizar uma lista de controlo de acesso (ACL) para definir as regras que permitem ou negam o tráfego de entrada para a porta pública do ponto de extremidade com base no respetivo endereço IP de origem. No entanto, se a máquina virtual está numa rede virtual do Azure, utilize grupos de segurança de rede em vez disso. Para obter mais informações, consulte [sobre os grupos de segurança de rede](../articles/virtual-network/security-overview.md).

> [!NOTE]
> Configuração da firewall para máquinas virtuais do Azure é feita automaticamente para as portas associadas com pontos finais de conectividade remota que o Azure configura automaticamente. Para portas especificadas para todos os outros pontos de extremidade, nenhuma configuração é feita automaticamente a firewall da máquina virtual. Quando cria um ponto final para a máquina virtual, certifique-se de que a firewall da máquina virtual também permite que o tráfego para o protocolo e porta privada correspondente à configuração do ponto final. Para configurar a firewall, consulte a documentação ou a ajuda online para o sistema operativo em execução na máquina virtual.
>
>

## <a name="create-an-endpoint"></a>Criar um ponto final
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **máquinas virtuais**e, em seguida, selecione a máquina virtual que pretende configurar.

3. Selecione **pontos de extremidade** no **definições** grupo. O **pontos de extremidade** é apresentada a página, que apresenta uma lista de todos os pontos finais atuais para a máquina virtual. (Neste exemplo é para uma VM do Windows. Uma VM do Linux por predefinição mostrará um ponto final de SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Pontos finais](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. Na barra de comando acima das entradas de ponto de extremidade, selecione **adicionar**. O **adicionar ponto final** é apresentada a página.

5. Para **nome**, introduza um nome para o ponto final.

6. Para **protocolo**, escolha o **TCP** ou **UDP**.

7. Para **Porta pública**, introduza o número de porta para o tráfego de entrada da internet. 

8. Para **porta privada**, introduza o número de porta em que a máquina virtual está a escutar. Os números das portas públicas e privadas podem ser diferentes. Certifique-se de que a firewall na máquina virtual foi configurada para permitir o tráfego correspondente para o protocolo e porta privada.

9. Selecione **OK**.

O novo ponto final está listado na **pontos de extremidade** página.

![Criação do ponto final com êxito](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Gerir a ACL num ponto final
Para definir o conjunto de computadores que podem enviar tráfego, a ACL num ponto final pode restringir o tráfego com base no endereço IP de origem. Siga estes passos para adicionar, modificar ou remover uma ACL num ponto final.

> [!NOTE]
> Se o ponto final fizer parte de um conjunto com balanceamento de carga, quaisquer alterações efetuadas à ACL num ponto final são aplicadas a todos os pontos finais no conjunto.
>
>

Se a máquina virtual está numa rede virtual do Azure, utilize grupos de segurança de rede em vez de ACLs. Para obter mais informações, consulte [sobre os grupos de segurança de rede](../articles/virtual-network/security-overview.md).

1. Inicie sessão no Portal do Azure.

2. Selecione **máquinas virtuais**e, em seguida, selecione o nome da máquina virtual que pretende configurar.

3. Selecione **pontos de extremidade**. Na lista de pontos de extremidade, selecione o ponto final adequado. A lista ACL é na parte inferior da página.

   ![Especifique os detalhes ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Utilize linhas da lista para adicionar, eliminar, ou editar regras de uma ACL e alterar a sua ordem. O **sub-rede remota** valor é um intervalo de endereços IP para o tráfego de entrada da internet que o Balanceador de carga do Azure utiliza para permitir ou negar o tráfego com base no respetivo endereço IP de origem. Certifique-se de que especifique o intervalo de endereços IP no classless entre domínios (CIDR) formato de encaminhamento, também conhecido como formato de prefixo de endereço. Por exemplo, `10.1.0.0/8`.

   ![Nova entrada ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Pode utilizar regras para permitir o tráfego apenas de computadores específicos correspondentes aos seus computadores na internet ou para negar o tráfego de intervalos de endereço específico, conhecido.

As regras são avaliadas por ordem, começando com a primeira regra e terminando a última regra. Por conseguinte, regras devem ser ordenadas de menos restritivo para mais restritiva. Para obter mais informações, consulte [o que é uma lista de controlo de acesso de rede](../articles/virtual-network/virtual-networks-acl.md).
