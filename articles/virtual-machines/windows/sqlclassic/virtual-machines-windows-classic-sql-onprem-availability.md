---
title: Expandir no local grupos de disponibilidade Always On para o Azure | Documentos da Microsoft
description: Este tutorial utiliza recursos criados com o modelo de implementação clássica e descreve como utilizar o Assistente Adicionar réplica no SQL Server Management Studio (SSMS) para adicionar uma réplica de grupo de Disponibilidade AlwaysOn no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: d3e56f1741a9cfd3f2d9f786c2ce22eb6a946ef2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481623"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Expandir no local grupos de disponibilidade Always On para o Azure
Grupos de Disponibilidade AlwaysOn proporcionar elevada disponibilidade para grupos de base de dados ao adicionar réplicas secundárias. Essas réplicas permitem efetuar a ativação pós-falha de bases de dados em caso de falha. Além disso pode ser utilizados para descarregar tarefas de cópia de segurança ou de cargas de trabalho de leitura.

Pode estender a grupos de disponibilidade no local para o Microsoft Azure ao aprovisionar uma ou mais VMs do Azure com o SQL Server e, em seguida, adicioná-los como réplicas de grupos de disponibilidade no local.

Este tutorial parte do princípio de que tem o seguinte:

* Uma subscrição ativa do Azure. Pode [Inscreva-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma existente grupo de Disponibilidade AlwaysOn no local. Para obter mais informações sobre grupos de disponibilidade, consulte [grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx).
* Conectividade entre a rede no local e a rede virtual do Azure. Para obter mais informações sobre como criar esta rede virtual, consulte [criar uma ligação Site a Site com o portal do Azure (clássico)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## <a name="add-azure-replica-wizard"></a>Adicionar Assistente de réplica do Azure
Esta secção mostra-lhe como utilizar o **Assistente para adicionar Azure réplica** para expandir a sua solução de grupo de Disponibilidade AlwaysOn para incluir as réplicas do Azure.

> [!IMPORTANT]
> O **Assistente para adicionar Azure réplica** só suporta máquinas virtuais criadas com o modelo de implementação clássica. Novas implementações de VM devem utilizar o modelo mais recente do Gestor de recursos. Se estiver a utilizar VMs com o Resource Manager, em seguida, tem de adicionar manualmente a réplica secundária do Azure, utilizando commmands de Transact-SQL (não mostrados aqui). Este assistente não irá funcionar no cenário do Resource Manager.

1. Partir do SQL Server Management Studio, expanda **elevada disponibilidade Always On** > **grupos de disponibilidade** > **[nome do seu grupo de disponibilidade]**.
2. Com o botão direito **réplicas de disponibilidade**, em seguida, clique em **Adicionar réplica**.
3. Por predefinição, o **adicionar réplicas ao Assistente do grupo de disponibilidade** é apresentado. Clique em **Seguinte**.  Se tiver selecionado o **não mostrar esta página novamente** opção na parte inferior da página durante uma inicialização anterior deste assistente, este ecrã não será apresentada.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Será solicitado para ligar a todas as réplicas secundárias existentes. Pode clicar em **Connect...** ao lado de cada réplica, ou pode clicar em **ligar todos os...** na parte inferior do ecrã. Após a autenticação, clique em **seguinte** para avançar para a próxima tela.
5. Sobre o **especificar réplicas** página, vários separadores são listados na parte superior: **Réplicas**, **pontos finais**, **preferências de cópia de segurança**, e **escuta**. Partir do **réplicas** separador, clique em **Adicionar réplica do Azure...** para iniciar o Assistente para Adicionar réplica do Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Selecione um certificado de gestão do Azure existente do arquivo de certificados local do Windows, se tiver instalado um antes. Selecione ou introduza o id de uma subscrição do Azure, se tiver utilizado um antes. Pode clicar em transferência para transferir e instalar um certificado de gestão do Azure e transferir a lista de subscrições com uma conta do Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Irá preencher cada campo na página com valores que vai ser utilizado para criar a Máquina Virtual (VM do Azure) que irá alojar a réplica.
   
   | Definição | Descrição |
   | --- | --- |
   | **Imagem** |Selecione a combinação pretendida de SO e o SQL Server |
   | **Tamanho da VM** |Selecione o tamanho de VM mais adequado às suas necessidades empresariais |
   | **Nome da VM** |Especifique um nome exclusivo para a nova VM. O nome tem de conter entre 3 e 15 carateres, pode conter apenas letras, números e hífenes e deve começar com uma letra e terminar com uma letra ou número. |
   | **Nome de utilizador VM** |Especifique um nome de utilizador que irá tornar-se a conta de administrador na VM |
   | **Palavra-passe de administrador VM** |Especifique uma palavra-passe para a nova conta |
   | **Confirmar palavra-passe** |Confirme a palavra-passe da nova conta |
   | **Rede Virtual** |Especifique a rede virtual do Azure que deve utilizar a nova VM. Para obter mais informações sobre redes virtuais, consulte [descrição geral da rede Virtual](../../../virtual-network/virtual-networks-overview.md). |
   | **Sub-rede de rede virtual** |Especifique a sub-rede de rede virtual que deve utilizar a nova VM |
   | **Domínio** |Confirmar que o valor preenchido previamente para o domínio está correto |
   | **Nome de utilizador de domínio** |Especifique uma conta que está no grupo de administradores locais em nós do local cluster |
   | **Palavra-passe** |Especifique a palavra-passe para o nome de utilizador de domínio |
8. Clique em **OK** para validar as definições de implementação.
9. Em seguida são apresentados os termos legais. Leia e clique em **OK** se concordar com estes termos.
10. O **especificar réplicas** página é exibida novamente. Verifique as definições para a nova réplica do Azure no **réplicas**, **pontos de extremidade**, e **preferências de cópia de segurança** separadores. Modifica as definições para cumprir os requisitos comerciais.  Para obter mais informações sobre os parâmetros contidos nessas guias, consulte [especificar a página de réplicas (disponibilidade Assistente novo grupo de Assistente/Adicionar réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Tenha em atenção que não não possível criar serviços de escuta com o separador de serviço de escuta para grupos de disponibilidade que contêm as réplicas do Azure. Além disso, se um serviço de escuta já tiver sido criado antes de iniciar o assistente, receberá uma mensagem a indicar que não é suportado no Azure. Vamos ver como criar serviços de escuta no **criar um serviço de escuta do grupo de disponibilidade** secção.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Clique em **Seguinte**.
12. Selecione o método de sincronização de dados que pretende utilizar no **selecionar sincronização de dados inicial** página e clique em **próxima**. Na maioria dos cenários, selecione **sincronização de dados completa**. Para obter mais informações sobre métodos de sincronização de dados, consulte [selecionar dados de sincronização de página inicial (sempre em disponibilidade grupo Assistentes)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Reveja os resultados na **validação** página. Corrigir problemas pendentes e volte a executar a validação, se necessário. Clique em **Seguinte**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Reveja as definições na **resumo** página, em seguida, clique em **concluir**.
15. Iniciar o processo de aprovisionamento. Quando o assistente ser concluído com êxito, clique em **fechar** para sair do assistente.

> [!NOTE]
> O Assistente para Adicionar réplica do Azure cria um ficheiro de registo no Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Este ficheiro de registo pode ser utilizado para resolver implementações de réplica do Azure com falha. Se o assistente não consegue executar qualquer ação, todas as operações anteriores são revertidas, incluindo a eliminar a VM aprovisionada.
> 
> 

## <a name="create-an-availability-group-listener"></a>Criar um serviço de escuta do grupo de disponibilidade
Depois de criado o grupo de disponibilidade, deve criar um serviço de escuta para clientes que ligam às réplicas. Serviços de escuta encaminham as ligações recebidas para a primária ou uma réplica secundária só de leitura. Para obter mais informações sobre serviços de escuta, consulte [configurar um serviço de escuta ILB para grupos de Disponibilidade AlwaysOn no Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Passos Seguintes
Além de utilizar o **Assistente para adicionar Azure réplica** para expandir o seu grupo de Disponibilidade AlwaysOn para o Azure, pode também mover algumas cargas de trabalho do SQL Server completamente para o Azure. Para começar a utilizar, veja [aprovisionar uma Máquina Virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados ao executar o SQL Server em VMs do Azure, consulte [SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

