---
title: Mover serviços de análise do Azure para uma região diferente Microsoft Docs
description: Descreve como mover um recurso Azure Analysis Services para uma região diferente.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 06/09/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 1f7ecf960ae94fae4d829e73daf051b9062e478d
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018199"
---
# <a name="move-analysis-services-to-a-different-region"></a>Mover serviços de análise para uma região diferente

Este artigo descreve como mover um recurso de servidor de Serviços de Análise para uma região de Azure diferente. Pode mover o seu servidor para outra região por uma série de razões, por exemplo, para aproveitar uma região de Azure mais próxima dos utilizadores, para utilizar planos de serviços suportados apenas em regiões específicas, ou para satisfazer os requisitos de política interna e governação. 

Neste e artigos associados associados, aprende-se a:

> [!div class="checklist"]
> 
> * Faça uma cópia de segurança de uma base de dados de modelos de servidor de origem para [o armazenamento do Blob](../storage/blobs/storage-blobs-introduction.md).
> * Exporte um modelo de recurso de servidor [de origem.](../azure-resource-manager/templates/overview.md)
> * Obtenha uma assinatura de acesso partilhado de armazenamento [(SAS)](../storage/common/storage-sas-overview.md).
> * Modifique o modelo de recursos.
> * Implemente o modelo para criar um novo servidor-alvo.
> * Restaurar uma base de dados de modelos para o novo servidor-alvo.
> * Verifique o novo servidor-alvo e base de dados.
> * Elimine o servidor de origem.

Este artigo descreve a utilização de um modelo de recursos para migrar um único servidor de Serviços de Análise com uma **configuração básica** para uma região diferente *e* grupo de recursos na mesma subscrição. A utilização de um modelo retém propriedades de servidor configuradas, garantindo que o servidor alvo está configurado com as mesmas propriedades, exceto a região e o grupo de recursos, como o servidor de origem. Este artigo não descreve recursos associados móveis que podem fazer parte do mesmo grupo de recursos, tais como fonte de dados, armazenamento e recursos de gateway. 

Antes de mover um servidor para uma região diferente, recomenda-se que crie um plano detalhado. Considere recursos adicionais, como gateways e armazenamento que também podem ter de ser movidos. Com qualquer plano, é importante completar uma ou mais operações de movimento experimental usando servidores de teste antes de mover um servidor de produção.

> [!IMPORTANT]
> As aplicações do cliente e as cadeias de conexão ligam-se aos Serviços de Análise utilizando o nome completo do servidor, que é um Uri que inclui a região em que o servidor se encontra. Por exemplo, `asazure://westcentralus.asazure.windows.net/advworks01`. Ao mover um servidor para uma região diferente, está efetivamente a criar um novo recurso de servidor numa região diferente, que terá uma região diferente no nome de servidor Uri. As aplicações do cliente e as cadeias de ligação utilizadas nos scripts devem ligar-se ao novo servidor utilizando o novo nome de servidor Uri. A utilização [de um pseudónimo de nome do Servidor](analysis-services-server-alias.md) pode atenuar o número de locais que o nome do servidor Uri tem de ser alterado, mas deve ser implementado antes de uma mudança de região.

> [!IMPORTANT]
> As regiões de Azure utilizam diferentes intervalos de endereços IP. Se tiver exceções de firewall configuradas para a região onde o seu servidor e/ou conta de armazenamento está, pode ser necessário configurar uma gama de endereços IP diferente. Para saber mais, consulte [perguntas frequentes sobre conectividade da rede de Serviços de Análise.](analysis-services-network-faq.md)

> [!NOTE]
> Este artigo descreve a restauração de uma cópia de segurança de base de dados para um servidor alvo a partir de um contentor de armazenamento na região do servidor de origem. Em alguns casos, restaurar cópias de segurança de uma região diferente pode ter um fraco desempenho, especialmente para grandes bases de dados. Para obter o melhor desempenho durante a restauração da base de dados, migrar ou criar um novo recipiente de armazenamento na região do servidor alvo. Copie os ficheiros de reserva .abf do recipiente de armazenamento da região de origem para o recipiente de armazenamento da região alvo antes de restaurar a base de dados para o servidor alvo. Embora fora do alcance deste artigo, em alguns casos, particularmente com bases de dados muito grandes, a elaboração de uma base de dados a partir do seu servidor de origem, a recriação e, em seguida, o processamento no servidor alvo para carregar dados de base de dados podem ser mais rentáveis do que usar cópia de segurança/restauro.

