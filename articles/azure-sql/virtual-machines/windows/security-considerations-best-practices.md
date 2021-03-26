---
title: Considerações de segurança | Microsoft Docs
description: Este tópico fornece orientações gerais para assegurar o funcionamento do SQL Server numa máquina virtual Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7ff77a407dfa87e408170573249876bbefee0abe
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558585"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tópico inclui diretrizes de segurança gerais que ajudam a estabelecer acesso seguro a instâncias do SQL Server numa máquina virtual Azure (VM).

O Azure cumpre vários regulamentos e padrões da indústria que lhe permitem construir uma solução compatível com o SQL Server a funcionar numa máquina virtual. Para obter informações sobre o cumprimento regulamentar do Azure, consulte [o Azure Trust Center](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Controle o acesso à máquina virtual SQL

Quando criar a sua máquina virtual SQL Server, considere como controlar cuidadosamente quem tem acesso à máquina e ao SQL Server. Em geral, deve fazer o seguinte:

- Restringir o acesso ao SQL Server apenas às aplicações e clientes que dele necessitem.
- Siga as melhores práticas para gerir contas de utilizador e senhas.

As seguintes secções fornecem sugestões sobre a reflexão através destes pontos.

## <a name="secure-connections"></a>Ligações seguras

Quando cria uma máquina virtual SQL Server com uma imagem de galeria, a opção **de conectividade do servidor SQL** dá-lhe a escolha de Local **(dentro de VM)**, **Privado (dentro da Rede Virtual)** ou **Público (Internet)**.

![Conectividade do Servidor SQL](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Para melhor segurança, escolha a opção mais restritiva para o seu cenário. Por exemplo, se estiver a executar uma aplicação que acede ao SQL Server no mesmo VM, então o **Local** é a escolha mais segura. Se estiver a executar uma aplicação Azure que requer acesso ao SQL Server, então o **Private** assegura a comunicação ao SQL Server apenas dentro da [rede virtual Azure](../../../virtual-network/virtual-networks-overview.md)especificada. Se necessitar de acesso **público** (internet) ao SQL Server VM, certifique-se de seguir outras boas práticas neste tópico para reduzir a área da superfície de ataque.

As opções selecionadas no portal utilizam regras de segurança de entrada no grupo de segurança de [rede](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG) da VM para permitir ou negar o tráfego de rede à sua máquina virtual. Pode modificar ou criar novas regras NSG de entrada para permitir o tráfego na porta do Servidor SQL (padrão 1433). Também pode especificar endereços IP específicos que são autorizados a comunicar através desta porta.

![Regras do grupo de segurança de rede](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Além das regras NSG para restringir o tráfego de rede, também pode utilizar o Windows Firewall na máquina virtual.

Se estiver a utilizar pontos finais com o modelo de implantação clássico, remova quaisquer pontos finais na máquina virtual se não os utilizar. Para obter instruções sobre a utilização de ACLs com pontos finais, consulte [gerir a ACL num ponto final](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Isto não é necessário para VMs que usam o Gestor de Recursos Azure.

Por fim, considere ativar as ligações encriptadas, por exemplo, o motor de base de dados do SQL Server na sua máquina virtual Azure. Configurar a instância do servidor SQL com um certificado assinado. Para obter mais informações, consulte [Ativar as ligações encriptadas à sêx de cabo do motor de base](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) de dados e à [sintaxe de cadeia de ligação.](/dotnet/framework/data/adonet/connection-string-syntax)

## <a name="encryption"></a>Encriptação

Os discos geridos oferecem Server-Side encriptação e encriptação do disco Azure. [A Encriptação do Lado do Servidor](../../../virtual-machines/disk-encryption.md) fornece encriptação em repouso e salvaguarda os seus dados para atender aos seus compromissos de segurança organizacional e de conformidade. [A Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utiliza a tecnologia Bitlocker ou DM-Crypt e integra-se com o Azure Key Vault para encriptar tanto o SISTEMA como os discos de dados. 

## <a name="use-a-non-default-port"></a>Utilize uma porta não padrão

Por predefinição, o SQL Server escuta numa porta bem conhecida, a 1433. Para uma maior segurança, configuure o SQL Server para ouvir numa porta não padrão, como o 1401. Se forja uma imagem de galeria SQL Server no portal Azure, pode especificar esta porta na lâmina de definições do **SQL Server.**

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para configurar isto após o provisionamento, você tem duas opções:

- Para VMs do Gestor de Recursos, pode selecionar **Segurança** a partir do [recurso de máquinas virtuais SQL.](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) Isto oferece uma opção para mudar a porta.

  ![Mudança de porta TCP no portal](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Para VMs clássicos ou para VMs do servidor SQL que não foram a provisionados com o portal, pode configurar manualmente a porta ligando-se remotamente ao VM. Para os passos de configuração, consulte [configurar um Servidor para ouvir numa porta TCP específica](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Se utilizar esta técnica manual, também tem de adicionar uma regra do Windows Firewall para permitir a entrada de tráfego nessa porta TCP.

> [!IMPORTANT]
> Especificar uma porta não padrão é uma boa ideia se a porta do SqL Server estiver aberta a ligações de internet públicas.

Quando o SQL Server estiver a ouvir uma porta não padrão, tem de especificar a porta quando se liga. Por exemplo, considere um cenário em que o endereço IP do servidor seja 13.55.255.255 e o SQL Server esteja a ouvir na porta 1401. Para ligar ao SQL Server, especificaria `13.55.255.255,1401` na cadeia de ligação.

## <a name="manage-accounts"></a>Gerir contas

Não quer que os agressores adivinhem facilmente nomes de contas ou palavras-passe. Use as seguintes dicas para ajudar:

- Crie uma conta de administrador local única que não seja nomeada **Administradora.**

- Use senhas fortes complexas para todas as suas contas. Para obter mais informações sobre como criar uma palavra-passe forte, consulte Criar um artigo [de senha forte.](https://support.microsoft.com/account-billing/how-to-create-a-strong-password-for-your-microsoft-account-f67e4ddd-0dbe-cd75-cebe-0cfda3cf7386)

- Por predefinição, o Azure seleciona a autenticação do Windows durante a configuração da máquina virtual SQL Server. Portanto, o login **SA** é desativado e uma palavra-passe é atribuída por configuração. Recomendamos que o login **SA** não seja utilizado ou ativado. Se tiver de fazer um login SQL, utilize uma das seguintes estratégias:

  - Crie uma conta SQL com um nome único que tenha adesão **à Sysadmin.** Pode fazê-lo a partir do portal, permitindo a **autenticação SQL** durante o provisionamento.

    > [!TIP] 
    > Se não ativar a autenticação SQL durante o fornecimento, tem de alterar manualmente o modo de autenticação para **SQL Server e Modo de Autenticação do Windows**. Para obter mais informações, consulte [o Modo de Autenticação do Servidor De Alteração](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se tiver de utilizar o login **SA,** ative o login após o provisionamento e atribua uma nova senha forte.

## <a name="additional-best-practices"></a>Melhores práticas adicionais

Além das práticas descritas neste tópico, recomendamos que reveja e implemente as melhores práticas de segurança tanto das práticas tradicionais de segurança no local, como das melhores práticas de segurança da máquina virtual. 

Para obter mais informações sobre práticas de segurança no local, consulte [Considerações de Segurança para uma Instalação do Servidor SQL](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) e para o [centro de segurança.](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database) 

Para obter mais informações sobre a segurança da máquina virtual, consulte a visão geral de segurança das [máquinas virtuais.](../../../security/fundamentals/virtual-machines-overview.md)


## <a name="next-steps"></a>Passos seguintes

Se também estiver interessado nas melhores práticas em torno do desempenho, consulte as [Melhores Práticas de Desempenho para SQL Server em Azure Virtual Machines](performance-guidelines-best-practices.md).

Para outros tópicos relacionados com a execução do SQL Server em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).