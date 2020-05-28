---
title: Considerações de segurança para o Servidor SQL em Azure Microsoft Docs
description: Este tópico fornece orientações gerais para assegurar o Servidor SQL em execução numa Máquina Virtual Azure.
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
ms.openlocfilehash: f04620430571a1f86d601eac2b1b662c77499a76
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047266"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações de Segurança para SQL Server em Máquinas Virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tópico inclui diretrizes de segurança globais que ajudam a estabelecer acesso seguro às instâncias do SQL Server numa máquina virtual Azure (VM).

O Azure cumpre vários regulamentos e padrões da indústria que lhe permitem construir uma solução compatível com o SQL Server a funcionar numa máquina virtual. Para obter informações sobre o cumprimento regulamentar do Azure, consulte [o Azure Trust Center.](https://azure.microsoft.com/support/trust-center/)

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Controlar o acesso ao SQL VM

Quando criar a sua máquina virtual SQL Server, considere como controlar cuidadosamente quem tem acesso à máquina e ao Servidor SQL. Em geral, deve fazer o seguinte:

- Restringir o acesso ao SQL Server apenas às aplicações e clientes que dele necessitem.
- Siga as melhores práticas para gerir as contas de utilizador e as palavras-passe.

As seguintes secções fornecem sugestões sobre a reflexão através destes pontos.

## <a name="secure-connections"></a>Ligações seguras

Quando cria uma máquina virtual SQL Server com uma imagem de galeria, a opção **de conectividade do Servidor SQL** dá-lhe a escolha de Local (dentro de **VM)**, **Privado (dentro da Rede Virtual)** ou **Público (Internet)**.

![Conectividade Do Servidor SQL](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Para obter a melhor segurança, escolha a opção mais restritiva para o seu cenário. Por exemplo, se estiver a executar uma aplicação que acede ao SQL Server no mesmo VM, então **o Local** é a escolha mais segura. Se estiver a executar uma aplicação Azure que requer acesso ao Servidor SQL, então o **Private** assegura a comunicação ao SQL Server apenas dentro da [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md)especificada . Se necessitar de acesso **público** (internet) ao VM do Servidor SQL, certifique-se de seguir outras boas práticas neste tópico para reduzir a sua área de superfície de ataque.

As opções selecionadas no portal utilizam regras de segurança de entrada no grupo de segurança de [rede](../../../active-directory/identity-protection/security-overview.md) (NSG) da VM para permitir ou negar o tráfego de rede à sua máquina virtual. Pode modificar ou criar novas regras de NSG de entrada para permitir o tráfego na porta SQL Server (padrão 1433). Também pode especificar endereços IP específicos que podem comunicar através desta porta.

![Regras do grupo de segurança de rede](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Além das regras do NSG para restringir o tráfego de rede, também pode utilizar o Firewall do Windows na máquina virtual.

Se estiver a utilizar pontos finais com o modelo de implementação clássico, remova quaisquer pontos finais na máquina virtual se não os utilizar. Para obter instruções sobre a utilização de ACLs com pontos finais, consulte [Gerir o ACL num ponto final](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Isto não é necessário para vMs que usam o Gestor de Recursos.

Por fim, considere permitir ligações encriptadas para a instância do Motor de Base de Dados do Servidor SQL na sua máquina virtual Azure. Configure a instância do servidor SQL com um certificado assinado. Para mais informações, consulte [Enable Encrypted Connections to the Database Engine](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) and Connection String [Syntax](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="encryption"></a>Encriptação

Os discos geridos oferecem encriptação lateral do servidor e encriptação do disco Azure. [A Encriptação do Lado](/azure/virtual-machines/windows/disk-encryption) do Servidor fornece encriptação em repouso e salvaguarda os seus dados para atender aos seus compromissos de segurança organizacional e conformidade. [A Encriptação do Disco Azure](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) utiliza tecnologia Bitlocker ou DM-Crypt e integra-se com o Azure Key Vault para encriptar tanto os DISCOS como os discos de dados. 

## <a name="use-a-non-default-port"></a>Utilize uma porta não predefinida

Por predefinição, o SQL Server escuta numa porta bem conhecida, a 1433. Para uma maior segurança, configure o SQL Server para ouvir uma porta não predefinida, como o 1401. Se fornecer uma imagem de galeria SQL Server no portal Azure, pode especificar esta porta na lâmina de definições do **Servidor SQL.**

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para configurar isto após o fornecimento, tem duas opções:

- Para VMs de Gestor de Recursos, pode selecionar **segurança** a partir do recurso de [máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Isto fornece uma opção para alterar a porta.

  ![Mudança de porta TCP no portal](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Para VMs clássicos ou para VMs de servidor SQL que não foram aprovisionados com o portal, pode configurar manualmente a porta ligando-a remotamente ao VM. Para os passos de configuração, consulte [Configure um Servidor para ouvir uma porta Específica de TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Se utilizar esta técnica manual, também precisa de adicionar uma regra do Windows Firewall para permitir o tráfego de entrada naquela porta TCP.

> [!IMPORTANT]
> Especificar uma porta não predefinida é uma boa ideia se a sua porta SQL Server estiver aberta a ligações de internet pública.

Quando o SQL Server estiver a ouvir uma porta não predefinida, deve especificar a porta quando se ligar. Por exemplo, considere um cenário em que o endereço IP do servidor é 13.55.255.255 e o Servidor SQL está a ouvir na porta 1401. Para ligar ao SQL Server, especificaria `13.55.255.255,1401` na cadeia de ligação.

## <a name="manage-accounts"></a>Gerir contas

Não queres que os atacantes adivinhem facilmente nomes de contas ou palavras-passe. Utilize as seguintes dicas para ajudar:

- Crie uma conta única de administrador local que não seja nomeada **Administradora.**

- Utilize senhas fortes complexas para todas as suas contas. Para obter mais informações sobre como criar uma senha forte, consulte Criar um artigo [de senha forte.](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password)

- Por predefinição, o Azure seleciona a autenticação do Windows durante a configuração da Máquina Virtual do Servidor SQL. Portanto, o login **SA** é desativado e uma palavra-passe é atribuída por configuração. Recomendamos que o login **SA** não seja utilizado ou ativado. Se tiver de ter um login SQL, utilize uma das seguintes estratégias:

  - Crie uma conta SQL com um nome único que tenha filiação em **sysadmin.** Pode fazê-lo a partir do portal, permitindo a **Autenticação SQL** durante o fornecimento.

    > [!TIP] 
    > Se não ativar a Autenticação SQL durante o fornecimento, tem de alterar manualmente o modo de autenticação para o Modo de Autenticação do **Servidor SQL e para o Windows**. Para mais informações, consulte o Modo de [Autenticação do Servidor de Alteração](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se tiver de utilizar o login **SA,** ative o login após o fornecimento e atribua uma nova senha forte.

## <a name="additional-best-practices"></a>Melhores práticas adicionais

Para além das práticas descritas neste tema, recomendamos que reveja e implemente as melhores práticas de segurança, tanto a partir das práticas tradicionais de segurança no local, como das melhores práticas de segurança das máquinas virtuais. 

Para obter mais informações sobre práticas de segurança no local, consulte [considerações de segurança para uma instalação do servidor SQL](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) e o centro de [segurança](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Para obter mais informações sobre a segurança das máquinas virtuais, consulte a visão geral da segurança das [máquinas virtuais](/azure/security/fundamentals/virtual-machines-overview).


## <a name="next-steps"></a>Passos Seguintes

Se também está interessado nas melhores práticas em torno do desempenho, consulte as [Melhores Práticas de Desempenho para o Servidor SQL em Máquinas Virtuais Azure](performance-guidelines-best-practices.md).

Para outros tópicos relacionados com a execução do Servidor SQL em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).