> [!NOTE]
> Se utilizar uma porta de dados no local para se ligar a fontes de dados, também deve mover o recurso gateway para a região do servidor alvo. Para saber mais, consulte [instalar e configurar um gateway de dados no local.](analysis-services-gateway-install.md)

## <a name="prerequisites"></a>Pré-requisitos

- **Conta de armazenamento Azure**: Obrigado a armazenar um ficheiro de reserva .abf.
- **SQL Server Management Studio (SSMS)**: Necessário para fazer backup e restaurar bases de dados de modelos.
- **Azure PowerShell**. Só é necessário se optar por completar esta tarefa utilizando o PowerShell.

## <a name="prepare"></a>Preparação

### <a name="backup-model-databases"></a>Bases de dados de modelos de backup

Se **as definições de armazenamento** ainda não estiverem configuradas para o servidor de origem, siga os passos nas [definições de armazenamento Configure](analysis-services-backup.md#configure-storage-settings).

Quando as definições de armazenamento estiverem configuradas, siga os passos em [Backup](analysis-services-backup.md#backup) para criar uma base de dados modelo .abf backup no seu recipiente de armazenamento. Mais tarde, restabelece a cópia de segurança .abf para o seu novo servidor-alvo.


### <a name="export-template"></a>Exportar modelo

O modelo contém propriedades de configuração do servidor de origem.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para exportar um modelo com o portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos**e, em seguida, selecione o seu servidor de Serviços de Análise.

3. Selecione > **Definições**  >  **Modelo de exportação**.

4. Escolha **Baixar** na lâmina do **modelo de exportação.**

5. Localize o ficheiro .zip que descarregou do portal e, em seguida, desaperte esse ficheiro para uma pasta.

   O ficheiro zip contém os ficheiros .json que compõem o modelo e os parâmetros necessários para implantar um novo servidor.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para exportar um modelo utilizando o PowerShell:

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se a sua identidade estiver associada a mais de uma subscrição, então desenrem a sua subscrição ativa para a subscrição do recurso do servidor que pretende mover.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporte o modelo do seu servidor de origem. Estes comandos guardam um modelo json com o Nome De Grupo de Recursos como nome de ficheiro para o seu diretório atual.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Obtenha assinatura de acesso compartilhado de armazenamento (SAS)

Ao implementar um servidor alvo a partir de um modelo, é necessário um token SAS (como Uri) da delegação do utilizador para especificar o recipiente de armazenamento que contém a cópia de segurança da base de dados.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter uma assinatura de acesso partilhado utilizando o portal:

1. No portal, selecione a conta de armazenamento utilizada para fazer backup da base de dados do servidor.

2. Selecione **o Explorador de Armazenamento**e, em seguida, expanda os **recipientes BLOB**. 

3. Clique com o botão direito no seu recipiente de armazenamento e, em seguida, **selecione Obter Assinatura de Acesso Partilhado**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Obter SAS":::

4. Na **Assinatura de Acesso Partilhado**, selecione **Criar**. Por predefinição, o SAS expirará em 24 horas.

5. Copiar e guardar o **URI.** 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter uma assinatura de acesso partilhado utilizando o PowerShell, siga os passos na [Create a user delegation SAS for a container or blob with PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Modificar o modelo

Utilize um editor de texto para modificar a template.jsno ficheiro que exportou, alterando as propriedades da região e do recipiente blob. 

Para modificar o modelo:

1. Num editor de texto, na propriedade de **localização,** especificar a nova região alvo. Na propriedade **backupBlobContainerUri,** cole o recipiente de armazenamento Uri com a chave SAS. 

    O exemplo a seguir define a região-alvo para o servidor advworks1 `South Central US` e especifica o recipiente de armazenamento Uri com assinatura de acesso partilhado: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Guarde o modelo.

#### <a name="regions"></a>Regiões

Para obter regiões de Azure, consulte [as localizações de Azure.](https://azure.microsoft.com/global-infrastructure/locations/) Para obter regiões usando PowerShell, executar o comando [Get-AzLocation.](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Mover

Para implementar um novo recurso de servidor numa região diferente, utilizará o **template.jsno** ficheiro que exportou e modificou nas secções anteriores.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No portal, **selecione Criar um recurso**.

2. Em **Search the Marketplace**, **digitar a implementação do modelo**e, em seguida, premir **ENTER**.

3. Selecione **a implementação do modelo**.

4. Selecione **Criar**.

5. Selecione **Construa o seu próprio modelo no editor.**

6. Selecione **carregar o ficheiro**, e, em seguida, siga as instruções para carregar atemplate.js** no** ficheiro que exportou e modificou.

7. Verifique se o editor de modelo mostra as propriedades corretas para o seu novo servidor-alvo.

8. Selecione **Guardar**.

9. Insira ou selecione os valores da propriedade:

    - **Subscrição**: selecione a subscrição do Azure.
    
    - **Grupo de recursos**: Selecione **Criar novo**e, em seguida, insira um nome de grupo de recursos. Pode selecionar um grupo de recursos existente desde que não contenha já um servidor de Serviços de Análise com o mesmo nome.
    
    - **Localização**: Selecione a mesma região especificada no modelo.

10. Selecione **'Rever e criar' ( Revisão e Criação).**

11. Reveja os termos e Básicos e, em seguida, **selecione Criar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize estes comandos para implementar o seu modelo:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Obtenha o servidor alvo Uri

Para se ligar ao novo servidor-alvo do SSMS para restaurar a base de dados do modelo, precisa de obter o novo servidor-alvo Uri.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para colocar o servidor Uri no portal:

1. No portal, aceda ao novo recurso do servidor alvo.

2. Na página **'Visão Geral',** copie o **nome do Servidor** Uri.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter o servidor Uri utilizando o PowerShell, utilize os seguintes comandos:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Copiar **ServerFullName** a partir da saída.

---

### <a name="restore-model-database"></a>Restaurar a base de dados de modelos

Siga os passos descritos no [Restore](analysis-services-backup.md#restore) para restaurar a base de dados do modelo .abf backup para o novo servidor-alvo.

Opcional: Depois de restaurar a base de dados do modelo, processe o modelo e as tabelas para atualizar os dados de fontes de dados. Para processar o modelo e a mesa utilizando SSMS:

1. Em SSMS, clique com o botão direito na base de dados do modelo > **Process Database**.

2. Expandir **tabelas,** clicar à direita numa tabela. Em **Tabelas de Processos,** selecione todas as tabelas e, em seguida, selecione **OK**.

## <a name="verify"></a>Verificação

1. No portal, vá para o novo servidor alvo.

2. Na página 'Vista Geral', no **servidor Modelos de Serviços de Análise,** verifique se aparecem os modelos restaurados.

3. Utilize uma aplicação de cliente como Power BI ou Excel para ligar ao modelo no novo servidor. Verifique se surgem objectos-modelo como tabelas, medidas, hierarquias. 

4. Executar quaisquer scripts de automação. Verifique se executaram com sucesso.

Opcional: [O ALM Toolkit](http://alm-toolkit.com/) é uma ferramenta *de código aberto* para comparar e gerir bases de dados de dados *e* serviços de análise de power bi. Utilize o conjunto de ferramentas para ligar as bases de dados de servidores de origem e alvo e comparar. Se a migração da sua base de dados for bem sucedida, os objetos-modelo terão a mesma definição. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="Obter SAS":::

## <a name="clean-up-resources"></a>Limpar recursos

Depois de verificar as aplicações do cliente pode ligar-se ao novo servidor e quaisquer scripts de automatização estão a ser executados corretamente, elimine o seu servidor de origem. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para eliminar o servidor de origem do portal:

Na página geral do seu servidor de **origem,** selecione **Delete**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para eliminar o servidor de origem utilizando o PowerShell, utilize o comando Remove-AzAnalysisServicesServer.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Depois de completar um movimento de região, recomenda-se que o seu novo servidor-alvo utilize um recipiente de armazenamento na mesma região para cópias de segurança, em vez do contentor de armazenamento na região do servidor de origem.