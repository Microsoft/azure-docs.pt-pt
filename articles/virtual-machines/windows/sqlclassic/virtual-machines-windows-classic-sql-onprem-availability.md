---
title: Alargar as instalações sempre em grupos de disponibilidade para o Azure [ Microsoft Docs
description: Este tutorial utiliza recursos criados com o modelo de implementação clássico, e descreve como usar o assistente de Réplica add no SQL Server Management Studio (SSMS) para adicionar uma réplica do Grupo Always On Availability group em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978054"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Expandir Grupos de Disponibilidade Always On no local para o Azure
Os Grupos sempre em disponibilidade proporcionam uma elevada disponibilidade para grupos de base de dados adicionando réplicas secundárias. Estas réplicas permitem a falha nas bases de dados em caso de falha. Além disso, podem ser usados para descarregar cargas de trabalho de leitura ou tarefas de backup.

Pode estender os grupos de disponibilidade no local ao Microsoft Azure, disponibilizando um ou mais VMs Azure com O Servidor SQL e, em seguida, adicionando-os como réplicas aos seus grupos de disponibilidade no local.

Este tutorial assume que tem o seguinte:

* Uma subscrição ativa do Azure. Pode [inscrever-se para um julgamento gratuito.](https://azure.microsoft.com/pricing/free-trial/)
* Um grupo sempre disponível no local. Para obter mais informações sobre grupos de disponibilidade, consulte [Sempre em Grupos de Disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx).
* Conectividade entre a rede no local e a sua rede virtual Azure. Para obter mais informações sobre a criação desta rede virtual, consulte [Criar uma ligação Site-a-Site utilizando o portal Azure (clássico)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## <a name="add-azure-replica-wizard"></a>Adicione o Assistente de Réplica Azure
Esta secção mostra-lhe como utilizar o Assistente de **Réplica Add Azure** para estender a sua solução sempre disponível para incluir réplicas Azure.

> [!IMPORTANT]
> O **Add Azure Replica Wizard** apenas suporta máquinas virtuais criadas com o modelo de implementação Classic. As novas implementações vm devem utilizar o novo modelo de Gestor de Recursos. Se estiver a utilizar VMs com Gestor de Recursos, então deve adicionar manualmente a réplica secundária do Azure utilizando comandos Transact-SQL (não mostrados aqui). Este assistente não funcionará no cenário do Gestor de Recursos.

1. A partir do Estúdio de Gestão de Servidores SQL, expanda sempre em**grupos** > de disponibilidade de **alta disponibilidade** > **[Nome do seu Grupo de Disponibilidade]**.
2. Clique direito **Nas Réplicas**de Disponibilidade, em seguida, clique em **Adicionar Réplica**.
3. Por predefinição, é apresentado o **Add Replica to Availability Group Wizard.** Clique em **Seguinte**.  Se tiver selecionado a opção **Não mostrar novamente esta página** na parte inferior da página durante um lançamento anterior deste assistente, este ecrã não será apresentado.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Será obrigado a ligar-se a todas as réplicas secundárias existentes. Pode clicar no **Connect...** ao lado de cada réplica ou pode clicar em **Ligar Tudo...** na parte inferior do ecrã. Após a autenticação, clique **em Next** para avançar para o ecrã seguinte.
5. Na página **'Replicões Especificadas',** vários separadores estão listados em todos os topos: **Réplicas, Pontos** **Finais,** **Preferências**de Backup e **Ouvinte**. A partir do separador **Replicas,** clique em **Adicionar Réplica Azure...** para lançar o Add Azure Replica Wizard.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Selecione um Certificado de Gestão Azure existente na loja de certificados Windows local se já tiver instalado um antes. Selecione ou introduza o id de uma subscrição Azure se já tiver usado uma antes. Pode clicar em Baixar para descarregar e instalar um Certificado de Gestão Azure e fazer o download da lista de subscrições através de uma conta Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Você vai povoar cada campo na página com valores que serão usados para criar a Máquina Virtual Azure (VM) que irá acolher a réplica.
   
   | Definição | Descrição |
   | --- | --- |
   | **Imagem** |Selecione a combinação desejada de OS e SQL Server |
   | **Tamanho VM** |Selecione o tamanho do VM que melhor se adequa às suas necessidades de negócio |
   | **Nome VM** |Especifique um nome único para o novo VM. O nome deve conter entre 3 e 15 caracteres, pode conter apenas letras, números e hífenes, e deve começar com uma letra e terminar com uma letra ou número. |
   | **Nome de utilizador VM** |Especifique um nome de utilizador que se tornará a conta de administrador no VM |
   | **Senha do Administrador VM** |Especificar uma palavra-passe para a nova conta |
   | **Confirmar palavra-passe** |Confirme a senha da nova conta |
   | **Rede Virtual** |Especifique a rede virtual Azure que o novo VM deve utilizar. Para obter mais informações sobre redes virtuais, consulte a [visão geral da rede virtual.](../../../virtual-network/virtual-networks-overview.md) |
   | **Subnet de rede virtual** |Especifique a subnet de rede virtual que o novo VM deve utilizar |
   | **Domínio** |Confirmar que o valor pré-povoado para o domínio está correto |
   | **Nome do utilizador do domínio** |Especifique uma conta que está no grupo de administradores locais nos nós de cluster local |
   | **Palavra-passe** |Especificar a palavra-passe para o nome de utilizador de domínio |
8. Clique em **OK** para validar as definições de implementação.
9. Os termos legais são apresentados em seguida. Leia e clique **em OK** se concordar com estes termos.
10. A página **'Replicões especificais'** é novamente exibida. Verifique as definições para a nova réplica Azure nas **réplicas,** **pontos finais**e separadores **de preferências** de backup. Modifique as definições para satisfazer os seus requisitos de negócio.  Para obter mais informações sobre os parâmetros contidos nestes separadores, consulte [a Página de Réplicas Especificadas (Novo Assistente de Grupo de Disponibilidade/Assistente de Réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Note que os ouvintes não podem ser criados usando o separador Listener para Grupos de Disponibilidade que contêm réplicas Azure. Além disso, se um ouvinte já tiver sido criado antes do lançamento do Assistente, receberá uma mensagem indicando que não é suportado em Azure. Vamos ver como criar ouvintes na secção **Criar um Grupo de Escutas** de Disponibilidade.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Clique em **Seguinte**.
12. Selecione o método de sincronização de dados que pretende utilizar na página **de Sincronização de Dados Inicial Select** e clique em **Next**. Para a maioria dos cenários, selecione **Full Data Synchronization**. Para obter mais informações sobre os métodos de sincronização de dados, consulte [Selecione Página inicial de sincronização de dados (Sempre Em Função dos Assistentes do Grupo)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Reveja os resultados na página **de Validação.** Corrija as questões pendentes e reecorra a validação se necessário. Clique em **Seguinte**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Reveja as definições na página **Resumo** e, em seguida, clique em **Terminar**.
15. O processo de provisionamento começa. Quando o assistente completar com sucesso, clique **perto** de sair do assistente.

> [!NOTE]
> O Assistente de Réplica Add Azure cria um ficheiro de registo nos Utilizadores\User Name\AppData\Local\SQL Server\AddReplicaWizard. Este ficheiro de registo pode ser usado para resolver as implementações de réplicas do Azure falhadas. Se o Assistente não executar qualquer ação, todas as operações anteriores são relançadas, incluindo a abater o VM provisionado.
> 
> 

## <a name="create-an-availability-group-listener"></a>Criar um ouvinte de grupo de disponibilidade
Depois de criado o grupo de disponibilidade, deverá criar um ouvinte para os clientes se conectarem às réplicas. Os ouvintes direcionam as ligações para a primária ou para uma réplica secundária apenas de leitura. Para obter mais informações sobre os ouvintes, consulte [Configure um ouvinte ILB para Sempre Em Grupos](../classic/ps-sql-int-listener.md)de Disponibilidade em Azure .

## <a name="next-steps"></a>Passos seguintes
Além de utilizar o Assistente de **Réplica Add Azure** para estender o seu Grupo Always On Availability para O Azure, também pode mover algumas cargas de trabalho do Servidor SQL completamente para O Azure. Para começar, consulte [o provisionamento de uma máquina virtual do Servidor SQL no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados com a execução do Servidor SQL em VMs Azure, consulte [o Servidor SQL em Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

