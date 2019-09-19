---
title: 'Tutorial: Adicionar uma instância gerenciada do banco de dados SQL a um grupo de failover'
description: Saiba como configurar um grupo de failover para sua instância gerenciada do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 04802b8b25ca21cc0099874e5a9ea69748868f6e
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103217"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Tutorial: Adicionar uma instância gerenciada do banco de dados SQL a um grupo de failover

Adicione uma instância gerenciada do banco de dados SQL a um grupo de failover. Neste artigo, ficará a saber como:

> [!div class="checklist"]
> - Criar uma instância gerenciada primária
> - Crie uma instância gerenciada secundária como parte de um [grupo de failover](sql-database-auto-failover-group.md). 
> - Ativação pós-falha de teste

  > [!NOTE]
  > - Ao percorrer este tutorial, verifique se você está configurando seus recursos com os [pré-requisitos para configurar grupos de failover para instância gerenciada](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - A criação de uma instância gerenciada pode levar uma quantidade significativa de tempo. Como resultado, este tutorial pode levar várias horas para ser concluído. Para obter mais informações sobre os tempos de provisionamento, consulte [operações gerenciadas de gerenciamento de instância](sql-database-managed-instance.md#managed-instance-management-operations). 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem: 

- Uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma. 


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1-Criar grupo de recursos e instância gerenciada primária
Nesta etapa, você criará o grupo de recursos e a instância gerenciada primária para seu grupo de failover usando o portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais sobre os bancos de dados diferentes selecionando Mostrar detalhes no bloco bancos de dados.
1. Selecione **criar** no bloco **instâncias gerenciadas do SQL** . 

    ![Selecionar instância gerenciada](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na página **criar instância gerenciada do banco de dados SQL do Azure** , na guia **noções básicas**
    1. Em **detalhes do projeto**, selecione sua **assinatura** na lista suspensa e, em seguida, escolha **criar novo** grupo de recursos. Digite um nome para seu grupo de recursos, como `myResourceGroup`. 
    1. Em **detalhes do instância gerenciada**, forneça o nome da sua instância gerenciada e a região em que você deseja implantar sua instância gerenciada. Deixe o **armazenamento de computação +** em valores padrão. 
    1. Em **conta de administrador**, forneça um logon de administrador, `azureuser`como e uma senha de administrador complexa. 

    ![Criar MI primário](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Deixe o restante das configurações em valores padrão e selecione **revisar + criar** para examinar as configurações de instância gerenciada. 
1. Selecione **criar** para criar sua instância gerenciada primária. 


## <a name="2---create-a-virtual-network"></a>2-criar uma rede virtual
Nesta etapa, você criará uma rede virtual para a instância gerenciada secundária. Essa etapa é necessária porque há um requisito de que a sub-rede das instâncias gerenciadas primárias e secundárias tenha intervalos de endereços não sobrepostos. 

Para verificar o intervalo de sub-rede de sua rede virtual primária, siga estas etapas:
1. No [portal do Azure](https://portal.azure.com), navegue até o grupo de recursos e selecione a rede virtual para sua instância primária. 
1. Selecione **sub-redes** em **configurações** e anote o **intervalo de endereços**. O intervalo de endereços de sub-rede da rede virtual para a instância gerenciada secundária não pode sobrepor isso. 


   ![Sub-rede principal](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Para criar uma rede virtual, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** e pesquise *rede virtual*. 
1. Selecione a opção **rede virtual** publicada pela Microsoft e, em seguida, selecione **criar** na próxima página. 
1. Preencha os campos obrigatórios para configurar a rede virtual para sua instância gerenciada secundária e, em seguida, selecione **criar**. 

   A tabela a seguir mostra os valores necessários para a rede virtual secundária:

    | **Campo** | Value |
    | --- | --- |
    | **Name** |  O nome da rede virtual a ser usada pela instância gerenciada secundária, `vnet-sql-mi-secondary`como. |
    | **Espaço de endereços** | O espaço de endereço para sua rede virtual, `10.128.0.0/16`como. | 
    | **Subscrição** | A assinatura em que reside a instância gerenciada primária e o grupo de recursos. |
    | **Região** | O local onde você vai implantar sua instância gerenciada secundária. |
    | **Sub-rede** | O nome da sua sub-rede. `default`é fornecido para você por padrão. |
    | **Intervalo de endereços**| O intervalo de endereços da sua sub-rede. Isso deve ser diferente do intervalo de endereços de sub-rede usado pela rede virtual de sua instância gerenciada primária, `10.128.0.0/24`como.  |
    | &nbsp; | &nbsp; |

    ![Valores de rede virtual secundária](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3-criar uma instância gerenciada secundária
Nesta etapa, você criará uma instância gerenciada secundária no portal do Azure, que também irá configurar a rede entre as duas instâncias gerenciadas. 

Sua segunda instância gerenciada deve:
- Ficar vazio. 
- Ter uma sub-rede e um intervalo de IPS diferentes da instância gerenciada primária. 

Para criar sua instância gerenciada secundária, siga estas etapas: 

1. Selecione **SQL do Azure** no menu à esquerda da portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais sobre os bancos de dados diferentes selecionando Mostrar detalhes no bloco bancos de dados.
1. Selecione **criar** no bloco **instâncias gerenciadas do SQL** . 

    ![Selecionar instância gerenciada](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na guia **noções básicas** da página **criar instância gerenciada do banco de dados SQL do Azure** , preencha os campos obrigatórios para configurar sua instância gerenciada secundária. 

   A tabela a seguir mostra os valores necessários para a instância gerenciada secundária:
 
    | **Campo** | Value |
    | --- | --- |
    | **Subscrição** |  A assinatura na qual a instância gerenciada primária é. |
    | **Grupo de recursos**| O grupo de recursos em que a instância gerenciada primária é. |
    | **Nome da instância gerida** | O nome da sua nova instância gerenciada secundária, como`sql-mi-secondary`  | 
    | **Região**| O local da sua instância gerenciada secundária.  |
    | **Início de sessão de administração da instância gerida** | O logon que você deseja usar para a nova instância gerenciada secundária, `azureuser`como. |
    | **Palavra-passe** | Uma senha complexa que será usada pelo logon de administrador para a nova instância gerenciada secundária.  |
    | &nbsp; | &nbsp; |

1. Na guia **rede** , para a **rede virtual**, selecione a rede virtual que você criou para a instância gerenciada secundária na lista suspensa.

   ![Rede de MI secundária](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Na guia **configurações adicionais** , para **replicação geográfica**, escolha **Sim** para _usar como failover secundário_. Selecione a instância gerenciada primária na lista suspensa. 
    1. Certifique-se de que o agrupamento e o fuso horário correspondem ao da instância gerenciada primária. A instância gerenciada primária criada neste tutorial usou o padrão de `SQL_Latin1_General_CP1_CI_AS` agrupamento e o `(UTC) Coordinated Universal Time` fuso horário. 

   ![Rede de MI secundária](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selecione **examinar + criar** para examinar as configurações de sua instância gerenciada secundária. 
1. Selecione **criar** para criar sua instância gerenciada secundária. 


## <a name="4---create-primary-virtual-network-gateway"></a>4-criar um gateway de rede virtual primário 

Para que duas instâncias gerenciadas participem de um grupo de failover, deve haver um gateway configurado entre as redes virtuais das duas instâncias gerenciadas para permitir a comunicação de rede. Você pode criar o gateway para a instância gerenciada primária usando o portal do Azure:

1. Na [portal do Azure](https://portal.azure.com), vá para o grupo de recursos e selecione o recurso de **rede virtual** para sua instância gerenciada primária. 
1. Selecione sub- **redes** em **configurações** e, em seguida, selecione para adicionar uma nova **sub-rede de gateway**. Deixe os valores padrão. 

   ![Adicionar gateway para instância gerenciada primária](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Depois que o gateway de sub-rede for criado, selecione **criar um recurso** no painel de navegação esquerdo e `Virtual network gateway` digite na caixa de pesquisa. Selecione o recurso de **Gateway de rede virtual** publicado pela **Microsoft**. 

   ![Criar um novo gateway de rede virtual](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Preencha os campos obrigatórios para configurar o gateway de sua instância gerenciada primária. 

   A tabela a seguir mostra os valores necessários para o gateway para a instância gerenciada primária:
 
    | **Campo** | Value |
    | --- | --- |
    | **Subscrição** |  A assinatura na qual a instância gerenciada primária é. |
    | **Name** | O nome do seu gateway de rede virtual, `primary-mi-gateway`como. | 
    | **Região** | A região onde sua instância gerenciada secundária é. |
    | **Tipo de gateway** | Selecione **VPN**. |
    | **Tipo de VPN** | Selecionar **baseado em rota** |
    | **SKU**| Mantenha o padrão `VpnGw1`de. |
    | **Location**| O local onde a instância gerenciada primária e a rede virtual primária é.   |
    | **Rede virtual**| Selecione a rede virtual que foi criada na seção 2, `vnet-sql-mi-primary`como. |
    | **Endereço IP público**| Selecione **Criar novo**. |
    | **Nome do endereço IP público**| Insira um nome para seu endereço IP, `primary-gateway-IP`como. |
    | &nbsp; | &nbsp; |
1. Deixe os outros valores como padrão e, em seguida, selecione **revisar + criar** para examinar as configurações do seu gateway de rede virtual.

   ![Configurações do gateway primário](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selecione **criar** para criar seu novo gateway de rede virtual. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5-configurar o gateway de rede virtual secundária 

Repita as etapas na seção anterior para criar a sub-rede de rede virtual e o gateway para a instância gerenciada secundária. Preencha os campos obrigatórios para configurar o gateway para sua instância gerenciada secundária. 

   A tabela a seguir mostra os valores necessários para o gateway para a instância gerenciada secundária:

   | **Campo** | Value |
   | --- | --- |
   | **Subscrição** |  A assinatura na qual a instância gerenciada secundária é. |
   | **Name** | O nome do seu gateway de rede virtual, `secondary-mi-gateway`como. | 
   | **Região** | A região onde sua instância gerenciada secundária é. |
   | **Tipo de gateway** | Selecione **VPN**. |
   | **Tipo de VPN** | Selecionar **baseado em rota** |
   | **SKU**| Mantenha o padrão `VpnGw1`de. |
   | **Location**| O local onde a instância gerenciada secundária e a rede virtual secundária são.   |
   | **Rede virtual**| Selecione a rede virtual que foi criada na seção 2, `vnet-sql-mi-secondary`como. |
   | **Endereço IP público**| Selecione **Criar novo**. |
   | **Nome do endereço IP público**| Insira um nome para seu endereço IP, `secondary-gateway-IP`como. |
   | &nbsp; | &nbsp; |

   ![Configurações do gateway secundário](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6-conectar os gateways
Nesta etapa, crie uma conexão entre gateways. Uma conexão deve ser estabelecida do gateway primário para o secundário e, em seguida, uma conexão separada deve ser estabelecida entre o secundário e o gateway primário. Certifique-se de usar a mesma **chave compartilhada** ao configurar a conectividade entre ambos os gateways. 

Para configurar a conectividade, siga estas etapas:

1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com) e selecione o gateway primário que você criou na etapa 4. 
1. Selecione **conexões** em **configurações** e, em seguida, selecione **Adicionar** para criar uma nova conexão. 

   ![Adicionar conexão ao gateway primário](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Insira um nome para a conexão, `Primary-connection`como e digite um valor para a `mi1mi2psk`chave compartilhada, como. 
1. Selecione o **segundo gateway de rede virtual** e, em seguida, selecione o gateway para a instância gerenciada secundária, `secondary-mi-gateway`como. 

   ![Criar conexão primária para secundária](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Selecione **OK** para adicionar a nova conexão de gateway primário a secundário.
1. Repita essas etapas para criar uma conexão do gateway da instância gerenciada secundária para o gateway da instância gerenciada primária. 

   ![Criar conexão secundária com primária](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7-criar um grupo de failover
Nesta etapa, você criará o grupo de failover e adicionará as duas instâncias gerenciadas a ele. 

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione a instância gerenciada primária que você criou na primeira seção, `sql-mi-primary`como. 
1. Em **configurações**, navegue até **instância grupos de failover** e, em seguida, escolha **Adicionar grupo** para abrir a página **grupo de failover de instância** . 

   ![Adicionar um grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na página **grupo de failover de instância** , digite o nome do grupo de failover, `failovergrouptutorial` como e escolha a `sql-mi-secondary` instância gerenciada secundária, como na lista suspensa. Selecione **criar** para criar seu grupo de failover. 

   ![Criar grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Depois que a implantação do grupo de failover for concluída, você será levado de volta à página **grupo de failover** . 

## <a name="8---test-failover"></a>8-failover de teste
Nesta etapa, você falhará no grupo de failover para o servidor secundário e, em seguida, fará o failback usando o portal do Azure. 

1. Navegue até sua instância gerenciada dentro do [portal do Azure](https://portal.azure.com) e selecione **grupos de failover de instância** em configurações. 
1. Examine qual instância gerenciada é a primária e qual instância gerenciada é a secundária. 
1. Selecione **failover** e, em seguida, selecione **Sim** no aviso sobre as sessões de TDS sendo desconectadas. 

   ![Fazer failover do grupo de failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Examine qual instância gerenciada é a primária e qual é a instância secundária. Se o failover for bem-sucedido, as duas instâncias deverão ter funções alternadas. 

   ![Instâncias gerenciadas têm funções alternadas após o failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Selecione **failover** novamente para falhar a instância primária de volta para a função primária. 


## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos excluindo primeiro a instância gerenciada, depois o cluster virtual, todos os recursos restantes e, por fim, o grupo de recursos. 

1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com). 
1. Selecione a instância gerenciada e, em seguida, selecione **excluir**. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. Esse processo pode levar algum tempo para ser concluído em segundo plano e, até que seja concluído, você não poderá excluir o *cluster virtual* nem outros recursos dependentes. Monitore a exclusão na guia atividade para confirmar se a instância gerenciada foi excluída. 
1. Depois que a instância gerenciada for excluída, exclua o *cluster virtual* selecionando-o em seu grupo de recursos e, em seguida, escolhendo **excluir**. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. 
1. Exclua todos os recursos restantes. Digite `yes` na caixa de texto para confirmar que deseja excluir o recurso e, em seguida, selecione **excluir**. 
1. Exclua o grupo de recursos selecionando **excluir grupo de recursos**, digitando o nome do grupo `myResourceGroup`de recursos, e, em seguida, selecionando **excluir**. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configurou um grupo de failover entre duas instâncias gerenciadas. Aprendeu a:

> [!div class="checklist"]
> - Criar uma instância gerenciada primária
> - Crie uma instância gerenciada secundária como parte de um [grupo de failover](sql-database-auto-failover-group.md). 
> - Ativação pós-falha de teste

Avance para o próximo guia de início rápido sobre como se conectar à sua instância gerenciada e como restaurar um banco de dados para sua instância gerenciada: 

> [!div class="nextstepaction"]
> [Conectar-se à sua instância](sql-database-managed-instance-configure-vm.md)
> gerenciada[restaurar um banco de dados para uma instância gerenciada](sql-database-managed-instance-get-started-restore.md)


