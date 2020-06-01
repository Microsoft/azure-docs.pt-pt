---
title: Modelo de ARM de endpoint privado Azure
description: Saiba mais sobre a Azure Private Link
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: a91415e7e3d91c2950cc4df2235c3d58df284cc0
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235975"
---
# <a name="create-a-private-endpoint---resource-manager-template"></a>Criar um ponto final privado - Modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos para criar um ponto final privado.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também pode completar este quickstart utilizando o [portal Azure](create-private-endpoint-portal.md), [Azure PowerShell,](create-private-endpoint-powershell.md)ou [Azure CLI](create-private-endpoint-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-private-endpoint"></a>Criar um ponto final privado

este modelo cria um ponto final privado para um servidor Azure SQL.

### <a name="review-the-template"></a>Rever o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-private-endpoint-sql/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : ponto final privado para aceder privadamente ao servidor Azure Sql
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : usado para resolver o endereço IP do ponto final privado
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : Para associar ponto final privado a uma zona private Dns
- [**Microsoft.Sql/servidores**](/azure/templates/microsoft.sql/servers) : Servidor Azure Sql com a base de dados de amostras
- [**Microsoft.Sql/servidores/bases de dados**](/azure/templates/microsoft.sql/servers/databases) : Base de dados de amostras
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Rede Virtual onde o Ponto Final Privado é implantado
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : Endereço IP público para aceder à máquina virtual
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : Máquina virtual para testar a ligação privada com o Ponto Final Privado ao servidor Azure Sql
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : Interface de rede para a máquina virtual

### <a name="deploy-the-template"></a>Implementar o modelo

Implementar o modelo do Gestor de Recursos para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria o ponto final privado, o servidor Azure SQL, a infraestrutura de rede e uma máquina virtual para validar.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos,
3. Digite o login e a palavra-passe do Administrador Sql
4. Digite o nome de utilizador e palavra-passe do administrador de máquina virtual.
5. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A colocação pode demorar 20 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

> [!NOTE]
> O modelo ARM gera um nome único para o recurso Virtual Machine myVm<b>{uniqueid}</b> e para o recurso sqlserver do servidor Azure SQL<b>{uniqueid},</b> substitua <b>{uniqueid}</b> pelo seu valor gerado.

### <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao VM _myVm{uniqueid}_ da internet da seguinte forma:

1. Na barra de pesquisa do portal, insira _myVm{uniqueid}_.

2. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

3. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop Protocol _(.rdp)_ e transfere-o para o computador.

4. Abra o ficheiro.rdp \* descarregado.

   a. Se lhe for pedido, selecione **Ligar**.

   b. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

      > [!NOTE]
      > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

5. Selecione **OK**.

6. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

7. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.

### <a name="access-sql-database-server-privately-from-the-vm"></a>Aceder ao SqL Database Server em privado a partir do VM

Nesta secção, irá ligar-se ao SqL Database Server a partir do VM utilizando o Ponto Final Privado.

1.  No Ambiente de Trabalho Remoto do _myVM{uniqueid}_, abra o PowerShell.
2.  Insira nslookup sqlserver{uniqueid}.database.windows.net   Receberá uma mensagem semelhante a esta:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Instalar o SQL Server Management Studio
4.  Em Ligar ao servidor, insira ou selecione esta informação: Tipo de servidor: Selecione o motor de base de dados.
    Nome do servidor: Selecione sqlserver{uniqueid}.database.windows.net Username: Insira um nome de utilizador fornecido durante a criação.
    Senha: Introduza uma palavra-passe fornecida durante a criação.
    Lembre-se da palavra-passe: Selecione Sim.

5.  Selecione **Ligar**.
6.  Procure bases **de dados a** partir do menu esquerdo.
7.  (Opcionalmente) Criar ou consultar informações a partir de _sample-db_
8.  Feche a ligação remota do ambiente de trabalho ao _myVm{uniqueid}_.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o ponto final privado, elimine o grupo de recursos. Isto remove o ponto final privado e todos os recursos conexos.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Private Link](private-link-overview.md)
