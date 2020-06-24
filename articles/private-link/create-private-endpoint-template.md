---
title: Criar um ponto final privado em Azure Private Link
description: Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um ponto final privado.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: a60edde222a6200a0378cd8c9c4f4774da9c2e50
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817975"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Quickstart: Criar um ponto final privado utilizando um modelo de Gestor de Recursos Azure

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um ponto final privado.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também pode completar este quickstart utilizando o [portal Azure](create-private-endpoint-portal.md), [Azure PowerShell,](create-private-endpoint-powershell.md)ou o [Azure CLI](create-private-endpoint-cli.md).

## <a name="prerequisite"></a>Pré-requisito

Precisa de uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Este modelo cria um ponto final privado para um exemplo de Azure SQL Database.

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Sql/servidores**](/azure/templates/microsoft.sql/servers): O caso da Base de Dados SQL com a base de dados da amostra.
- [**Microsoft.Sql/servidores/bases de dados**](/azure/templates/microsoft.sql/servers/databases): A base de dados de amostras.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): A rede virtual onde o ponto final privado é implantado.
- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): O ponto final privado para aceder à instância da Base de Dados SQL.
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): A zona utilizada para resolver o endereço IP do ponto final privado.
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): O grupo de zona utilizado para associar o ponto final privado a uma zona privada de DNS.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): O endereço IP público utilizado para aceder à máquina virtual.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): A interface de rede para a máquina virtual.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): A máquina virtual utilizada para testar a ligação privada com o ponto final privado ao exemplo da Base de Dados SQL.

### <a name="deploy-the-template"></a>Implementar o modelo

Aqui está como implementar o modelo do Gestor de Recursos Azure para Azure:

1. Para iniciar súm na Azure e abrir o modelo, **selecione Implementar para Azure**. O modelo cria o ponto final privado, a instância da Base de Dados SQL, a infraestrutura de rede e uma máquina virtual para validar.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos.
3. Digite o administrador SQL e a palavra-passe.
4. Digite o nome de utilizador e palavra-passe do administrador de máquina virtual.
5. Leia a declaração de termos e condições. Se concordar, **selecione concordo com os termos e condições acima**  >  **de Compra.** A colocação pode demorar 20 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

> [!NOTE]
> O modelo Azure Resource Manager gera um nome único para o recurso myVm<b>{uniqueid}</b> da máquina virtual e para o sql Database sqlserver<b>{uniqueid}</b> . Substitua o seu valor gerado por **{uniqueid}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao VM _myVm{uniqueid}_ da internet da seguinte forma:

1. Na barra de pesquisa do portal, insira _myVm{uniqueid}_.

2. Selecione **Ligar**. **Ligue-se à máquina virtual.**

3. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop Protocol _(.rdp)_ e transfere-o para o computador.

4. Abra o ficheiro .rdp transferido.

   a. Se lhe for pedido, selecione **Ligar**.

   b. Introduza o nome de utilizador e a palavra-passe que especificou quando criou o VM.

      > [!NOTE]
      > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

5. Selecione **OK**.

6. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

7. Depois de aparecer o ambiente de trabalho em VM, minimize-o para voltar ao seu ambiente de trabalho local.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>Aceda ao servidor SQL Database em privado a partir do VM

Aqui está como ligar ao servidor SQL Database a partir do VM usando o ponto final privado.

1.  No Ambiente de Trabalho Remoto do _myVM{uniqueid}_, abra o PowerShell.
2.  Introduza o seguinte: nslookup sqlserver{uniqueid}.database.windows.net. 
    Receberá uma mensagem semelhante a esta:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Instale o SQL Server Management Studio.
4.  Em **Ligar ao servidor,** insira ou selecione estas informações:
    - **Tipo de servidor**: Selecione **o motor da base de dados**.
    - **Nome do servidor**: Selecione **sqlserver{uniqueid}.database.windows.net**.
    - **Nome de utilizador**: Introduza um nome de utilizador fornecido durante a criação.
    - **Senha**: Introduza uma palavra-passe fornecida durante a criação.
    - **Lembre-se da palavra-passe**: Selecione **Sim**.

5.  Selecione **Ligar**.
6.  A partir do menu à esquerda, vá às **Bases de Dados.**
7.  Opcionalmente, pode criar ou consultar informações a partir de _sample-db_.
8.  Fechar a ligação remote desktop ao _myVm{uniqueid}_.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o ponto final privado, elimine o grupo de recursos. Isto remove o ponto final privado e todos os recursos conexos.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Private Link](private-link-overview.md).
