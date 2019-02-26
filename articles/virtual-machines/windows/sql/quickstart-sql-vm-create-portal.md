---
title: Criar uma VM do SQL Server do Windows no portal | Microsoft Docs
description: Este tutorial mostra como criar uma máquina virtual do SQL Server 2017 do Windows no portal do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0a583a75b72286718b34b84e67ee5aff34726be0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818243"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Início rápido: Crie uma máquina virtual do SQL Server 2017 Windows no portal do Azure

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Estes passos do guia de introdução explicam a criação de uma máquina virtual de SQL Server com o portal do Azure.

> [!TIP]
> Este início rápido proporciona um caminho para aprovisionar e ligar a uma VM do SQL de forma rápida. Para obter mais informações sobre outras opções de aprovisionamento de VMs do SQL, veja o [Guia de aprovisionamento de VMs do SQL Server do Windows no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Obter uma subscrição do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a id="select"></a> Selecionar uma imagem de VM do SQL Server

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) utilizando a sua conta.

1. No portal do Azure, clique em **Criar um recurso**. 

1. No campo de pesquisa, escreva **SQL Server 2017 Developer on Windows Server 2016** (SQL Server 2017 Developer no Windows Server 2016) e prima ENTER.

1. Selecione o **gratuitos a licença do SQL Server: SQL Server 2017 Developer no Windows Server 2016** imagem.

   ![Nova janela de procura](./media/quickstart-sql-vm-create-portal/newsearch.png)

   > [!TIP]
   > A edição de Programador é utilizada neste tutorial porque é uma edição com todas as funcionalidades do SQL Server que é gratuita para fins de teste de programação. Apenas paga pelo custo de execução da VM. Para obter considerações mais compeltas sobre preços, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. Clique em **Criar**.

## <a id="configure"></a> Forneça os detalhes básicos

Na janela **Informações Básicas**, forneça as seguintes informações:

1. No campo **Nome**, introduza um nome de máquina virtual exclusivo. 

1. No campo **Nome de utilizador**, introduza um nome para a conta de administrador local na VM.

1. Forneça uma **Palavra-passe** forte.

1. Introduza um novo nome para o **Grupo de recursos**. Este grupo ajudar a gerir todos os recursos associados à máquina virtual.

1. Verifique as outras predefinições e clique em **OK** para continuar.

   ![Janela Informações Básicas do SQL](./media/quickstart-sql-vm-create-portal/azure-sql-basic.png)

## <a name="choose-virtual-machine-size"></a>Selecionar o tamanho da máquina virtual

1. No passo **Tamanho**, escolha um tamanho de máquina virtual na janela **Escolher um tamanho**.

   Para este início rápido, selecione **D2S_V3**. O portal mostra o custo estimado mensal da máquina para utilização contínua (sem incluir os custos de licenciamento do SQL Server). Tenha em atenção que a Edição de Programador não tem custos de licenciamento extra para o SQL Server. Para obter mais informações de preços mais específicas, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

   > [!TIP]
   > O tamanho de máquina **D2S_V3** poupa dinheiro ao testar. Mas para cargas de trabalho de produção, veja os tamanhos e as configurações de máquinas recomendados em [Performance best practices for SQL Server in Azure Virtual Machine](virtual-machines-windows-sql-performance.md) (Melhores práticas de desempenho para o SQL Server nas Máquinas Virtuais do Azure).

1. Clique em **Selecionar** para continuar.

## <a name="configure-optional-features"></a>Configurar funcionalidades opcionais

1. Na janela **Definições**, selecione a porta **RDP (3389)** na lista **Selecionar portas de entrada públicas** se pretender aceder à VM através do ambiente de trabalho remoto.

   ![Portas de entrada](./media/quickstart-sql-vm-create-portal/inbound-ports.png)

   > [!NOTE]
   > Pode selecionar a porta **MS SQL (1433)** para aceder remotamente ao SQL Server. No entanto, não é necessário fazê-lo porque o passo **Definições do SQL Server** também fornece esta opção. Se selecionar a porta 1433 neste passo, esta será aberta independentemente das suas seleções no passo **Definições do SQL Server**.

1. Clique em **OK** para guardar as alterações e continuar.

## <a name="sql-server-settings"></a>Definições do SQL Server

Na janela **Definições do SQL Server**, configure as seguintes opções.

1. Na lista pendente de **Conectividade de SQL**, selecione **Pública (Internet)**. Isto permite as ligações do SQL Server pela internet.

1. Altere a **Porta** para **1401** para evitar um nome de porta conhecido no cenário público.

1. Em **Autenticação SQL**, clique em **Ativar**. O Início de Sessão de SQL está definido para o mesmo nome de utilizador e palavra-passe que configurou para a VM.

1. Altere quaisquer outras definições, se necessário e clique em **OK** para concluir a configuração da VM do SQL Server.

   ![Definições do SQL Server](./media/quickstart-sql-vm-create-portal/sql-settings.png)

## <a name="create-the-sql-server-vm"></a>Criar a VM do SQL Server

Na janela **Resumo**, reveja o resumo e clique em **Comprar** para criar o SQL Server, o grupo de recursos e os recursos especificados para esta VM.

Pode monitorizar a implementação a partir do portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação.

> [!TIP]
> Implementar uma VM do SQL Server em Windows pode demorar vários minutos.

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server

1. No portal, encontre o **endereço de IP Público** da sua VM na secção **Descrição Geral** das propriedades da sua máquina virtual.

1. Num computador diferente ligado à Internet, abra o SQL Server Management Studio (SSMS).

   > [!TIP]
   > Se não tiver o SQL Server Management Studio, poderá transferi-lo [aqui](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Na caixa de diálogo **Ligar ao Servidor** ou **Ligar ao Motor de Base de Dados**, edite o valor **Nome do servidor**. Introduza o endereço IP público da VM. Em seguida, adicione uma vírgula e a porta personalizada, **1401**, que especificamos quando configurou a nova VM. Por exemplo, `11.22.33.444,1401`.

1. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.

1. Na caixa **Início de sessão**, escreva o nome de um início de sessão SQL válido.

1. Na caixa **Palavra-passe**, escreva a palavra-passe do início de sessão.

1. Clique em **Ligar**.

    ![ligação SSMS](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Inicie a sessão para a VM remotamente

Utilize os seguintes passos para ligar à máquina virtual do SQL Server com o Ambiente de Trabalho Remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar que a sua VM SQL execute continuamente, pode evitar despesas desnecessárias ao pará-la quando não estiver a utilizar. Também pode eliminar permanentemente todos os recursos associados à máquina virtual ao eliminar o seu grupo de recursos associados no portal. Isto também elimina permanentemente a máquina virtual, pelo que utilize este comando com cuidado. Para mais informações, consulte [Manage Azure resources through portal (Gerir recursos do Azure através do portal)](../../../azure-resource-manager/manage-resource-groups-portal.md).

## <a name="next-steps"></a>Passos Seguintes

Neste guia de introdução, criou uma máquina virtual do SQL Server 2017 no Portal do Azure. Para saber mais sobre como migrar os seus dados para o novo SQL Server, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Migrar uma base de dados para uma VM de SQL](virtual-machines-windows-migrate-sql.md)
