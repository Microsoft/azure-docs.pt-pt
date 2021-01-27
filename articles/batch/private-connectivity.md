---
title: Utilizar pontos finais privados com contas do Azure Batch
description: Aprenda a ligar-se em privado a uma conta Azure Batch utilizando pontos finais privados.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920008"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Utilizar pontos finais privados com contas do Azure Batch

Por padrão, [as contas do Azure Batch](accounts.md) têm um ponto final público e são acessíveis ao público. O serviço Batch oferece a capacidade de criar contas privadas do Batch, desativando o acesso à rede pública.

Utilizando [o Azure Private Link,](../private-link/private-link-overview.md)pode ligar-se a uma conta Azure Batch através de um [ponto final privado.](../private-link/private-endpoint-overview.md) O ponto final privado é um conjunto de endereços IP privados numa sub-rede dentro da sua rede virtual. Em seguida, pode limitar o acesso a uma conta Azure Batch em endereços IP privados.

O Private Link permite que os utilizadores acedam a uma conta Azure Batch a partir da rede virtual ou de qualquer rede virtual. Os recursos mapeados para private link também são acessíveis no local através de peering privado através de VPN ou [Azure ExpressRoute](../expressroute/expressroute-introduction.md). Pode ligar-se a uma conta Azure Batch configurada com o Private Link utilizando o [método de aprovação automática ou manual.](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)

> [!IMPORTANT]
> O apoio à conectividade privada em Azure Batch está atualmente disponível para todas as regiões, exceto Alemanha Central, Alemanha Nordeste, China Leste, China East 2, China Norte e China Norte 2.

Este artigo descreve os passos para criar uma conta privada do Batch e acedê-la através de um ponto final privado.

## <a name="azure-portal"></a>Portal do Azure

Utilize os seguintes passos para criar uma conta privada do Lote utilizando o portal Azure:

1. A partir do painel de recursos Criar um painel de **recursos,** escolha **o Serviço de Lote** e, em seguida, selecione **Criar**.
2. Introduza o nome da conta de subscrição, grupo de recursos, região e lote no **separador Básicos** e, em seguida, selecione **Seguinte: Avançado**.
3. No separador **Avançado,** desative o **acesso da rede pública** a **Deficientes**.
4. Em **Definições**, selecione **ligações de ponto final privado e,** em seguida, selecione **+ ponto final privado**.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Conexões de ponto final privado":::
5. No painel **Basics,** introduza ou selecione a subscrição, grupo de recursos, nome de recurso de ponto final privado e detalhes da região, selecione **Next: Resource**.
6. No painel **de recursos,** desaprote o **tipo de recurso** paraMicrosoft.Batcontas **ch/batchAccounts**. Selecione a conta batch privada a que pretende aceder e, em seguida, selecione **Seguinte: Configuração**.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Criar um ponto final privado - Painel de recursos":::
7. No painel **de configuração,** introduza ou selecione esta informação:
   - **Rede virtual**: Selecione a sua rede virtual.
   - **Sub-rede**: Selecione a sua sub-rede.
   - **Integre-se com a zona privada do DNS**: Selecione **Sim**. Para se ligar em privado com o seu ponto final privado, precisa de um registo DNS. Recomendamos que integre o seu ponto de terminação privado com uma zona privada de DNS. Também pode utilizar os seus próprios servidores DNS ou criar registos DNS utilizando os ficheiros anfitriões nas suas máquinas virtuais.
   - **Zona privada do DNS**: Selecione privatelink. \<region\> . batch.azure.com. A zona privada de DNS é determinada automaticamente. Não pode mudá-lo usando o portal Azure.
8. Selecione **Rever + criar,** em seguida, esperar pelo Azure para validar a sua configuração.
9. Quando vir a mensagem **A validação passou**, selecione **Criar**.

Depois de o ponto final privado ser a provisionado, pode aceder à conta Batch a partir de VMs na mesma rede virtual utilizando o ponto final privado.

> [!IMPORTANT]
> A realização de operações fora da rede virtual onde o ponto final privado é aprovisionado resultará numa mensagem "AutorizaçãoFailure" no Portal Azure.

Para ver o endereço IP a partir do portal Azure:

1. Selecione **Todos os recursos**.
2. Procure o ponto final privado que criou anteriormente.
3. Selecione o **separador 'Vista Geral'** para ver as definições de DNS e endereços IP.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="Definições privadas de DNS e endereços IP":::

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Ao criar uma conta Batch utilizando o [modelo Azure Resource Manager,](quick-create-template.md)modifique o modelo para definir **o PublicNetworkAccess** para **Desativado** como mostrado abaixo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>Configurar zonas DNS

Use uma [zona de DNS privada](../dns/private-dns-privatednszone.md) dentro da sub-rede onde criou o ponto final privado. Configure os pontos finais de modo a que cada endereço IP privado seja mapeado para uma entrada DE DNS.

Quando estiver a criar o ponto final privado, pode integrá-lo com uma [zona privada de DNS](../dns/private-dns-privatednszone.md) em Azure. Se optar por utilizar um [domínio personalizado,](../dns/dns-custom-domain.md)deve configurá-lo para adicionar registos DNS para todos os endereços IP privados reservados para o ponto final privado.

## <a name="pricing"></a>Preços

Para obter detalhes sobre os custos relacionados com os pontos finais privados, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Limitações e boas práticas atuais

Ao criar a sua conta privada Batch, tenha em mente o seguinte:

- Os recursos de ponto final privado devem ser criados na mesma subscrição que a conta Batch.
- Para eliminar a ligação privada, tem de eliminar o recurso de ponto final privado.
- Uma vez criada uma conta Batch com acesso à rede pública, não é possível alterá-la apenas para acesso privado.
- Os registos DNS na zona privada do DNS não são removidos automaticamente quando elimina um ponto final privado ou quando remove uma região da conta Batch. Deve remover manualmente os registos DNS antes de adicionar um novo ponto final privado ligado a esta zona privada de DNS. Se não limpar os registos do DNS, podem ocorrer problemas inesperados de planos de dados, tais como interrupções de dados em regiões adicionadas após a remoção do ponto final privado ou remoção da região.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar piscinas de Lote em redes virtuais.](batch-virtual-network.md)
- Saiba como [criar piscinas de Lote sem endereços IP públicos](batch-pool-no-public-ip-address.md)
- Saiba como [criar piscinas de lote com endereços IP públicos especificados.](create-pool-public-ip.md)
- Saiba mais sobre [a Azure Private Link](../private-link/private-link-overview.md).
