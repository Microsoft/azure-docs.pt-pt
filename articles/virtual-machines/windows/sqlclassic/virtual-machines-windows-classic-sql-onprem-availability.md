---
title: Alargar sempre no local grupos de disponibilidade para aZure Microsoft Docs
description: Este tutorial utiliza recursos criados com o modelo de implementação clássico, e descreve como usar o assistente de réplica de adicionar no SQL Server Management Studio (SSMS) para adicionar uma réplica do Grupo Always On Availability em Azure.
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
ms.openlocfilehash: 928162282ca973811315728a07b4fbe40100324e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84017584"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Expandir Grupos de Disponibilidade Always On no local para o Azure
Os Grupos Sempre Disponível fornecem alta disponibilidade para grupos de base de dados adicionando réplicas secundárias. Estas réplicas permitem falhar sobre bases de dados em caso de falha. Além disso, podem ser usados para descarregar cargas de trabalho ou tarefas de backup.

Pode estender os Grupos de Disponibilidade no local ao Microsoft Azure, fornecendo um ou mais VMs Azure com O Servidor SQL e, em seguida, adicionando-os como réplicas aos grupos de disponibilidade no local.

Este tutorial pressupõe que tem o seguinte:

* Uma subscrição ativa do Azure. Pode [inscrever-se para um julgamento gratuito.](https://azure.microsoft.com/pricing/free-trial/)
* Um grupo sempre disponível no local. Para obter mais informações sobre grupos de disponibilidade, consulte [Sempre Em Grupos de Disponibilidade.](https://msdn.microsoft.com/library/hh510230.aspx)
* Conectividade entre a rede no local e a sua rede virtual Azure. Para obter mais informações sobre a criação desta rede virtual, consulte [Criar uma ligação Site-to-Site utilizando o portal Azure (clássico)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## <a name="add-azure-replica-wizard"></a>Adicionar assistente de réplica Azure
Esta secção mostra-lhe como utilizar o **Add Azure Replica Wizard** para estender a solução "Always On Availability Group" para incluir réplicas Azure.

> [!IMPORTANT]
> O **Add Azure Replica Wizard** suporta apenas máquinas virtuais criadas com o modelo de implementação Classic. As novas implementações de VM devem utilizar o modelo mais recente do Gestor de Recursos. Se estiver a utilizar VMs com Gestor de Recursos, deve adicionar manualmente a réplica secundária do Azure utilizando comandos Transact-SQL (não mostrados aqui). Este assistente não funcionará no cenário de Gestor de Recursos.

1. A partir do SQL Server Management Studio, expanda sempre em grupos de disponibilidade **de alta disponibilidade**  >  **Availability Groups**  >  **[Nome do seu Grupo de Disponibilidade]**.
2. Clique com o botão **direito Réplicas de disponibilidade,** em seguida, clique em **Adicionar Réplica**.
3. Por predefinição, é apresentado o **Assistente do Grupo de Adicionar à Disponibilidade.** Clique em **Seguinte**.  Se tiver selecionado a opção Não voltar a **mostrar esta página** na parte inferior da página durante o lançamento anterior deste assistente, este ecrã não será exibido.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Será obrigado a ligar-se a todas as réplicas secundárias existentes. Pode clicar no **Connect...** ao lado de cada réplica ou pode clicar **em Ligar Tudo...** na parte inferior do ecrã. Após a autenticação, clique **em Seguinte** para avançar para o ecrã seguinte.
5. Na página **'Especificar réplicas',** vários separadores são listados em todos os **topos: Réplicas,** **Pontos finais,** **Preferências de backup**e **ouvinte**. A partir do separador **Replicas,** clique em **Adicionar Réplica Azure...** para lançar o Add Azure Replica Wizard.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Selecione um Certificado de Gestão Azure existente na loja de certificados local Windows se já tiver instalado um antes. Selecione ou introduza o id de uma subscrição Azure se já tiver usado uma antes. Pode clicar em Baixar para descarregar e instalar um Certificado de Gestão Azure e descarregar a lista de subscrições utilizando uma conta Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Irá preencher cada campo na página com valores que serão utilizados para criar a Máquina Virtual Azure (VM) que irá acolher a réplica.
   
   | Definição | Descrição |
   | --- | --- |
   | **Imagem** |Selecione a combinação desejada de SERVIDOR OS e SQL |
   | **Tamanho VM** |Selecione o tamanho de VM que melhor se adapte às necessidades do seu negócio |
   | **Nome VM** |Especifique um nome único para o novo VM. O nome deve conter entre 3 e 15 caracteres, pode conter apenas letras, números e hífens, e deve começar com uma letra e terminar com uma letra ou número. |
   | **Nome de utilizador VM** |Especifique um nome de utilizador que se tornará a conta de administrador no VM |
   | **Senha do administrador VM** |Especifique uma senha para a nova conta |
   | **Confirmar senha** |Confirme a senha da nova conta |
   | **Rede Virtual** |Especificar a rede virtual Azure que o novo VM deve utilizar. Para obter mais informações sobre redes virtuais, consulte [a Visão Geral da Rede Virtual.](../../../virtual-network/virtual-networks-overview.md) |
   | **Sub-rede de rede virtual** |Especificar a sub-rede de rede virtual que o novo VM deve usar |
   | **Domain** |Confirmar que o valor pré-povoado para o domínio está correto |
   | **Nome do utilizador do domínio** |Especifique uma conta que está no grupo de administradores locais nos nóns de cluster locais |
   | **Palavra-passe** |Especificar a palavra-passe para o nome de utilizador do domínio |
8. Clique **em OK** para validar as definições de implementação.
9. Os termos legais são apresentados em seguida. Leia e clique **OK** se concordar com estes termos.
10. A página **'Especificar réplicas'** é novamente exibida. Verifique as definições da nova réplica do Azure nos **separadores Replicas**, **Endpoints**e **Backup Preferences.** Modifique as definições para satisfazer os requisitos do seu negócio.  Para obter mais informações sobre os parâmetros contidos nestes separadores, consulte [a página de replicações especificadas (Novo Assistente do Grupo de Disponibilidade/Assistente de Réplica de Adicionar)](https://msdn.microsoft.com/library/hh213088.aspx). Note que os ouvintes não podem ser criados usando o separador Listener para Grupos de Disponibilidade que contenham réplicas Azure. Além disso, se um ouvinte já tiver sido criado antes de lançar o Assistente, receberá uma mensagem indicando que não é suportada no Azure. Vamos ver como criar ouvintes na secção **Criar um Observador do Grupo de Disponibilidade.**
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Clique em **Seguinte**.
12. Selecione o método de sincronização de dados que pretende utilizar na página **de sincronização inicial de dados** e clique em **Seguinte**. Para a maioria dos cenários, selecione **A Sincronização completa de dados**. Para obter mais informações sobre os métodos de sincronização de [dados, consulte a página inicial de sincronização de dados (Sempre em Programas de Grupo de Disponibilidade)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Reveja os resultados na página **de Validação.** Corrija as questões pendentes e reencamúndia da validação, se necessário. Clique em **Seguinte**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Reveja as definições na página **Resumo** e, em seguida, clique em **Terminar**.
15. O processo de provisionamento começa. Quando o assistente estiver concluído com sucesso, clique **em "Close** to exit out" do assistente.

> [!NOTE]
> O Add Azure Replica Wizard cria um ficheiro de registo nos Utilizadores\Nome do utilizador\AppData\Local\SQL Server\AddReplicaWizard. Este ficheiro de registo pode ser usado para resolver implementações de réplicas falhadas do Azure. Se o Assistente falhar na execução de qualquer ação, todas as operações anteriores são revoadas, incluindo a eliminação do VM previsto.
> 
> 

## <a name="create-an-availability-group-listener"></a>Criar um ouvinte de grupo de disponibilidade
Após a criação do grupo de disponibilidade, deverá criar um ouvinte para que os clientes se conectem às réplicas. Os ouvintes direcionam as ligações de entrada para a réplica primária ou apenas para leitura. Para obter mais informações sobre os ouvintes, consulte [Configurar um ouvinte do ILB para sempre grupos de disponibilidade em Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Próximos passos
Além de utilizar o **Add Azure Replica Wizard** para estender o seu Grupo de Sempre À Disponibilidade para Azure, também pode mover algumas cargas de trabalho do SQL Server completamente para Azure. Para começar, consulte [a Provisioning a SQL Server Virtual Machine on Azure](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

Para outros tópicos relacionados com a execução do SQL Server em VMs Azure, consulte [o SQL Server em Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

