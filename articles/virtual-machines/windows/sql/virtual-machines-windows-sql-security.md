---
title: Considerações de segurança para SQL Server no Azure | Microsoft Docs
description: Este tópico fornece diretrizes gerais para proteger SQL Server em execução em uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6b2f9853c2699b69a0c9be13e6925a4b30f358f7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102033"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações de Segurança para SQL Server em Máquinas Virtuais do Azure

Este tópico inclui diretrizes gerais de segurança que ajudam a estabelecer acesso seguro a instâncias de SQL Server em uma VM (máquina virtual) do Azure.

O Azure está em conformidade com várias normas e padrões do setor que podem permitir que você crie uma solução em conformidade com SQL Server em execução em uma máquina virtual. Para obter informações sobre a conformidade regulatória com o Azure, consulte [central de confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Controlar o acesso à VM do SQL

Ao criar sua máquina virtual SQL Server, considere como controlar cuidadosamente quem tem acesso ao computador e SQL Server. Em geral, você deve fazer o seguinte:

- Restringir o acesso a SQL Server apenas aos aplicativos e clientes que precisam dele.
- Siga as práticas recomendadas para gerenciar contas de usuário e senhas.

As seções a seguir fornecem sugestões sobre como pensar nesses pontos.

## <a name="secure-connections"></a>Conexões seguras

Quando você cria uma máquina virtual SQL Server com uma imagem da galeria, a **SQL Server** opção de conectividade oferece a opção de **local (dentro da VM)** , **privada (dentro da rede virtual)** ou **pública (Internet)** .

![Conectividade de SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Para obter a melhor segurança, escolha a opção mais restritiva para seu cenário. Por exemplo, se você estiver executando um aplicativo que acessa SQL Server na mesma VM, o **local** será a opção mais segura. Se você estiver executando um aplicativo do Azure que requer acesso ao SQL Server, o **privado** protegerá a comunicação com SQL Server somente dentro da [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md)especificada. Se você precisar de acesso **público** (Internet) à VM SQL Server, certifique-se de seguir outras práticas recomendadas neste tópico para reduzir a área de superfície de ataque.

As opções selecionadas no portal usam regras de segurança de entrada no NSG ( [grupo de segurança de rede](../../../virtual-network/security-overview.md) ) da VM para permitir ou negar o tráfego de rede para sua máquina virtual. Você pode modificar ou criar novas regras de NSG de entrada para permitir o tráfego para a porta de SQL Server (padrão 1433). Você também pode especificar endereços IP específicos que têm permissão para se comunicar por essa porta.

![Regras do grupo de segurança de rede](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Além das regras NSG para restringir o tráfego de rede, você também pode usar o Firewall do Windows na máquina virtual.

Se você estiver usando pontos de extremidade com o modelo de implantação clássico, remova todos os pontos de extremidade na máquina virtual se não os usar. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Manage The ACL on a Endpoint](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Isso não é necessário para VMs que usam o Gerenciador de recursos.

Por fim, considere habilitar conexões criptografadas para a instância do SQL Server Mecanismo de Banco de Dados em sua máquina virtual do Azure. Configure a instância do SQL Server com um certificado assinado. Para obter mais informações, consulte [habilitar conexões criptografadas com a mecanismo de banco de dados e a sintaxe da cadeia de](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) [conexão](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Usar uma porta não padrão

Por padrão, SQL Server escuta em uma porta conhecida, 1433. Para aumentar a segurança, configure SQL Server para escutar em uma porta não padrão, como 1401. Se você provisionar uma imagem da Galeria de SQL Server no portal do Azure, poderá especificar essa porta na folha **configurações de SQL Server** .

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para configurar isso após o provisionamento, você tem duas opções:

- Para VMs do Gerenciador de recursos, você pode selecionar **segurança** no [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). Isso fornece uma opção para alterar a porta.

  ![Alteração de porta TCP no portal](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Para VMs clássicas ou para VMs SQL Server que não foram provisionadas com o portal, você pode configurar manualmente a porta conectando-se remotamente à VM. Para obter as etapas de configuração, consulte [configurar um servidor para escutar em uma porta TCP específica](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Se você usar essa técnica manual, também precisará adicionar uma regra de firewall do Windows para permitir o tráfego de entrada nessa porta TCP.

> [!IMPORTANT]
> A especificação de uma porta não padrão é uma boa ideia se sua porta de SQL Server estiver aberta para conexões de Internet pública.

Quando SQL Server estiver escutando em uma porta não padrão, você deverá especificar a porta ao se conectar. Por exemplo, considere um cenário em que o endereço IP do servidor é 13.55.255.255 e SQL Server está escutando na porta 1401. Para se conectar ao SQL Server, você especificaria `13.55.255.255,1401` na cadeia de conexão.

## <a name="manage-accounts"></a>Gerir contas

Você não quer que os invasores adivinhem facilmente nomes de conta ou senhas. Use as seguintes dicas para ajudar:

- Crie uma conta de administrador local exclusiva que não seja denominada **administrador**.

- Use senhas fortes complexas para todas as suas contas. Para obter mais informações sobre como criar uma senha forte, consulte [o artigo criar uma senha forte](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) .

- Por padrão, o Azure seleciona a autenticação do Windows durante SQL Server configuração da máquina virtual. Portanto, o logon **SA** é desabilitado e uma senha é atribuída pela instalação do. Recomendamos que o logon de **SA** não seja usado ou habilitado. Se você precisar ter um logon do SQL, use uma das estratégias a seguir:

  - Crie uma conta SQL com um nome exclusivo que tenha a associação **sysadmin** . Você pode fazer isso no portal habilitando a **autenticação do SQL** durante o provisionamento.

    > [!TIP] 
    > Se você não habilitar a autenticação do SQL durante o provisionamento, será necessário alterar manualmente o modo de autenticação para **SQL Server e o modo de autenticação do Windows**. Para obter mais informações, consulte [alterar o modo de autenticação do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se você precisar usar o logon **SA** , habilite o logon após o provisionamento e atribua uma nova senha forte.

## <a name="follow-on-premises-best-practices"></a>Siga as práticas recomendadas locais

Além das práticas descritas neste tópico, recomendamos que você revise e implemente as práticas de segurança locais tradicionais, quando aplicável. Para obter mais informações, consulte [Security Considerations for a SQL Server Installation](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Próximos Passos

Se você também estiver interessado em práticas recomendadas sobre o desempenho, consulte [práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados à execução de SQL Server em VMs do Azure, consulte [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

