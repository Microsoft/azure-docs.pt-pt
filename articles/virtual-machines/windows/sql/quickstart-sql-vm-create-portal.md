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
ms.openlocfilehash: fb35cc99164cc8da047e8309d63bf7909abf4815
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846020"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Início rápido: Crie uma máquina virtual do SQL Server 2017 Windows no portal do Azure

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Estes passos do guia de introdução explicam a criação de uma máquina virtual de SQL Server com o portal do Azure.


  > [!TIP]
  > - Este início rápido proporciona um caminho para aprovisionar e ligar a uma VM do SQL de forma rápida. Para obter mais informações sobre outras opções de aprovisionamento de VMs do SQL, veja o [Guia de aprovisionamento de VMs do SQL Server do Windows no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).
  > - Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Obter uma subscrição do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a id="select"></a> Selecionar uma imagem de VM do SQL Server

1. Entre no [portal do Azure](https://portal.azure.com) usando sua conta.

1. No portal do Azure, selecione **Criar um recurso**. 

1. No campo de pesquisa, digite `SQL Server 2017 Developer on Windows Server 2016`e pressione Enter. Expanda a opção SQL Server 2017 no Windows Server 2016. 

1. Selecione a **licença de SQL Server gratuita: SQL Server desenvolvedor 2017 na imagem do Windows** Server 2016. A edição de Programador é utilizada neste tutorial porque é uma edição com todas as funcionalidades do SQL Server que é gratuita para fins de teste de programação. Apenas paga pelo custo de execução da VM. Para obter considerações mais compeltas sobre preços, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

   ![Nova janela de procura](./media/quickstart-sql-vm-create-portal/newsearch.png)


1. Selecione **Criar**.

## <a id="configure"></a> Forneça os detalhes básicos

Na guia **noções básicas** , forneça as seguintes informações:

1. Na seção **detalhes do projeto** , selecione sua assinatura do Azure e, em seguida, selecione **criar nova** para criar um novo grupo de recursos. Digite _SQLVM-RG_ para o nome.

   ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. Em **detalhes da instância**:
    1. Digite _SQLVM_ para o **nome da máquina virtual**. 
    1. Escolha um local para a sua **região**. 
    1. Para a finalidade deste guia de início rápido, deixe **as opções de disponibilidade** definidas como _sem redundância de infraestrutura necessária_. Para obter mais informações sobre as opções de disponibilidade, consulte [disponibilidade](../../windows/availability.md). 
    1. Na lista **imagem** , selecione _licença de SQL Server gratuita: SQL Server desenvolvedor 2017 no Windows Server 2016_. 
    1. Escolha **alterar o tamanho** do **tamanho** da máquina virtual e selecione a oferta **básica a2** . Certifique-se de limpar seus recursos depois de concluí-los para evitar cobranças inesperadas. 

   ![Detalhes da instância](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. Em **conta de administrador**, forneça um nome de usuário, como _azureuser_ e uma senha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Conta de administrador](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. Em **regras de porta de entrada**, escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** na lista suspensa. 

   ![Regras da porta de entrada](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Definições do SQL Server

Na guia **configurações de SQL Server** , configure as seguintes opções:

1. Em **segurança & rede**, selecione conectividade _pública (Internet_) para **SQL** e altere a porta para `1401` para evitar o uso de um número de porta conhecido no cenário público. 
1. Em **autenticação do SQL**, selecione **habilitar**. O Início de Sessão de SQL está definido para o mesmo nome de utilizador e palavra-passe que configurou para a VM. Use as configurações padrão para **Azure Key Vault integração** e **configuração de armazenamento**.  

   ![Configurações de segurança do SQL Server](media/quickstart-sql-vm-create-portal/sql-server-settings.png)

1. Altere todas as outras configurações, se necessário, e selecione revisar **+ criar**. 

   ![Examinar + criar](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Criar a VM do SQL Server

Na guia **revisar + criar** , examine o resumo e selecione **criar** para criar SQL Server, grupo de recursos e recursos especificados para essa VM.

Pode monitorizar a implementação a partir do portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação. A implantação pode levar vários minutos. 

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server

1. No portal, localize o **endereço IP público** de sua VM SQL Server na seção **visão geral** das propriedades da sua máquina virtual.

1. Em um computador diferente conectado à Internet, abra [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. Na caixa de diálogo **Ligar ao Servidor** ou **Ligar ao Motor de Base de Dados**, edite o valor **Nome do servidor**. Introduza o endereço IP público da VM. Em seguida, adicione uma vírgula e a porta personalizada, **1401**, que especificamos quando configurou a nova VM. Por exemplo, `11.22.33.444,1401`.

1. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.

1. Na caixa **Início de sessão**, escreva o nome de um início de sessão SQL válido.

1. Na caixa **Palavra-passe**, escreva a palavra-passe do início de sessão.

1. Selecione **Ligar**.

    ![ligação SSMS](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Inicie a sessão para a VM remotamente

Utilize os seguintes passos para ligar à máquina virtual do SQL Server com o Ambiente de Trabalho Remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar que a sua VM SQL execute continuamente, pode evitar despesas desnecessárias ao pará-la quando não estiver a utilizar. Também pode eliminar permanentemente todos os recursos associados à máquina virtual ao eliminar o seu grupo de recursos associados no portal. Isto também elimina permanentemente a máquina virtual, pelo que utilize este comando com cuidado. Para mais informações, consulte [Manage Azure resources through portal (Gerir recursos do Azure através do portal)](../../../azure-resource-manager/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou uma máquina virtual SQL Server 2017 no portal do Azure. Para saber mais sobre como migrar os seus dados para o novo SQL Server, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Migrar uma base de dados para uma VM de SQL](virtual-machines-windows-migrate-sql.md)
