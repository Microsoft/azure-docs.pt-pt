---
title: Considerações de segurança para o SQL Server no Azure | Documentos da Microsoft
description: Este tópico fornece orientações gerais para proteger o SQL Server em execução na máquina Virtual do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 69b6bd07699d179fc87ac6c5364a7a34b23d14eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477550"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações de Segurança para SQL Server em Máquinas Virtuais do Azure

Este tópico inclui as diretrizes de segurança geral que ajudam a estabelecer acesso seguro a instâncias do SQL Server numa máquina virtual do Azure (VM).

Azure está em conformidade com várias normas do setor e padrões que podem permitir que criar uma solução em conformidade com o SQL Server em execução numa máquina virtual. Para obter informações sobre a conformidade a normas com o Azure, consulte [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Controlar o acesso à VM do SQL

Ao criar a máquina virtual do SQL Server, considere como cuidadosamente controlar quem tem acesso à máquina e para o SQL Server. Em geral, deve fazer o seguinte:

- Restringir o acesso ao SQL Server para apenas as aplicações e os clientes que precisam dele.
- Siga as melhores práticas para a gestão de contas de utilizador e palavras-passe.

As secções seguintes fornecem sugestões sobre pensando esses pontos.

## <a name="secure-connections"></a>Ligações seguras

Quando cria uma máquina virtual do SQL Server com uma imagem de galeria, a **conectividade do SQL Server** opção dá-lhe a opção de **Local (no interior da VM)**, **privado (dentro da rede Virtual)**, ou **público (Internet)**.

![Conectividade do SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Para garantir a segurança melhor, escolha a opção mais restritiva para o seu cenário. Por exemplo, se estiver a executar uma aplicação que acede ao SQL Server na mesma VM, em seguida, **Local** é a opção mais segura. Se estiver a executar uma aplicação do Azure, que requer acesso ao SQL Server, em seguida, **privada** protege a comunicação para o SQL Server apenas dentro de especificado [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Se precisar **público** (internet) acesso à VM do SQL Server, em seguida, certifique-se seguir outras práticas recomendadas neste tópico para reduzir a área de superfície de ataque.

As opções selecionadas no portal do utilizam regras de segurança de entrada da VM [grupo de segurança de rede](../../../virtual-network/security-overview.md) (NSG) para permitir ou negar o tráfego de rede à máquina virtual. Pode modificar ou criar novas regras NSG de entrada para permitir o tráfego para a porta do SQL Server (padrão é 1433). Também pode especificar endereços IP específicos que têm permissão para comunicar através desta porta.

![Regras do grupo de segurança de rede](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Para além das regras NSG para restringir o tráfego de rede, também pode utilizar a Firewall do Windows na máquina virtual.

Se estiver a utilizar pontos de extremidade com o modelo de implementação clássica, remova quaisquer pontos de extremidade na máquina virtual, se não usá-los. Para obter instruções sobre como utilizar as ACLs com pontos finais, consulte [gerir a ACL num ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Isso não é necessário para as VMs que utilizam o Gestor de recursos.

Finalmente, considere ativar ligações encriptadas para a instância do motor de base de dados do SQL Server na sua máquina virtual do Azure. Configure instância do SQL server com um certificado assinado. Para obter mais informações, consulte [ativar ligações encriptadas para o motor de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) e [sintaxe da cadeia de ligação](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Utilizar uma porta não predefinido

Por predefinição, o SQL Server escuta na porta bem conhecida, 1433. Para maior segurança, configure o SQL Server para escutar numa porta não predefinidos, como 1401. Se aprovisionar uma imagem da galeria do SQL Server no portal do Azure, pode especificar esta porta no **definições do SQL Server** painel.

Para configurar esta opção após o aprovisionamento, tem duas opções:

- Para VMs do Resource Manager, pode selecionar **configuração do SQL Server** do painel de descrição geral da VM. Isso fornece uma opção para alterar a porta.

  ![Alterar a porta TCP no portal](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Para VMs clássicas ou VMs do SQL Server que não tenham sido aprovisionados com o portal, pode configurar manualmente a porta ao ligar remotamente à VM. Para obter os passos de configuração, consulte [configurar um servidor para escutar numa porta TCP específica](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Se usar essa técnica manual, terá também de adicionar uma regra de Firewall do Windows para permitir o tráfego de entrada nessa porta de TCP.

> [!IMPORTANT]
> Especificar uma porta não predefinido é uma boa idéia se a porta do SQL Server está aberta para ligações de internet pública.

Quando o SQL Server está à escuta numa porta não predefinido, tem de especificar a porta quando se liga. Por exemplo, considere um cenário em que o endereço IP do servidor é 13.55.255.255 e SQL Server está escutando na porta 1401. Para ligar ao SQL Server, tem de especificar `13.55.255.255,1401` na cadeia de ligação.

## <a name="manage-accounts"></a>Gerir contas

Não quer os atacantes facilmente adivinhar os nomes de contas ou palavras-passe. Utilize as seguintes sugestões para:

- Criar uma conta de administrador local exclusivas que não tem o nome **administrador**.

- Utilize palavras-passe fortes complexas para todas as suas contas. Para obter mais informações sobre como criar uma palavra-passe segura, consulte [criar uma palavra-passe forte](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) artigo.

- Por predefinição, o Azure seleciona a autenticação do Windows durante a configuração de Máquina Virtual do SQL Server. Por conseguinte, o **SA** início de sessão está desabilitado e uma palavra-passe é atribuída pela configuração. Recomendamos que o **SA** início de sessão não deve ser utilizado ou ativado. Se tem de ter um início de sessão do SQL, utilize uma das seguintes estratégias:

  - Criar uma conta SQL com um nome exclusivo que tenha **sysadmin** associação. Pode fazê-lo no portal, permitindo **autenticação de SQL** durante o aprovisionamento.

    > [!TIP] 
    > Se não ativar a autenticação de SQL durante o aprovisionamento, tem de alterar manualmente o modo de autenticação para **SQL Server e o modo de autenticação do Windows**. Para obter mais informações, consulte [Alterar modo de autenticação de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se tiver de utilizar o **SA** início de sessão, ativar o início de sessão após o aprovisionamento e atribuir uma nova palavra-passe segura.

## <a name="follow-on-premises-best-practices"></a>Siga as melhores práticas de no local

Além das práticas descritas neste tópico, recomendamos que reveja e implemente as práticas de segurança tradicionais no local, onde aplicável. Para obter mais informações, consulte [considerações de segurança para uma instalação do SQL Server](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Próximos Passos

Se também estiver interessado em práticas recomendadas sobre o desempenho, consulte [melhores práticas de desempenho para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados ao executar o SQL Server em VMs do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

