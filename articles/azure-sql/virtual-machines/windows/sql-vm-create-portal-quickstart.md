---
title: Criar O SERVIDOR SQL numa máquina virtual do Windows no portal Azure Microsoft Docs
description: Este tutorial mostra como criar uma máquina virtual Windows com SQL Server 2017 no portal Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d7d82db7fc8a39a0865e80ee7873ee849627c583
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791074"
---
# <a name="quickstart-create-sql-server-2017-on-a-windows-virtual-machine-in-the-azure-portal"></a>Quickstart: Criar SQL Server 2017 numa máquina virtual Windows no portal Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Windows](sql-vm-create-portal-quickstart.md)
> * [Linux](../linux/sql-vm-create-portal-quickstart.md)

Este arranque rápido passa pela criação de uma máquina virtual SQL Server (VM) no portal Azure.


  > [!TIP]
  > - Este início rápido proporciona um caminho para aprovisionar e ligar a uma VM do SQL de forma rápida. Para obter mais informações sobre outras opções de provisionamento SQL VM, consulte o [guia de provisionamento do SQL Server no Windows VM no portal Azure](create-sql-vm-portal.md).
  > - Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Obtenha uma assinatura Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Selecionar uma imagem de VM do SQL Server

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta.

1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva *Azure SQL* na caixa de pesquisa.
1. **Selecione +Adicione** para abrir a página **de opção de implementação Select SQL.** Pode ver informações adicionais selecionando **detalhes do Show** no azulejo das **máquinas virtuais SQL.**
1. Selecione a **Licença de servidor SQL grátis: SQL Server 2017 Developer on Windows Server 2016** image from the dropdown.

   ![Nova janela de procura](./media/sql-vm-create-portal-quickstart/select-sql-2017-vm-image.png)

1. Selecione **Criar** .

   ![Nova janela de procura](./media/sql-vm-create-portal-quickstart/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Forneça os detalhes básicos

No **separador Básicos,** forneça as seguintes informações:

1. Na secção Detalhes do **Projeto,** selecione a subscrição do Azure e, em seguida, **selecione Criar novo** para criar um novo grupo de recursos. Tipo _SQLVM-RG_ para o nome.

   ![Subscrição](./media/sql-vm-create-portal-quickstart/basics-project-details.png)

1. De **acordo com os detalhes do exemplo:**
    1. Tipo _SQLVM_ para o **nome da máquina virtual.** 
    1. Escolha um local para a sua **Região.** 
    1. Para efeitos deste arranque rápido, deixe **as opções de disponibilidade definidas** sem _necessidade de redundância de infraestrutura._ Para obter mais informações sobre as opções de disponibilidade, consulte [Disponibilidade.](../../../virtual-machines/availability.md) 
    1. Na lista **de imagens,** selecione _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_ . 
    1. Opte por alterar o **tamanho** do **tamanho** da máquina virtual e selecione a oferta **A2 Basic.** Certifique-se de limpar os seus recursos assim que terminar com eles para evitar quaisquer acusações inesperadas. 

   ![Detalhes da instância](./media/sql-vm-create-portal-quickstart/basics-instance-details.png)

1. Na **conta do Administrador,** forneça um nome de utilizador, como _azureuser_ e uma palavra-passe. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Conta de administrador](./media/sql-vm-create-portal-quickstart/basics-administrator-account.png)

1. De acordo com **as regras da porta de entrada** , escolha Localizar portas **selecionadas** e, em seguida, selecionar **RDP (3389)** a partir do drop-down. 

   ![Regras da porta de entrada](./media/sql-vm-create-portal-quickstart/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Definições do SQL Server

No separador de definições do **SQL Server,** configufique as seguintes opções:

1. Em **Segurança & Networking,** selecione _Public (Internet)_ para **a Conectividade SQL** e altere a porta `1401` para evitar a utilização de um número de porta bem conhecido no cenário público. 
1. Sob **autenticação SQL** , selecione **Enable** . As credenciais de login SQL são definidas com o mesmo nome de utilizador e palavra-passe que configuraste para o VM. Utilize a definição padrão para [**integração do Cofre da Chave Azure**](azure-key-vault-integration-configure.md). **A configuração de armazenamento** não está disponível para a imagem básica do SQL Server VM, mas pode encontrar mais informações sobre as opções disponíveis para outras imagens na [configuração de armazenamento](storage-configuration.md#new-vms).  

   ![Definições de segurança do servidor SQL](./media/sql-vm-create-portal-quickstart/sql-server-settings.png)


1. Altere quaisquer outras definições se necessário e, em seguida, selecione **Review + create** . 

   ![Rever + criar](./media/sql-vm-create-portal-quickstart/review-create.png)


## <a name="create-the-sql-server-vm"></a>Criar a VM do SQL Server

No **separador 'Rever + criar',** rever o resumo e selecione  **Criar** para criar o SQL Server, grupo de recursos e recursos especificados para este VM.

Pode monitorizar a implementação a partir do portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação. A implantação pode demorar vários minutos. 

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server

1. No portal, encontre o **endereço IP público** do seu SQL Server VM na secção **visão geral** das propriedades da sua máquina virtual.

1. Num computador diferente ligado à Internet, abra o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. Na caixa de diálogo **Ligar ao Servidor** ou **Ligar ao Motor de Base de Dados** , edite o valor **Nome do servidor** . Introduza o endereço IP público da VM. Em seguida, adicione uma vírgula e adicione a porta personalizada **(1401** ) que especificou quando configurar o novo VM. Por exemplo, `11.22.33.444,1401`.

1. Na caixa **Autenticação** , selecione **Autenticação do SQL Server** .

1. Na caixa **Início de sessão** , escreva o nome de um início de sessão SQL válido.

1. Na caixa **Palavra-passe** , escreva a palavra-passe do início de sessão.

1. Selecione **Ligar** .

    ![ligação SSMS](./media/sql-vm-create-portal-quickstart/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Inicie a sessão para a VM remotamente

Utilize os seguintes passos para ligar à máquina virtual do SQL Server com o Ambiente de Trabalho Remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar que a sua VM SQL execute continuamente, pode evitar despesas desnecessárias ao pará-la quando não estiver a utilizar. Também pode eliminar permanentemente todos os recursos associados à máquina virtual ao eliminar o seu grupo de recursos associados no portal. Isto também elimina permanentemente a máquina virtual, pelo que utilize este comando com cuidado. Para mais informações, consulte [Manage Azure resources through portal (Gerir recursos do Azure através do portal)](../../../azure-resource-manager/management/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma máquina virtual SQL Server 2017 no portal Azure. Para saber mais sobre como migrar os seus dados para o novo SQL Server, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Migrar uma base de dados para uma VM de SQL](migrate-to-vm-from-sql-server.md)